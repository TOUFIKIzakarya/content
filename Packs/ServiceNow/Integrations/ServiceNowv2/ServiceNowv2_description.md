 To use ServiceNow on Cortex XSOAR, ensure your user account has the rest_api_explorer and web_service_admin roles.
 These roles are required to make API calls.
 However, they may not suffice for viewing records in some tables.
 Please make sure you have the correct role so you have permissions to work with the relevant table.
  
### Instance Configuration
The integration supports two types of authorization:
1. Basic authorization using username and password.
2. OAuth 2.0 authorization.

#### OAuth 2.0 Authorization
To use OAuth 2.0 authorization follow the next steps:
1. Login to your ServiceNow instance and create an endpoint for XSOAR to access your instance (please see [Snow OAuth](https://docs.servicenow.com/bundle/xanadu-platform-security/page/administer/security/concept/c_OAuthApplications.html) for more information). 
2. Copy the `Client Id` and `Client Secret` (press the lock next to the client secret to reveal it) that were automatically generated when creating the endpoint into the `Username` and `Password` fields of the instance configuration.
3. Select the `Use OAuth Login` checkbox and click the `Done` button.
4. Run the command `!servicenow-oauth-login` from the XSOAR CLI and fill in the username and password of the ServiceNow instance. This step generates an access token to the ServiceNow instance and is required only in the first time after configuring a new instance in the XSOAR platform.
5. (Optional) Test the created instance by running the `!servicenow-oauth-test` command.

**Notes:**
1. When running the `!servicenow-oauth-login` command, a refresh token is generated and will be used to produce new access tokens after the current access token has expired.
2. Every time the refresh token expires you will have to run the `servicenow-oauth-login` command again. Hence, we recommend to set the `Refresh Token Lifespan` field in the endpoint created in step 1 to a long period (can be set to several years). 
3. The grant type used to get an access token is `Resource owner password credentials`. See the [Snow documentation](https://docs.servicenow.com/bundle/xanadu-platform-security/page/administer/security/concept/c_OAuthApplications.html#d25788e201) for more information.


### Using Multi-Factor Authentication (MFA)
MFA can be used both when using basic authorization and when using OAuth 2.0 authorization, however we strongly recommend using OAuth 2.0 when using MFA.
If MFA is enabled for your user, follow the next steps:
1. Open the Google Authenticator application on your mobile device and make note of the number. The number refreshes every 30 seconds.
2. Enter your username and password, and append the One Time Password (OTP) that you currently see on your mobile device to your password without any extra spaces. For example, if your password is `12345` and the current OTP code is `424 058`, enter `12345424058`.

**Notes:**
1. When using basic authorization, you will have to update your password with the current OTP every time the current code expires (30 seconds), hence we recommend using OAuth 2.0 authorization.
2. For using OAuth 2.0 see the above instructions. The OTP code should be appended to the password parameter in the `!servicenow-oauth-login` command.
3. **look-back parameter note**:
In case the **look-back** parameter is initialized with a certain value and during a time that incidents were fetched, if changing 
the look back to a number that is greater than the previous value, then in the initial incident fetching there will be incidents duplications.
If the integration was already set with look back > 0, and the look-back is not being increased at any point of time, then those incident duplications would not occur.


#### JWT Authentication


#### Prerequisites in order to support JWT

1. Create a Java Key Store and upload it to the instance. (Accessing from the upper menu :**Al** > **System Definition** > **Certificates**.)
(Private key will be used as an integration parameter)
2. Configure a JWT signing key (Use the keystore from above. Keep the Key ID. It will be used as kid integration parameter)
(All→System OAuth→JWT Keys)
3. Create a JWT provider with a JWT signing key
(Customer required to set  in Standard Claims the same values for aud, iss and sub that will be used as integration parameters. Claim Name sub in Standard Claims has to be existing non-admin servicenow user with all necessary roles)

(All→System OAuth→JWT providers)
4. Connect to an OAuth provider and create an OAuth application registry (aud in JWT provider has to be equal to Client ID from OAuth JWT application - update JWT provider If necessary. The value of kid in JWT Verifier Maps has to be the  same as Key Id in JWT signing key. The value can be updated if necessary.)
(All→System OAuth→Application Registry)


5. Create API Access Policy or add Authentication profile to existing Policy (All→System Web Services→API Access Policies→Rest API Access Policies )

**IMPORTANT:**

1. Standard Authentication Profile of type Oauth should be already present in ServiceNow and this one needs to be added to Policy.
API Access Policy should be configured as global in order to cover all available resources and not just now/table
2. Granting JWT to admin is not allowed.
You should have a non-admin user with all necessary roles (only non-admin roles) in addition to the existing  role snc_platform_rest_api_access that is required to make API calls.

---
[View Integration Documentation](https://xsoar.pan.dev/docs/reference/integrations/service-now-v2)
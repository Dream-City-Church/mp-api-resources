# Dream City Church MP API Resources
#### Resources and notes for using the Ministry Platform API, specifically for Logic Apps and PowerAutomate

## Resource Links
- [Microsoft Azure Sponsorship for Non-Profits](https://www.microsoft.com/en-us/nonprofits/azure "Microsoft Azure Sponsorship for Non-Profits")
- [Faith Chapel API Documentation](https://github.com/faithchapel/mp-rest-documentation "Faith Chapel API Documentation")
- [MP API Swagger](https://your.church.org/ministryplatformapi/swagger "MP API Swagger")
- MP Knowledge Base:
 - [Webhooks Article](https://www.ministryplatform.com/kb/ministryplatform/advanced-users/extending-the-platform/webhooks "Webhooks")
 - [Webhooks Webinar](https://www.ministryplatform.com/kb/webinars/get-mp-talking-with-webhooks "Webhooks Webinar")
 - [REST API](https://www.ministryplatform.com/kb/develop/rest-api "REST API")
 
## Creating a Logic App Resource in Azure
1. Log in to your Azure account at https://portal.azure.com
2. Open the top-left menu and select "Create a resource".
3. Select or search for "Azure Logic Apps" and select "Create".
4. Select your Azure Subscription to bill your Logic App to, then create or select a Resource Group for your Logic App to be placed into.
5. Give your Logic App a unique name.
6. Select a region for your logic app. Some regions may or may not have some features, such as Zone Redundancy.
7. Change the Plan Type to "Consumption".
8. Enable or disabled Zone Redundancy. Zone Redundancy may improve the availablility of your Logic App in the event of an Azure regional outage, however additional charges may apply.
9. Select "Review + Create" to create your Logic App. Once your Logic App is deployed you will be able to select a trigger.

## Requesting an Auth Token for your Logic App
Most calls to the MP API will require you to send an Authentication Token in the API call header. In order to get this token, you will need to first send a call to OAuth with the MP API Client ID and Client Secret.

1. Create an API Client in Ministry Platform (Administration > API Clients). Remember, the API Client will have the same permissions as the API User selected and will show as the API User in the Audit Log. Note the Client ID and Client Secret for the next steps.
2. In your Azure Logic App, create a new HTTP action:
 - Method: POST
 - URI: https://[your.church.org]/ministryplatformapi/oauth/connect/token
 - Headers:
    - Authorization | Basic [BASE64 value of "ClientID:ClientSecret"]
    - Content-Type | application/x-www-form-urlencoded
 - Body:
`grant_type=client_credentials&client_id=[ClientID]&client_secret=[ClientSecret]&scope=http%3A%2F%2Fwww.thinkministry.com%2Fdataplatform%2Fscopes%2Fall&=`
![](https://raw.githubusercontent.com/Dream-City-Church/mp-api-resources/1b32f13df0a7274f9339f50cec7e6d606f2a730c/screenshots/Azure-Logic-App-HTTP-MP-Auth.png)
3. Next, create a Parse JSON action with the "Content" being the Body of the HTTP action and the below Schema:
```json
{
    "properties": {
        "access_token": {
            "type": "string"
        },
        "expires_in": {
            "type": "integer"
        },
        "token_type": {
            "type": "string"
        }
    },
    "type": "object"
}
```

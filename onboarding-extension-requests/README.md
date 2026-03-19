
### Onboarding Extension Request Firestore collection model

```json
{
  "id": "String",
  "type": "String",
  "userId": "String",
  "state": "String",
  "oldEndsOn": "number",
  "newEndsOn": "number",
  "message": "String",
  "createdAt": "Timestamp",
  "updatedAt": "Timestamp",
  "lastModifiedBy": "String",
  "reason": "String",
  "requestedBy": "String",
  "requestedNumber": "number"
}
```

#### Fields

| Field         | Type      | Description                                                             |
| ------------- | --------- | ------------------------------------------------------------------------|
| id            | String    | Unique identifier for the document.                                     |
| userId        | String    | The id of the user who created the request.                             |
| state         | String    | The state of the request like APPROVED, REJECTED, PENDING.              | 
| oldEndsOn     | number    | Old deadline of the onboarding task in millisecond.                     |
| newEndsOn     | number    | New deadline of the onboarding task in millisecond.                     |
| message       | String    | The message provided by superuser while approving or rejecting request. | 
| createdAt     | Timestamp | Unix timestamp for the creation time of the request.                    |
| updatedAt     | Timestamp | Unix timestamp for the last update time of the request.                 |
| lastModifiedBy| String    | The id of the superuser or request owner who processed the request.     |  
| reason        | String    | The reason for the extension request.                                   |
| requestedBy   | String    | The username of the user who created the request.                       |
| requestNumber | number    | The current request number of onboarding extension request.             |
| type          | String    | The type of request, i.e., ONBOARDING.                                  |

### Example data

#### Example for PENDING state

```json
{
  "id": "VYSTHeIERJlEtQdpViWo",
  "createdAt": 1741328586836,
  "updatedAt": 1741328586836,
  "type": "ONBOARDING",
  "state": "PENDING",
  "userId": "7bTCwaMgEnKSumhenkwj",
  "requestedBy": "mridul-khandelwal-1",
  "oldEndsOn": 1743683053755,
  "newEndsOn": 1744115053755,
  "reason": "testing",
  "requestNumber": 1
}
```

#### Example for APPROVED state

```json
{
  "id": "Vv5IPkhghnNyjPYhw6Ws",
  "createdAt": 1738686977925,
  "type": "ONBOARDING",
  "userId": "p86xesKNsxic6uFe6SZi",
  "requestedBy": "mohit-ramani-1",
  "oldEndsOn": 1740584089491,
  "requestNumber": 1,
  "reason": "test 2",
  "newEndsOn": 1741132800000,
  "lastModifiedBy": "65QiTlqudZfDk3i5W9WO",
  "state": "APPROVED",
  "message": "super-user message for testing",
  "updatedAt": 1738830512066
}
```

#### Example for REJECTED state

```json
{
  "id": "Vv5IPkhghnNyjPYhw6Ws",
  "createdAt": 1738686977925,
  "type": "ONBOARDING",
  "userId": "p86xesKNsxic6uFe6SZi",
  "requestedBy": "mohit-ramani-1",
  "oldEndsOn": 1740584089491,
  "requestNumber": 1,
  "reason": "test 2",
  "newEndsOn": 1741132800000,
  "lastModifiedBy": "65QiTlqudZfDk3i5W9WO",
  "state": "REJECTED",
  "message": "super-user message for testing",
  "updatedAt": 1738830512066
}
```

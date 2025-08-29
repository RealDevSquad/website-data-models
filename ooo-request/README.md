### OOO Status request Firestore collection model

```json
{
  "id": "String",
  "status": "<PENDING | APPROVED | REJECTED>",
  "from": "Timestamp",
  "until": "Timestamp",
  "comment": "String | null",
  "createdAt": "Timestamp",
  "requestedBy":"String",
  "updatedAt": "Timestamp",
  "lastModifiedBy": "String | null",
  "reason": "String",
  "type":"OOO"
}
```

#### Fields

| Field         | Type      | Description                                                |
| ------------- | --------- | ---------------------------------------------------------- |
| id            | String    | Unique identifier for the document.                        |
| requestedBy   | String    | UID of the user who created the request.                   |
| status        | String    | current status for the request: PENDING | APPROVED | REJECTED.                     |
| from          | Timestamp | Unix timestamp for the start date of the OOO request.      |
| until         | Timestamp | Unix timestamp for the end date of the OOO request.        |
| reason        | String    | Reason for requesting the OOO.                     |
| createdAt     | Timestamp | Unix timestamp for the creation time of the request.       |
| updatedAt     | Timestamp | Unix timestamp for the last update time of the request.    |
| lastModifiedBy| String    | The id of the superuser who processed the request          |
| comment       | String    | The reason for the APPROVED or REJECTED.                   |
| type          | String    | The type of request being sent like OOO, Onboarding etc    |

### Example data

#### Example for PENDING state

```json
{
  "id": "OfsT1Tlid4Y6Y0d",
  "requestedBy": "dfdsd5T1Tlid4Y6Y0d",
  "status": "PENDING",
  "from": 1709438800000,
  "until": 1709870800000,
  "reason": "Out of office for personal reasons.",
  "createdAt": 1709438900000,
  "updatedAt": 1709438900000,
  "comment": null,
  "lastModifiedBy":null,
  "type":"OOO"
}
```

#### Example for APPROVED state

```json
{
  "id": "MpykhM8sT1Tlid4Y6Y0d",
  "requestedBy": "dfdsd5T1Tlid4Y6Y0d",
  "status": "APPROVED",
  "from": 1709525300000,
  "until": 1709870800000,
  "reason": "Attending a work conference.",
  "createdAt": 1709525400000,
  "updatedAt": 1709827800000,
  "lastModifiedBy": "Sedv5T1Tlid4Y6Y0d",
  "comment": "Nice to have you back.",
  "type":"OOO"
}
```

#### Example for REJECTED state

```json
{
  "id": "Me8sT1Tlid4Y6Y0d",
  "requestedBy": "dfdsd5T1Tlid4Y6Y0d",
  "status": "REJECTED",
  "from": 1709603700000,
  "until": 1709785600000,
  "reason": "Out of office for personal reasons.",
  "createdAt": 1708763200000,
  "updatedAt": 1708841500000,
  "lastModifiedBy": "Sedv5T1Tlid4Y6Y0d",
  "comment": "Not enough vacation days.",
  "type":"OOO"
}
```

# Firestore user status collection data model

```
{
  "id": "String",
  "userId": "String",
  "currentStatus": {
    "state": "OOO | IDLE | ACTIVE",
    "updatedAt": "TimeStamp",
    "from": "TimeStamp",
    "until": "TimeStamp",
    "message": "String"
  },
  "futureStatus": {
    "state": "OOO | IDLE | ACTIVE",
    "updatedAt": "TimeStamp",
    "from": "TimeStamp",
    "until": "TimeStamp",
    "message": "String"
  },
  "monthlyHours": {
    "committed": "Number",
    "updatedAt": "TimeStamp"
  },
  "idleWindowStartedAt": "Number | null",
  "lastOooFrom": "Number | null",
  "lastOooUntil": "Number | null",
  "oooPeriods": "Array<{ from: Number, until: Number }>"
}
```
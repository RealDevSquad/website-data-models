# External Accounts

## Firestore collection:

```
{
  "type": string,
  "token": string,
  "createdOn": timestamp,
  "attributes" : object
}
```

- Optional fields for `attributes`:
    - `discordId` - number
    - `username` - string
    - `avatar` - string 
    - `discriminator` - string
    - `expiry` - timestamp

### Example
- For Discord Accounts
```
{
  "type": string,
  "token": string,
  "createdOn": timestamp,
  "attributes" : {
    "discordId": number,
    "username": string,
    "avatar": string,
    "discriminator": string,
    "expiry": timestamp,
  }
}
```

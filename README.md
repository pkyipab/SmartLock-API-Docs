<h2 align="center">SmartLock API Docs</h2>

Last Updates : 2023-05-18
```diff
+ Added Au3.deleteUserByUserId
+ Added I16.updateUserCompanyId
+ Added Cloud Function (6) onShareDeviceDeleteTrigger for removing in `users` -> `devices` -> `friendMap`. Excute when `shareDevices` Object deleted.

! Modified Au1.createUserByEmailAndPassword : allows insert companyId when creation. 
! Modified I13.getUserDevices : returns `documentId` (i.e. device's id)
```

# Authentication [Important]
Please note that our private APIs are specifically designed for internal or authorized use only. As such, the 'Authorization' header is a mandatory requirement to maintain the security and privacy of our sensitive data and functionality. It prevents unauthorized access and ensures that our APIs are utilized exclusively by approved users.

# Firebase Collections

By including the firebase collection segment (e.g. `/shareDevices/`) in the API endpoint URL, the API is specifically designed to interact with the documents stored within the shareDevices collection in Firestore. This allows users to perform operations, such as retrieving devices based on their descriptions, within the context of this collection.

# APIs
- [Authenticate](#Authenticate)
- [addDevices](#aaddDevices)
- [deviceSN](#bdeviceSN)
- [devices](#cdevices)
- [inbox](#dinbox)
- [models](#emodels)
- [openDeviceStatus](#fopenDeviceStatus)
- [shareDevices](#gshareDevices)
- [userOpenID](#huserOpenID)
- [users](#iusers)
- [Server Status](#status)

# Cloud Functions
These Cloud Functions are designed to handle various triggers and events within the Firestore database and Firebase Authentication. The functions perform specific tasks related to user creation, device updates, device deletion, email verification, and share device creation.

### 1. onUserCreateTrigger<br>
- Trigger: onCreate event of the Firestore document "users/{docId}"
- Description: Creates a new user and assigns an openID to the user.


### 2. onUserDeviceUpdate<br>

- Trigger: onUpdate event of the Firestore document "users/{docId}/devices/{docId}"
- Description: Updates shared device information when a user modifies their device.

### 3. onUserDeviceDelete<br>

- Trigger: onDelete event of the Firestore document "users/{docId}/devices/{docId}"
- Description: Handles device deletion for a user and removes corresponding shared devices.

### 4. sendConfirmationEmail<br>
- Trigger: onCreate event of a new user in Firebase Authentication
- Description: Sends a confirmation email with a verification link to the new user.
- 
### 5. onShareDeviceCreateTrigger<br>
- Trigger: onCreate event of the Firestore document "shareDevices/{docId}"
- Description: Handles the creation of a shared device and updates the user's device information.

### 6. onShareDeviceDeleteTrigger<br>
- Trigger: onDelete event of the Firestore document "shareDevices/{docId}"
- Description: Handles the deletion of a shared device and updates the user's device (friendMap) information.


### Command for deploying cloud functions

```bash
$ firebase deploy --only functions --debug
```

## Authenticate

### Au1. createUserByEmailAndPassword (Create User With Firebase Auth, and store user in DB) <br> 
- URL : https://api-smartlock.woowmoment.com/api/auth/createUserByEmailAndPassword<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```sql
email TEXT NOT NULL
password TEXT NOT NULL
name TEXT NOT NULL
companyId TEXT COMMENT 'Refers to company owner uid : if null -> create a company User with his own UID, otherwise add the company's UID to the new user'
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

### Au2. register ( Create User in DB ONLY ) <br>  
- URL : https://api-smartlock.woowmoment.com/api/auth/register<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
{
  "user" : {
    "uid" : "1234" //required field
// others
  }
}
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addOrEditerUser``, ``createUser``

### Au3. deleteUserByUserId <br> 
- URL : https://api-smartlock.woowmoment.com/api/auth/deleteUserByUserId<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```sql
userId TEXT NOT NULL
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

## A.addDevices

### A1. getAddDevicesByInviationCode<br> 
- URL : https://api-smartlock.woowmoment.com/api/addDevices/getAddDevicesByInviationCode<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
invitationCode TEXT NOT NULL
```
Response Type:
```
AddDevicesObject
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: {
    image: "https://firebasestorage.googleapis.com/v0/b/1.png",
    deviceName: "8e",
    userKey: null,
    ownerEmail: "x@gmail.com",
    macAddress: "F4:04:4C:95:43:8E",
    dataBaseId: null,
    invitationCode: "95438E",
  },
}
```

#Reference: ``seachDeviceAddAddInboxForCloudFirestore``

### A2. getAddDevicesByMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/addDevices/getAddDevicesByMacAddress<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
macAddress: TEXT NOT NULL 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": {
        "image": "https://firebasestorage.googleapis.com/v0/b/bg-connect-test.appspot.com/o/model%2Fpadlock_icon_small.png?alt=media&token=7c32b7c0-2bea-4e83-86b0-772c4c881026",
        "deviceName": "10EA",
        "userKey": null,
        "ownerEmail": "geginfoitem@gmail.com",
        "macAddress": "F4:04:4C:18:10:EA",
        "dataBaseId": null,
        "invitationCode": "1810EA"
    }
}
```

#Reference: ``getAddDevicesForCloudFirestore``

## B.deviceSN

### B1. getDeviceSNBybtMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getDeviceSNBybtMacAddress<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
btMacAddress: TEXT NOT NULL 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": {
        "serialNumber": "BGLA0120LA000408",
        "nicWifiMacAddress": null,
        "manufacturerDate": null,
        "modelNo": "BG-PL-1338",
        "adminkey": null,
        "defaultkey": "5788F13A4914DFDBD31CCF5FAA173096",
        "token": null,
        "userkey": null,
        "companyId": "A01",
        "qrCode": "002C0C9CCF697248D940D20C0B0ED576F851B27159D6B136",
        "imei": null,
        "btMacAddress": "F4:04:4C:18:10:EA",
        "productNo": null
    }
}
```

#Reference: ``getDefaultKey``

### B2. getDeviceSNByQrCode<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getDeviceSNByQrCode<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
qrCode: TEXT NOT NULL 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": {
        "serialNumber": "BGLA0120LA000408",
        "nicWifiMacAddress": null,
        "manufacturerDate": null,
        "modelNo": "BG-PL-1338",
        "adminkey": null,
        "defaultkey": "5788F13A4914DFDBD31CCF5FAA173096",
        "token": null,
        "userkey": null,
        "companyId": "A01",
        "qrCode": "002C0C9CCF697248D940D20C0B0ED576F851B27159D6B136",
        "imei": null,
        "btMacAddress": "F4:04:4C:18:10:EA",
        "productNo": null
    }
}
```

#Reference: ``getDeviceInfomation``

### B3. create<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/create<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
type deviceSNSchema = {
  adminKey: string;
  btMacAddress: string;
  defaultKey: string;
  imei: string;
  manufacturerDate: string;
  modelNo: string;
  nicWifiMacAddress: string;
  productNo: string;
  qrCode: string;
  serialNumber: string;
  token: string;
  userkey: string;
}
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addDeviceSN``

### B4. getDeviceSNBybtCompanyId<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getDeviceSNBybtCompanyId<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```sql
companyId TEXT NOT NULL 
```
- Response:
```
List<DeviceSN>
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      serialNumber: "BGLA0120LA000408",
      nicWifiMacAddress: null,
      manufacturerDate: null,
      modelNo: "BG-PL-1338",
      adminkey: null,
      defaultkey: "5788F13A4914DFDBD31CCF5FAA173096",
      token: null,
      userkey: null,
      companyId: "A01",
      qrCode: "002C0C9CCF697248D940D20C0B0ED576F851B27159D6B136",
      imei: null,
      btMacAddress: "F4:04:4C:18:10:EA",
      productNo: null,
      documentId: "002C0C9CCF697248D940D20C0B0ED576F851B27159D6B136",
    },
  ],
}
```

### B5. getDeviceSNByModelNo<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getDeviceSNByModelNo<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```sql
models Array<String> NOT NULL
```
- Response:
```
List<Map<DeviceSN>>
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      "BG-PL-1338": [
        {
          serialNumber: "BGLA0120LA000408",
          nicWifiMacAddress: null,
          manufacturerDate: null,
          modelNo: "BG-PL-1338",
          adminkey: null,
          defaultkey: "5788F13A4914DFDBD31CCF5FAA173096",
          token: null,
          userkey: null,
          companyId: "A01",
          qrCode: "002C0C9CCF697248D940D20C0B0ED576F851B27159D6B136",
          imei: null,
          btMacAddress: "F4:04:4C:18:10:EA",
          productNo: null,
        },
      ],
    },
  ],
}
```

### B6. deleteDeviceSNBybtMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/deleteDeviceSNBybtMacAddress<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```sql
btMacAddress TEXT NOT NULL 
```
- Response:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```


### B7. getAllDeviceSN<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getAllDeviceSN<br>
- Method : GET<br>
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": [
        {
            "lastScanTime": 0,
            "note": "this is app share lock",
            "latitude": 0,
            "companyName": null,
            "placeAddress": null,
            "endData": "2023-05-31",
            "nicMacAddress": null,
            "imageL": null,
            "ownerId": "SkyBOGreX9f7qhWDXteVr6U5YuL2",
            "stratTime": "12:00:00",
            "type": "lock",
            "deviceName": "1810b7",
            "companyID": "SkyBOGreX9f7qhWDXteVr6U5YuL2",
            "dataBaseId": null,
            "from": "ivorlaikawai@gmail.com",
            "btMacAddress": "F4:04:4C:18:10:B7",
            "map": null,
            "email": "ivorlaikawai+3@gmail.com",
            "longitude": 0,
            "image": null,
            "rssi": null,
            "locationName": null,
            "canShowMap": true,
            "userDeviceId": "MUfNy3UZeeJ4ni74zPVA",
            "userKey": "5zg5MTJHMBWtUSlb",
            "registrationToken": "ce-Mj7dTQleKPtFuZZDFZH:APA91bEKrTVAYVf-9YD6oMlvjQUeYIHax-bn3gqj1Xz7i3YdywCNFZs_Og0bKiNMIANWxy7SWEibtmMLeY78e3wEg-58X87yDjdyBlVkINgk0D_yJ142OmyPqsXPZ-V63jZ4qqvw6MuB",
            "guest": null,
            "endTime": "12:00:00",
            "stratData": "2023-05-01",
            "accessDays": [
                "Monday",
                "Friday"
            ],
            "isChange": false,
            "msd": null
        }
    ]
}
```

### B8. getAllDeviceSNCount<br> 
- URL : https://api-smartlock.woowmoment.com/api/deviceSN/getAllDeviceSNCount<br>
- Method : GET<br>
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": 2
}
```


## C.devices

### C1. create<br> 
**Allows static URL: (Use form-data if upload image needed) 
image : File | String
imageL : File | String
- URL : https://api-smartlock.woowmoment.com/api/devices/create<br>
- Accept: form-data OR application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body 
```
type devicesSchema = {
  description: string;
  image: string;
  imageL: string;
  lockType: string;
  modelName: string;
  modelNo: string;
  modelNumber: string;
  type: string;
}
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addDevice``

### C2. getAllDevices<br> 
- URL : https://api-smartlock.woowmoment.com/api/devices/getAllDevices<br>
- Method : GET<br>
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": [
        {
            "modelName": "E-Lock",
            "modelNumber": "E-Lock",
            "type": "lock",
            "description": "BG-PL-1338",
            "modelNo": "BG-PL-1338",
            "lockType": "main",
            "imageL": "https://firebasestorage.googleapis.com/v0/b/x.png",
            "image": "https://firebasestorage.googleapis.com/v0/b/x.png"
        }
    ]
}
```
#Reference: ``getDevices`` & ``getDevicesForCloudFirestore``

### C3. getDevicesByModelNo<br> 
- URL : https://api-smartlock.woowmoment.com/api/devices/getDevicesByModelNo<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
modelNo TEXT NOT NULL 
```
- Response Type:
```
List<Devices> 
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      image: "22",
      modelName: "1",
      description: "1",
      modelNumber: "1",
      imageL: "22",
      lockType: "1",
      modelNo: "1",
      type: "1",
    },
  ],
}
```

### C4. updateDevicesByModelNo<br> 
- URL : https://api-smartlock.woowmoment.com/api/devices/updateDevicesByModelNo<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
modelNo TEXT NOT NULL 
data Object NOT NULL 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

### C5. deleteDevicesByModelNo<br> 
- URL : https://api-smartlock.woowmoment.com/api/devices/deleteDevicesByModelNo<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
modelNo TEXT NOT NULL 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

### C6. getDevicesByDescription<br> 
- URL : https://api-smartlock.woowmoment.com/api/devices/getDevicesByDescription<br>
- Accept: application/json OR x-www-form-urlencoded<br>
- Method : POST<br>
- Body: 
```sql
description TEXT NOT NULL
```

- Response type :
```sql
List<Devices> NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      modelName: "E-Lock",
      modelNumber: "E-Lock",
      type: "lock",
      description: "BG-PL-1338",
      modelNo: "BG-PL-1338",
      lockType: "main",
      imageL: "https://firebasestorage.googleapis.com/v0/1.png",
      image: "https://firebasestorage.googleapis.com/v0/b/2.png",
    },
  ],
}
```

#Reference: ``seachDevice``

## D.inbox

### D1. create<br> 
- URL : https://api-smartlock.woowmoment.com/api/inbox/create<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
InboxObject
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addInboxForCloudFirestore``

### D2. deleteInboxByDocumentId<br> 
- URL : https://api-smartlock.woowmoment.com/api/inbox/deleteInboxByDocumentId<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
documentId TEXT 
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``deleteInbox``, ``deleteInboxAfterDeleteDevice``,``deleteInboxAfterKeyDevieLock``

### D3. getInboxByCustomField<br> 
- URL : https://api-smartlock.woowmoment.com/api/inbox/getInboxByCustomField<br>
- Accept: application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body Example: 
```jsx
fieldName TEXT 
value TEXT
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      fromDeviceImage: null,
      fromMacAddress: "F4:04:4C:65:43:7A",
      type: "from_lock",
      opertionTime1: null,
      opertionTime2: null,
      userKey: null,
      fromEmail: "mliweijian@gmail.com",
      toMacAddress: "F4:04:4C:95:44:AB",
      toDeviceImage: null,
      createTimeL: 1623754821598,
      fromDeviceName: "7A",
      fromName: "黎伟健",
      nextStep: "Finish",
      dataBaseId: "0G34jzo71sNTxHCzG2ru",
      step: "Removed",
      toDeviceName: "AB",
      toEmail: "mliweijian@gmail.com",
    },....
  ],
}
```

#Reference: ``getInboxForCloudFirestore``, ``getInboxForCloudFirestoreNofity``,``editerInboxForCloudFirestore``

## E.models

### E1. create<br> 
- URL : https://api-smartlock.woowmoment.com/api/models/create<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
ModelObject
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addModel``

### E2. getAllModels<br> 
- URL : https://api-smartlock.woowmoment.com/api/models/getAllModels<br>
- Method : GET<br>
Response Type:
```
List<Models>
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": [
        {
            "modelName": "Smart-Key",
            "modelNumber": "Smart-Key",
            "type": "BT"
        },
        {
            "type": "BT",
            "modelName": "E-Lock",
            "modelNumber": "E-Lock"
        },
        {
            "type": "BT",
            "modelName": "Gateway",
            "modelNumber": "Gateway"
        }
    ]
}
```

#Reference: ``getModels``, ``getModelsForCloudFirestore``

## F.openDeviceStatus

### F1. getStatusByLockMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/openDeviceStatus/getStatusByLockMacAddress<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
lockMacAddress TEXT NOT NULL
```
- Response type :
```sql
List<openDeviceStatus> NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      date: "2021-05-20 04:27:57",
      lockMacaddress: "F4:04:4C:18:11:18",
      userName: "poon poon",
      userId: "R8gd0U3C8ebVc3MXkH9qPl6kTvD2",
      openType: "open shackle",
    },
  ],
};
```

#Reference: ``getDeviceStatusForCloudFirestore``

### F2. createOpenDeviceStatus<br> 
- URL : https://api-smartlock.woowmoment.com/api/openDeviceStatus/createOpenDeviceStatus<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
deviceStatus deviceStatusObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``addLogForCloudFirestore``

## G.shareDevices


### G1. getShareDevicesByEmail<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/getShareDevicesByEmail<br>
- Accept:  application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body: 
```sql
email TEXT NOT NULL
```
Response Type:
```
Array<ShareDevice>
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      lastScanTime: 0,
      note: "steve",
      companyName: null,
      latitude: 0,
    },
  ],
}
```

#Reference: ``getShareDevicesForCloudFirestore``

### G2. getShareDevicesByDocumentId<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/getShareDevicesByDocumentId<br>
- Accept:  application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body: 
```sql
documentId TEXT NOT NULL
```
Response Type:
```
Array<ShareDevice>
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      lastScanTime: 0,
      note: "steve",
      companyName: null,
      latitude: 0,
    },
  ],
}
```

#Reference: ``getShareDevicesByIdForCloudFirestore``

### G3. getAllShareDevices<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/getAllShareDevices<br>
- Method : GET<br>
Response Type:
```
Array<ShareDevice>
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      lastScanTime: 0,
      note: "steve",
      companyName: null,
      latitude: 0
    },
  ],
}
```

#Reference: ``getShareDevice``


### G4. deleteShareDevicesByDocumentId<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/deleteShareDevicesByDocumentId<br>
- Accept:  application/json OR x-www-form-urlencoded
- Method : POST<br>
- Body: 
```sql
documentId TEXT NOT NULL
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``deleteShareDevice``

### G5. createShareDevices<br>
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/createShareDevices<br>
- Accept:  application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
deviceId TEXT NOT NULL
shareEmail TEXT NOT NULL
shareDevicePeriod shareDevicePeriodObject NOT NULL
additionalData Object
```
- Body Sample: 
```jsx
{
    "userId": "rNDVlnwt9mW6kghje7URNplIbz22",
    "deviceId": "byoMq7Jn9dl74QVBquo8",
    "shareEmail": "victoryip1234@gmail.com",
    "shareDevicePeriod": {
        "startDate": "2023-05-01",
        "startTime": "18:00:00",
        "endDate": "2023-05-31",
        "endTime": "23:59:59"
    },
    "additionalData" :{
         "type": "lock",
         "others": "remarks...."
    }
}
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``addShareDevice``

### G6. updateShareDevicesByDocumentId<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/updateShareDevicesByDocumentId<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```jsx
{
    "docuementId": "ID",
    "values": {
    "fieldName" : "newValue",
    "fieldName_2" : "newValue_2",
    ....
    }
}
```
Response Type:
```
BOOLEAN
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

#Reference: ``shareDevice``

### G7. getShareDevicesBybtMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/shareDevices/getShareDevicesBybtMacAddress<br>
- Accept: application/json
- Method : POST<br>
- Body Example: 
```sql
btMacAddress TEXT NOT NULL
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": [
        {
            "lastScanTime": 0,
            "note": "this is app share lock",
            "latitude": 0,
            "companyName": null,
            "placeAddress": null,
            "endData": "2023-05-31",
            "nicMacAddress": null,
            "imageL": null,
            "ownerId": "SkyBOGreX9f7qhWDXteVr6U5YuL2",
            "stratTime": "12:00:00",
            "type": "lock",
            "deviceName": "1810b7",
            "companyID": "SkyBOGreX9f7qhWDXteVr6U5YuL2",
            "dataBaseId": null,
            "from": "ivorlaikawai@gmail.com",
            "btMacAddress": "F4:04:4C:18:10:B7",
            "map": null,
            "email": "ivorlaikawai+3@gmail.com",
            "longitude": 0,
            "image": null,
            "rssi": null,
            "locationName": null,
            "canShowMap": true,
            "userDeviceId": "MUfNy3UZeeJ4ni74zPVA",
            "userKey": "5zg5MTJHMBWtUSlb",
            "registrationToken": "ce-Mj7dTQleKPtFuZZDFZH:APA91bEKrTVAYVf-9YD6oMlvjQUeYIHax-bn3gqj1Xz7i3YdywCNFZs_Og0bKiNMIANWxy7SWEibtmMLeY78e3wEg-58X87yDjdyBlVkINgk0D_yJ142OmyPqsXPZ-V63jZ4qqvw6MuB",
            "guest": null,
            "endTime": "12:00:00",
            "stratData": "2023-05-01",
            "accessDays": [
                "Monday",
                "Friday"
            ],
            "isChange": false,
            "msd": null,
            "documentId": "CdN3zm2j3fPuEauc4u67"
        },
    ]
}
```

## H.userOpenID

### H1. getUserOpenIdByUserId<br> 
- URL : https://api-smartlock.woowmoment.com/api/userOpenId/getUserOpenIdByUserId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
```
Response Type:
```
userOpenIdObject
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: {
    openID: 29,
    userEmail: "abc@gmail.com",
    userName: "ssfh",
    userID: "126aUoZHhWYXTd4Sfbc1ajT3b8d2",
  },
}
```

#Reference: ``getUserOpenIDOrAddUserOpenID``

### H2. updateUserOpenIdByUserId<br> 
- URL : https://api-smartlock.woowmoment.com/api/userOpenId/updateUserOpenIdByUserId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
userName TEXT NOT NULL
```
Response (if success):
```jsx
{
    "status": 0,
    "message": "success",
    "data": true
}
```

## I.users

### I1. createUserDeviceByUserId<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/createUserDeviceByUserId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
device DeviceObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``addMyDevice``,``addMyDeviceForCloudFirestore``,``addDeviceForWriteBatch``
    
### I2. updateDeviceByDeviceIdAndUserId<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateDeviceByDeviceIdAndUserId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL
data Object NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerDevice``
    
    
### I3. updateShareDevice<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateShareDevice<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
shareId TEXT NOT NULL
friendMap friendMapObject NOT NULL
shareDeviceMap shareDeviceMapObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editShareDeviceToFriendForCloudFirestore``

### I4. deleteShareDevice<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/deleteShareDevice<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
shareId TEXT NOT NULL
friendMap friendMapObject NOT NULL
```
- Response type :
```sql
List<Devices> NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``deleteShareFriendForCloudFirestore``

### I5. updateUserDeviceLocation<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDeviceLocation<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
location LocationObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerDeviceLastLocations``


### I6. updateUserDevicePin<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDevicePin<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
pinUsers pinUsersObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``saveDevicePinUsersForCloudFirestore``


### I7. updateUserDeviceLastUpdatedTime<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDeviceLastUpdatedTime<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
lastUpdatedTime unsigned int NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerDeviceLogLastUpdateTime``

### I8. updateUserDevice<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDevice<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
device deviceObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerMyDeviceForCloudFirestore``

### I9. updateUserDeviceKeyOrLockList<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDeviceKeyOrLockList<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
keyOrLockList keyOrLockListObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerKeyLockOrLockKeyForCloudFirestore``

### I10. updateUserDeviceGeoFencing<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserDeviceGeoFencing<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
geofencing geofencingObject NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerGeofencingForCloudFirestore``,``addGeofencingForCloudFirestore``

### I11. getUserOpenIdByIdList<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/getUserOpenIdByIdList<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
list List<Integer> NOT NULL
```
Response Type:
```java
List<userOpenID> 
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      openID: 2,
      userName: "黎伟健",
      userID: "9pkfNe1IefdWPnkenIqlzfya2mI2",
      userEmail: "mliweijian@gmail.com",
    },
  ],
}
```

#Reference: ``getUserOpenID``

### I12. getUserDeviceGeofencingByDocumentId<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/getUserDeviceGeofencingByDocumentId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
documentId TEXT NOT NULL COMMENT 'User's Device Id'
```
Response Type:
```
GeofecingObject
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: {
    geofencing_data_1: "first",
    geofencing_data_2: "second",
  },
}
```

#Reference: ``getDeviceGeofencingForCloudFirestore``

### I13. getUserDevices<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/getUserDevices<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
```
Response Type:
```
List<UserDevice> 
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      userSettingName: "bg-123",
      createTime: 0,
      friendMap: {
        test: 123,
      },
      lastUpdatedTime: 0,
      btMacAddress: "1234",
      documentId: "2WwtrxauXeYQRq7KLue32320"
    },
  ],
}
```

#Reference: ``getMyDeviceForCloudFirestore``, ``getMyDeviceForCloudFirestoreNotify``

### I14. updateUserSortType<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserSortType<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
sortType Number NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

#Reference: ``editerUserHomePageSortType``

### I15. getUserDeviceByMacAddress<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/getUserDeviceByMacAddress<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
btMacAddress TEXT NOT NULL
```
Response Type:
```
List<UserDevice> 
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
      userSettingName: "bg-123",
      createTime: 0,
      friendMap: {
        test: 123,
      },
      lastUpdatedTime: 0,
      btMacAddress: "1234",
    },
  ],
}
```

#Reference: ``getMyDeviceWhereEquaToMacAddress``

### I16. updateUserCompanyId<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/updateUserCompanyId<br>
- Accept: application/json
- Method : POST<br>
- Body: 
```sql
userId TEXT NOT NULL
companyId TEXT NOT NULL
```
Response Type:
```
Boolean
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: true,
}
```

### I17. getUserByEmail<br> 
- URL : https://api-smartlock.woowmoment.com/api/users/getUserByEmail<br>
- Accept: application/json 
- Method : POST<br>
- Body: 
```sql
email TEXT NOT NULL
```
Response (if success):
```jsx
{
  status: 0,
  message: "success",
  data: [
    {
            "image": null,
            "devices": null,
            "loginType": "email",
            "keys": null,
            "openID": 187,
            "jurisdiction": null,
            "uid": "COMPANY_ID",
            "registrationToken": "1234",
            "sortType": 0,
            "shareDevices": null,
            "kye": [],
            "shareDeviceList": null,
            "deviceList": null,
            "pwd": null,
            "age": null,
            "email": "USER_EMAIL",
            "key": null,
            "gropingRooms": null,
            "companyID": "COMPANY_ID",
            "companyName": "USER_COMPANY_NAME",
            "name": "USER_NAME",
            "documentId": "USER_UID"
        }
    ]
}
```

## Status

### health<br> 
- URL : https://api-smartlock.woowmoment.com/api/health<br>
- Method : GET<br>
Response (if success):
```jsx
OK
```






 

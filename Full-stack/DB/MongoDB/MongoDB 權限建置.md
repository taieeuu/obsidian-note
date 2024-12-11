首先當然要有一個Mongodb佈署至雲端或是本地，那我們當然連線進去是一個完全沒有權限的系統，如果這是一個正式的系統顯然這是不安全的，所以需要根據你的角色去建立權限。

### 工具

視覺化我們可以使用 MongoDB Compass ，然後使用 Mongo Shell 來進行指令輸入。

**注意**: 如果mongoDB未設置帳號密碼登入，則mongodb會預設為無身份驗證（noauth）模式運行，代表任何人都可以進行登入。如果沒有設置帳密的話，也就是預設狀態的話，我們在執行docker時最後面加上`--auth`來開啟身分驗證模式，

舉例: docker中設置，這樣設置代表這裡是根角色，具有一個最高權限的角色。
```yml
version: '3.9'
services:
  mongo:
    image: mongo:latest
    restart: always
    container_name: mongodb
    ports:
      - '27017:27017'
    volumes:
      - mongodb:/data/db
      - ./scripts:/scripts
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: root1234
volumes:
  mongodb: {}
```
### Step 1 建立Admin user
如果你沒有建立一個根角色的話，則需要創建一個管理權線來管理，這邊也就是根角色。
**且還需要進行啟用身分驗證，需要在 MongoDB 配置中啟用身份驗證。這通常涉及到修改 MongoDB 的配置文件（如 `mongod.conf`），在其中設置 `security.authorization: enabled` 來啟用身份驗證。並重啟**
```shell=
> use admin  
> db.createUser({  
'user': 'admin',  
'pwd': '<your-admin-password>',  
'roles': [{'role': 'root', 'db': 'admin'}]  
})
```
### Step 2 建立DB的Role
這邊的collection可設定可不設定，action也可進行權限的調整
```shell
> use db_name
> db.createRole({
  role: "<role_name>",
  privileges: [
    {
      resource: { db: "<db_name>", collection: "" },
      actions: ["find", "insert", "update", "remove"]
    }
  ],
})
```
查看所有roles
```shell
> use db_name
> db.getRoles({showBuildinRoles:true})
```
更進階設定可查看，[官方文件](https://www.mongodb.com/docs/v4.2/reference/method/db.createRole/)
### Step 3 建立DB的user
更進階設定可查看，[官方文件](https://www.mongodb.com/docs/v3.6/reference/method/db.createUser/)
```shell
> use db_name
> db.createUser({
'user':'<user_name>',
'pwd':'<user_pwd>',
'roles':[{'role':'readWrite', 'db':'<db_name>'}]
})
```
這邊的role也可以使用現有的role進行配置`{ role: "<custom_role_name>", db: "<db_name>" }`，在陣列中改為這樣。

上述修改完之後，我們需要重啟服務，並且指定mongodb 的 security.authorization 連線才可以。

查看DB User
```shell
>use db_name
>db.getUsers()
```
### Step4 建置完後，要如何連線
```shell
mongodb://dataReader:mdc6620@localhost:27017/?authSource=mdc_uat
```
>[!info]
>記得這邊要加上 哪個 DB 的權限
### 進階設定Mongod.conf 
但是如果有更多的設定我們會寫在 mongod.conf 來做設定，路徑會在 /etc/mongod.conf，詳情設定可參考，[官方文件](https://www.mongodb.com/docs/manual/reference/configuration-options/)
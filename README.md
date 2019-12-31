# Encrypted and Safely Hidden Properties #

This project is a demonstration of the [encrypted](https://docs.mulesoft.com/mule-runtime/4.2/secure-configuration-properties) and [safely hidden](https://docs.mulesoft.com/runtime-manager/secure-application-properties) properties.

## Versions ##
- AnyPoint Studio 7.4.1
- Mule Runtime 4.2.2


## Safely Hidden Properties ##
1. Create desired *.yaml* or *.properties* file to contain sensitive values
```YAML
db:
  host: "mydb.databases.com"
  port: "3306"
  user: "dbuser"
  password: "password123"
```
2. Add *Configuration Properties* global configuration
3. Update mule-artifact.json file to include keys to be hidden:
```JSON
{
  ...
  "minMuleVersion": "4.2.2",
  "secureProperties": ["db.host", "db.port", "db.user", "db.password"],
  ...
}
```
4. Deploy application to CloudHub
5. Set properties under Runtime Manager > Application > Settings...values should be hidden e.g.:
```
db.host=*******
db.port=*******
db.user=*******
db.password=*******
```


## Encrypted Values ##
1. Create desired *.yaml* or *.properties* file to contain sensitive values
```YAML
db:
  host: "mydb.databases.com"
  port: "3306"
  user: "dbuser"
  password: "password123"
```
2. Use [secure-properties-tool](https://docs.mulesoft.com/downloads/mule-runtime/4.2/secure-properties-tool.jar) from MuleSoft to encrypt the desired values, e.g.:
```bash
java -jar secure-properties-tool.jar \
  string \
  encrypt \
  Blowfish \
  CBC \
  mule123 \
  "password123"
```
3. Update properties file to contain the encrypted values within quotes and braces, e.g.:
```YAML
db:
  host: "mydb.databases.com"
  port: "3306"
  user: "![1Y8k7inov6KxB6otAjat0hnm6N5MWbGe8qIEJtEbJRtTaiWc7d5DgQ==]"
  password: "![PzHHkm5mzAT556cwRZ9ojiv2W6XiYStSzF0obf5jvYoS7iJaxiHAYg==]"
```
3. Add *Secure Properties* module to project
4. Add *Secure Properties Config* global element to project, specifying encryption settings, e.g.:
```XML
  <secure-properties:config name="Secure_Properties_Config" doc:name="Secure Properties Config"
    doc:id="87118ad6-c1c7-40c1-9320-88ae8c5ba69c" file="config.yaml" key="${secure.key}" >
	  <secure-properties:encrypt algorithm="Blowfish" />
  </secure-properties:config>
```
5. Reference the encrypted values from the secure properties file using the `${secure::key}` syntax, e.g.:
```XML
  <db:config name="Database_Config" doc:name="Database Config" doc:id="161b1833-f369-42d4-8319-eceb9ae63bb0" >
    <db:my-sql-connection host="${secure::db.host}" port="${secure::db.port}" user="${secure::db.user}" 
      password="${secure::db.password}" database="${secure::db.db}" />
  </db:config>
```


**NOTE**: These strategies can be combined, as demonstrated within this project.
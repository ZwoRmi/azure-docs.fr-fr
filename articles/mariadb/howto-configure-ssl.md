---
title: Configurer la connectivité SSL pour se connecter de manière sécurisée à Azure Database for MariaDB
description: Instructions pour configurer correctement Azure Database for MariaDB et les applications associées afin d’utiliser correctement les connexions SSL
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 00e8479eab927acccc8f797311a0a2d440bb96da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961669"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Configuration de la connectivité SSL dans votre application pour se connecter de manière sécurisée à Azure Database for MariaDB
Azure Database for MariaDB prend en charge la connexion de votre serveur Azure Database for MariaDB aux applications clientes à l’aide de SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="obtain-ssl-certificate"></a>Obtenir un certificat SSL
Téléchargez le certificat nécessaire pour communiquer via le protocole SSL avec votre serveur Azure Database for MariaDB à partir de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) et enregistrez le fichier de certificat sur votre disque local (ce tutoriel utilise c:\ssl à titre d’exemple).
**Pour Microsoft Internet Explorer et Microsoft Edge :** une fois le téléchargement terminé, renommez le certificat BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Créer une liaison SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connexion au serveur à l’aide de MySQL Workbench via le protocole SSL
Configurez MySQL Workbench pour vous connecter en toute sécurité via le protocole SSL. Dans la boîte de dialogue Configurer une nouvelle connexion, accédez à l’onglet **SSL**. Dans le champ **SSL CA File:**, entrez l’emplacement du fichier **BaltimoreCyberTrustRoot.crt.pem**. 
![enregistrer une vignette personnalisée](./media/howto-configure-ssl/mysql-workbench-ssl.png) Pour les connexions existantes, vous pouvez lier SSL en cliquant avec le bouton droit sur l’icône de connexion et en choisissant Modifier. Accédez ensuite à l’onglet **SSL**, puis liez le fichier de certificat.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connexion au serveur à l’aide de l’interface de ligne de commande MySQL via le protocole SSL
Vous pouvez également lier le certificat SSL dans l’interface de ligne de commande MySQL en exécutant la commande suivante :
```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="enforcing-ssl-connections-in-azure"></a>Application de connexions SSL dans Azure 
### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
À partir du portail Azure, accédez à votre serveur Azure Database for MariaDB, puis cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**, puis cliquez sur **Enregistrer**. Microsoft recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez activer ou désactiver le paramètre **ssl-enforcement** en utilisant respectivement les valeurs Enabled ou Disabled dans Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Vérifier la connexion SSL
Exécuter la commande MySQL **status** pour vérifier que vous êtes connecté à votre serveur MariaDB à l’aide de SSL :
```sql
status
```
Vérifiez que la connexion est chiffrée en examinant la sortie, qui doit indiquer : **SSL: Cipher in use is AES256-SHA** 

## <a name="sample-code"></a>Exemple de code
Pour établir une connexion sécurisée à Azure Database for MariaDB via SSL à partir de votre application, consultez les exemples de code suivants :

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
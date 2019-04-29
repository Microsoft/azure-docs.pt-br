---
title: Conectar aplicativos ao Banco de Dados do Azure para MariaDB
description: Este documento lista as cadeias de conexão atualmente com suporte para os aplicativos conectarem-se o Banco de Dados do Azure para MariaDB, incluindo ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61039662"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Como conectar aplicativos ao Banco de Dados do Azure para MariaDB
Este tópico lista os tipos de cadeia de conexão com suporte no Banco de Dados do Azure para MariaDB, juntamente com modelos e exemplos. Você pode ter parâmetros e configurações diferentes na cadeia de conexão.

- Para obter o certificado, consulte [Como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- Formato {your_user}@{servername} = userID para realizar a autenticação corretamente.  Se você usar somente a ID do usuário, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Neste exemplo, o nome do servidor é `mydemoserver`, o nome do banco de dados é `wpdb`, o nome de usuário é `WPAdmin` e a senha é `mypassword!2`. Como resultado, a cadeia de conexão deverá ser:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter os detalhes da cadeia de conexão no portal do Azure
No [portal do Azure](https://portal.azure.com), acesse o servidor de Banco de Dados do Azure para MariaDB e clique em **Cadeias de conexão** para obter a lista de cadeia de caracteres da sua instância: ![O painel de cadeias de conexão no portal do Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, servidor e outros parâmetros de conexão de banco de dados. Modifique esses exemplos para usar seus próprios parâmetros, como o nome do banco de dados, a senha e assim por diante. Depois, use essa cadeia de caracteres para se conectar ao servidor por meio do código e dos aplicativos.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->

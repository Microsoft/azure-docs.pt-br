---
title: Cadeias de conexão-banco de dados do Azure para MySQL
description: Este documento lista as cadeias de conexão com suporte no momento para os aplicativos se conectarem ao Banco de Dados do Azure para MySQL, incluindo ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541480"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como conectar aplicativos ao Banco de Dados do Azure para MySQL
Este tópico lista os tipos de cadeia de conexão com suporte do Banco de Dados do Azure para MySQL, juntamente com modelos e exemplos. Você pode ter parâmetros e configurações diferentes na cadeia de conexão.

- Para obter o certificado, consulte [Como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = \<servername>.mysql.database.azure.com
- Formato {your_user}@{servername} = userID para realizar a autenticação corretamente.  Se você usar somente a ID do usuário, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Neste exemplo, o nome do servidor é `mydemoserver`, o nome do banco de dados é `wpdb`, o nome de usuário é `WPAdmin` e a senha é `mypassword!2`. Como resultado, a cadeia de conexão deverá ser:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBCODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter os detalhes da cadeia de conexão no portal do Azure
No [Portal do Azure](https://portal.azure.com), acesse o servidor de Banco de Dados do Azure para MySQL e clique em **Cadeias de conexão** para obter a lista de cadeia de caracteres da sua instância: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="O painel Cadeias de conexão no Portal do Azure":::

A cadeia de caracteres fornece detalhes como o driver, servidor e outros parâmetros de conexão de banco de dados. Modifique esses exemplos para usar seus próprios parâmetros, como o nome do banco de dados, a senha e assim por diante. Depois, use essa cadeia de caracteres para se conectar ao servidor por meio do código e dos aplicativos.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre bibliotecas de conexões, consulte [Conceitos – Bibliotecas de conexões](./concepts-connection-libraries.md).

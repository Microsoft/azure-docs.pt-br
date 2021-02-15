---
title: Conectividade criptografada usando TLS/SSL no banco de dados do Azure para MySQL-servidor flexível
description: Instruções e informações sobre como se conectar usando TLS/SSL no banco de dados do Azure para MySQL-servidor flexível.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933749"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Conectividade criptografada usando 1,2 TLS (segurança de camada de transporte) no banco de dados do Azure para MySQL-servidor flexível

> [!IMPORTANT]
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

O servidor flexível do banco de dados do Azure para MySQL oferece suporte à conexão de aplicativos cliente com o serviço MySQL usando TLS (segurança de camada de transporte), anteriormente conhecido como SSL (protocolo SSL). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos cliente, permitindo que você atenda aos requisitos de conformidade.

O banco de dados do Azure para MySQL servidor flexível dá suporte apenas a conexões criptografadas que usam 1,2 TLS (segurança de camada de transporte) e todas as conexões de entrada com TLS 1,0 e TLS 1,1 serão negadas. Para todos os servidores flexíveis, a imposição de conexões TLS está habilitada e você não pode desabilitar o TLS/SSL para se conectar ao servidor flexível.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS/SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável para se conectar com segurança. O banco de dados do Azure para MySQL servidor flexível usa a *autoridade de certificação raiz global do DigiCert*. Baixe esse certificado necessário para se comunicar por SSL da [AC raiz global do DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e salve o arquivo de certificado em seu local preferido. Por exemplo, este tutorial usa `c:\ssl`.

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Conectar usando o cliente de linha de comando do MySQL com TLS/SSL

Caso tenha criado um servidor flexível com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Crie uma máquina virtual e adicione-a à VNET criada com o servidor flexível.

Caso tenha criado um servidor flexível com *acesso público (endereços IP permitidos)* , será possível adicionar seu endereço IP local à lista de regras de firewall no servidor.

Você pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md)--> para se conectar ao servidor do seu ambiente local. 

O exemplo a seguir mostra como se conectar ao seu servidor usando a interface de linha de comando do MySQL. Use a `--ssl-mode=REQUIRED` configuração cadeia de conexão para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `--ssl-ca` parâmetro. Substitua os valores por um nome do servidor e uma senha que sejam reais. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Confirme se o valor passado para `--ssl-ca` corresponde ao caminho do arquivo para o certificado que você salvou.

### <a name="verify-the-tlsssl-connection"></a>Verificar a conexão TLS/SSL

Execute o comando de **status** do MySQL para verificar se você se conectou ao servidor MySQL usando TLS/SSL:

```dos
mysql> status
```
Confirme se a conexão está criptografada examinando a saída, que deve mostrar:  **SSL: a criptografia em uso é AES256-SHA**. Esse conjunto de codificação mostra um exemplo e, com base no cliente, você pode ver um conjunto de codificação diferente.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS

Algumas estruturas de aplicativo que usam o MySQL para seus serviços de banco de dados não habilitam o TLS por padrão durante a instalação. O servidor MySQL impõe conexões TLS, mas se o aplicativo não estiver configurado para TLS, o aplicativo poderá falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="sample-code"></a>Código de exemplo
As cadeias de conexão predefinidas na página "cadeias de conexão" disponíveis para o servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando TLS/SSL. O parâmetro TLS/SSL varia de acordo com o conector. Por exemplo, "useSSL = true", "sslmode = required" ou "ssl_verify_cert = true" e outras variações.

Para estabelecer uma conexão criptografada com o servidor flexível sobre TLS/SSL do seu aplicativo, consulte os exemplos de código a seguir:

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (usando PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (conector do MySQL para Java)

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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (conector do MariaDB para Java)

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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Próximas etapas
- [Use o MySQL Workbench para se conectar e consultar dados no servidor flexível do Azure para MySQL](./connect-workbench.md)
- [Usar PHP para se conectar e consultar dados no servidor do Azure para MySQL flexível](./connect-php.md)
- [Criar e gerenciar uma rede virtual do servidor flexível do banco de dados do Azure para MySQL usando o CLI do Azure](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre a [rede no banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md)
- Saiba mais sobre [as regras de firewall de servidor flexíveis do banco de dados do Azure para MySQL](./concepts-networking.md#public-access-allowed-ip-addresses)

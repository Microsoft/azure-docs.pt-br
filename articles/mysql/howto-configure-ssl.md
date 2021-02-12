---
title: Configurar SSL-banco de dados do Azure para MySQL
description: Instruções sobre como configurar apropriadamente o Banco de Dados do Azure para MySQL e aplicativos associados a fim de usar as conexões SSL corretamente
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591573"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL

O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="step-1-obtain-ssl-certificate"></a>Etapa 1: Obter um certificado SSL

Baixe o certificado necessário para se comunicar por SSL com o servidor de banco de dados do Azure para MySQL do [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e salve o arquivo de certificado em sua unidade local (este tutorial usa c:\ssl, por exemplo).
**Para o Microsoft Internet Explorer e Microsoft Edge:** depois que o download for concluído, renomeie o certificado para BaltimoreCyberTrustRoot.crt.pem.

>[!NOTE]
> Com base nos comentários dos clientes, estendemos a substituição do certificado raiz para nossa CA raiz Baltimore existente até 15 de fevereiro de 2021 (02/15/2021).

> [!IMPORTANT] 
> O certificado raiz SSL está definido para expirar a partir de 15 de fevereiro de 2021 (02/15/2021). Atualize seu aplicativo para usar o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Para saber mais, confira [atualizações de certificado planejadas](concepts-certificate-rotation.md)

Consulte os links a seguir para obter certificados para servidores em nuvens soberanas: [Azure governamental](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Alemanha](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="step-2-bind-ssl"></a>Etapa 2: associar o SSL

Para obter cadeias de conexão de linguagem de programação específica, consulte o [código de exemplo](howto-configure-ssl.md#sample-code) abaixo.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Conectando ao servidor usando o MySQL Workbench sobre SSL

Configure o MySQL Workbench para se conectar com segurança por SSL.

1. Na caixa de diálogo Configurar Nova Conexão, navegue até a guia **SSL**.

1. Atualize o campo **usar SSL** para "exigir".

1. No campo **Arquivo de AC SSL** :, insira o local do arquivo de **BaltimoreCyberTrustRoot.crt.pem**.

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="Salvar configuração SSL":::

Para conexões existentes, você pode associar o SSL clicando com o botão direito do mouse no ícone de conexão e escolhendo Editar. Em seguida, navegue até a guia **SSL** e associe o arquivo de certificado.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conectar-se ao servidor usando a CLI do MySQL sobre SSL

Outra maneira de associar o certificado SSL é usar a interface de linha de comando do MySQL executando os seguintes comandos.

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Ao usar a interface de linha de comando do MySQL no Windows, é possível que você receba um erro `SSL connection error: Certificate signature check failed`. Se isso ocorrer, substitua os parâmetros `--ssl-mode=REQUIRED --ssl-ca={filepath}` por `--ssl`.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Etapa 3: impor conexões SSL no Azure

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Usando o Portal do Azure, visite seu servidor de Banco de Dados do Azure para MySQL e, em seguida, clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL** e, em seguida, clique em **Salvar**. A Microsoft recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Captura de tela de portal do Azure para impor conexões SSL no banco de dados do Azure para MySQL":::

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores Enabled ou Disabled respectivamente na CLI do Azure.

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Etapa 4: Verificar a conexão SSL

Execute o comando **status** de mysql para verificar se você se conectou ao servidor MySQL usando o SSL:

```dos
mysql> status
```

Confirme que a conexão é criptografada, revisando a saída, que deve mostrar: **SSL: A criptografia em uso é AES256 SHA**

## <a name="sample-code"></a>Código de exemplo

Para estabelecer uma conexão segura com o Banco de Dados do Azure para MySQL sobre SSL de seu aplicativo, veja os exemplos de código a seguir:

Consulte a lista de [drivers compatíveis](concepts-compatibility.md) com suporte pelo banco de dados do Azure para o serviço mysql.

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (usando PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Próximas etapas

Analise as várias opções de conectividade do aplicativo em [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

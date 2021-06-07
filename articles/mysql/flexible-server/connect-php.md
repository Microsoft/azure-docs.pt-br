---
title: 'Início Rápido: Conectar-se usando o PHP – Banco de Dados do Azure para MySQL – Servidor Flexível'
description: Este início rápido fornecerá vários exemplos de código PHP que poderão ser usados para se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível e consultar dados dele.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: baba4d373d4a79ab0c339aac00bb9ab48de9262b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109600"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Início Rápido: Usar o PHP para se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível e consultar dados dele

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este início rápido demonstrará como se conectar a um Servidor Flexível do Banco de Dados do Azure para MySQL usando um aplicativo [PHP](https://secure.php.net/manual/intro-whatis.php). Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este artigo pressupõe que você está familiarizado com um desenvolvimento que usa o PHP, mas não tem experiência em trabalhar com um Servidor Flexível do Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:

- [Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-server-portal.md)
- [Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Como preparar sua estação de trabalho cliente
1. Caso tenha criado um servidor flexível com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Crie uma máquina virtual e adicione-a à VNET criada com o servidor flexível. Consulte [Criar e gerenciar uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./how-to-manage-virtual-network-cli.md).

2. Caso tenha criado um servidor flexível com *acesso público (endereços IP permitidos)* , será possível adicionar seu endereço IP local à lista de regras de firewall no servidor. Consulte [Criar e gerenciar regras de firewall do Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>Instalar o PHP

Instalar o PHP em seu próprio servidor ou crie um [aplicativo Web](../../app-service/overview.md) do Azure que inclua o PHP.  Consulte [Criar e gerenciar regras de firewall](./how-to-manage-firewall-portal.md) para saber como criar regras de firewall.

#### <a name="macos"></a>macOS

1. Baixar o [PHP 7.1.4 versão](https://secure.php.net/downloads.php).
2. Instalar o PHP e consultar o [manual do PHP](https://secure.php.net/manual/install.macosx.php) para outras configurações.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Baixar o [PHP 7.1.4 versão protegida não thread (x64)](https://secure.php.net/downloads.php).
2. Instalar o PHP e consultar o [manual do PHP](https://secure.php.net/manual/install.unix.php) para outras configurações.

#### <a name="windows"></a>Windows

1. Baixar o [PHP 7.1.4 versão protegida não thread (x64)](https://windows.php.net/download#php-7.1).
2. Instalar o PHP e consultar o [manual do PHP](https://secure.php.net/manual/install.windows.php) para outras configurações.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao Servidor Flexível do Banco de Dados do Azure para MySQL. Será necessário obter um nome do servidor totalmente qualificado e as credenciais de entrada.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Selecione o nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Como conectar-se a um servidor flexível usando TLS/SSL no PHP

Para estabelecer uma conexão criptografada com um servidor flexível por TLS/SSL do aplicativo, consulte os exemplos de código a seguir. Será possível baixar o certificado necessário para se comunicar por TLS/SSL de [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Conectar-se e criar uma tabela

Use o código a seguir para se conectar e criar uma tabela usando a instrução SQL **CREATE TABLE**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. Os métodos de chamadas de código [mysqli_init](https://secure.php.net/manual/mysqli.init.php) e [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) para se conectar ao MySQL. Em seguida, ele chama o método [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta. Em seguida, ele chama o método [mysqli_close](https://secure.php.net/manual/mysqli.close.php) para fechar a conexão.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Inserir dados

Use o código a seguir para se conectar e inserir dados usando uma instrução SQL **INSERT**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar um instrução insert preparada, associa parâmetros a cada valor de coluna inserido usando o método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Ler dados

Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**.  O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta sql e o método [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) para buscar as linhas resultantes.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Atualizar dados

Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução update preparada e associa os parâmetros de cada valor de coluna atualizada usando o método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução delete preparada e associa os parâmetros da cláusula where na instrução usando o método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Próximas etapas
- [Conectividade criptografada usando o TLS 1.2 (Transport Layer Security) no Banco de Dados do Azure para MySQL – Servidor Flexível](./how-to-connect-tls-ssl.md).
- Saiba mais sobre a [Rede no Servidor Flexível do Banco de Dados do Azure para MySQL](./concepts-networking.md).
- [Crie e gerencie regras de firewall do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-firewall-portal.md).
- [Crie e gerencie uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-virtual-network-portal.md).
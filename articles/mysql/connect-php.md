---
title: 'Início Rápido: Conectar-se usando PHP – Banco de Dados do Azure para MySQL'
description: Este guia de início rápido fornece vários exemplos de código PHP que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132895"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Início Rápido: Usar PHP para se conectar e consultar dados no Banco de Dados do Azure para MySQL
Este guia de início rápido demonstra como se conectar a um banco de dados do Azure para MySQL usando aplicativo [PHP](https://secure.php.net/manual/intro-whatis.php). Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados.

## <a name="prerequisites"></a>Pré-requisitos
Para este início rápido você precisa:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- Criar um servidor individual de Banco de Dados do Azure para MySQL usando o [portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> ou a [CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) se ainda não tiver um.
- Com base em se você está usando o acesso público ou privado, conclua **UMA** das ações abaixo para habilitar a conectividade.

    |Ação| Método de conectividade|Guia de instruções|
    |:--------- |:--------- |:--------- |
    | **Configurar regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **Configurar Ponto de Extremidade de Serviço** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **Configurar link privado** | Privados | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Criar um banco de dados e um usuário não administrador](./howto-create-users.md?tabs=single-server)
- Instalar a versão mais recente do PHP para o sistema operacional
    - [PHP no macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP no Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP no Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Estamos usando a biblioteca [MySQLi](https://www.php.net/manual/en/book.mysqli.php) para gerenciar a conexão e consultar o servidor neste início rápido.

## <a name="get-connection-information"></a>Obter informações de conexão
Você pode obter as informações de conexão do servidor de banco de dados do portal do Azure seguindo estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Navegue até a página Bancos de Dados do Azure para MySQL. Você pode pesquisar e selecionar o **Banco de Dados do Azure para MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Localizar o Banco de Dados do Azure para MySQL":::

2. Selecione o servidor MySQL (como **mydemoserver**).
3. Na página **Visão Geral**, copie o nome do servidor totalmente qualificado ao lado do **Nome do servidor** e o nome de usuário administrador, ao lado do **Nome de logon do administrador do servidor**. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!IMPORTANT]
> - Caso tenha esquecido a senha, você poderá [redefini-la](./howto-create-manage-server-portal.md#update-admin-password).
> - Substitua os parâmetros **host, nome de usuário, senha** e **db_name** pelos seus valores**

## <a name="step-1-connect-to-the-server"></a>Etapa 1: Conectar ao servidor
O SSL é habilitado por padrão. Talvez seja necessário baixar o [certificado SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) para se conectar do ambiente local. Esse código chama:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) para inicializar o MySQLi.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) para apontar para o caminho do certificado SSL. Isso é necessário para seu ambiente local, mas não é necessário para o aplicativo Web do Serviço de Aplicativo ou para as Máquinas Virtuais do Azure.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) para se conectar ao MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) para fechar a conexão.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Etapa 2: Criar uma tabela
Use o código a seguir para se conectar. Esse código chama:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta.
```php
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
```

## <a name="step-3-insert-data"></a>Etapa 3: Inserir dados
Use o código a seguir para inserir dados usando uma instrução SQL **INSERT**. Esse código usa os métodos:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução INSERT preparada
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) para associar os parâmetros a cada valor de coluna inserido.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) para fechar a instrução usando o método


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Etapa 4: Ler dados
Use o código a seguir para ler os dados empregando uma instrução SQL **SELECT**.  Esse código usa os métodos:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta **SELECT**
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) para buscar as linhas resultantes.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Etapa 5: Excluir dados
Use o código a seguir para excluir linhas usando uma instrução SQL **DELETE**. O código usa os métodos:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução DELETE preparada
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) para associar os parâmetros
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) para executar a instrução DELETE preparada
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) para fechar a instrução

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando o portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando a CLI](./how-to-manage-single-server-cli.md)

[Não foi possível encontrar o que estava procurando? Fale conosco.](https://aka.ms/mysql-doc-feedback)

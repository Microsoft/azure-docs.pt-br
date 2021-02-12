---
title: Implementar uma solução distribuída geograficamente
description: Saiba como configurar seu banco de dados no banco de dados SQL do Azure e no aplicativo cliente para failover para um banco de dados replicado e failover de teste.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 98e3eb4927b8eb9e52fd974c1ef7c417aff2ad54
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422783"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Tutorial: implementar um banco de dados distribuído geograficamente (banco de dados SQL do Azure)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configure um banco de dados no banco de dados SQL e no aplicativo cliente para failover para uma região remota e teste um plano de failover. Você aprenderá como:

> [!div class="checklist"]
>
> - Criar um [grupo de failover](auto-failover-group-overview.md)
> - Executar um aplicativo Java para consultar um banco de dados no banco de dados SQL
> - Failover de Teste

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para concluir o tutorial, verifique se você instalou os seguintes itens:

- [PowerShell do Azure](/powershell/azure/)
- Um banco de dados individual no banco de dados SQL do Azure. Para criar um, use
  - [O portal do Azure](single-database-create-quickstart.md)
  - [A CLI do Azure](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > O tutorial usa o banco de dados de exemplo *AdventureWorksLT*.

- Java e Maven, confira [Compilar um aplicativo usando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), realce **Java** e selecione seu ambiente; em seguida, siga as etapas.

> [!IMPORTANT]
> Não se esqueça de configurar regras de firewall para usar o endereço IP público do computador em que você está executando as etapas nesse tutorial. As regras de firewall no nível do banco de dados serão replicadas automaticamente para o servidor secundário.
>
> Para obter informações, consulte [criar uma regra de firewall no nível de banco de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP usado para a regra de firewall no nível de servidor para seu computador, consulte [criar um firewall de nível de servidor](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Criar um grupo de failover

Usando Azure PowerShell, crie [grupos de failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor em outra região. Em seguida, adicione o banco de dados ao grupo de failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Para criar um grupo de failover, execute o seguinte script:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

> [!IMPORTANT]
> Execute `az login` para entrar no Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

As configurações de replicação geográfica também podem ser alteradas no portal do Azure, selecionando seu banco de dados e, em seguida, **configurações** de  >  **replicação geográfica**.

![Configurações da replicação geográfica](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Executar o projeto de exemplo

1. No console, crie um projeto do Maven com o seguinte comando:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Digite **Y** e pressione **Enter**.

1. Altere os diretórios para o novo projeto.

   ```bash
   cd SqlDbSample
   ```

1. Usando seu editor favorito, abra o arquivo *pom.xml* na pasta do projeto.

1. Adicione a dependência do Microsoft JDBC Driver para SQL Server adicionando a seção `dependency` a seguir. A dependência deve ser colada dentro da seção `dependencies` maior.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Especifique a versão de Java adicionando a seção `properties` após a seção `dependencies`:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Dê suporte a arquivos de manifesto adicionando a seção `build` após a seção `properties`:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Salve e feche o arquivo *pom.xml*.

1. Abra o arquivo *App.java* localizado em ..\SqlDbSample\src\main\java\com\sqldbsamples e substitua o conteúdo pelo código a seguir:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Salve e feche o arquivo *app. java* .

1. No console de comando, execute o seguinte comando:

   ```bash
   mvn package
   ```

1. Inicie o aplicativo que será executado por cerca de 1 hora até ser interrompido manualmente, permitindo que você tenha tempo para executar o teste de failover.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Failover de Teste

Execute os scripts a seguir para simular um failover e observe os resultados do aplicativo. Observe como algumas inserções e seleções falharão durante a migração do banco de dados.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Você pode verificar a função do servidor de recuperação de desastre durante o teste com o seguinte comando:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Para testar um failover:

1. Inicie um failover manual do grupo de failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Reverta o grupo de failover de volta para o servidor primário:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Você pode verificar a função do servidor de recuperação de desastre durante o teste com o seguinte comando:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Para testar um failover:

1. Inicie um failover manual do grupo de failover:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Reverta o grupo de failover de volta para o servidor primário:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um banco de dados no banco de dados SQL do Azure e um aplicativo para failover para uma região remota e testou um plano de failover. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar um grupo de failover de replicação geográfica
> - Executar um aplicativo Java para consultar um banco de dados no banco de dados SQL
> - Failover de Teste

Avance para o próximo tutorial sobre como adicionar uma instância do Azure SQL Instância Gerenciada a um grupo de failover:

> [!div class="nextstepaction"]
> [Adicionar uma instância do Azure SQL Instância Gerenciada a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md)

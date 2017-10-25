---
title: "Implementar uma solução de Banco de Dados SQL do Azure distribuída geograficamente | Microsoft Docs"
description: Saiba como configurar o Banco de Dados SQL do Azure e o aplicativo para o failover para um banco de dados replicado e failover de teste.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: 6fdbf12830a402eeb004587202eee604037430c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="implement-a-geo-distributed-database"></a>Implementar um banco de dados distribuído geograficamente

Neste tutorial, você irá configurar um banco de dados SQL do Azure e o aplicativo para o failover para uma região remota e, em seguida, testar o seu plano de failover. Você aprenderá como: 

> [!div class="checklist"]
> * Criar usuários de banco de dados e conceder a eles permissões
> * Configurar uma regra de firewall de nível de banco de dados
> * Criar um [grupo de failover de replicação geográfica](sql-database-geo-replication-overview.md)
> * Criar e compilar um aplicativo Java para consultar um Banco de Dados SQL do Azure
> * Executar uma análise de recuperação de desastre

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

- O [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) mais recente instalado. 
- Um Banco de Dados SQL do Azure instalado. Este tutorial usa o banco de dados de exemplo AdventureWorksLT com um nome de **mySampleDatabase** de um desses inícios rápidos:

   - [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
   - [Criar Banco de dados - CLI](sql-database-get-started-cli.md)
   - [Criar Banco de dados - PowerShell](sql-database-get-started-powershell.md)

- Tendo identificado um método para executar scripts SQL no banco de dados, você pode usar uma das ferramentas de consulta a seguir:
   - O editor de consultas no [Portal do Azure](https://portal.azure.com). Para obter mais informações sobre como usar o editor de consultas no Portal do Azure, consulte [Conectar e consultar usando o Editor de Consultas](sql-database-get-started-portal.md#query-the-sql-database).
   - A versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) é um ambiente integrado para gerenciar qualquer infraestrutura do SQL, do SQL Server para o Banco de Dados SQL do Microsoft Windows.
   - A versão mais recente do [Visual Studio Code](https://code.visualstudio.com/docs), que é um editor de código gráfico para o Linux, macOS e Windows que dá suporte às extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, Banco de Dados SQL do Azure e SQL Data Warehouse. Para obter mais informações sobre como usar essa ferramenta com o Banco de Dados SQL do Azure, consulte [Conectar e consultar com código VS](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Criar usuários de banco de dados e conceder permissões

Conecte-se ao banco de dados e crie contas de usuário usando uma das ferramentas de consulta a seguir:

- O editor de consultas no Portal do Azure
- SQL Server Management Studio
- Visual Studio Code

Essas contas de usuário são replicadas automaticamente para o servidor secundário (e mantidas sincronizadas). Para usar o SQL Server Management Studio ou o Visual Studio Code, talvez seja necessário configurar uma regra de firewall, se você estiver se conectando de um cliente em um endereço IP para o qual você ainda não tiver configurou um firewall. Para encontrar as etapas detalhadas, consulte [Criar uma regra de firewall no nível de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Na janela de consulta, execute a consulta a seguir para criar duas contas de usuário em seu banco de dados. Esse script concede ao **db_owner** permissões para a conta **app_admin** e concede as permissões **SELECT** e **UPDATE** à conta **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Criar firewall no nível de banco de dados

Crie uma [regra de firewall de nível de banco de dados](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) para seu Banco de Dados SQL. Essa regra de firewall de nível de banco de dados é replicada automaticamente para o servidor secundário criado neste tutorial. Para manter a simplicidade (neste tutorial), use o endereço IP público do computador em que você está executando as etapas nesse tutorial. Para determinar o endereço IP usado para a regra de firewall de nível de servidor para o computador atual, consulte [Criar um firewall de nível de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).  

- Na janela de consulta aberta, substitua a consulta anterior pela consulta seguinte, substituindo os endereços IP pelos endereços IP apropriados para o seu ambiente.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Criar um grupo de failover automático de replicação geográfica ativa 

Usando o Azure PowerShell, crie um [grupo de failover automático de replicação geográfica ativa](sql-database-geo-replication-overview.md) entre o SQL Server do Azure existente e o novo SQL Server do Azure vazio em uma região do Azure e adicione o banco de dados de exemplo ao grupo de failover.

> [!IMPORTANT]
> Esses cmdlets requerem o Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Popule variáveis para seus scripts do PowerShell usando os valores para seu servidor existente e o banco de dados de exemplo e forneça um valor globalmente exclusivo para o nome do grupo de failover.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Criar um servidor de backup vazio na sua região de failover.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Crie um grupo de failover entre os dois servidores.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Adicione o banco de dados ao grupo de failover.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Instalar o software Java

As etapas nesta seção pressupõem que você esteja familiarizado com o desenvolvimento usando o Java e começou recentemente a trabalhar com o Banco de Dados SQL do Azure. 

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto Java. Instale o **brew** e o **Maven** ao inserir os seguintes comandos: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Para obter orientações detalhadas sobre como instalar e configurar o ambiente Java e Maven, acesse o [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Criar um aplicativo usando SQL Server), selecione **Java**, **MacOS** e siga as instruções detalhadas para configurar o Java e o Maven nas etapas 1.2 e 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto Java. Instale o **Maven** ao inserir os seguintes comandos:

```bash
sudo apt-get install maven
```

Para obter orientações detalhadas sobre como instalar e configurar o ambiente Java e Maven, acesse o [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Criar um aplicativo usando SQL Server), selecione **Java**, **Ubuntu** e siga as instruções detalhadas para configurar o Java e o Maven nas etapas 1.2, 1.3 e 1.4.

### <a name="windows"></a>**Windows**
Instale o [Maven](https://maven.apache.org/download.cgi) usando o instalador oficial. Use o Maven para ajudar a gerenciar dependências, compilar, testar e executar seu projeto Java. Para obter orientações detalhadas sobre como instalar e configurar o ambiente Java e Maven, acesse o [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Criar um aplicativo usando SQL Server), selecione **Java**, Windows e siga as instruções detalhadas para configurar o Java e o Maven nas etapas 1.2 e 1.3.

## <a name="create-sqldbsample-project"></a>Criar projeto SqlDbSample

1. No console de comando (como Bash), crie um projeto do Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Digite **Y** e clique em **Enter**.
3. Altere os diretórios para seu projeto recém-criado.

   ```bash
   cd SqlDbSamples
   ```

4. Usando seu editor favorito, abra o arquivo pom.xml na pasta do projeto. 

5. Adicione o Microsoft JDBC Driver para a dependência do SQL Server ao seu projeto do Maven abrindo o editor de texto favorito e copiando e colando as seguintes linhas no arquivo pom.xml. Não substitua os valores existentes pré-populados no arquivo. A dependência do JDBC deve ser colada dentro da seção “dependencies” maior ( ).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Especifique a versão do Java com a qual compilar o projeto adicionando a seguinte seção “properties” no arquivo pom.xml após a seção “dependencies”. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Adicione a seguinte seção "build" no arquivo pom.xml após a seção "properties" para dar suporte a arquivos de manifesto nos jars.       

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
8. Salve e feche o arquivo pom.xml.
9. Abra o arquivo App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) e substitua o conteúdo pelo seguinte conteúdo. Substitua o nome do grupo de failover pelo nome do seu grupo de failover. Se você tiver alterado os valores para o nome do banco de dados, usuário ou senha, altere esses valores também.

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
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
6. Salve e feche o arquivo App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Compilar e executar o projeto SqlDbSample

1. No console de comando, execute o comando a seguir.

   ```bash
   mvn package
   ```
2. Quando terminar, execute o seguinte comando para executar o aplicativo (ele é executado por aproximadamente 1 hora, a menos que você pare manualmente):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Executar o treinamento de recuperação de desastre

1. Chame o failover manual do grupo de failover. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Observe os resultados do aplicativo durante o failover. Algumas inserções falham enquanto o cache de DNS é atualizado.     

3. Descubra qual função seu servidor de recuperação de desastre executando.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Failback.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Observe os resultados do aplicativo durante o failback. Algumas inserções falham enquanto o cache de DNS é atualizado.     

6. Descubra qual função seu servidor de recuperação de desastre executando.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md).

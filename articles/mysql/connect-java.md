---
title: 'Início Rápido: Usar o Java e o JDBC com o Banco de Dados do Azure para MySQL'
description: Saiba como usar o Java e o JDBC com um banco de dados do Banco de Dados do Azure para MySQL.
author: jdubois
ms.author: judubois
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 457f7e07391c647d2ab0e7d78197086f6f5e2cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187775"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql"></a>Início Rápido: Usar o Java e o JDBC com o Banco de Dados do Azure para MySQL

Este tópico demonstra como criar um aplicativo de exemplo que usa o Java e o [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) para armazenar e recuperar informações no [Banco de Dados do Azure para MySQL](./index.yml).

O JDBC é a API Java padrão para se conectar a bancos de dados relacionais tradicionais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../cloud-shell/quickstart.md) ou [CLI do Azure](/cli/azure/install-azure-cli). É recomendável usar o Azure Cloud Shell para que o logon seja feito automaticamente e você tenha acesso a todas as ferramentas necessárias.
- Um [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) compatível, versão 8 (incluído no Azure Cloud Shell).
- A ferramenta de build [Apache Maven](https://maven.apache.org/).

## <a name="prepare-the-working-environment"></a>Preparar o ambiente de trabalho

Vamos usar as variáveis de ambiente para limitar erros de digitação e facilitar a personalização da configuração a seguir para as suas necessidades específicas.

Configure essas variáveis de ambiente usando os seguintes comandos:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Substitua os espaços reservados pelos seguintes valores, que são usados em todo este artigo:

- `<YOUR_DATABASE_NAME>`: O nome do servidor MySQL. O nome deve ser exclusivo em todo o Azure.
- `<YOUR_AZURE_REGION>`: A região do Azure que você usará. Você pode usar `eastus` por padrão, mas é recomendável configurar uma região mais próxima de onde você mora. Você pode ter a lista completa de regiões disponíveis ao digitar `az account list-locations`.
- `<YOUR_MYSQL_PASSWORD>`: A senha do servidor de banco de dados MySQL. Essa senha deveria ter um mínimo de oito caracteres. Os caracteres deveriam ser de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
- `<YOUR_LOCAL_IP_ADDRESS>`: O endereço IP do computador local, no qual você executará o aplicativo Java. Uma forma conveniente de encontrá-lo é apontar o navegador para [whatismyip.akamai.com](http://whatismyip.akamai.com/).

Em seguida, crie um grupo de recursos:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Usamos o utilitário `jq`, que é instalado por padrão no [Azure Cloud Shell](https://shell.azure.com/) para exibir dados JSON e torná-los mais legíveis.
> Se você não gostar desse utilitário, poderá remover com segurança a parte `| jq` de todos os comandos que usaremos.

## <a name="create-an-azure-database-for-mysql-instance"></a>Criar uma instância do Banco de Dados do Azure para MySQL

A primeira coisa que criaremos é um servidor MySQL gerenciado.

> [!NOTE]
> Você pode ler informações mais detalhadas sobre como criar servidores MySQL em [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md).

No [Azure Cloud Shell](https://shell.azure.com/), execute o seguinte script:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
  	| jq
```

Esse comando cria um servidor MySQL pequeno.

### <a name="configure-a-firewall-rule-for-your-mysql-server"></a>Configurar uma regra de firewall para o servidor MySQL

As instâncias do Banco de Dados do Azure para MySQL são protegidas por padrão. Elas têm um firewall que não permite nenhuma conexão de entrada. Para usar o banco de dados, você precisa adicionar uma regra de firewall que permitirá que o endereço IP local acesse o servidor de banco de dados.

Como você configurou nosso endereço IP local no início deste artigo, você pode abrir o firewall do servidor ao executar:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-mysql-database"></a>Configurar um banco de dados MySQL

O servidor MySQL que você criou anteriormente está vazio. Ele não tem nenhum banco de dados que você possa usar com o aplicativo Java. Criar um banco de dados chamado `demo`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Criar um projeto Java

Usando o seu IDE favorito, crie um projeto Java e adicione um arquivo `pom.xml` no diretório raiz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Esse arquivo é um [Apache Maven](https://maven.apache.org/) que configura o projeto a ser usado:

- Java 8
- Um driver MySQL recente para Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Preparar um arquivo de configuração para se conectar ao Banco de Dados do Azure para MySQL

Crie o arquivo *src/main/resources/application.properties* e adicione:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo@$AZ_DATABASE_NAME
password=$AZ_MYSQL_PASSWORD
```

- Substitua as duas variáveis `$AZ_DATABASE_NAME` pelo valor que você configurou no início deste artigo.
- Substitua a variável `$AZ_MYSQL_PASSWORD` pelo valor que você configurou no início deste artigo.

> [!NOTE]
> Acrescentamos `?serverTimezone=UTC` à propriedade de configuração `url` para instruir o driver JDBC a usar o formato de data UTC (Tempo Universal Coordenado) ao conectar-se ao banco de dados. Caso contrário, nosso servidor Java não usaria o mesmo formato de data que o banco de dados, o que resultaria em um erro.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Criar um arquivo SQL para gerar o esquema de banco de dados

Usaremos um arquivo *src/main/resources/`schema.sql`* para criar um esquema de banco de dados. Crie esse arquivo com o seguinte conteúdo:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Codificar o aplicativo

### <a name="connect-to-the-database"></a>Conectar-se ao banco de dados

Em seguida, adicione o código Java que usará o JDBC para armazenar e recuperar dados do servidor MySQL.

Crie um arquivo *src/main/java/DemoApplication.java* que contém:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

Esse código Java usará os arquivos *application.properties* e *schema.sql* que criamos anteriormente para se conectar ao servidor MySQL e criar um esquema que armazenará nossos dados.

Nesse arquivo, você pode ver que comentamos os métodos para inserir, ler, atualizar e excluir dados: codificaremos esses métodos no restante deste artigo e você poderá remover as marcas de comentários uma após a outra.

> [!NOTE]
> As credenciais de banco de dados são armazenadas nas propriedades *user* e *password* no arquivo *application.properties*. Essas credenciais são usadas durante a execução de `DriverManager.getConnection(properties.getProperty("url"), properties);`, pois o arquivo de propriedades é passado como um argumento.

> [!NOTE]
> A linha `AbandonedConnectionCleanupThread.uncheckedShutdown();` no final é um comando específico do driver MySQL para destruir um thread interno ao desligar o aplicativo.
> Ele pode ser ignorado com segurança. 

Agora, você pode executar esta classe principal com a sua ferramenta favorita:

- Usando o IDE, você deve clicar com o botão direito do mouse na classe *DemoApplication* e executá-la.
- Usando o Maven, você pode executar o aplicativo por meio do: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`.

O aplicativo deve se conectar ao Banco de Dados do Azure para MySQL, criar um esquema de banco de dados e fechar a conexão, como você vê nos logs do console:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Criar uma classe de domínio

Crie uma classe Java `Todo`, ao lado da classe `DemoApplication` e adicione o seguinte código:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Essa classe é um modelo de domínio mapeado na tabela `todo` que você criou ao executar o script *schema.sql*.

### <a name="insert-data-into-azure-database-for-mysql"></a>Inserir dados no Banco de Dados do Azure para MySQL

No arquivo *src/main/java/DemoApplication.java*, após o método principal, adicione o seguinte método para inserir dados no banco de dados:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Agora você pode remover a marca de comentário das seguintes duas linhas no método `main`:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

A execução da classe principal deve produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-mysql"></a>Como ler dados do Banco de Dados do Azure para MySQL

Vamos ler os dados inseridos anteriormente para validar se o nosso código funciona corretamente.

No arquivo *src/main/java/DemoApplication.java*, após o método `insertData`, adicione o seguinte método para ler dados do banco de dados:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Agora você pode remover a marca de comentário da seguinte linha no método `main`:

```java
todo = readData(connection);
```

A execução da classe principal deve produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-database-for-mysql"></a>Como atualizar dados no Banco de Dados do Azure para MySQL

Vamos atualizar os dados que inserimos anteriormente.

Ainda no arquivo *src/main/java/DemoApplication.java*, após o método `readData`, adicione o seguinte método para atualizar os dados no banco de dados:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Agora você pode remover a marca de comentário das seguintes duas linhas no método `main`:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

A execução da classe principal deve produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-database-for-mysql"></a>Como excluir dados no Banco de Dados do Azure para MySQL

Por fim, vamos excluir os dados que inserimos anteriormente.

Ainda no arquivo *src/main/java/DemoApplication.java*, após o método `updateData`, adicione o seguinte método para excluir os dados no banco de dados:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Agora você pode remover a marca de comentário da seguinte linha no método `main`:

```java
deleteData(todo, connection);
```

A execução da classe principal deve produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Parabéns! Você criou um aplicativo Java que usa o JDBC para armazenar e recuperar dados do Banco de Dados do Azure para MySQL.

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar seu banco de dados MySQL para o Banco de Dados do Azure para MySQL usando despejo e restauração](concepts-migrate-dump-restore.md)

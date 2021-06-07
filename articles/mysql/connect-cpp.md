---
title: 'Início Rápido: Conectar-se usando C++ – Banco de Dados do Azure para MySQL'
description: Este início rápido fornece um exemplo de código C++ que você pode usar para conectar e consultar os dados no Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: e8b2842300e43a9de88cc0b9b3ae9ce6cd754612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535836"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Início Rápido: Usar o Conector/C++ para conectar e consultar dados no Banco de Dados do Azure para MySQL

Este guia de início rápido demonstra como se conectar a um Banco de Dados do Azure para MySQL usando um aplicativo C++. Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este tópico pressupõe que você está familiarizado com o desenvolvimento usando C++ e começou recentemente a trabalhar com o Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Você também precisará:
- Instalar o [.NET Framework](https://www.microsoft.com/net/download)
- Instalar o [Visual Studio](https://www.visualstudio.com/downloads/)
- Instalar o [Conector MySQL/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Instalar [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Verifique se o endereço IP do qual você está se conectando foi adicionado às regras de firewall do servidor usando o [portal do Azure](./howto-manage-firewall-using-portal.md) ou a [CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-visual-studio-and-net"></a>Instalar o Visual Studio e o .NET
As etapas nesta seção pressupõem que você esteja familiarizado com desenvolvimento usando o .NET.

### <a name="windows"></a>**Windows**
- Instalar o Visual Studio 2019 Community. O Visual Studio 2019 Community é um IDE completo, extensível e gratuito. Com esse IDE, você pode criar aplicativos modernos para Android, iOS, Windows, Web e aplicativos de banco de dados e serviços de nuvem. É possível instalar o .NET Framework completo ou apenas o .NET Core: os snippets de código no Início rápido funcionam com qualquer um. Se você já tiver o Visual Studio instalado em seu computador, ignore as duas etapas a seguir.
   1. Baixe o [instalador do Visual Studio 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Execute o instalador e siga os prompts de instalação para concluir a instalação.

### <a name="configure-visual-studio"></a>**Configurar o Visual Studio**
1. No Visual Studio, Projeto -> Propriedades -> Vinculador -> Geral > Diretórios de Biblioteca Adicionais, adicione o diretório "\lib\opt" (por exemplo: C:\Arquivos de Programas (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt) do conector do C++.
2. No Visual Studio, Projeto > Propriedades -> C/C++ > Geral > Diretórios Adicionais de Inclusão:
   - Adicione o diretório "\include" do conector do C++ (por exemplo: C:\Arquivos de Programas (x86)\MySQL\MySQL Connector C++ 1.1.9\include\).
   - Adicione o diretório raiz da biblioteca Boost (por exemplo: C:\boost_1_64_0\).
3. No Visual Studio, Projeto -> Propriedades -> Vinculador -> Entrada > Dependências Adicionais, adicione **mysqlcppconn.lib** ao campo de texto.
4. Copie **mysqlcppconn.dll** na pasta da biblioteca do conector do C++ na etapa 3 para o mesmo diretório do executável do aplicativo ou adicione-o à variável de ambiente para que seu aplicativo possa localizá-lo.

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Nome do servidor do Banco de Dados do Azure para MySQL":::

## <a name="connect-create-table-and-insert-data"></a>Conectar-se, criar tabela e inserir dados
Use o código a seguir para se conectar e carregar os dados usando as instruções SQL **CREATE TABLE** e **INSERT INTO**. O código usa a classe sql::Driver com o método connect() para estabelecer uma conexão com o MySQL. Em seguida, o código usa os métodos createStatement() e execute() para executar os comandos do banco de dados. 

Substitua os parâmetros Host, DBName, User e Password. Substitua os parâmetros pelos valores que você especificou quando criou o servidor e o banco de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Ler dados

Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. O código usa a classe sql::Driver com o método connect() para estabelecer uma conexão com o MySQL. Em seguida, o código usa os métodos prepareStatement() e executeQuery() para executar os comandos de seleção. Em seguida, o código usa next() para prosseguir com os registros nos resultados. Por fim, o código usa getInt() e getString() para analisar os valores no registro.

Substitua os parâmetros Host, DBName, User e Password. Substitua os parâmetros pelos valores que você especificou quando criou o servidor e o banco de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Atualizar dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **UPDATE**. O código usa a classe sql::Driver com o método connect() para estabelecer uma conexão com o MySQL. Então, o código usa os métodos prepareStatement() e executeQuery() para executar os comandos de atualização. 

Substitua os parâmetros Host, DBName, User e Password. Substitua os parâmetros pelos valores que você especificou quando criou o servidor e o banco de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. O código usa a classe sql::Driver com o método connect() para estabelecer uma conexão com o MySQL. Então, o código usa os métodos prepareStatement() e executeQuery() para executar os comandos de exclusão.

Substitua os parâmetros Host, DBName, User e Password. Substitua os parâmetros pelos valores que você especificou quando criou o servidor e o banco de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
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
> [Migrar seu banco de dados MySQL para o Banco de Dados do Azure para MySQL usando despejo e restauração](concepts-migrate-dump-restore.md)

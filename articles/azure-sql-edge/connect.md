---
title: Conectar e consultar o Azure SQL Edge
description: Saiba como se conectar e consultar o Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395199"
---
# <a name="connect-and-query-azure-sql-edge"></a>Conectar e consultar o Azure SQL Edge

No Azure SQL Edge, depois de implantar um contêiner, você pode se conectar ao mecanismo de banco de dados de qualquer um dos seguintes locais:

- Dentro do contêiner
- De outro contêiner do Docker em execução no mesmo host
- No computador host
- De qualquer outro computador cliente na rede

## <a name="tools-to-connect-to-azure-sql-edge"></a>Ferramentas para se conectar ao SQL do Azure no Edge

Você pode se conectar a uma instância da instância do Azure SQL Edge de qualquer uma dessas ferramentas comuns:

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): as ferramentas de cliente do sqlcmd já estão incluídas na imagem de contêiner do Azure SQL Edge. Se você anexar a um contêiner em execução com um shell Bash interativo, poderá executar as ferramentas localmente. As ferramentas de cliente do SQL não estão disponíveis na plataforma ARM64, pois elas não estão incluídas na versão ARM64 dos contêineres do SQL Edge. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Para se conectar a um mecanismo de banco de dados do Azure SQL Edge de um computador de rede, você precisará do seguinte:

- **Endereço IP ou nome de rede do computador host** : Este é o computador host onde o contêiner do Azure SQL Edge está em execução.
- **Mapeamento de porta do host do contêiner do Azure SQL Edge** : esse é o mapeamento para a porta do contêiner do Docker para uma porta no host. Dentro do contêiner, o Azure SQL Edge é sempre mapeado para a porta 1433. Você pode alterar isso se desejar. Para alterar o número da porta, atualize as **Opções de criação do contêiner** para o módulo do Azure SQL Edge no Azure IOT Edge. No exemplo a seguir, a porta 1433 no contêiner é mapeada para a porta 1600 no host.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Senha SA para a instância do Azure SQL Edge** : esse é o valor especificado para a `SA_PASSWORD` variável de ambiente durante a implantação do Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Conectar-se ao mecanismo de banco de dados de dentro do contêiner

As [ferramentas de linha de comando SQL Server](/sql/linux/sql-server-linux-setup-tools) estão incluídas na imagem de contêiner do Azure SQL Edge. Se você anexar ao contêiner com um prompt de comando interativo, poderá executar as ferramentas localmente. As ferramentas de cliente do SQL não estão disponíveis na plataforma ARM64, pois elas não estão incluídas na versão ARM64 dos contêineres do SQL Edge. 

1. Use o comando `docker exec -it` para iniciar um shell bash interativo dentro do contêiner em execução. No exemplo a seguir, `e69e056c702d` é a ID do contêiner.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Você nem sempre precisa especificar toda a ID do contêiner. Você só precisa especificar caracteres suficientes para identificá-la exclusivamente. Portanto, neste exemplo, pode ser suficiente usar `e6` ou `e69` , em vez da ID completa.

2. Quando você estiver dentro do contêiner, conecte-se localmente com o sqlcmd. O sqlcmd não está no caminho por padrão, portanto, você precisa especificar o caminho completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Quando tiver concluído o sqlcmd, digite `exit` .

4. Quando tiver concluído o prompt de comando interativo, digite `exit` . O contêiner continuará a ser executado depois que você sair do shell bash interativo.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Conectar-se ao Azure SQL Edge de outro contêiner no mesmo host

Como dois contêineres que estão sendo executados no mesmo host estão na mesma rede do Docker, você pode acessá-los facilmente usando o nome do contêiner e o endereço da porta para o serviço. Por exemplo, se você estiver se conectando à instância do Azure SQL Edge de outro módulo python (contêiner) no mesmo host, poderá usar uma cadeia de conexão semelhante à seguinte. (Este exemplo supõe que o Azure SQL Edge esteja configurado para escutar na porta padrão.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Conectar-se ao Azure SQL Edge de outro computador de rede

Talvez você queira se conectar à instância do Azure SQL Edge de outro computador na rede. Para fazer isso, use o endereço IP do host do Docker e a porta do host para o qual o contêiner do Azure SQL Edge está mapeado. Por exemplo, se o endereço IP do host do Docker for *xxx.xxx.xxx.xxx* e o contêiner do Azure SQL Edge for mapeado para a porta de host *1600* , o endereço do servidor para a instância do Azure SQL Edge será *xxx. xxx. xxx. xxx, 1600*. O script Python atualizado é:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Para se conectar a uma instância do Azure SQL Edge usando SQL Server Management Studio em execução em um computador Windows, consulte [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Para se conectar a uma instância do Azure SQL Edge usando Visual Studio Code em um computador Windows, Mac ou Linux, consulte [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Para se conectar a uma instância do Azure SQL Edge usando Azure Data Studio em um computador Windows, Mac ou Linux, consulte [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Próximas etapas

[Conectar e consultar](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instalar as ferramentas do SQL Server no Linux](/sql/linux/sql-server-linux-setup-tools)
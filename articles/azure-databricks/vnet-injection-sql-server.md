---
title: Consulta de um contêiner de Docker do Linux do SQL Server em uma rede virtual de um bloco de anotações do Azure Databricks
description: Este artigo descreve como implantar o Azure Databricks em sua rede virtual, também conhecida como injeção de rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770493"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: Consulta de um contêiner de Docker do Linux do SQL Server em uma rede virtual de um bloco de anotações do Azure Databricks

Este tutorial ensina como integrar o Azure Databricks com um contêiner do Docker do Linux do SQL Server em uma rede virtual. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar um espaço de trabalho do Databricks do Azure em uma rede virtual
> * Instalar uma máquina virtual Linux em uma rede pública
> * Instalar o Docker
> * Instalar o Microsoft SQL Server no contêiner de docker do Linux
> * Consultar o SQL Server usando o JDBC de um notebook do Databricks

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma [espaço de trabalho do Databricks em uma rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Instale [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Baixe o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual Linux

1. No portal do Azure, selecione o ícone **máquinas virtuais**. Em seguida, selecione **+ adicionar**.

    ![Adicionar nova máquina virtual do Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Sobre o **Noções básicas sobre** guia, escolha o Ubuntu Server 16.04 LTS. Altere o tamanho da VM para B1ms, que tem um VCPUS e 2 GB de RAM. O requisito mínimo para um contêiner do Docker do Linux SQL Server é 2 GB. Escolha um administrador de nome de usuário e senha.

    ![Guia de Noções básicas da nova configuração de máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navegue até a **rede** guia. Escolha a rede virtual e sub-rede pública que inclui seu cluster do Azure Databricks. Selecione **revisar + criar**, em seguida, **criar** para implantar a máquina virtual.

    ![Guia rede da nova configuração de máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Quando a implantação for concluída, navegue até a máquina virtual. Observe que o endereço IP público e Virtual/sub-rede de rede na **visão geral**. Selecione o **endereço IP público**

    ![Visão geral da máquina virtual](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Alterar o **atribuição** ao **estático** e insira um **rótulo de nome DNS**. Selecione **salvar**e reinicie a máquina virtual.

    ![Configuração de endereço IP público](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecione o **Networking** guia sob **configurações**. Observe que o grupo de segurança de rede que foi criado durante a implantação do Azure Databricks está associado com a máquina virtual. Selecione **Adicionar regra de porta de entrada**.

7. Adicione uma regra para abrir a porta 22 para SSH. Use as configurações a seguir:
    
    |Configuração|Valor sugerido|DESCRIÇÃO|
    |-------|---------------|-----------|
    |Fonte|Endereços IP|Endereços IP Especifica que tráfego de entrada de uma fonte específica de endereço IP será permitido ou negado por essa regra.|
    |Endereços IP da fonte|< seu ip público\>|Insira o seu endereço IP público. Você pode encontrar seu endereço IP público visitando [bing.com](https://www.bing.com/) e procurando **"Meu IP"**.|
    |Intervalos de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Endereços IP Especifica que o tráfego para uma fonte específica de endereço IP será permitido ou negado por essa regra de saída.|
    |Endereços IP de destino|< seu ip público da vm\>|Insira o endereço IP público da máquina virtual. Você pode encontrá-lo sobre a **visão geral** página de sua máquina virtual.|
    |Intervalos de portas de destino|22|Abra a porta 22 para SSH.|
    |Prioridade|290|Atribuir uma prioridade para a regra.|
    |NOME|ssh-databricks-tutorial-vm|Dê um nome para a regra.|


    ![Adicionar regra de segurança de entrada para a porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Adicione uma regra para abrir a porta 1433 para o SQL com as seguintes configurações:

    |Configuração|Valor sugerido|DESCRIÇÃO|
    |-------|---------------|-----------|
    |Fonte|Endereços IP|Endereços IP Especifica que tráfego de entrada de uma fonte específica de endereço IP será permitido ou negado por essa regra.|
    |Endereços IP da fonte|10.179.0.0/16|Insira o intervalo de endereços para sua rede virtual.|
    |Intervalos de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Endereços IP Especifica que o tráfego para uma fonte específica de endereço IP será permitido ou negado por essa regra de saída.|
    |Endereços IP de destino|< seu ip público da vm\>|Insira o endereço IP público da máquina virtual. Você pode encontrá-lo sobre a **visão geral** página de sua máquina virtual.|
    |Intervalos de portas de destino|1433|Abra a porta 22 para o SQL Server.|
    |Prioridade|300|Atribuir uma prioridade para a regra.|
    |NOME|sql-databricks-tutorial-vm|Dê um nome para a regra.|

    ![Adicionar regra de segurança de entrada para a porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Executar o SQL Server em um contêiner do Docker

1. Abra [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), ou qualquer outra ferramenta que permitirá que você SSH na máquina virtual. Navegue até sua máquina virtual no portal do Azure e selecione **Connect** para obter o comando SSH que você precisa se conectar.

    ![Conectar-se à máquina virtual](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Insira o comando no terminal do Ubuntu e insira a senha de administrador que você criou quando configurou a máquina virtual.

    ![Ubuntu terminal SSH entrar](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Use o seguinte comando para instalar o Docker na máquina virtual.

    ```bash
    sudo apt-get install docker.io
    ```

    Verifique se a instalação do Docker com o seguinte comando:

    ```bash
    sudo docker --version
    ```

4. Instale a imagem.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique se as imagens.

    ```bash
    sudo docker images
    ```

5. Execute o contêiner da imagem.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique se que o contêiner está em execução.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Abra o SQL Server Management Studio e conecte-se ao servidor usando o nome do servidor e autenticação do SQL. É o nome de usuário de entrada **SA** e a senha é a senha definida no comando Docker. A senha no comando de exemplo é `Password1234`.

    ![Conectar-se ao SQL Server usando o SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Depois que você se conectou com êxito, selecione **nova consulta** e insira o seguinte trecho de código para criar um banco de dados, uma tabela e inserir alguns registros na tabela.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Consulta para criar um banco de dados do SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Consultar o SQL Server do Azure Databricks

1. Navegue até seu espaço de trabalho do Databricks do Azure e verifique se que você criou um cluster como parte dos pré-requisitos. Em seguida, selecione **criar um Notebook**. Nomeie o bloco de notas, selecione *Python* como a linguagem e selecione o cluster que você criou.

    ![Novas configurações de notebook do Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Use o seguinte comando para executar ping no endereço IP interno da máquina virtual do SQL Server. Esse ping deve ser bem-sucedida. Caso contrário, verifique se o contêiner está em execução e examine a configuração de NSG (grupo) de segurança de rede.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Você também pode usar o comando nslookup para examinar.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Depois que você tiver o ping com êxito o SQL Server, você pode consultar o banco de dados e tabelas. Execute o python seguinte código:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessário, exclua o grupo de recursos, o workspace do Azure Databricks e todos os recursos relacionados. Excluir o trabalho evita cobrança desnecessária. Se você está planejando usar o workspace do Azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se você não pretende continuar a usar esse workspace do Azure Databricks, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do grupo de recursos criado.

2. Em sua página de grupo de recursos, selecione **Excluir**, digite o nome do recurso a ser excluído na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender a extrair, transformar e carregar dados usando o Azure Databricks.
> [!div class="nextstepaction"]
> [Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

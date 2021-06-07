---
title: 'Início Rápido: Conectar-se ao MySQL Workbench – Banco de Dados do Azure para MariaDB'
description: Este início rápido fornece as etapas para usar o MySQL Workbench para se conectar e consultar dados do Banco de Dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 3/18/2020
ms.openlocfilehash: 209c793903f1f706fc27102a783aa76e65382fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664377"
---
# <a name="quickstart-azure-database-for-mariadb-use-mysql-workbench-to-connect-and-query-data"></a>Início Rápido: Banco de Dados do Azure para MariaDB: Usar o MySQL Workbench para se conectar e consultar dados

Este início rápido demonstra como se conectar a um Banco de Dados do Azure para MariaDB usando o MySQL Workbench. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa os recursos que são criados em um destes guias como ponto de partida:

- [Criar um servidor de Banco de Dados do Azure para MariaDB usando o portal do Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MariaDB usando a CLI do Azure](./quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Instalar o MySQL Workbench

[Baixar o MySQL Workbench](https://dev.mysql.com/downloads/workbench/) e instalá-lo em seu computador.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar à instância do Banco de Dados do Azure para MariaDB. Você precisa do nome do servidor e das credenciais de entrada totalmente qualificados.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda no portal do Azure, selecione **Todos os recursos**. Pesquise o servidor que você criou (como **mydemoserver**).

3. Selecione o nome do servidor.

4. Na página **Visão Geral** do servidor, anote os valores do **Nome do servidor** e do **Nome de logon do administrador do servidor**. Caso tenha esquecido sua senha, também é possível redefini-la nessa página.

   ![Nome do servidor do Banco de Dados do Azure para MariaDB e nome de logon do administrador do servidor](./media/connect-workbench/1_server-overview-name-login.png)

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Conectar-se ao servidor usando MySQL Workbench

Para conectar-se ao servidor do Banco de Dados do Azure para MariaDB usando o MySQL Workbench:

1. Abra o MySQL Workbench em seu computador. 

2. Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   | Configuração | Valor sugerido | Descrição do campo |
   |---|---|---|
   |   Nome da Conexão | **Conexão de demonstração** | Especifique um rótulo para essa conexão. |
   | Método de Conexão | **Padrão (TCP/IP)** | Padrão (TCP/IP) é suficiente. |
   | Nome do host | *nome do servidor* | Especifique o valor do nome do servidor usado para criar a instância de Banco de Dados do Azure para MariaDB. Nosso servidor de exemplo é **mydemoserver.mariadb.database.azure.com**. Use o nome de domínio totalmente qualificado (\*.mariadb.database.azure.com) conforme mostrado no exemplo. Caso não se lembre do nome do servidor, conclua as etapas da seção anterior para ter acesso às informações de conexão.  |
   | Porta | **3306** | Sempre use a porta 3306 ao conectar-se ao Banco de Dados do Azure para MariaDB. |
   | Nome de Usuário |  *nome de logon do administrador do servidor* | Insira o nome de usuário do logon do administrador do servidor usado para criar a instância de Banco de Dados do Azure para MariaDB. Nosso nome de usuário de exemplo é **myadmin\@mydemoserver**. Caso não se lembre do nome de logon do administrador do servidor, complete as etapas da seção anterior para obter as informações de conexão. O formato é *nome de usuário\@nome do servidor*.
   | Senha | *sua senha* | Para salvar a senha, escolha **Armazenar no Cofre** . |

   ![Configurar uma nova conexão](./media/connect-workbench/2-setup-new-connection.png)

3. Para testar se todos os parâmetros estão configurados corretamente, escolha **Testar Conexão**. 

4. Selecione **OK** para salvar a conexão. 

5. Sob **Conexões do MySQL**, selecione o bloco que corresponde ao seu servidor. Aguarde até que a conexão seja estabelecida.

   Uma nova guia do SQL é aberta, com um editor em branco no qual você pode digitar suas consultas.
    
   > [!NOTE]
   > Por padrão, a segurança da conexão SSL é exigida e imposta no servidor do Banco de Dados do Azure para MariaDB. Embora normalmente nenhuma configuração adicional para certificados SSL seja necessária para que o MySQL Workbench se conecte ao servidor, recomendamos associar a certificação de AC SSL com o MySQL Workbench. Caso precise desabilitar o SSL, na página de visão geral do servidor no portal do Azure, selecione **Segurança de conexão** no menu. Em **Impor conexão SSL**, marque **Desabilitado**.

## <a name="create-table-and-insert-read-update-and-delete-data"></a>Criar tabela e inserir, ler, atualizar e excluir dados

1. Copie e cole o código de exemplo do SQL a seguir na página de uma guia SQL em branco para ilustrar alguns dados de exemplo.

    Esse código cria um banco de dados vazio chamado **quickstartdb**. Em seguida, ele cria uma tabela de exemplo denominada **inventário**. O código insere algumas linhas, depois as lê. Ele altera os dados com uma instrução de atualização, depois lê as linhas novamente. Por fim, o código exclui uma linha e lê as linhas novamente.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    A captura de tela mostra um exemplo do código SQL no MySQL Workbench e a saída após sua execução:
    
    ![Selecione a guia SQL do MySQL Workbench para executar o código SQL de exemplo](media/connect-workbench/3-workbench-sql-tab.png)

2. Para executar o código SQL de exemplo, selecione o ícone de raio na barra de ferramentas da guia **Arquivo SQL**.
3. Observe os resultados em três guias na seção **Grade de resultados** no meio da página. 
4. Observe a lista de **Saída** na parte inferior da página. O status de cada comando é mostrado. 

Neste início rápido, você se conectou ao Banco de Dados do Azure para MariaDB usando o MySQL Workbench e consultou dados usando a linguagem SQL.

<!--
## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./concepts-migrate-import-export.md)
-->

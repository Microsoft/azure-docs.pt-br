---
title: 'Início Rápido: Conectar – MySQL Workbench – Banco de Dados do Azure para MySQL – Servidor Flexível'
description: Este Início Rápido fornecerá as etapas necessárias para usar o MySQL Workbench para se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível e consultar dados dele.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 95ecb64165075a7effe0c6eaf568ee172ad67b9b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226287"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Início Rápido: Usar o MySQL Workbench para se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível (Versão Prévia) e consultar dados dele


> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este início rápido demonstrará como se conectar a um Servidor Flexível do Banco de Dados do Azure para MySQL usando o aplicativo MySQL Workbench.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:

- [Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-server-portal.md)
- [Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Como preparar sua estação de trabalho cliente
- Caso tenha criado um servidor flexível com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Crie uma máquina virtual e adicione-a à VNET criada com o servidor flexível. Consulte [Criar e gerenciar uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./how-to-manage-virtual-network-cli.md).
- Caso tenha criado um servidor flexível com *acesso público (endereços IP permitidos)* , será possível adicionar seu endereço IP local à lista de regras de firewall no servidor. Consulte [Criar e gerenciar regras de firewall do Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure](./how-to-manage-firewall-cli.md).

- Baixe e instale o MySQL Workbench em seu computador e do [site do MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao servidor flexível. Será necessário obter um nome do servidor totalmente qualificado e as credenciais de entrada.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Selecione o nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Conectar-se ao servidor usando MySQL Workbench

Para se conectar ao Servidor Flexível do Banco de Dados do Azure para MySQL usando o MySQL Workbench, siga estas etapas:

1. Inicie o aplicativo MySQL Workbench em seu computador.

2. Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="configurar nova conexão":::

    | **Parâmetros** | **Valor sugerido** | **Descrição do campo** |
    |---|---|---|
    |    Nome da Conexão | Conexão de demonstração | Especifique um rótulo para essa conexão. |
    | Método de Conexão | Padrão (TCP/IP) | Padrão (TCP/IP) é suficiente. |
    | Nome do host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para MySQL anteriormente. Nosso servidor de exemplo mostrado é mydemoserver.mysql.database.azure.com. Use o nome de domínio totalmente qualificado (\*.mysql.database.azure.com) conforme mostrado no exemplo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do seu nome do servidor.  |
    | Porta | 3306 | Sempre use a porta 3306 ao conectar o Banco de Dados do Azure para MySQL. |
    | Nome de Usuário |  *nome de logon do administrador do servidor* | Digite o nome de usuário de logon do administrador do servidor fornecido na criação do Banco de Dados do Azure para MySQL anteriormente. Nosso nome de usuário de exemplo será myadmin. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do nome do usuário.
    | Senha | sua senha | Clique no botão **Armazenar no cofre...** para salvar a senha. |

3. Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente.

4. Clique em **OK** para salvar a conexão.

5. Na lista de **Conexões MySQL**, clique no bloco correspondente ao seu servidor e aguarde até que a conexão seja estabelecida.

    Uma nova guia do SQL é aberta, com um editor em branco no qual você pode digitar suas consultas.

> [!NOTE]
> Uma conexão criptografada usando o TLS 1.2 será necessária e aplicada no Servidor Flexível do Banco de Dados do Azure para MySQL. Embora nenhuma configuração adicional com certificados TLS/SSL normalmente seja necessária para que o MySQL Workbench se conecte ao servidor, recomendamos associar a certificação de AC TLS/SSL ao MySQL Workbench. Para obter mais informações, confira [conectar-se usando TLS/SSL](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Criar uma tabela, inserir dados, ler dados, atualizar dados, excluir dados

1. Copie e cole o código de exemplo do SQL em uma guia SQL em branco para ilustrar alguns dados de exemplo.

    Esse código cria um banco de dados vazio chamado quickstartdb e, em seguida, cria uma tabela de exemplo chamada inventory. Ele insere algumas linhas e, em seguida, lê as linhas. Ele altera os dados com uma instrução de atualização e lê as linhas novamente. Por fim, exclui uma linha e lê as linhas novamente.

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

    A captura de tela mostra um exemplo do código SQL no SQL Workbench e a saída após sua execução.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Guia de SQL do MySQL Workbench para executar código SQL de exemplo":::

2. Para executar o código SQL de exemplo, clique no ícone de raio na barra de ferramentas da guia **Arquivo SQL**.
3. Observe os resultados em três guias na seção **Grade de Resultados** no meio da página.
4. Observe a lista de **Saída** na parte inferior da página. O status de cada comando é mostrado.

Você já se conectou ao Servidor Flexível do Banco de Dados do Azure para MySQL usando o MySQL Workbench e consultou dados usando a linguagem SQL.

## <a name="next-steps"></a>Próximas etapas
- [Conectividade criptografada usando o TLS 1.2 (Transport Layer Security) no Banco de Dados do Azure para MySQL – Servidor Flexível](./how-to-connect-tls-ssl.md).
- Saiba mais sobre a [Rede no Servidor Flexível do Banco de Dados do Azure para MySQL](./concepts-networking.md).
- [Crie e gerencie regras de firewall do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-firewall-portal.md).
- [Crie e gerencie uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-virtual-network-portal.md).

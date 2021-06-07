---
title: Conectar com o Excel
description: Saiba como conectar o Microsoft Excel a um banco de dados no banco de dados SQL do Azure ou no Azure SQL Instância Gerenciada. Importar dados para o Excel para exploração de dados e geração de relatórios.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91444040"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Conectar o Excel a um banco de dados no banco de dados SQL do Azure ou Azure SQL Instância Gerenciada e criar um relatório
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Você pode conectar o Excel a um banco de dados e, em seguida, importar e criar tabelas e gráficos com base em valores no banco de dados. Neste tutorial, você irá configurar a conexão entre o Excel e uma tabela do banco de dados, salvar o arquivo que armazena os dados e as informações de conexão para o Excel, em seguida, criar um gráfico dinâmico a partir dos valores do banco de dados.

Você precisará criar um banco de dados antes de começar. Se você não tiver uma, consulte [criar um banco de dados no banco de dados SQL do Azure](single-database-create-quickstart.md) e [criar um firewall de IP no nível de servidor](firewall-create-server-level-portal-quickstart.md) para obter um banco de dados com exemplo em funcionamento em alguns minutos.

Neste artigo, você importará dados de exemplo do artigo para o Excel, mas poderá seguir etapas semelhantes em seus próprios dados.

Você também precisará de uma cópia do Excel. Este artigo usa o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-and-load-data"></a>Conectar o Excel e carregar dados

1. Para conectar o Excel a um banco de dados no banco de dados SQL, abra o Excel e crie uma nova pasta de trabalho ou abra uma pasta de trabalho existente do Excel.
2. Na barra de menus na parte superior da página, selecione a guia **Dados**, selecione **Obter dados**, selecione do Azure e, em seguida, selecione **do Banco de Dados SQL do Azure**.

   ![Selecione a fonte de dados: conectar o Excel ao SQL Database.](./media/connect-excel/excel_data_source.png)

3. Na caixa de diálogo **SQL Server banco de dados** , digite o **nome do servidor** ao qual você deseja se conectar no formato <*ServerName* > **. Database.Windows.net**. Por exemplo, **msftestserver.database.windows.net**. Opcionalmente, insira o nome de seu banco de dados. Selecione **OK** para abrir a janela de credenciais.

   ![Caixa de diálogo conectar ao servidor de banco de dados](./media/connect-excel/server-name.png)

4. Na caixa de diálogo **SQL Server banco de dados** , selecione **banco de dados** no lado esquerdo e, em seguida, insira seu **nome de usuário** e **senha** para o servidor ao qual você deseja se conectar. Selecione **Conectar** para abrir o **Navegador**.

   ![Digite as credenciais de logon e nome do servidor](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > Dependendo do seu ambiente de rede, você não poderá se conectar ou poderá perder a conexão se o servidor não permitir o tráfego do seu endereço IP do cliente. Vá para o [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, no firewall em configurações e adicione o endereço IP do cliente. Consulte [Como definir as configurações de firewall](firewall-configure.md) para obter mais detalhes.

5. No **Navegador**, selecione o banco de dados que você deseja trabalhar na lista, selecione as tabelas ou exibições com o qual você deseja trabalhar (escolhemos **vGetAllCategories**) e, em seguida, selecione **Carregar** para mover os dados do banco de dados para sua planilha do Excel.

    ![Selecione um banco de dados e a tabela.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico

Agora que a conexão foi estabelecida, você tem várias opções diferentes quanto à forma de carregar os dados. Por exemplo, as etapas a seguir criam um gráfico dinâmico com base nos dados encontrados em seu banco de dados no banco de dados SQL.

1. Siga as etapas na seção anterior, mas desta vez, em vez de selecionar **Carregar**, selecione **Carregar para** da lista suspensa **Carregar**.
2. Em seguida, selecione o modo como deseja exibir estes dados na pasta de trabalho. Escolhemos **Gráfico Dinâmico**. Você também pode optar por criar uma **Nova planilha** ou **Adicionar dados a um Modelo de Dados**. Para obter mais informações sobre os Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Escolhendo o formato dos dados no Excel](./media/connect-excel/import-data.png)

    Agora, a planilha tem uma tabela dinâmica vazia e um gráfico.
3. Em **Campos da Tabela Dinâmica**, selecione todas as caixas de seleção para os campos que você deseja exibir.

    ![Configure o relatório do banco de dados.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Se você quiser se conectar a outras pastas de trabalho do Excel e planilhas para o banco de dados, selecione a guia **Dados** e selecione **Fontes recentes** para iniciar a caixa de diálogo **Fontes recentes**. A partir daí, escolha a conexão que você criou da lista e, em seguida, clique em **Abrir**.
> ![Caixa de diálogo de fontes recentes](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Criar uma conexão permanente usando um arquivo .odc

Para salvar os detalhes de conexão permanentemente, você pode criar um arquivo .odc e tornar essa conexão uma opção selecionável na caixa de diálogo **Conexões existentes**.

1. Na barra de menus na parte superior da página, selecione a guia **Dados** e, em seguida, selecione **Conexões existentes** para iniciar a caixa de diálogo **Conexões existentes**.
   1. Selecione **Procurar mais** para abrir a caixa de diálogo **Selecionar fonte de dados**.
   2. Selecione o arquivo **+NewSqlServerConnection.odc** e, em seguida, selecione **Abrir** para abrir o **Assistente para conexão de dados**.

      ![Caixa de diálogo Nova conexão](./media/connect-excel/new-connection.png)

2. Em **Assistente para conexão de dados**, digite seu nome do servidor e suas credenciais do Banco de Dados SQL. Selecione **Avançar**.
   1. Selecione o banco de dados que contém os dados na lista suspensa.
   2. Selecione a tabela ou exibição que você está interessado. Escolhemos vGetAllCategories.
   3. Selecione **Avançar**.

      ![Assistente de conexão de dados](./media/connect-excel/data-connection-wizard.png)

3. Selecione o local do arquivo, o **Nome de arquivo** e o **Nome amigável** na próxima tela do Assistente de Conexão de dados. Também é possível salvar a senha no arquivo, embora isso pode expor seus dados para acesso não desejado. Selecione **Concluir** quando estiver pronto.

    ![Salvar Conexão de dados](./media/connect-excel/save-data-connection.png)

4. Selecione como você deseja importar os dados. Escolhemos a fazer uma Tabela dinâmica. Você também pode modificar as propriedades de conexão selecionando **Propriedades**. Selecione **OK** quando estiver pronto. Se você não escolheu salvar a senha com o arquivo, você será solicitado a digitar suas credenciais.

    ![Importar dados](./media/connect-excel/import-data2.png)

5. Verifique se sua nova conexão foi salvo, expandindo a guia **Dados** e selecionando **Conexões existentes**.

    ![Conexão existente](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [se conectar e consultar com SQL Server Management Studio](connect-query-ssms.md) para análise e consulta avançadas.
* Saiba mais sobre os benefícios dos [pools elásticos](elastic-pool-overview.md).
* Saiba como [criar um aplicativo Web que se conecta ao banco de dados SQL do Azure no back-end](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

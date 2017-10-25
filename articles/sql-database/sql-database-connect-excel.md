---
title: Conectar o Excel ao banco de dados SQL | Microsoft Docs
description: "Saiba como conectar o Microsoft Excel ao banco de dados SQL do Azure na nuvem. Importar dados para o Excel para exploração de dados e geração de relatórios."
services: sql-database
keywords: conectar o excel ao sql, importar dados para o excel
documentationcenter: 
author: joseidz
manager: jhubbard
editor: 
ms.assetid: 906924bc-2707-48d3-bac6-397976a0409d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jhubbard
ms.openlocfilehash: 68eadf7a7eef4ba5e6bcd9af1279227a01efdf70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Conectar o Excel a um Banco de Dados SQL do Azure e criar um relatório

Conectar o Excel a um banco de dados SQL na nuvem e importar dados e criar tabelas e gráficos com base nos valores no banco de dados. Neste tutorial, você irá configurar a conexão entre o Excel e uma tabela do banco de dados, salvar o arquivo que armazena os dados e as informações de conexão para o Excel, em seguida, criar um gráfico dinâmico a partir dos valores do banco de dados.

Você precisará de um banco de dados SQL no Azure antes de começar. Se você não tiver um, consulte [Criar seu primeiro banco de dados SQL](sql-database-get-started-portal.md) para obter um banco de dados com dados de exemplo em funcionamento em alguns minutos. Neste artigo, você importará dados de exemplo do artigo para o Excel, mas poderá seguir etapas semelhantes em seus próprios dados.

Você também precisará de uma cópia do Excel. Este artigo usa o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Conectar o Excel a um banco de dados SQL e criar um arquivo odc
1. Para conectar o Excel ao banco de dados SQL, abra o Excel e crie uma nova pasta de trabalho ou abra uma pasta do Excel existente.
2. Na barra de menus na parte superior da página, clique em **Dados**, clique em **De Outras Fontes** e, em seguida, clique em **Do SQL Server**.
   
   ![Selecione a fonte de dados: Conectar o Excel ao banco de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)
   
   O Assistente de conexão de dados é aberto.
3. Na caixa de diálogo **Conectar ao servidor do banco de dados**, digite o **Nome do servidor** do Banco de Dados SQL que você deseja conectar no formato <*nomeservidor*>**.database.windows.net**. Por exemplo, **adworkserver.database.windows.net**.
4. Em **Credenciais de logon**, clique em **Usar o seguinte Nome de Usuário e Senha**, digite o **Nome de Usuário** e a **Senha** que você configurou para o servidor do Banco de Dados SQL ao criá-lo, em seguida, clique em **Avançar**.
   
   ![Digite as credenciais de logon e nome do servidor](./media/sql-database-connect-excel/connect-to-server.png)
   
   > [!TIP]
   > Dependendo do seu ambiente de rede, você não poderá conectar ou poderá perder a conexão se o servidor do Banco de Dados SQL não permitir o tráfego a partir de seu endereço IP do cliente. Vá para o [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, no firewall em configurações e adicione o endereço IP do cliente. Consulte [Como definir as configurações de firewall](sql-database-configure-firewall-settings.md) para obter mais detalhes.
   > 
   > 
5. Na caixa de diálogo **Selecionar Banco de Dados e Tabela**, selecione o banco de dados com o qual você deseja trabalhar na lista, em seguida, clique nas tabelas ou exibições com as quais deseja trabalhar (escolhemos **vGetAllCategories**), em seguida, clique em **Avançar**.
   
    ![Selecione um banco de dados e a tabela.](./media/sql-database-connect-excel/select-database-and-table.png)
   
    A caixa de diálogo **Salvar Arquivo de Conexão de Dados e Concluir** é aberta, em que você fornece informações sobre o arquivo de conexão de banco de dados do Office (*.odc) que o Excel usa. Você pode deixar os padrões ou personalizar suas seleções.
6. Você pode deixar os padrões, mas observe o **Nome de Arquivo** em particular. Uma **Descrição**, **Nome Amigável** e **Palavras-Chave de Pesquisa** ajudam você e outros usuários a lembrarem o que está sendo conectado e a encontrarem a conexão. Clique em **Sempre tentar usar este arquivo para atualizar os dados** se você quiser as informações de conexão armazenadas no arquivo odc para que ele possa ser atualizado quando você se conectar e, em seguida, clique em **Concluir**.
   
    ![Salvando um arquivo odc](./media/sql-database-connect-excel/save-odc-file.png)
   
    A caixa de diálogo **Importar dados** é exibida.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico
Agora que você estabeleceu a conexão e criou o arquivo com dados e informações de conexão, está pronto para importar os dados.

1. Na caixa de diálogo **Importar Dados**, clique na opção desejada para apresentar os dados na planilha, em seguida, clique em **OK**. Escolhemos **Gráfico Dinâmico**. Você também pode optar por criar uma **Nova planilha** ou **Adicionar dados a um Modelo de Dados**. Para obter mais informações sobre os Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Clique em **Propriedades** para explorar as informações sobre o arquivo odc que você criou na etapa anterior e escolha as opções para atualizar os dados.
   
    ![Escolhendo o formato dos dados no Excel](./media/sql-database-connect-excel/import-data.png)
   
    Agora, a planilha tem uma tabela dinâmica vazia e um gráfico.
2. Em **Campos da Tabela Dinâmica**, selecione todas as caixas de seleção para os campos que você deseja exibir.
   
    ![Configure o relatório do banco de dados.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Se você quiser conectar outras pastas de trabalho do Excel e planilhas ao banco de dados, clique em **Dados**, **Conexões**, **Adicionar**, escolha a conexão que você criou na lista, em seguida, clique em **Abrir**.
> ![Abrir uma conexão a partir de outra pasta de trabalho](./media/sql-database-connect-excel/open-from-another-workbook.png)
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Conectar o Banco de Dados SQL com o SQL Server Management Studio](sql-database-connect-query-ssms.md) para ter uma consulta e análise avançadas.
* Saiba mais sobre os benefícios dos [pools elásticos](sql-database-elastic-pool.md).
* Saiba como [criar um aplicativo Web que se conecta ao Banco de Dados SQL em back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).


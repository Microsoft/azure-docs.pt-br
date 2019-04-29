---
title: Usar dados do Azure Blockchain Workbench no Microsoft Excel
description: Saiba como carregar e exibir dados de Banco de Dados SQL do Azure Blockchain Workbench no Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 560958c2b420d3a1ec592e6bd7e0124ecad7c984
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715246"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Exibir dados do Azure Blockchain Workbench com o Microsoft Excel

Você pode usar o Microsoft Excel para exibir dados no Banco de Dados SQL do Azure Blockchain Workbench. Este artigo fornece as etapas de que você precisa para:

* Conecte-se ao banco de dados do Blockchain Workbench pelo Microsoft Excel
* Examine as tabelas de banco de dados e as exibições do Blockchain Workbench
* Carregar dados de exibição do Blockchain Workbench no Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Conecte-se ao banco de dados do Blockchain Workbench

Para se conectar a um banco de dados do Blockchain Workbench:

1. Abra o Microsoft Excel.
2. Na guia **Dados**, escolha **Obter Dados**.
3. Selecione **Do Azure** e depois selecione **Do Banco de Dados SQL do Azure**.

   ![Conectar banco de dados SQL do Azure](./media/data-excel/connect-sql-db.png)

4. Na caixa de diálogo **Banco de dados SQL Server**:

    * Para **Servidor**, digite o nome do servidor do Blockchain Workbench.
    * Para **Banco de Dados (opcional)**, insira o nome do banco de dados.

   ![Fornecer o servidor de banco de dados e o banco de dados](./media/data-excel/provide-server-db.png)

5. Na barra de navegação da caixa de diálogo **Banco de dados SQL Server**, selecione **Banco de Dados**. Insira seu **Nome de Usuário** e **Senha** e selecione **Conectar**.

    > [!NOTE]
    > Se você estiver usando as credenciais criadas durante o processo de implantação do Azure Blockchain Workbench, o **Nome de usuário** será `dbadmin`. A **Senha** é a que você criou quando implantou o Blockchain Workbench.
    
   ![Fornecer credenciais para acessar o banco de dados](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Examine as tabelas e as exibições de banco de dados

A caixa de diálogo Navegador do Excel é aberta após você se conectar ao banco de dados. Você pode usar o Navegador para examinar as tabelas e exibições no banco de dados. As exibições são criadas para emissão de relatórios e seus nomes são prefixados com **vw**.

   ![Visualização do Navegador do Excel de uma exibição](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Carregar dados de exibição em uma pasta de trabalho do Excel

O exemplo a seguir mostra como você pode carregar dados de uma exibição em uma pasta de trabalho do Excel.

1. Na barra de rolagem do **Navegador**, selecione a exibição **vwContractAction**. A exibição **vwContractAction** mostra todas as ações relacionadas a um contrato no banco de dados do Blockchain Workbench.
2. Selecione **Carregar** para recuperar todos os dados na exibição e colocá-los em sua pasta de trabalho do Excel.

   ![Dados carregados de uma exibição](./media/data-excel/view-data.png)

Agora que os dados estão carregados, você pode usar os recursos do Excel para criar seus próprios relatórios usando os metadados e os dados de transação do banco de dados do Azure Blockchain Workbench.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)
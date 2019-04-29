---
title: Obter detalhes do banco de dados do Azure Blockchain Workbench
description: Saiba como obter informações do servidor de banco de dados e de banco de dados do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816858"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Obter informações sobre seu banco de dados do Azure Blockchain Workbench

Este artigo mostra como obter informações detalhadas sobre seu banco de dados do Azure Blockchain Workbench.

## <a name="overview"></a>Visão geral

São fornecidas informações sobre aplicativos, fluxos de trabalho e execução de contrato inteligente usando exibições de banco de dados no banco de dados de SQL do Workbench Blockchain. Os desenvolvedores podem usar essas informações ao usar ferramentas como o Microsoft Excel, o Power BI, o Visual Studio e o SQL Server Management Studio.

Para que um desenvolvedor possa se conectar ao banco de dados, ele precisa do seguinte:

* Acesso de cliente externo permitido no firewall do banco de dados. Este artigo sobre como configurar um artigo de firewall do banco de dados explica como permitir o acesso.
* O nome do servidor de banco de dados e o nome do banco de dados.

## <a name="connect-to-the-blockchain-workbench-database"></a>Conecte-se ao banco de dados do Blockchain Workbench

Para se conectar ao banco de dados:

1. Entre no portal do Azure com uma conta que tenha permissões de **Proprietário** para os recursos do Azure Blockchain Workbench.
2. No painel de navegação esquerdo, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para sua implantação do Blockchain Workbench.
4. Selecione **Tipo** para classificar a lista de recursos e, em seguida, escolha o **SQL Server**. A lista classificada na próxima captura de tela mostra dois bancos de dados SQL, "master" e outro que usa "lhgn" como o **Prefixo de recurso**.

   ![Lista de recursos classificados do Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Para ver informações detalhadas sobre o banco de dados do Blockchain Workbench, selecione o link para o banco de dados com o **Prefixo de recurso** fornecido para a implantação do Blockchain Workbench.

   ![Detalhes do banco de dados](./media/getdb-details/workbench-db-details.png)

O nome do servidor de banco de dados e o nome do banco de dados permitem que você se conecte ao banco de dados do Blockchain Workbench usando sua ferramenta de desenvolvimento ou relatórios.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)
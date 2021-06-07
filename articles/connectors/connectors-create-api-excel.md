---
title: Gerenciar dados, planilhas e tabelas no Excel online
description: Gerenciar dados em planilhas e tabelas no Excel online for Business ou Excel online para OneDrive usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400716"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerenciar dados do Excel Online com Aplicativos Lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector do [Excel online for Business](/connectors/excelonlinebusiness/) ou [Excel online para onedrive](/connectors/excelonline/) , você pode criar tarefas e fluxos de trabalho automatizados com base em seus dados no Excel online para empresas ou onedrive. Esse conector fornece ações que ajudam você a trabalhar com seus dados e gerenciar planilhas, por exemplo:

* Crie novas planilhas e tabelas.
* Obter e gerenciar planilhas, tabelas e linhas.
* Adicionar linhas únicas e colunas de chave.

Em seguida, você pode usar as saídas dessas ações com ações para outros serviços. Por exemplo, se você usa uma ação que cria planilhas a cada semana, você pode usar outra ação que envia email de confirmação por meio do conector do Office 365 Outlook.

Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Os conectores do [Excel Online for Business](/connectors/excelonlinebusiness/) e do [Excel Online for OneDrive](/connectors/excelonline/) funcionam com os Aplicativos Lógicos do Azure e diferem do [conector do Excel para o PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta corporativa ou de estudante](https://www.office.com/) para sua conta corporativa ou conta Microsoft pessoal

  Os dados do Excel podem existir como um arquivo CSV (valores separados por vírgulas) em uma pasta de armazenamento, por exemplo, no OneDrive. 
  Você também pode usar o mesmo arquivo CSV com o [conector de arquivo simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar os dados do Excel Online. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho **Recorrência**.

## <a name="add-excel-action"></a>Adicionar ação de Excel

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. No gatilho, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira "excel" como filtro. Na lista de ações, selecione a ação desejada.

   > [!NOTE]
   > O designer do aplicativo lógico não pode carregar tabelas que tenham 100 ou mais colunas. Se possível, reduza o número de colunas na tabela selecionada para que o designer possa carregar a tabela.

1. Se solicitado, entre em sua conta corporativa ou de estudante.

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão com o Excel Online e acessar seus dados.

1. Continue fornecendo os detalhes necessários para a ação selecionada, criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelos arquivos OpenAPI (anteriormente Swagger) do conector, consulte estas páginas de referência do conector:

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

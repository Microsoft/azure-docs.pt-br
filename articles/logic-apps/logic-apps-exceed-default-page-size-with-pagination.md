---
title: Obter mais itens ou registros com paginação
description: Configurar paginação para exceder o limite de tamanho de página padrão para ações de conector nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87090256"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obter mais dados, itens ou registros usando paginação em Aplicativos Lógicos do Azure

Quando você recupera dados, itens ou registros usando uma ação de conector nos [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), você pode obter conjuntos de resultados tão grandes que a ação não retorna todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho de página padrão do conector. Nesse caso, a ação retorna apenas a primeira página de resultados. Por exemplo, o tamanho de página padrão para a ação **Obter linhas** do conector do SQL Server é 2048, mas pode variar com base em outras configurações.

Algumas ações permitem que você ative uma configuração de *paginação* para que seu aplicativo lógico possa recuperar mais resultados até o limite de paginação, mas retornar esses resultados como uma única mensagem quando a ação for concluída. Ao usar a paginação, você deverá especificar um valor *limite*, que é o número de destino de resultados que você deseja que a ação retorne. A ação recupera os resultados até atingir o limite especificado. Quando o número total de itens é menor que o limite especificado, a ação recupera todos os resultados.

A ativação das configurações de paginação recupera páginas de resultados com base no tamanho da página de um conector. Esse comportamento significa que você pode obter mais resultados do que o limite especificado. Por exemplo, ao usar a ação **obter linhas** do SQL Server, que dá suporte à configuração de paginação:

* O tamanho da página padrão da ação é de 2.048 registros por página.
* Suponha que você tenha 10.000 registros e especifique 5.000 registros como o mínimo.
* A paginação obtém páginas de registros; portanto, para obter pelo menos o mínimo especificado, a ação retornará 6.144 registros (3 páginas de 2.048 registros), e não 5.000 registros.

Aqui está uma lista com apenas alguns dos conectores onde você pode exceder o tamanho de página padrão para ações específicas:

* [Armazenamento de Blobs do Azure](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Equipes da Microsoft](/connectors/teams/)
* [Banco de dados Oracle](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico e a ação em que você deseja ativar a paginação. Se você não tiver um aplicativo lógico, confira [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ativar paginação

Para determinar se uma ação dá suporte à paginação no Designer do Aplicativo Lógico, verifique as configurações da ação para a configuração **Paginação**. Este exemplo mostra como ativar a paginação na ação **obter linhas** do SQL Server.

1. No canto do superior direito da ação, escolha o botão de reticências ( **...** ) e escolha **Configurações**.

   ![Abra as configurações da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação dá suporte à paginação, a ação mostra as configurações de **Paginação**.

1. Altere a configuração de **Paginação** de **Desligada** para **Ligada**. Na propriedade **Limite**, especifique um valor inteiro para o número de destino de resultados que você deseja que a ação retorne.

   ![Especificar número mínimo de resultados a serem retornados](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **Concluído**.

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando você ativa a paginação para uma ação que dá suporte a esse recurso, a definição de fluxo de trabalho do aplicativo lógico inclui a propriedade `"paginationPolicy"` juntamente com a propriedade `"minimumItemCount"` na propriedade `"runtimeConfiguration"` da ação, por exemplo:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Obtenha suporte

Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html).

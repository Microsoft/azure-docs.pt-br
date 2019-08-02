---
title: 'Visualizar dados no Azure Data Explorer usando uma consulta importada no Power BI '
description: 'Neste artigo, você aprenderá a usar uma das três opções para visualizar dados no Power BI: importando uma consulta de Data Explorer do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 6c2c8457cee004bf1288656ec9746b703d41aee1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806416"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Visualizar dados usando uma consulta importada ao Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Power BI é uma solução de análise de negócios que permite que você visualize os dados e compartilhar os resultados na sua organização.

O Azure Data Explorer fornece três opções para se conectar a dados no Power BI: usar o conector interno, importar uma consulta do Azure Data Explorer ou usar uma consulta SQL. Este artigo mostra como importar uma consulta para que você possa obter dados e visualizá-los em um relatório do Power BI.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este artigo:

* Uma conta de email organizacional que seja membro do Azure Active Directory, de modo que você possa se conectar ao [Cluster de ajuda do Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRATUITO**)

* [Aplicativo de área de trabalho do Azure Data Explorer](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados do Azure Data Explorer

Primeiro, crie uma consulta no aplicativo de área de trabalho do Azure Data Explorer e exporte-o para usar no Power BI. Em seguida, conecte-se ao cluster de ajuda do Azure Data Explorer e use um subconjunto dos dados da tabela de *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Em um navegador, vá até [https://help.kusto.windows.net/](https://help.kusto.windows.net/) para iniciar o aplicativo de área de trabalho do Azure Data Explorer.

1. No aplicativo da área de trabalho, copie a consulta a seguir na janela de consulta do canto superior direito, depois execute-a.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    As primeiras linhas do conjunto de resultados devem ser semelhantes à imagem a seguir.

    ![Resultados da consulta](media/power-bi-imported-query/query-results.png)

1. Na guia **Ferramentas**, selecione **Consulta no Power BI**, depois **OK**.

    ![Consulta de exportação](media/power-bi-imported-query/export-query.png)

1. No Power BI Desktop, na guia **Início**, selecione **Obter Dados** e depois em **Consulta em branco**.

    ![Obter dados](media/power-bi-imported-query/get-data.png)

1. No Editor do Power Query, na guia **Página inicial**, selecione **Editor avançado**.

1. Na janela **Editor avançado**, cole a consulta que você exportou e selecione **Concluído**.

    ![Colar consulta](media/power-bi-imported-query/paste-query.png)

1. Na janela do Editor do Power Query principal, selecione **Editar credenciais**. Selecione **Conta organizacional**, entre e selecione **Conectar**.

    ![Editar credenciais](media/power-bi-imported-query/edit-credentials.png)

1. Na guia **Página inicial**, selecione **Fechar e Aplicar**.

    ![Fechar e aplicar](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizar dados em um relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisa mais o relatório que você criou para este artigo, exclua o arquivo do Power BI Desktop (. pbix).

## <a name="next-steps"></a>Próximas etapas

[Visualizar dados usando o conector do Gerenciador de dados do Azure para o Power BI](power-bi-connector.md)
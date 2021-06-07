---
title: Guia de início rápido – Criar um cluster do Azure Stream Analytics
description: Saiba como criar um cluster do Azure Stream Analytics.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/09/2021
ms.openlocfilehash: 7298862d29c02709afe737558421b4a8743ca5b0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305474"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Início Rápido: Criar um cluster dedicado do Azure Stream Analytics usando o portal do Azure

Use o portal do Azure para criar um cluster do Azure Stream Analytics. Um [cluster do Stream Analytics](cluster-overview.md) é uma implantação de locatário único que pode ser usada para casos de uso de streaming complexos e mais rígidos. Execute vários trabalhos do Stream Analytics em um cluster do Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Conclusão do [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Criar um cluster do Stream Analytics

Nesta seção, você criará um recurso de cluster do Stream Analytics.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. Na caixa de pesquisa *Pesquisar no Marketplace*, digite e selecione **cluster do Stream Analytics**. Em seguida, selecione **Adicionar**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Resultado da pesquisa de cluster do Stream Analytics.":::

1. Na página **Criar cluster do Stream Analytics**, insira as configurações básicas do novo cluster.

   |Configuração|Valor|Descrição |
   |---|---|---|
   |Subscription|Nome da assinatura|Selecione a assinatura do Azure que deseja usar para esse cluster do Stream Analytics. |
   |Grupo de recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo**, então insira um nome exclusivo para o novo grupo de recursos. |
   |Nome do cluster|Um nome exclusivo|Insira um nome para identificar o cluster do Stream Analytics.|
   |Localização|A região mais próxima das fontes de dados e dos coletores|Selecione uma localização geográfica para hospedar o cluster do Stream Analytics. Use a localização mais próxima das fontes de dados e dos coletores para obter uma análise de baixa latência.|
   |Capacidade da unidade de streaming|36 a 216 |Determine o tamanho do cluster estimando quantos trabalhos do Stream Analytics você pretende executar e o total de SUs necessárias para o trabalho. Você pode começar com 36 SUs e, mais tarde, aumentar ou diminuir esse número, conforme necessário.|

   ![Criar cluster](./media/create-cluster/create-cluster.png)

1. Selecione **Examinar + criar**. Ignore as seções **Marcas**.

1. Examine as configurações do cluster e selecione **Criar**. A criação do cluster é uma operação de execução prolongada e pode levar aproximadamente 60 minutos para ser concluída. Aguarde até que a página do portal exiba **Sua implantação está concluída**. Enquanto isso, você pode criar e desenvolver [trabalhos do Stream Analytics](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) que deseja executar nesse cluster, caso ainda não tenha feito isso.

1. Selecione **Ir para recurso** para acesse a página do cluster do Stream Analytics.

## <a name="delete-your-cluster"></a>Excluir o cluster

Exclua o cluster do Stream Analytics se não pretende executar trabalhos do Stream Analytics nele. Exclua o cluster seguindo as etapas no portal do Azure:

1. Acesse **Trabalhos do Stream Analytics** em **Configurações** e interrompa todos os trabalhos que estão em execução.

1. Acesse **Visão geral** do cluster. Selecione **Excluir** e siga as instruções para excluir o cluster.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar um cluster do Azure Stream Analytics. Prossiga para o próximo artigo para saber como executar um trabalho do Stream Analytics no seu cluster:

> [!div class="nextstepaction"]
> [Gerenciar trabalhos do Stream Analytics em um cluster do Stream Analytics](manage-jobs-cluster.md)

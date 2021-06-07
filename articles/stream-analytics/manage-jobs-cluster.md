---
title: Criar e excluir trabalhos em um cluster do Azure Stream Analytics
description: Saiba como gerenciar trabalhos do Stream Analytics em um cluster do Azure Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 04/16/2021
ms.openlocfilehash: 4a6334d33dea959bdd3704f848e7bc8250b6e7c6
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600702"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Adicionar e remover trabalhos em um cluster do Azure Stream Analytics

Execute vários trabalhos do Azure Stream Analytics em um cluster do Stream Analytics. A execução de trabalhos em um cluster é um processo simples de duas etapas: adicionar o trabalho ao cluster e iniciar o trabalho. Este artigo mostra como adicionar e remover trabalhos de um cluster existente. Siga o guia de início rápido para [criar um cluster do Stream Analytics](create-cluster.md), caso ainda não tenha um.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Adicionar um trabalho do Stream Analytics a um cluster

Somente os trabalhos existentes do Stream Analytics podem ser adicionados a clusters. Siga o guia de início rápido para [saber como criar um trabalho](stream-analytics-quick-create-portal.md) usando o portal do Azure. Depois que você tiver um trabalho que desejar adicionar a um cluster, use as etapas a seguir para adicioná-lo ao cluster.

1. No portal do Azure, localize e selecione seu cluster do Stream Analytics.

1. Em **Configurações**, selecione **Trabalhos do Stream Analytics**. Em seguida, selecione **Adicionar trabalho existente**.

1. Selecione a assinatura e o trabalho do Stream Analytics que deseja adicionar ao cluster. Somente os trabalhos do Stream Analytics que estejam na mesma região do cluster podem ser adicionados ao cluster.

   ![Adicionar um trabalho ao cluster](./media/manage-jobs-cluster/add-job.png)

1. Depois de adicionar o trabalho ao cluster, procure o recurso de trabalho e [inicie o trabalho](start-job.md#azure-portal). Em seguida, o trabalho começará a ser executado no cluster.

Você pode realizar todas as outras operações, como monitoramento, alertas e logs de diagnóstico, na página do recurso de trabalho do Stream Analytics.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Remover um trabalho do Stream Analytics de um cluster

Os trabalhos do Stream Analytics precisam estar em um estado parado para serem removidos do cluster. Se o trabalho ainda estiver em execução, interrompa-o antes de realizar as etapas a seguir.

1. Localize e selecione o cluster do Stream Analytics.

1. Em **Configurações**, selecione **Trabalhos do Stream Analytics**.

1. Selecione os trabalhos que deseja remover do cluster e escolha **Remover**.

   ![remover um trabalho do cluster](./media/manage-jobs-cluster/remove-job.png)

   Quando um trabalho é removido de um cluster do Stream Analytics, ele retorna ao ambiente multilocatário padrão.

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como adicionar e remover trabalhos no cluster do Azure Stream Analytics. Em seguida, saiba como gerenciar pontos de extremidade privados e escalar seus clusters:

* [Escalar um cluster do Azure Stream Analytics](scale-cluster.md)
* [Gerenciar pontos de extremidade privados em um cluster do Azure Stream Analytics](private-endpoints.md)

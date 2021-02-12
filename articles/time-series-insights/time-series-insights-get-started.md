---
title: Criar um ambiente – Azure Time Series Insights | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um novo ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023319"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Criar um novo ambiente de Azure Time Series Insights Gen1 no portal do Azure

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve como criar um novo ambiente de Azure Time Series Insights usando o portal do Azure.

Azure Time Series Insights permite que você comece a visualizar e consultar dados que fluem para hubs IoT do Azure e hubs de eventos em minutos, permitindo que você consulte grandes volumes de dados de série temporal em segundos.  Ele foi projetado para a escala da Internet das Coisas (IoT) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Etapas para criar o ambiente

Siga estas etapas para criar um ambiente:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione o botão **+ Criar um recurso**.

1. Selecione a categoria **Internet das Coisas** e selecione **Time Series Insights**.

   [![Criar o ambiente de Azure Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na página **Time Series Insights**, selecione **Criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:

   [![Criar o grupo de recursos Azure Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Configuração|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Esse nome representa o ambiente no [Explorador do time Series](https://insights.timeseries.azure.com)
   Subscription | Sua assinatura | Se você tiver várias assinaturas, escolha a assinatura que contenha a origem do evento de preferência. Azure Time Series Insights pode detectar automaticamente o Hub IoT do Azure e os recursos do hub de eventos existentes na mesma assinatura.
   Resource group | Criar um novo ou usar um existente | Um grupo de recursos é uma coleção de recursos do Azure que são usados juntos. Você pode escolher um grupo de recursos existente, como aquele que contém seu Hub de Eventos ou Hub IoT. Ou você pode criar um novo se esse recurso não estiver relacionado a outros recursos.
   Location | Mais próximo da origem do evento | Preferencialmente, escolha o mesmo local de centro de dados que contenha os dados de origem do evento, em um esforço para evitar os custos de largura de banda entre regiões e zonas e a latência adicional ao mover os dados para fora da região.
   Tipo de preço | S1 | Escolha a taxa de transferência necessária. Para ter custos mais baixos e a capacidade inicial, selecione S1.
   Capacidade | 1 | A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.  Você pode alterar a capacidade de um ambiente após a criação. Para custos mais baixos, selecione uma capacidade 1.
  
1. Selecione **Criar** para iniciar o processo de provisionamento. Isso poderá levar alguns minutos.

1. Para monitorar o processo de implantação, selecione o símbolo **Notificações** (ícone de sino).

   [![Assistir às notificações](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Na **visão geral** do recurso, verifique as definições de configuração da implantação.

   [![Criar o Azure Time Series Insights fixar no painel](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcional)** Selecione o **ícone de pino** no canto superior direito para acessar facilmente seu ambiente de Azure Time Series insights no futuro.

## <a name="next-steps"></a>Próximas etapas

* [Defina as políticas de acesso de dados](./concepts-access-policies.md) para proteger seu ambiente.

* [Adicione uma origem de evento do Hub de Eventos](./how-to-ingest-data-event-hub.md) ao ambiente do Azure Time Series Insights.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Exiba seu ambiente no [Azure Time Series insights Explorer](https://insights.timeseries.azure.com).
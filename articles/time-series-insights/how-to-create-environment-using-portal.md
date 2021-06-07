---
title: Configurar um ambiente Gen2 usando o portal do Azure-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba como configurar um ambiente no Azure Time Series Insights Gen2 usando portal do Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952840"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Criar um ambiente de Azure Time Series Insights Gen2 usando o portal do Azure

Este artigo descreve como criar um ambiente de Azure Time Series Insights Gen2 usando o [portal do Azure](https://portal.azure.com/).

O tutorial de provisionamento de ambiente orientará você pelo processo. Você aprenderá a selecionar a ID da série temporal correta e exibir exemplos de dois conteúdos JSON.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Visão geral

Ao provisionar um ambiente de Azure Time Series Insights Gen2, você cria esses recursos do Azure:

* Um Azure Time Series Insights ambiente Gen2 que segue o modelo de preços pago conforme o uso
* Uma conta de armazenamento do Azure
* Um armazenamento inesperado opcional para consultas mais rápidas e ilimitadas

> [!TIP]
>
> * Saiba [como planejar seu ambiente](./how-to-plan-your-environment.md).
> * Leia sobre como [Adicionar uma origem do hub de eventos](./how-to-ingest-data-event-hub.md) ou como [Adicionar uma origem do Hub IOT](./how-to-ingest-data-iot-hub.md).

Você saberá como:

1. Associe cada ambiente Azure Time Series Insights Gen 2 a uma origem de evento. Você também fornecerá uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo para garantir que o ambiente tenha acesso aos eventos apropriados.

1. Após a conclusão do provisionamento, você pode modificar suas políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócios.

   > [!NOTE]
   > A primeira etapa é opcional ao provisionar um ambiente. Se você ignorar essa etapa, deverá anexar uma origem do evento ao ambiente posteriormente para que os dados possam começar a fluir para o seu ambiente e possam ser acessados por meio da consulta.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente Azure Time Series Insights Gen 2:

1. Crie um recurso de Azure Time Series Insights em *Internet das coisas* no [portal do Azure](https://portal.azure.com/).

1. Selecione **Gen2 (L1)** como a **camada**. Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a serem usados. Em seguida, selecione um local com suporte para hospedar o ambiente.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Criar uma instância do Azure Time Series Insights." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Insira uma ID do Time Series.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Crie uma configuração de ambiente de Azure Time Series Insights, continue." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * A ID da série temporal diferencia *maiúsculas de minúsculas* e *imutável*. (Ela não poderá ser alterada após definida.)
   > * As IDs de série temporal podem ter até *três* chaves. Imagine-o como uma chave primária em um banco de dados, que representa exclusivamente cada sensor de dispositivo que enviaria dados para o seu ambiente. Pode ser uma propriedade ou uma combinação de até três propriedades.
   > * Leia mais sobre [como escolher uma ID de série temporal](./how-to-select-tsid.md)

1. Crie uma conta de armazenamento do Azure selecionando um nome de conta de armazenamento, tipo de conta e designando uma opção de [replicação](../storage/common/redundancy-migration.md?tabs=portal) . Fazer isso cria automaticamente uma conta de armazenamento do Azure. Por padrão, a conta de [uso geral v2](../storage/common/storage-account-overview.md) será criada. A conta é criada na mesma região que o ambiente de Azure Time Series Insights Gen2 que você selecionou anteriormente.
Como alternativa, você também pode colocar seu próprio armazenamento (BYOS) por meio de um [modelo de Azure Resource Manager](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) ao criar um novo ambiente do Azure Time Series Gen2.

1. **(Opcional)** Habilite a loja a quente para seu ambiente se você quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes em seu ambiente. Você também pode criar ou excluir uma loja quente por meio da opção de **configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente de Azure Time Series insights Gen2.

1. **(Opcional)** Você pode adicionar uma origem do evento agora. Você também pode aguardar até que a instância tenha sido provisionada.

   * O Azure Time Series Insights dá suporte ao [Hub IOT do Azure](./how-to-ingest-data-iot-hub.md) e aos [hubs de eventos do Azure](./how-to-ingest-data-event-hub.md) como opções de origem do evento. Embora seja possível adicionar apenas uma única origem do evento ao criar o ambiente, você pode adicionar outra origem do evento posteriormente.

     Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento. Certifique-se de que a origem do evento use um grupo de consumidores exclusivo para o seu ambiente ler dados nele.

   * Escolha a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a configuração mais adequada para uso em cenários de eventos de lote ou em cenários de carregamento de dados históricos. Nesses casos, certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Guia Configuração de origem do evento" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Selecione **examinar + criar** para confirmar se o seu ambiente foi provisionado e configurado da maneira desejada.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Revisar + criar guia" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre Azure Time Series Insights ambientes geralmente disponíveis e ambientes Gen2 lendo [planejar seu ambiente](./how-to-plan-your-environment.md).
* Saiba mais sobre as [fontes de evento de ingestão de streaming](./concepts-streaming-ingestion-event-sources.md) para seu ambiente de Azure Time Series insights Gen2.
* Saiba mais sobre [como gerenciar seu ambiente](./how-to-provision-manage.md).

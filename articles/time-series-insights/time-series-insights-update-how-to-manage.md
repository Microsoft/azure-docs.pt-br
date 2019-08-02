---
title: Provisionar e gerenciar a Versão Prévia do Azure Time Series | Microsoft Docs
description: Noções básicas sobre como provisionar e gerenciar a Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: f626ce2e009a18afcb4d04b7caa6850ea58c7483
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446823"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar a Versão Prévia do Azure Time Series Insights

Este artigo descreve como criar e gerenciar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Visão geral

Os ambientes de Versão Prévia do Azure Time Series Insights são ambientes de PAYG (pago conforme o uso).

Ao provisionar um ambiente de versão prévia do Azure Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights  
* Uma conta de uso geral v1 do Armazenamento do Microsoft Azure
  
Saiba [como planejar o ambiente](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Para a visualização, verifique se você estiver usando um armazenamento do Azure, uso geral v1 (GPv1) de conta.

Opcionalmente, você poderá associar cada ambiente de Versão Prévia do Azure Time Series Insights a uma origem do evento. Para obter mais informações, leia [Adicionar uma origem de Hub de Eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Adicionar uma origem de Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Durante essa etapa, você fornecerá uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo. Isso garante que o ambiente tenha acesso aos eventos apropriados.

Após concluir o provisionamento, você poderá modificar as políticas de acesso e outros atributos do ambiente para atender aos requisitos de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

As etapas a seguir descrevem como criar um ambiente de Versão Prévia do Azure Time Series Insights:

1. Selecione o botão **PAYG** no menu **SKU**. Forneça um nome de ambiente e escolha qual grupo de assinaturas e qual grupo de recursos usar. Em seguida, selecione uma localização com suporte na qual hospedar o ambiente.

   [![Crie uma instância do Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Insira uma ID do Time Series.

    >[!NOTE]
    > * A ID do Time Series diferencia maiúsculas de minúsculas e é imutável. (Ela não poderá ser alterada após definida.)
    > * IDs de Série Temporal podem ter de até três chaves.
    > * Para obter mais informações sobre como selecionar uma ID do Time Series, leia [Escolher uma ID do Time Series](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de armazenamento do Azure, selecionando um nome de conta de armazenamento e designando uma opção de replicação. Ao fazer isso, você cria automaticamente uma conta de uso geral v1 do Armazenamento do Azure. Ela será criada na mesma região que o ambiente de Versão Prévia do Azure Time Series Insights que você selecionou anteriormente.

    [![Criar uma conta de armazenamento do Azure para sua instância](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Opcionalmente, você pode adicionar uma origem do evento.

   * O Time Series Insights dá suporte para [Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) e [Hubs de Eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções. Embora seja possível adicionar apenas uma única origem do evento no momento da criação do ambiente, você poderá adicionar outra origem do evento posteriormente. É recomendável criar um grupo de consumidores exclusivo para garantir que todos os eventos sejam visíveis na instância de Versão Prévia do Azure Time Series Insights. Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento.

   * Também é necessário escolher a propriedade Timestamp apropriada. Por padrão, o Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a melhor configuração definida para usar em cenários de eventos em lotes ou de upload de dados históricos. Verifique sua decisão de usar ou não uma propriedade de Carimbo de Data/Hora nesses casos.

     [![Guia de origem do evento](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Confirme que seu ambiente foi provisionado com as configurações desejadas.

    [![Revisar + criar o guia](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

É possível gerenciar o ambiente de Versão Prévia do Azure Time Series Insights usando o portal do Azure. Estas são as principais diferenças no gerenciamento de um ambiente de Versão Prévia do Azure Time Series Insights de PAYG, ao contrário de um ambiente S1 ou S2, usando o portal do Azure:

* A folha de **Visão geral** do portal do Azure permanece inalterada no Azure Time Series Insights, exceto conforme a seguir:
  * A Capacidade é removida, porque esse conceito não é relevante para ambientes de PAYG.
  * A propriedade de ID de Série Temporal foi adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da conta de armazenamento do Azure é listado.

* A folha **Configurar** do portal do Azure foi removida na Versão Prévia do Azure Time Series Insights porque os ambientes de PAYG não são configuráveis.

* A folha **Dados de referência** do portal do Azure foi removida na Versão Prévia do Azure Time Series Insights porque os dados de referência não são um componente dos ambientes de PAYG.

[![Ambiente de visualização dos Insights de série de tempo no portal do Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Leia [Planejar seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [adicionar uma origem do hub de evento](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configure [uma origem do hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
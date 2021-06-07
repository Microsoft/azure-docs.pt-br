---
title: Adicionar um evento de hubs de eventos fonte-Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma fonte de eventos de hubs de eventos do Azure ao seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461126"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao seu ambiente de Azure Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que leia de um Hubs de Eventos do Azure para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo se aplicam aos ambientes Azure Time Series Insights Gen 1 e Azure Time Series Insights Gen 2.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de Azure Time Series Insights conforme descrito em [criar um ambiente de Azure Time Series insights](./tutorial-set-up-environment.md).
- Crie um hub de eventos. Leia [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos deve ter eventos de mensagem ativos enviados a ele. Saiba como [enviar eventos para os hubs de eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no Hub de eventos do qual o ambiente de Azure Time Series Insights pode consumir. Cada fonte de evento de Azure Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não é compartilhado com nenhum outro consumidor. Se vários leitores consomem eventos do mesmo grupo de consumidores, é provável que todos os leitores apresentem falhas. Há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, leia o [Guia de programação dos hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao hub de eventos

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hubs de Eventos do Azure. Para ler dados de forma confiável do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Azure Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub de eventos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra sua instância do hub de eventos no painel **visão geral** do seu namespace do hub de eventos. Selecione **entidades > hubs de eventos** ou localize sua instância em **nome**.

    [![Abrir o namespace do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Na sua instância do hub de eventos, selecione **entidades > grupos de consumidores**. Em seguida, selecione **+ grupo de consumidores** para adicionar um novo grupo de consumidores.

   [![Hub de eventos-adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Caso contrário, selecione um grupo de consumidores existente e pule para a próxima seção.

1. Na página **Grupos de consumidores**, insira um novo valor exclusivo para **Nome**.  Use esse mesmo nome ao criar uma nova origem de evento no ambiente de Azure Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [portal do Azure](https://portal.azure.com).

1. Localize seu ambiente de Azure Time Series Insights existente. No menu à esquerda, selecione **todos os recursos** e, em seguida, selecione seu ambiente de Azure Time Series insights.

1. Selecione **origens do evento** e, em seguida, selecione **Adicionar**.

   [![Em Fontes de Evento, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Insira um valor para o **nome de origem do evento** que seja exclusivo para esse Azure Time Series insights ambiente, como `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. Para **Origem**, selecione **Hub de Eventos**.

1. Selecione os valores apropriados para **Opção de importação**:

   - Se você tiver um hub de eventos existente em uma de suas assinaturas, selecione **Usar Hub de Eventos de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.

     [![Selecionar uma opção de importação de origem de evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub de Eventos de assinaturas disponíveis**:

       [![Detalhes de assinatura e Hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscription | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | Nome do Hub de Eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | Preenchido previamente com base no valor da política do hub de eventos selecionado. |

   - Se o hub de eventos for externo às suas assinaturas ou se você quiser selecionar opções avançadas, selecione **Fornecer configurações do Hub de Eventos manualmente**.

       A tabela a seguir descreve as propriedades necessárias para a opção **Fornecer configurações do Hub de Eventos manualmente**:

       | Propriedade | Descrição |
       | --- | --- |
       | ID da assinatura | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Resource group | O grupo de recursos ao qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Namespace do Hub de Eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | Nome do Hub de Eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**. |
       | Chave de política do Hub de Eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Insira aqui a chave primária ou secundária. |

   - Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do Hub de Eventos | O grupo de consumidor que lê os eventos do hub de eventos. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento devem estar nesse formato ou os dados não podem ser lidos. |
       | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub de eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome do grupo de consumidores de Azure Time Series Insights dedicado que você adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   Depois que a origem do evento é criada, Azure Time Series Insights automaticamente começa a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

- [Definir as políticas de acesso de dados](./concepts-access-policies.md) para proteger os dados.

- [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

- Acesse seu ambiente no [Azure Time Series insights Explorer](https://insights.timeseries.azure.com).

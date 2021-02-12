---
title: Como adicionar um evento de Hub IoT fonte-Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma origem de evento do Hub IoT ao seu ambiente do Azure Time Series Insight.
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
ms.openlocfilehash: bb13df0f87cd10719f33afe4ec080c4c785df720
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695553"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Adicionar uma origem de evento do Hub IoT ao seu ambiente do Azure Time Series Insight

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que lê dados de um Hub IoT para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As instruções neste artigo se aplicam aos ambientes Azure Time Series Insights Gen 1 e Azure Time Series Gen 2.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [ambiente de Azure Time Series insights](./tutorials-set-up-tsi-environment.md).
* Criar um [Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md).
* O hub IoT precisa ter eventos de mensagem ativos enviados.
* Crie um grupo de consumidores dedicado no Hub IoT para o ambiente do Azure Time Series Insight a ser consumido. Cada fonte de evento do Azure Time Series Insight deve ter seu próprio grupo de consumidores dedicado que não é compartilhado com nenhum outro consumidor. Se vários leitores consomem eventos do mesmo grupo de consumidores, é provável que todos os leitores apresentem falhas. Para obter detalhes, leia o [Guia do desenvolvedor do Hub IOT do Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hub IoT do Azure. Para ler dados de forma confiável de seu hub IoT, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente do Azure Time Series Insight.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub IOT.

1. Em **configurações**, selecione **pontos de extremidade internos** e, em seguida, selecione o ponto final de **eventos** .

   [![Na página Pontos de Extremidade Internos, selecione o botão Eventos](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Em **Grupos de consumidores**, insira um nome exclusivo para o grupo de consumidores. Use esse mesmo nome em seu ambiente do Azure Time Series Insight ao criar uma nova origem do evento.

1. Selecione **Salvar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione seu ambiente do Azure Time Series Insight.

1. Em **configurações**, selecione **origens do evento** e, em seguida, selecione **Adicionar**.

   [![Selecione Fontes de eventos e selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. No **novo painel origem do evento** , para **nome da origem do evento**, insira um nome que seja exclusivo para este ambiente do Azure Time Series Insight. Por exemplo, digite **fluxo-de-evento**.

1. Para **Origem**, selecione **Hub IoT**.

1. Selecione um valor para **Importar opção**:

   * Se você já tiver um Hub IoT em uma de suas assinaturas, selecione **Usar o Hub IoT de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.

     [![Selecione as opções no painel Nova origem do evento](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub IoT de assinaturas disponíveis**:

       [![Painel Nova origem do evento – Propriedades a serem definidas na opção Use o Hub IoT de assinaturas disponíveis](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Assinatura | A assinatura à qual o Hub IOT desejado pertence. |
       | Nome do Hub IoT | O nome do Hub IOT selecionado. |
       | Nome da política do Hub IoT | Selecione a política de acesso compartilhado. Você pode encontrar a política de acesso compartilhado na guia Configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**. |
       | Chave de política do Hub IoT | A chave é preenchida previamente. |

   * Se o hub IoT for externo às suas assinaturas ou se você quiser escolher opções avançadas, selecione **Fornecer configurações do Hub IoT manualmente**.

      A tabela a seguir descreve as propriedades necessárias para **Fornecer configurações do Hub IoT manualmente**:

       | Propriedade | Descrição |
       | --- | --- |
       | ID da assinatura | A assinatura à qual o Hub IOT desejado pertence. |
       | Resource group | O nome do grupo de recursos no qual o hub IoT foi criado. |
       | Nome do Hub IoT | O nome de seu hub IoT. Quando criou o hub IoT, você inseriu um nome para ele. |
       | Nome da política do Hub IoT | A política de acesso compartilhado. Você pode criar a política de acesso compartilhado na guia Configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**. |
       | Chave de política do Hub IoT | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço do Azure. Insira aqui a chave primária ou secundária. |

   * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do Hub IoT | O grupo de consumidores que lê eventos do hub IoT. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento deverão estar no formato a seguir ou nenhum dado poderá ser lido. |
       | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome do grupo de consumidores do Azure Time Series dedicado que você adicionou ao seu hub IoT.

1. Selecione **Criar**.

1. Depois de criar a origem do evento, o Azure Time Series Insight inicia automaticamente o streaming de dados para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Definir as políticas de acesso de dados](./concepts-access-policies.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Acesse seu ambiente no [Gerenciador de informações da série temporal do Azure](https://insights.timeseries.azure.com).

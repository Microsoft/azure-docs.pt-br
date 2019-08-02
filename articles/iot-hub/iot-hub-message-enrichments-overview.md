---
title: Visão geral dos aprimoramentos de mensagens do Hub IoT do Azure
description: Visão geral dos aprimoramentos de mensagem para mensagens do Hub IoT do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 8e24489ad2909879b035a08316e66788034e99bc
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377203"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Aprimoramentos de mensagem para mensagens do Hub IoT do dispositivo para a nuvem (versão prévia)

Os aprimoramentos de *mensagens* são a capacidade do Hub IOT de *carimbar* mensagens com informações adicionais antes que as mensagens sejam enviadas para o ponto de extremidade designado. Um motivo para usar os aprimoramentos de mensagem é incluir dados que possam ser usados para simplificar o processamento de downstream. Por exemplo, enriquecer mensagens de telemetria do dispositivo com uma marca de dispositivo de Altova pode reduzir a carga em clientes para fazer chamadas à API do dispositivo.

![Fluxo de aprimoramentos de mensagens](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Um enriquecimento de mensagem tem três elementos principais:

* Nome ou chave de enriquecimento

* Um valor

* Um ou mais [pontos de extremidade](iot-hub-devguide-endpoints.md) para os quais o enriquecimento deve ser aplicado.

A chave pode ser qualquer cadeia de caracteres.

O valor pode ser qualquer um dos seguintes exemplos:

* Qualquer cadeia de caracteres estática. Valores dinâmicos, como condições, lógica, operações e funções, não são permitidos. Por exemplo, se você desenvolver um aplicativo SaaS que é usado por vários clientes, poderá atribuir um identificador a cada cliente e torná-lo disponível no aplicativo. Quando o aplicativo for executado, o Hub IoT carimbará as mensagens de telemetria do dispositivo com o identificador do cliente, tornando possível processar as mensagens de forma diferente para cada cliente.

* O nome do Hub IoT que envia a mensagem. Esse valor é *$iothubname*.

* Informações do dispositivo de dados, como seu caminho. Os exemplos são *$Twin. Tags. Field* e *$Twin. Tags. latitude*.

   > [!NOTE]
   > Neste momento, somente $iothubname, $twin. Tags, $twin. Properties. Desired e $twin. Properties. Reported são variáveis com suporte para o enriquecimento de mensagens.

Os aprimoramentos de mensagens são adicionados como propriedades do aplicativo às mensagens enviadas para os pontos de extremidade escolhidos.  

## <a name="applying-enrichments"></a>Aplicando aprimoramentos

As mensagens podem vir de qualquer fonte de dados com suporte no [Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md), incluindo os seguintes exemplos:

* telemetria do dispositivo, como temperatura ou pressão
* notificações de alteração de troca de dispositivo--alterações no dispositivo.
* eventos do ciclo de vida do dispositivo, como quando o dispositivo é criado ou excluído

Você pode adicionar aprimoramentos a mensagens que vão para o ponto de extremidade interno de um hub IoT ou mensagens que estão sendo roteadas para pontos de extremidades personalizados, como o armazenamento de BLOBs do Azure, uma fila do barramento de serviço ou um tópico do barramento de serviço.

Você também pode adicionar aprimoramentos às mensagens que estão sendo publicadas na grade de eventos selecionando o ponto de extremidade como grade de eventos. Para obter mais informações, consulte [Hub IOT e grade de eventos](iot-hub-event-grid.md).

Os aprimoramentos são aplicados por ponto de extremidade. Se você especificar cinco aprimoramentos a serem carimbados para um ponto de extremidade específico, todas as mensagens que vão para esse ponto de extremidade serão carimbadas com os mesmos cinco aprimoramentos.

Para ver como experimentar os aprimoramentos de mensagens, consulte o tutorial de aprimoramentos de [mensagem](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitações

* Você pode adicionar até 10 aprimoramentos por Hub IoT para esses hubs na camada Standard ou básica. Para os hubs IoT na camada gratuita, você pode adicionar até 2 aprimoramentos.

* Em alguns casos, se você estiver aplicando um enriquecimento com um valor definido para uma marca ou propriedade no dispositivo de texto, o valor será marcado como um valor de cadeia de caracteres. Por exemplo, se um valor de enriquecimento for definido como $twin. Tags. Field, as mensagens serão carimbadas com a cadeia de caracteres "$twin. Tags. Field" em vez do valor desse campo a partir de o texto. Isso acontece nos seguintes casos:

   * O Hub IoT está na camada básica. Os hubs IoT da camada básica não dão suporte a dispositivos gêmeos.

   * O Hub IoT está na camada Standard, mas o dispositivo que está enviando a mensagem não tem nenhum dispositivo.

   * O Hub IoT está na camada Standard, mas o caminho de Altova do dispositivo usado para o valor do enriquecimento não existe. Por exemplo, se o valor de enriquecimento for definido como $twin. Tags. Location e o dispositivo "r" não tiver uma propriedade Location em marcas, a mensagem será carimbada com a cadeia de caracteres "$twin. Tags. Location". 

* As atualizações em um dispositivo de atualização podem levar até cinco minutos para serem refletidas no valor de enriquecimento correspondente.

* O tamanho total da mensagem, incluindo os aprimoramentos, não pode exceder 256 KB. Se um tamanho de mensagem exceder 256 KB, o Hub IoT removerá a mensagem. Você pode usar [métricas do Hub IOT](iot-hub-metrics.md) para identificar e depurar erros quando as mensagens são descartadas. Por exemplo, você pode monitorar D2C. telemetria. egresso. Invalid.

## <a name="pricing"></a>Preços

Os aprimoramentos de mensagens estão disponíveis sem custo adicional. Atualmente, você é cobrado quando envia uma mensagem para um hub IoT. Você é cobrado apenas uma vez por essa mensagem, mesmo que a mensagem vá para vários pontos de extremidade.

## <a name="availability"></a>Disponibilidade

Essa funcionalidade está disponível na versão prévia e está disponível em todas as regiões, exceto leste dos EUA, oeste dos EUA, Europa Ocidental, [Azure governamental](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china)e [Azure Alemanha](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Próximas etapas

Confira estes artigos para obter mais informações sobre como rotear mensagens para um hub IoT:

* [Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade](iot-hub-devguide-messages-d2c.md)

* [Tutorial: Roteamento do Hub IoT](tutorial-routing.md)

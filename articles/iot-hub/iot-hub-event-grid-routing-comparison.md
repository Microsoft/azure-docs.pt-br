---
title: Comparar a Grade de Eventos, roteamento para Hub IoT | Microsoft Docs
description: O Hub IoT oferece seu próprio serviço de roteamento de mensagens, mas também integra com a Grade de Eventos para publicação de eventos. Compare os dois recursos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897698"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar roteamento de mensagens e Grade de Eventos para Hub IoT

O Hub IoT do Azure fornece a capacidade de transmitir dados de seus dispositivos conectados e integrá-los aos seus aplicativos de negócios. O Hub IoT oferece dois métodos para integrar eventos IoT em outros serviços do Azure ou aplicativos de negócios. Este artigo discute os dois recursos que fornecem essa capacidade para que você possa escolher qual é a melhor opção para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md)**: esse recurso do Hub IOT permite que os usuários direcionem mensagens do dispositivo para a nuvem para pontos de extremidade de serviço como contêineres de armazenamento do Azure, hubs de eventos, filas do barramento de serviço e tópicos do barramento de serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de encaminhá-los aos pontos de extremidade. Além dos dados de telemetria do dispositivo, você também pode enviar [eventos de não-telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para acionar ações. 

**Integração do Hub IoT com a Grade de Eventos**: a Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. U Hub IoT e a Grade de Eventos trabalham em conjunto para [integrar os eventos do Hub IoT em serviços Azure e não-Azure](iot-hub-event-grid.md), quase em tempo real. O Hub IoT publica [eventos de dispositivo](iot-hub-event-grid.md#event-types) e eventos de telemetria.

## <a name="differences"></a>Diferenças

Embora o roteamento de mensagens e a Grade de Eventos habilitam a configuração de alerta, há algumas diferenças importantes entre os dois. Consulte a tabela a seguir para obter detalhes:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Mensagens e eventos do dispositivo** | Sim, o roteamento de mensagens pode ser usado para dados de telemetria, alterações de dispositivo de troca, eventos de ciclo de vida de dispositivo e eventos de alteração de mensagens digitais. | Sim, a grade de eventos pode ser usada para dados de telemetria e eventos de dispositivo, como dispositivos criados/excluídos/conectados/desconectados. Mas a grade de eventos não pode ser usada para eventos de alteração de dispositivo e eventos de alteração de troca digital. |
| **Ordenando** | Sim, a ordenação de eventos é mantida.  | Não, a ordem dos eventos não é garantida. | 
| **Filtragem** | Filtragem avançada em propriedades do aplicativo de mensagens, propriedades do sistema de mensagens, corpo da mensagem, tags de gêmeos de dispositivo e propriedades de gêmeos de dispositivo. A filtragem não é aplicada a eventos de alteração de troca digital. Para exemplos, consulte [Sintaxe de Consulta de Roteamento de Mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem com base no tipo de evento, tipo de assunto e atributos em cada evento. Para obter exemplos, consulte [entender os eventos de filtragem em assinaturas da grade de eventos](../event-grid/event-filtering.md). Ao assinar eventos de telemetria, você pode aplicar filtros adicionais aos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o dispositivo de mensagens no Hub IoT, antes de publicar na grade de eventos. Consulte [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Pontos de extremidade** | <ul><li>Hubs de Eventos</li> <li>Armazenamento do Blobs do Azure</li> <li>Fila do Barramento de Serviço</li> <li>Tópicos do Barramento de Serviço</li></ul><br>As SKUs do Hub IoT pagas (S1, S2 e S3) estão limitadas a 10 pontos de extremidades personalizados. 100 rotas podem ser criadas por Hub IoT. | <ul><li>Azure Functions</li> <li>Automação do Azure</li> <li>Hubs de Eventos</li> <li>Aplicativos Lógicos</li> <li>Blob de Armazenamento</li> <li>Tópicos personalizados</li> <li>Armazenamento de Filas</li> <li>Power Automate</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>500 pontos de extremidade por Hub IoT têm suporte. Para obter a lista mais atualizada de pontos de extremidades, consulte [Manipuladores de eventos da Grade de Eventos](../event-grid/overview.md#event-handlers). |
| **Custo** | Não há encargos separados para roteamento de mensagens. Somente o ingresso de telemetria no Hub IoT é cobrado. Por exemplo, se você tiver uma mensagem roteada para três pontos de extremidades diferentes, você será cobrado por apenas uma mensagem. | Não há nenhum custo do Hub IoT. A grade de eventos oferece as primeiras 100.000 operações por mês gratuitamente e, em seguida, $0.60 por milhão de operações posteriormente. |

## <a name="similarities"></a>Semelhanças

O roteamento de mensagens do Hub IoT e a Grade de Eventos também têm semelhanças, algumas das quais detalhadas na tabela a seguir:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para nuvem | 256 KB, dispositivo para nuvem |
| **Confiabilidade** | Alta: entrega cada mensagem ao ponto de extremidade pelo menos uma vez por cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alta: entrega cada mensagem ao webhook pelo menos uma vez por cada assinatura. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alta: otimizado para suportar milhões de dispositivos conectados simultaneamente enviando bilhões de mensagens. | Alto: capaz de rotear 10.000.000 eventos por segundo por região. |
| **Latência** | Baixa: quase em tempo real. | Baixa: quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  
| **Segurança** | O HUB Iot fornece identidade por dispositivo e controle de acesso revogável. Para obter mais informações, consulte o [Controle de acesso do Hub IoT](iot-hub-devguide-security.md). | A Grade de Eventos fornece validação em três pontos: assinaturas de eventos, publicação de eventos e entrega de eventos do webhook. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). |

## <a name="how-to-choose"></a>Como escolher

O roteamento de mensagens do Hub IoT e a integração do Hub IoT com a Grade de Eventos realizam ações diferentes para obter resultados semelhantes. Ambos tomam informações da solução do Hub IoT e informam para que outros serviços possam reagir. Então, como você decide qual deles usar? Considere as seguintes perguntas para ajudar a orientar sua decisão: 

* **Que tipo de dados você está enviando para os pontos de extremidade?**

   Use o roteamento de mensagens do Hub IoT quando for necessário enviar dados telemétricos para outros serviços. O roteamento de mensagens também permite consultar o aplicativo de mensagens e as propriedades do sistema, o corpo da mensagem, as tags gêmeas do dispositivo e as propriedades gêmeas do dispositivo.

   A integração do Hub IoT com a Grade de Eventos funciona com os eventos que ocorrem no serviço do Hub IoT. Esses eventos do Hub IoT incluem dados de telemetria, dispositivos criados, excluídos, conectados e desconectados. Ao assinar eventos de telemetria, você pode aplicar filtros adicionais aos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o dispositivo de mensagens no Hub IoT, antes de publicar na grade de eventos. Consulte [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quais pontos de extremidade precisam receber essas informações?**

   O roteamento de mensagens do Hub IoT dá suporte a um número limitado de pontos de extremidade exclusivos e tipos de ponto de extremidades, mas você pode criar conectores para redirecionar os dados e eventos para pontos de extremidades adicionais. Para obter uma lista completa dos pontos de extremidade com suporte, consulte a tabela na seção anterior. 

   A integração do Hub IoT com a grade de eventos dá suporte a pontos de extremidade de 500 por Hub IoT e a uma variedade maior de tipos de Endpoint. Ele se integra nativamente com Azure Functions, aplicativos lógicos, armazenamento e filas do barramento de serviço, além de funcionar com WebHooks para estender o envio de dados fora do ecossistema de serviços do Azure e para aplicativos de negócios de terceiros.

* **É importante os dados chegarem em ordem?**

   O roteamento de mensagens do Hub IoT mantém a ordem em que as mensagens são enviadas para que elas cheguem da mesma maneira.

   A Grade de Eventos não garante que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. Para os casos em que a ordem absoluta de mensagens é significativa e/ou em que um consumidor precisa de um identificador exclusivo confiável para mensagens, é recomendável usar o roteamento de mensagens. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md) e os [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).
* Saiba mais sobre a [Grade de Eventos do Azure](../event-grid/overview.md).
* Para aprender como criar Rotas de Mensagens, confira o tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas](../iot-hub/tutorial-routing.md).
* Experimente a integração da grade de eventos [enviando notificações por email sobre eventos do Hub IOT do Azure usando aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

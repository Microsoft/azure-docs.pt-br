---
title: Entender os pontos de extremidade do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor ‑ Informações de referência sobre pontos de extremidade do Hub IoT voltados para o dispositivo e para o serviço."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 47f8949139c48ffa79f5530552b0a2e27b0f9ee0
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reference---iot-hub-endpoints"></a>Referência - Pontos de extremidade do Hub IoT

## <a name="iot-hub-names"></a>Nomes de Hub IoT

Você pode encontrar o nome do Hub IoT que hospeda os pontos de extremidade no portal na folha **Visão geral**. Por padrão, o nome DNS de um Hub IoT parece com: `{your iot hub name}.azure-devices.net`.

Você pode usar o DNS do Azure para criar um nome DNS personalizado para o Hub IoT. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../dns/dns-custom-domain.md#azure-iot).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos de extremidade internos do Hub IoT

O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a vários atores. O diagrama a seguir mostra os diversos pontos de extremidade que o Hub IoT expõe.

![Pontos de extremidade do Hub IoT][img-endpoints]

A lista a seguir descreve os pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos de Hub IoT expõe uma interface do [Azure Resource Manager][lnk-arm]. Essa interface permite que proprietários de assinatura do Azure criem e excluam Hubs IoT e atualizem as propriedades de Hub IoT. As propriedades do Hub IoT regem as [políticas de segurança no nível do hub][lnk-accesscontrol], ao contrário do controle de acesso no nível do dispositivo, e as opções funcionais para sistema de mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos do Hub IoT também permite [exportar identidades do dispositivo][lnk-importexport].
* **Gerenciamento de identidades dos dispositivos**. Cada Hub IoT expõe um conjunto de pontos de extremidade HTTPS REST para o gerenciamento de identidades do dispositivo (criar, recuperar, atualizar e excluir). [As identidades do dispositivo][lnk-device-identities] são usadas para controle de acesso e autenticação do dispositivo.
* **Gerenciamento do dispositivo gêmeo**. Cada Hub IoT expõe um conjunto de ponto de extremidade voltado para o serviço HTTPS REST para consultar e atualizar [dispositivos gêmeos][lnk-twins] (atualizar marcas e propriedades).
* **Gerenciamento de trabalhos**. Cada Hub IoT expõe um conjunto de ponto de extremidade voltado para o serviço HTTPS REST para consultar e gerenciar [trabalhos][lnk-jobs].
* **Pontos de extremidade do dispositivo**. Para cada dispositivo no registro de identidade, o Hub IoT expõe um conjunto de pontos de extremidade:

  * *Enviar mensagens do dispositivo para a nuvem*. Um dispositivo usa este ponto de extremidade para [enviar mensagens do dispositivo para a nuvem][lnk-d2c].
  * *Receber mensagens da nuvem para o dispositivo*. Um dispositivo usa este ponto de extremidade para receber [mensagens direcionadas da nuvem para o dispositivo][lnk-c2d].
  * *Inicie os uploads de arquivos*. Um dispositivo usa esse ponto de extremidade para receber um URI de SAS do Armazenamento do Azure do Hub IoT para [carregar um arquivo][lnk-upload].
  * *Recupere e atualize as propriedades do dispositivo gêmeo*. Um dispositivo usa esse ponto de extremidade para acessar as propriedades do [dispositivo gêmeo][lnk-twins].
  * *Receber solicitações de métodos diretos*. Um dispositivo usa esse ponto de extremidade para escutar as solicitações dos [métodos diretos][lnk-methods].

    Estes pontos de extremidade são expostos usando os protocolos [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 e [AMQP 1.0][lnk-amqp]. O AMQP também está disponível sobre [WebSockets][lnk-websockets] na porta 443.

* **Pontos de extremidade do serviço**. Cada Hub IoT expõe um conjunto de pontos de extremidade para que o seu back-end da sua solução se comunique com os seus dispositivos. Com uma exceção, esses pontos de extremidade só são expostos usando o protocolo [AMQP][lnk-amqp]. O ponto de extremidade de invocação de método é exposto através do protocolo HTTPS.
  
  * *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [Hubs de Eventos do Azure][lnk-event-hubs]. Um serviço de back-end pode usá-lo para ler todas as [mensagens do dispositivo para a nuvem][lnk-d2c] enviadas por seus dispositivos. Você pode criar pontos de extremidade personalizados em seu hub IoT, além desse ponto de extremidade interno.
  * *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o seu back-end de solução envie [mensagens da nuvem para o dispositivo][lnk-c2d] confiáveis e receba as confirmações de entrega ou de vencimento correspondentes.
  * *Receba notificações de arquivo*. Esse ponto de extremidade de mensagens permite que você receba notificações quando os dispositivos carregarem com êxito um arquivo. 
  * *Invocação direta de método*. Esse ponto de extremidade permite que um serviço de back-end invoque um [método direto][lnk-methods] em um dispositivo.
  * *Eventos de monitoramento de operações de recebimento*. Esse ponto de extremidade permitirá que você receba eventos de monitoramento de operações se o hub IoT tiver sido configurado para emiti-los. Para obter mais informações, consulte [Monitoramento de operações do Hub IoT][lnk-operations-mon].

O artigo [SDKs do Azure IoT][lnk-sdks] descreve as várias maneiras de acessar esses pontos de extremidade.

Todos os pontos de extremidade do Hub IoT usam o protocolo [TLS][lnk-tls] e que nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

## <a name="custom-endpoints"></a>Pontos de extremidade personalizados

Você pode vincular os serviços existentes do Azure em sua assinatura ao Hub IoT para serem usados como pontos de extremidade no direcionamento de mensagens. Esses agem como pontos de extremidade de serviço e são usados como "coletores" para rotas de mensagens. Os dispositivos não podem gravar diretamente nos pontos de extremidade adicionais. Para saber mais sobre rotas de mensagens, confira a entrada do guia do desenvolvedor sobre [como enviar e receber mensagens com o hub IoT][lnk-devguide-messaging].

Atualmente, o Hub IoT é compatível com os seguintes serviços do Azure como pontos de extremidade adicionais:

* Contêineres de Armazenamento do Azure
* Hubs de Eventos
* Filas de barramento de serviço
* Tópicos do Service Bus

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade para que o direcionamento de mensagens funcione. Se você configurar os pontos de extremidade por meio do Portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar os serviços para dar suporte à taxa de transferência esperada. Quando você configurar pela primeira vez sua solução IoT, talvez seja necessário monitorar seus pontos de extremidade adicionais ao configurar e fazer eventuais ajustes necessários para a carga real.

Se uma mensagem corresponder a várias rotas e todas apontarem para o mesmo ponto de extremidade, o Hub IoT entregará mensagens para esse ponto apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação nem no tópico, nem na fila do Barramento de Serviço. Em filas particionados, a afinidade de partição garante a ordenação das mensagens.

Para conhecer os limites para a quantidade de pontos de extremidade que você pode adicionar, confira [Cotas e limitação][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Ao usar os contêineres de Armazenamento do Azure

O Hub IoT oferece suporte somente à gravação de dados em contêineres de Armazenamento do Azure como blobs no formato [Apache Avro](http://avro.apache.org/). O Hub IoT coloca as mensagens em lotes e grava os dados em um blob quando atinge um determinado tamanho ou após um determinado período de tempo decorrido, o que ocorrer primeiro. O Hub IoT não gravará um blob vazio se não houver nenhum dado para gravação.

O Hub IoT segue a seguinte convenção de nomenclatura de arquivo padrão:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Você pode usar qualquer convenção de nomenclatura que desejar, porém você deve usar todos os tokens listados.

### <a name="when-using-service-bus-queues-and-topics"></a>Ao usar filas e tópicos do Barramento de Serviço

As filas e os tópicos do Barramento de Serviço utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

## <a name="field-gateways"></a>Gateways de campo

Em uma solução IoT, um *gateway de campo* fica entre os dispositivos e os pontos de extremidade do hub IoT. Normalmente, ele se localiza perto dos dispositivos. Os dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo conecta-se com um ponto de extremidade Hub IoT usando um protocolo que tem suporte do Hub IoT. Um gateway de campo pode ser um dispositivo de hardware dedicado ou em um computador de baixa capacidade que executam o software do gateway personalizado.

Use o [Azure IoT Edge][lnk-iot-edge] para implementar um gateway de campo. O IoT Edge oferece funcionalidades como a multiplexação de comunicações de vários dispositivos na mesma conexão do Hub IoT.

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-devguide-query]
* [Cotas e limitações][lnk-devguide-quotas]
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

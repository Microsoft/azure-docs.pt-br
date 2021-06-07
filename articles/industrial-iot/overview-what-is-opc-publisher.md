---
title: Microsoft OPC Publisher
description: Este artigo fornece uma visão geral do módulo OPC Publisher do Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800526"
---
# <a name="what-is-the-opc-publisher"></a>O que é o OPC Publisher?

O OPC Publisher é um produto da Microsoft com suporte completo, desenvolvido como software livre, que preenche a lacuna entre os ativos industriais e a nuvem do Microsoft Azure. Ele faz isso conectando-se a ativos habilitados para OPC UA ou a programas de software de conectividade industrial e publica os dados telemétricos no Hub IoT do Azure em vários formatos, incluindo o formato padrão IEC62541 OPC UA PubSub (da versão 2.6 em diante).

Ele é executado no Azure IoT Edge como um módulo ou em um Docker simples como um contêiner. Como ele aproveita o runtime multiplataforma do .NET, também é executado nativamente no Linux e no Windows 10.

OPC Publisher é uma implementação de referência que demonstra como:

- Conectar-se a servidores UA OPC existentes.
- Publicar dados de telemetria codificados em JSON de servidores de UA OPC no formato Pub/Sub UA OPC, usando uma carga JSON, para o Hub IoT do Azure.

Você pode usar qualquer um dos protocolos de transporte com suporte do SDK do cliente do Hub IoT do Azure: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* UA OPC para se conectar a servidores UA OPC existentes em sua rede.
- Um *servidor* UA OPC na porta 62222 que você pode usar para gerenciar o que é publicado e oferece métodos diretos do Hub IoT para fazer o mesmo.

Você pode baixar a [implementação de referência do OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) do GitHub.

O aplicativo é implementado usando a tecnologia .NET Core e pode ser executado em plataformas com suporte do .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>O que o OPC Publisher faz?

O OPC Publisher implementa a lógica de repetição para estabelecer conexões com pontos de extremidade que não respondem a um determinado número de solicitações de keep alive. Por exemplo, se um servidor UA OPC parar de responder devido a uma interrupção de energia.

Para cada intervalo de publicação distinto em um servidor UA OPC, o aplicativo cria uma assinatura separada na qual todos os nós no intervalo de publicação são atualizados.

O OPC Publisher oferece suporte ao envio em lote dos dados ao Hub IoT para reduzir a carga de rede. Este envio em lote envia um pacote ao Hub IoT somente se o tamanho do pacote configurado for alcançado.

Esse aplicativo usa a pilha de referência OPC UA da OPC Foundation como pacotes NuGet. Confira os [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) para os termos de licenciamento.

## <a name="next-steps"></a>Próximas etapas
Agora que aprendeu o que é o OPC Publisher, você pode começar a implantá-lo:

> [!div class="nextstepaction"]
> [Implantar o OPC Publisher no modo autônomo](tutorial-publisher-deploy-opc-publisher-standalone.md)

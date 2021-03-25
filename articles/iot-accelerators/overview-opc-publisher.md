---
title: O que é o OPC Publisher - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos do OPC Publisher. Ele permite que você publique dados de telemetria JSON codificados usando uma carga JSON, para o Hub IoT do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281772"
---
# <a name="what-is-opc-publisher"></a>O que é o OPC Publisher?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

OPC Publisher é uma implementação de referência que demonstra como:

- Conectar-se a servidores UA OPC existentes.
- Publicar dados de telemetria codificados em JSON de servidores de UA OPC no formato Pub/Sub UA OPC, usando uma carga JSON, para o Hub IoT do Azure.

Você pode usar qualquer um dos protocolos de transporte com suporte do SDK do cliente do Hub IoT do Azure: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* UA OPC para se conectar a servidores UA OPC existentes em sua rede.
- Um *servidor* UA OPC na porta 62222 que você pode usar para gerenciar o que é publicado e oferece métodos diretos do Hub IoT para fazer o mesmo.

Você pode baixar a [implementação de referência do OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) do GitHub.

O aplicativo é implementado usando a tecnologia .NET Core e pode ser executado em plataformas com suporte do .NET Core.

O OPC Publisher implementa a lógica de repetição para estabelecer conexões com pontos de extremidade que não respondem a um determinado número de solicitações de keep alive. Por exemplo, se um servidor UA OPC parar de responder devido a uma interrupção de energia.

Para cada intervalo de publicação distinto em um servidor UA OPC, o aplicativo cria uma assinatura separada na qual todos os nós no intervalo de publicação são atualizados.

O OPC Publisher oferece suporte ao envio em lote dos dados ao Hub IoT para reduzir a carga de rede. Este envio em lote envia um pacote ao Hub IoT somente se o tamanho do pacote configurado for alcançado.

Esse aplicativo usa a pilha de referência OPC UA da OPC Foundation como pacotes NuGet. Confira os [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) para os termos de licenciamento.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu o que é o OPC Publisher, a próxima etapa sugerida é aprender a:

[Configurar o OPC Publisher](howto-opc-publisher-configure.md)
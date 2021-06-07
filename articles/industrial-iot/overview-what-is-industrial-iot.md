---
title: Visão geral da IoT Industrial do Azure
description: Este artigo fornecerá uma visão geral da IoT Industrial. Ele explicará de que modo funcionam a conectividade do chão de fábrica e os componentes de segurança na IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787606"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é a IIoT (IoT Industrial)?

A IIoT (Internet das Coisas Industrial) aprimora a eficiência industrial por meio da aplicação de IoT no setor de fabricação.

![IoT Industrial](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Melhorar as eficiências industriais
Aprimore a produtividade e a rentabilidade operacionais usando a IoT Industrial do Azure. Reduza o processo lento de acessar ativos localmente. Conecte e monitore equipamentos e dispositivos industriais na nuvem, incluindo computadores que já operam no chão de fábrica. Analise dados de IoT para obter insights que ajudarão você a expandir o desempenho de toda a fábrica.

## <a name="industrial-iot-components"></a>Componentes da IoT Industrial

**Dispositivos do IoT Edge**: um dispositivo do IoT Edge é composto por Módulos do Edge e o Runtime do Edge. 
- *Módulos do Edge* são contêineres do Docker. Eles são a menor unidade de computação, como o Editor OPC e o Gêmeo OPC. 
- O *Dispositivo de borda* é usado para implantar esses módulos, que atuam como mediadores entre o servidor OPC UA e o Hub IoT na nuvem. Para obter mais informações sobre o IoT Edge, clique [aqui](https://azure.microsoft.com/services/iot-edge/).

**Hub IoT**: o Hub IoT funciona como um hub central de mensagens de comunicação bidirecional entre um aplicativo de IoT e os dispositivos que ele gerencia. Ele é uma plataforma como serviço de nuvem aberta e flexível. Além disso, ela é compatível com SDKs de software livre e vários protocolos. Leia mais sobre o Hub IoT [aqui](https://azure.microsoft.com/services/iot-hub/).

**Módulos do Edge Industrial**
- *Editor OPC*: o Editor OPC é executado dentro do IoT Edge. Ele se conecta a servidores OPC UA e publica dados telemétricos e codificados em JSON desses servidores no Hub IoT do Azure usando o formato "Pub/Sub" do OPC UA. Todos os protocolos de transporte compatíveis com o SDK do cliente do Hub IoT do Azure podem ser usados, como HTTPS, AMQP e MQTT.
- *Gêmeo OPC*: o Gêmeo OPC é formado por microsserviços que usam o Azure IoT Edge e o Hub IoT para conectar a nuvem e a rede de fábrica. O OPC Twin fornece descoberta, registro e controle remoto de dispositivos industriais por meio de APIs REST. O Gêmeo OPC não requer obter um SDK para OPC UA (Arquitetura Unificada OPC). Ele é agnóstico em relação à linguagem de programação e pode ser incluído em um fluxo de trabalho sem servidor.
- *Descoberta*: o módulo de descoberta, representado pela identidade do detector, fornece serviços de descoberta no Edge, que incluem a descoberta do servidor OPC UA. Caso a descoberta esteja configurada e habilitada, o módulo enviará os resultados de uma investigação de verificação ao Serviço de Integração por meio do caminho de telemetria do IoT Edge e do Hub IoT. O serviço processará os resultados e atualizará todas as Identidades do Registro.


**Descobrir, registrar e gerenciar Ativos Industriais usando o Azure**: a IoT Industrial do Azure permite que operadores de fábrica descubram servidores habilitados para OPC UA em uma rede de fábrica e os registrem no Hub IoT do Azure. A equipe de operações pode assinar eventos e reagir a eles no chão de fábrica de todos os lugares do mundo. As APIs REST de Microsserviços espelham serviços OPC UA do Edge. Eles são protegidos com uma autenticação e autorização OAUTH com suporte do AAD (Azure Active Directory). Isso permite que seus aplicativos de nuvem procurem espaços de endereço do servidor ou variáveis de leitura/gravação, bem como executem métodos usando HTTPS e conteúdos simples de OPC UA JSON.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu o que é a IoT Industrial, será possível ler informações sobre a Plataforma de IoT Industrial e o Editor OPC:

> [!div class="nextstepaction"]
> [O que é a Plataforma de IoT Industrial?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [O que é o Editor OPC?](overview-what-is-opc-publisher.md)
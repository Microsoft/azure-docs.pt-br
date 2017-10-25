---
title: "Visão geral do Hub IoT do Azure | Microsoft Docs"
description: "Visão geral do serviço Hub IoT do Azure: o que é Hub IoT, conectividade do dispositivo, padrões de comunicação da Internet das coisas, gateways e o padrão de comunicação assistida de serviços"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5f44d2ae42ffc6f75887a64c9ef988fe6d8fd69
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Visão geral do serviço do Hub IoT do Azure

Bem-vindo ao Hub IoT do Azure. Este artigo fornece uma visão geral do Hub IoT do Azure e descreve o motivo pelo qual você deve usar esse serviço para implementar uma solução IoT (Internet das Coisas). O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. Hub IoT do Azure:

* Fornece várias opções de comunicação do dispositivo para a nuvem e da nuvem para o dispositivo. Essas opções incluem mensagens unidirecionais, transferência de arquivos e métodos de solicitação-resposta.
* Fornece o roteamento de mensagens declarativas interno para outros serviços do Azure.
* Fornece um armazenamento consultável para metadados de dispositivo e informações de estado sincronizado.
* Habilita comunicações seguras e controle de acesso usando chaves de segurança por dispositivo ou certificados X.509.
* Fornece monitoramento abrangente para eventos de gerenciamento de identidade do dispositivo e de conectividade do dispositivo.
* Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

O artigo [Comparação do Hub IoT com os Hubs de Eventos][lnk-compare] descreve as principais diferenças entre esses dois serviços e destaca as vantagens de usar o Hub IoT em suas soluções de IoT.

Para saber mais sobre como o Azure e o Hub Iot ajudam a proteger sua solução IoT, veja [Segurança da Internet das Coisas desde o princípio][lnk-security-ground-up].

![Hub IoT do Azure como gateway de nuvem em solução de Internet das coisas][img-architecture]

> [!NOTE]
> Para ver uma análise detalhada da arquitetura IoT, consulte a [Arquitetura de Referência do IoT do Microsoft Azure][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>Desafios de conectividade do dispositivo IoT

O Hub IoT e as bibliotecas de dispositivo ajudam a enfrentar os desafios de como conectar dispositivos de maneira confiável e segura ao back-end da solução. Dispositivos IoT:

* Com frequência, são sistemas internos sem operadores humanos.
* Podem estar em locais remotos, onde o acesso físico é caro.
* Só podem ser acessados por meio do back-end da solução.
* Podem ter recursos de energia e de processamento limitados.
* Podem ter conectividade de rede intermitente, lenta ou cara.
* Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
* Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante é de implementação difícil e demorada quando você usa tecnologias tradicionais, como contêineres da Web e agentes de mensagens.

## <a name="why-use-azure-iot-hub"></a>Por que usar o Hub IoT do Azure?

O Hub IoT do Azure oferece um conjunto avançado de opções de comunicação do [dispositivo para nuvem][lnk-d2c-guidance] e da [nuvem para o dispositivo][lnk-c2d-guidance]. Além disso, o Hub IoT do Azure resolve das seguintes formas os desafios que acompanham a conexão confiável e segura aos dispositivos:

* **Gêmeos de dispositivo**. Usando os [Gêmeos de dispositivo][lnk-twins] você pode armazenar, sincronizar e consultar informações de metadados e o estado do dispositivo. Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo, como metadados, configurações e condições. O Hub IoT mantém um dispositivo gêmeo para cada dispositivo que você conecta ao Hub IoT.

* **Autenticação por dispositivo e conectividade segura**. Você pode provisionar cada dispositivo com sua própria [chave de segurança][lnk-devguide-security] para habilitá-lo a se conectar ao Hub IoT. O [Registro de identidades do Hub IoT][lnk-devguide-identityregistry] armazena as identidades e as chaves dos dispositivos em uma solução. Um back-end da solução pode adicionar dispositivos individuais para permitir ou negar listas para habilitar o controle completo sobre o acesso ao dispositivo.

* **Rotear mensagens de dispositivo para a nuvem para serviços do Azure com base em regras declarativas**. O Hub IoT permite que você defina rotas de mensagens com base em regras de roteamento para controlar para onde o hub envia mensagens de dispositivo para a nuvem. As regras de roteamento não exigem que você escreva nenhum código e podem tomar o lugar de distribuidores de mensagens pós-ingestão personalizados.

* **Monitoramento das operações de conectividade do dispositivo**. Você pode receber os logs de operação detalhados sobre operações de gerenciamento de identidade do dispositivo e eventos de conectividade do dispositivo. Esse recurso de monitoramento permite que sua solução de IoT identifique problemas de conectividade. Use esses logs para identificar os dispositivos que fornecem credenciais incorretas, enviam mensagens com muita frequência ou rejeitam todas as mensagens da nuvem para o dispositivo.

* **Um amplo conjunto de bibliotecas de dispositivos**. Os [SDKs do dispositivo IoT do Azure][lnk-device-sdks] estão disponíveis e têm suporte para várias linguagens e plataformas: C para muitas distribuições Linux, Windows e sistemas operacionais em tempo real. Os SDKs de dispositivo IoT do Azure também oferecem suporte a linguagens gerenciadas, como C#, Java e JavaScript.

* **Protocolos e extensibilidade do IoT**. Se sua solução não puder usar as bibliotecas de dispositivos, o Hub IoT exporá um protocolo público para permitir que os dispositivos usem os protocolos MQTT v3.1.1, HTTPS 1.1 ou AMQP 1.0 nativamente. Você também pode estender o Hub IoT para dar suporte a protocolos personalizados ao:

  * Criar um gateway de campo com o [ Edge IoT do Azure][lnk-iot-edge] que converte seu protocolo personalizado em um dos três protocolos compreendidos pelo Hub IoT.
  * Personalização do [gateway de protocolo IoT do Azure][protocol-gateway], um componente de software livre que é executado na nuvem.

* **Escala**. O Hub IoT do Azure pode ser dimensionado para milhões de dispositivos conectados simultaneamente, além de milhões de eventos por segundo.

## <a name="gateways"></a>Gateways

Um gateway em uma solução IoT geralmente é um [gateway de protocolo][lnk-iotedge] implantado na nuvem, ou um [gateway de campo][lnk-field-gateway] implantado localmente com seus dispositivos. Um gateway de protocolo executa a conversão do protocolo, por exemplo, de MQTT para AMQP. Um gateway de campo pode executar análises na borda, tomar decisões baseadas em tempo para reduzir a latência, fornecer serviços de gerenciamento de dispositivo, impor restrições de privacidade e segurança e também executar a conversão de protocolo. Os dois tipos de gateway atuam como intermediários entre os seus dispositivos e o Hub IoT.

Um gateway de campo é diferente de um dispositivo de roteamento de tráfego simples (como um dispositivo conversão de endereços de rede ou firewall), pois geralmente ele executa uma função ativa no gerenciamento de acesso e no fluxo de informações da solução.

A mesma solução pode incluir gateways de protocolo e de campo.

## <a name="how-does-iot-hub-work"></a>Como funciona o Hub IoT?

O Hub IoT do Azure implementa o padrão [comunicação assistida de serviço][lnk-service-assisted-pattern] para intermediar as interações entre os dispositivos e o back-end da solução. A meta da comunicação assistida de serviço é estabelecer caminhos de comunicação bidirecional e confiável entre um sistema de controle, como o Hub IoT, e dispositivos de finalidade especial em um espaço físico não confiável. O padrão estabelece os seguintes princípios:

* A segurança tem precedência sobre todos os outros recursos.

* Os dispositivos não aceitam informações de rede não solicitadas. Um dispositivo estabelece todas as conexões e rotas de forma apenas de saída. Para que um dispositivo receba um comando da solução de back-end, ele deverá iniciar regularmente uma conexão para verificar a existência de qualquer comando pendente a ser processado.

* Os dispositivos devem apenas se conectar a rotas ou estabelecê-las para serviços conhecidos com os quais eles estejam emparelhados, como o Hub IoT.

* O caminho de comunicação entre o dispositivo e o serviço ou entre o dispositivo e o gateway é protegido na camada de protocolo do aplicativo.

* A autenticação e a autorização no nível de sistema são baseadas nas identidades por dispositivo. Elas tornam as permissões e credenciais de acesso quase instantaneamente revogáveis.

* Para dispositivos que se conectam esporadicamente devido a problemas com energia ou conectividade, a comunicação bidirecional funciona por meio da retenção de comandos e de notificações até que um dispositivo se conecte para recebê-las. O Hub IoT mantém filas específicas de dispositivo para os comandos enviados.

* Os dados de carga do aplicativo são protegidos separadamente para proteger o tráfego que passa pelos gateways até um serviço específico.

O setor de dispositivos móveis tem usado o padrão de comunicação assistida de serviço para implementar os serviços de notificação por push, como os [Serviços de Notificação por Push do Windows][lnk-wns], o [Google Cloud Messaging][lnk-google-messaging] e o [Apple Push Notification Service][lnk-apple-push].

O Hub IoT tem suporte no caminho público de emparelhamento do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

Para saber como enviar mensagens de um dispositivo e recebê-las do Hub IoT, bem como configurar rotas de mensagens, confira [Enviar e receber mensagens com o Hub IoT][lnk-send-messages].

Para saber como o Hub IoT permite o gerenciamento de dispositivos baseado em padrões para gerenciar, configurar e atualizar os dispositivos remotamente, consulte [Visão geral do gerenciamento de dispositivos com o Hub IoT][lnk-device-management].

Para implementar aplicativos cliente em uma grande variedade de sistemas operacionais e plataformas de hardware do dispositivo, você pode usar os SDKs do dispositivo IoT do Azure. Os SDKs do dispositivo incluem bibliotecas que facilitam o envio de telemetria para um hub IoT e o recebimento de mensagens da nuvem para o dispositivo. Ao usar os SDKs de dispositivo, você poderá escolher entre vários protocolos de rede para comunicar-se com o Hub IoT. Para saber mais, confira as [informações sobre SDKs de dispositivo][lnk-device-sdks].

Para começar a escrever código e executar alguns exemplos, confira o tutorial [Introdução ao Hub IoT][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicação Assistida de Serviço, postagem de blog feita por Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md

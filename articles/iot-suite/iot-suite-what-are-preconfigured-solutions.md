---
title: "Soluções pré-configuradas do Azure IoT | Microsoft Docs"
description: "Uma descrição das soluções pré-configuradas IoT Azure e sua arquitetura com links para recursos adicionais."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: e0e79cb3b4c71c5d424f3b46af72fcb8b2f63ead
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quais são as soluções pré-configuradas do Pacote do Azure IoT?

As soluções pré-configuradas do Azure IoT Suite são implementações de padrões de solução IoT comuns que você pode implantar no Azure usando a sua assinatura. Você pode usar as soluções pré-configuradas:

* Como ponto de partida para suas próprias soluções de IoT.
* Para saber mais sobre os padrões comuns no desenvolvimento e no design da solução de IoT.

Cada solução pré-configurada é uma implementação completa de ponta a ponta que usa dispositivos simulados para gerar telemetria.

Você pode baixar o código-fonte completo para personalizar e estender a solução a fim de atender às suas necessidades específicas de IoT.

> [!NOTE]
> Para implantar uma das soluções pré-configuradas, acesse o [Microsoft Azure IoT Suite][lnk-azureiotsuite]. O artigo [Introdução às soluções IoT pré-configuradas][lnk-getstarted-preconfigured] fornece mais informações sobre como implantar e executar uma das soluções.

A tabela a seguir mostra como essas soluções são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Gerenciamento de dispositivos | Comando e controle | Regras e ações | Análise preditiva |
| --- | --- | --- | --- | --- | --- | --- |
| [Monitoramento remoto][lnk-getstarted-preconfigured] |Sim |Sim |Sim |Sim |Sim |- |
| [Manutenção preditiva][lnk-predictive-maintenance] |Sim |Sim |- |Sim |Sim |Sim |
| [Fábrica conectada][lnk-getstarted-factory] |Sim |Sim |Sim |Sim |sim |- |

* *Ingestão de dados*: entrada de dados em escala para a nuvem.
* *Identidade do dispositivo*: gerenciar identidades exclusivas de dispositivo e controlar o acesso do dispositivo à solução.
* *Gerenciamento de dispositivo*: gerenciar metadados de dispositivo e executar operações como atualizações de firmware e reinicializações do dispositivo.
* *Comando e controle*: para fazer o dispositivo realizar uma ação, envie mensagens a um dispositivo pela nuvem.
* *Regras e ações*: para atuar em dados específicos do dispositivo para a nuvem, o back-end da solução usa regras.
* *Análise preditiva*: o back-end da solução analisa dados do dispositivo para a nuvem para prever quando ações específicas devem ocorrer. Por exemplo, analisar a telemetria de motores de aeronave para determinar quando deve ser realizada a manutenção do motor.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Visão geral da solução pré-configurada de Monitoramento Remoto

Optamos por abordar a solução pré-configurada de monitoramento remoto neste artigo porque ela ilustra vários elementos comuns de design compartilhados pelas outras soluções.

O diagrama a seguir ilustra os principais elementos da solução de monitoramento remoto. As seções a seguir fornecem mais informações sobre esses elementos.

![Arquitetura da solução pré-configurada de monitoramento remoto][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Quando você implanta a solução pré-configurada de monitoramento remoto, quatro dispositivos simulados são previamente provisionados na solução que simula um dispositivo de resfriamento. Esses dispositivos simulados têm um modelo interno de temperatura interna e de umidade que emite a telemetria. Esses dispositivos simulados estão incluídos para:

- Ilustrar o fluxo de dados de ponta a ponta através da solução.
- Fornecer uma fonte conveniente de telemetria.
- Fornecer um destino para métodos ou comandos caso você seja um desenvolvedor de back-end usando a solução como um ponto de partida para uma implementação personalizada.

Os dispositivos simulados na solução podem responder às seguintes comunicações do dispositivo para nuvem:

- *Métodos ([métodos diretos][lnk-direct-methods])*: um método de comunicação bidirecional em que se espera que um dispositivo conectado responda imediatamente.
- *Comandos (mensagens da nuvem para o dispositivo)*: um método de comunicação unidirecional em que um dispositivo recupera o comando de uma fila durável.

Para uma comparação dessas diferentes abordagens, consulte [Orientação sobre comunicações da nuvem para o dispositivo][lnk-c2d-guidance].

Quando um dispositivo se conecta pela primeira vez ao Hub IoT na solução pré-configurada, ele envia uma mensagem de informação sobre o dispositivo ao hub. Esta mensagem enumera os métodos aos quais o dispositivo pode responder. Na solução pré-configurada de monitoramento remoto, os dispositivos simulados oferecem suporte a esses métodos:

* *Iniciar atualização do Firmware*: esse método inicia uma tarefa assíncrona no dispositivo para executar uma atualização de firmware. A tarefa assíncrona usa propriedades relatadas para fornecer atualizações de status ao painel da solução.
* *Reinicializar*: esse método faz com que o dispositivo simulado seja reinicializado.
* *FactoryReset*: esse método dispara uma redefinição de fábrica no dispositivo simulado.

Quando um dispositivo se conecta pela primeira vez ao Hub IoT na solução pré-configurada, ele envia uma mensagem de informação sobre o dispositivo ao hub. Esta mensagem enumera os comandos aos quais o dispositivo pode responder. Na solução pré-configurada de monitoramento remoto, os dispositivos simulados oferecem suporte a esses comandos:

* *Executar Ping*: o dispositivo responde a esse comando com uma confirmação. Esse comando é útil para verificar se o dispositivo ainda está ativo e ouvindo.
* *Iniciar Telemetria*: instrui o dispositivo a iniciar o envio de telemetria.
* *Parar Telemetria*: instrui o dispositivo a parar o envio de telemetria.
* *Alterar Ponto de Definição da Temperatura*: controla os valores de telemetria de temperatura simulados enviados pelo dispositivo. Esse comando é útil para testar a lógica de back-end.
* *Diagnosticar Telemetria*: controla se o dispositivo deve enviar a temperatura externa como telemetria.
* *Alterar Estado do Dispositivo*: define a propriedade de metadados do estado do dispositivo relatado pelo dispositivo. Esse comando é útil para testar a lógica de back-end.

Você pode adicionar mais dispositivos simulados à solução que emite a mesma telemetria e responde aos mesmos métodos e comandos.

Além de responder a comandos e métodos, a solução usa [dispositivos gêmeos][lnk-device-twin]. Os dispositivos usam os dispositivos gêmeos para relatar valores de propriedade para o back-end da solução. O painel da solução usa dispositivos gêmeos para definir como novos os valores de propriedade desejados nos dispositivos. Por exemplo, durante o processo de atualização do firmware o dispositivo simulado relata o status da atualização usando as propriedades relatadas.

## <a name="iot-hub"></a>Hub IoT

Nesta solução pré-configurada, a instância do Hub IoT corresponde ao *Gateway de Nuvem* em uma [arquitetura da solução de IoT][lnk-what-is-azure-iot] típica.

Um Hub IoT recebe telemetria dos dispositivos em um único ponto de extremidade. Um hub IoT também mantém os pontos de extremidade específicos do dispositivo onde cada dispositivo pode recuperar os comandos enviados a ele.

O Hub IoT disponibiliza a telemetria recebida por meio do ponto de extremidade de leitura de telemetria no lado do serviço.

A funcionalidade de gerenciamento de dispositivo do Hub IoT permite gerenciar as propriedades do dispositivo no portal da solução e agendar trabalhos que executam operações tais como:

- Reinicialização de dispositivos
- Alterar estados do dispositivo
- Atualizações de firmware

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

A solução pré-configurada usa três trabalhos do ASA ([Stream Analytics do Azure][lnk-asa]) para filtrar o fluxo de telemetria dos dispositivos:

* *Trabalho DeviceInfo* – envia dados para um Hub de eventos que encaminha mensagens específicas de registro do dispositivo para o registro do dispositivo da solução. Esse registro de dispositivo é um banco de dados do BD Cosmos do Azure. Essas mensagens são enviadas quando um dispositivo se conecta pela primeira vez ou em resposta a um comando **Alterar estado do dispositivo**.
* *Trabalho de telemetria* – envia toda a telemetria bruta para o armazenamento de blobs do Azure para armazenamento frio e calcula as agregações de telemetria exibidas no painel de solução.
* *Trabalho de regras* – filtra o fluxo de telemetria para os valores que excedem os limites de regras e retornam os dados para um Hub de Eventos. Quando uma regra é disparada, a exibição de painel do portal da solução mostra esse evento como uma nova linha na tabela de histórico de alarmes. Essas regras também podem disparar uma ação com base nas configurações definidas nas exibições **Regras** e **Ações** no portal da solução.

Nesta solução pré-configurada, os trabalhos ASA fazem parte do **back-end da solução IoT** em uma [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## <a name="event-processor"></a>Processador de eventos

Nesta solução pré-configurada, o processador de eventos faz parte do **back-end da solução IoT** em uma [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

Os trabalhos ASA **DeviceInfo** e **Rules** enviam suas saídas para os Hubs de Eventos para entrega em outros serviços de back-end. A solução usa uma instância de [EventPocessorHost][lnk-event-processor], em execução em um [WebJob][lnk-web-job], para ler as mensagens desses Hubs de Eventos. O **EventProcessorHost** usa:
- Os dados de **DeviceInfo** para atualizar os dados do dispositivo no banco de dados Cosmos DB.
- Os dados de **Regras** para invocar o aplicativo lógico e atualizar a exibição de alertas no portal da solução.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Registro de identidade do dispositivo, dispositivo gêmeo e Cosmos DB

Cada Hub IoT inclui um [registro de identidade do dispositivo][lnk-identity-registry] que armazena as chaves do dispositivo. O Hub IoT usa esses dispositivos de autenticação de informação – um dispositivo deve ser registrado e ter uma chave válida para poder ser conectado ao Hub.

Um [dispositivo gêmeo][lnk-device-twin] é um documento JSON gerenciado pelo Hub IoT. Um dispositivo gêmeo para um dispositivo contém:

- Propriedades relatadas enviadas pelo dispositivo para o hub. Você pode exibir essas propriedades no portal da solução.
- Propriedades desejadas que você quer enviar para o dispositivo. Você pode definir essas propriedades no portal da solução.
- Marcas que existem apenas no dispositivo gêmeo e não no dispositivo. Você pode usar essas marcas para filtrar listas de dispositivos no portal da solução.

Essa solução usa dispositivos gêmeos para gerenciar metadados do dispositivo. A solução também usa um banco de dados Cosmos DB para armazenar dados adicionais de dispositivo, específicos da solução, como os comandos aos quais cada dispositivo dá suporte e o histórico de comandos.

A solução também deverá manter as informações no registro de identidade do dispositivo sincronizadas com os conteúdos do banco de dados do Cosmos DB. O **EventProcessorHost** usa os dados do trabalho de análise de fluxo de **DeviceInfo** para gerenciar a sincronização.

## <a name="solution-portal"></a>Portal de solução

![portal da solução][img-dashboard]

O portal da solução é uma interface de usuário baseada na Web implantado na nuvem como parte da solução pré-configurada. Ele permite que você:

* Exiba o histórico de telemetria e de alarmes em um painel.
* Provisione novos dispositivos.
* Gerencie e monitore dispositivos.
* Envie comandos para dispositivos específicos.
* Invoque métodos em dispositivos específicos.
* Gerencie regras e ações.
* Agende trabalhos a serem executados em um ou mais dispositivos.

Nesta solução pré-configurada, o portal de solução faz parte do **back-end de solução IoT** e parte da **Conectividade de processamento e de negócios** na [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as arquiteturas de solução de IoT, confira [Serviços de IoT do Microsoft Azure: arquitetura de referência][lnk-refarch].

Agora você sabe o que é uma solução pré-configurada, poderá começar pela implantação da solução pré-configurada de *monitoramento remoto*: [Introdução às soluções pré-configuradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md

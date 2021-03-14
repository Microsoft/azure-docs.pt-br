---
title: Glossário de termos do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor – um glossário explicando alguns dos termos comuns usados nos artigos do Azure IoT.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 3e8a2ac93e9fea6ad045030759be894617557658
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022056"
---
# <a name="glossary-of-iot-terms"></a>Glossário de termos de IoT

Este artigo lista alguns dos termos comuns usados nos artigos de IoT.

## <a name="a"></a>Um

### <a name="advanced-message-queueing-protocol"></a>Procolo AMQP

O [AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) é um dos protocolos de mensagens com suporte do [Hub IoT](#iot-hub) para comunicação com dispositivos. Para saber mais sobre os protocolos de mensagens com suporte do Hub IoT, consulte [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="automatic-device-management"></a>Gerenciamento automático de dispositivo

O gerenciamento automático do dispositivo no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Com o gerenciamento automático do dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo.  Consiste em [configurações de dispositivo automáticas](../iot-hub/iot-hub-automatic-device-management.md) e [implantações automáticas do IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

### <a name="automatic-device-configuration"></a>Configuração de dispositivo automática

O back-end da sua solução pode usar [configurações de dispositivo automáticas](../iot-hub/iot-hub-automatic-device-management.md) para atribuir as propriedades desejadas a um conjunto de [dispositivos gêmeos](#device-twin) e relatar o status usando métricas do sistema e métricas personalizadas. 

### <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure

Há _SDKs de dispositivo_ disponíveis para vários idiomas que permitem que você crie [aplicativos de dispositivo](#device-app) que interagem com um hub IoT. Os tutoriais do Hub IoT mostram como usar esses SDKs de dispositivo. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs de dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-explorer"></a>Gerenciador de IoT do Azure

O [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer) é usado para exibir a telemetria que o dispositivo está enviando, trabalhar com propriedades de dispositivo e chamar comandos. Você também pode usar o Explorer para interagir e testar seus dispositivos e para gerenciar dispositivos de [plug and Play de IOT](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure

Há _SDKs de serviço_ disponíveis para vários idiomas que permitem que você crie [aplicativos de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais do Hub IoT mostram como usar esses SDKs de serviço. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-tools"></a>Ferramentas do Azure IoT

As [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão multiplataforma do Visual Studio Code, o código-fonte aberto que ajuda você a gerenciar dispositivos no VS Code e o Hub IoT do Azure. Com as Ferramentas do Azure IoT, os desenvolvedores de IoT podem desenvolver o projeto de IoT no VS Code com facilidade.

## <a name="b"></a>B

### <a name="back-end-app"></a>Aplicativo de back-end

No contexto do [Hub IoT](#iot-hub), um aplicativo de back-end é um aplicativo que se conecta a um dos pontos de extremidade voltados para o serviço em um hub IoT. Por exemplo, um aplicativo de back-end pode recuperar mensagens do [dispositivo para nuvem](#device-to-cloud) ou gerenciar o [registro de identidade](#identity-registry). Normalmente, um aplicativo de back-end é executado na nuvem, mas em muitos dos tutoriais dos aplicativos de back-end há aplicativos de console em execução no seu computador de desenvolvimento local.

### <a name="built-in-endpoints"></a>Pontos de extremidade internos

Cada hub IoT inclui um [ponto de extremidade](../iot-hub/iot-hub-devguide-endpoints.md) interno que é compatível com o Hub de Eventos. Você pode usar qualquer mecanismo que funciona com os Hubs de Eventos para ler mensagens de dispositivo para nuvem desse ponto de extremidade.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Gateway de nuvem

Um gateway de nuvem permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Um gateway de nuvem é hospedado na nuvem em comparação a um [gateway de campo](#field-gateway) executado no local para seus dispositivos. Um caso de uso típico para um gateway de nuvem é implementar a conversão de protocolo para seus dispositivos.

### <a name="cloud-to-device"></a>Nuvem para o dispositivo

Refere-se às mensagens enviadas de um hub IoT para um dispositivo conectado. Geralmente, essas mensagens são comandos que instruem o dispositivo a executar uma ação. Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Comandos

No Plug and Play IoT, os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [digital](#digital-twin). Por exemplo, um comando para reinicializar um dispositivo.

### <a name="component"></a>Componente

No Plug and Play de IoT, os componentes permitem criar uma [interface](#interface) de modelo como um assembly de outras interfaces. Um [modelo de dispositivo](#device-model) pode combinar várias interfaces como componentes. Por exemplo, um modelo pode incluir um componente de switch e um componente termostato. Vários componentes em um modelo também podem usar o mesmo tipo de interface. Por exemplo, um modelo pode incluir dois componentes termostato.

### <a name="configuration"></a>Configuração

No contexto da [configuração de dispositivo automática](../iot-hub/iot-hub-automatic-device-management.md), uma configuração no Hub IoT define a configuração desejada para um conjunto de dispositivos gêmeos e fornece um conjunto de métricas para relatar o status e o progresso.

### <a name="connection-string"></a>Cadeia de conexão

Você pode usar cadeias de conexão no código do aplicativo para encapsular as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço das informações de segurança e de ponto de extremidade, mas os formatos da cadeia de conexão variam de acordo com os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- *Cadeias de conexão do dispositivo* permitem que os dispositivos se conectem aos pontos de extremidade voltados para o dispositivo em um Hub IoT.

- *Cadeias de conexão do Hub IoT* permitem que os aplicativos de back-end se conectem aos pontos de extremidade voltados para o serviço em um Hub IoT.

### <a name="custom-endpoints"></a>Pontos de extremidade personalizados

Você pode criar [pontos de extremidade](../iot-hub/iot-hub-devguide-endpoints.md) em um hub IoT entregar as mensagens enviadas por uma [regra de roteamento](#routing-rules). Pontos de extremidade personalizados se conectar diretamente a um hub de eventos, uma fila do barramento de serviço ou um tópico do barramento de serviço.

### <a name="custom-gateway"></a>Gateway personalizado

Um gateway permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Você pode usar o Azure IoT Edge para criar gateways personalizados que implementam a lógica personalizada para lidar com mensagens, conversões de protocolo personalizado e outros processamentos no Edge.

## <a name="d"></a>D

### <a name="data-point-message"></a>Mensagem de ponto de dados

Uma mensagem de ponto de dados é uma mensagem do [dispositivo para nuvem](#device-to-cloud) que contém dados de [telemetria](#telemetry), como velocidade do vento ou temperatura.

### <a name="default-component"></a>Componente padrão

No Plug and Play IoT, todos os [modelos de dispositivo](#device-model) têm um componente padrão. Um modelo de dispositivo simples tem apenas um componente padrão-tal modelo também é conhecido como um dispositivo sem componente. Um modelo mais complexo tem vários componentes aninhados sob o componente padrão.

### <a name="device-certification"></a>Certificado de dispositivo

O programa de certificação de dispositivo do IoT Plug and Play verifica se um dispositivo atende aos requisitos de certificação do IoT Plug and Play. Você pode adicionar um dispositivo certificado ao [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/devicecatalog).

### <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo usa a [linguagem de definição de gêmeos digital](#digital-twins-definition-language) para descrever os recursos de um dispositivo de plug and Play de IOT. Um modelo de dispositivo simples usa uma única interface para descrever os recursos do dispositivo. Um modelo de dispositivo mais complexo inclui vários componentes, cada um deles descreve um conjunto de recursos. Para saber mais, confira [componentes de IoT plug and Play em modelos](../iot-pnp/concepts-components.md).

### <a name="device-builder"></a>Construtor de dispositivos

Um construtor de dispositivos usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os criadores de dispositivos normalmente usam um dos [SDKs do dispositivo IOT do Azure](#azure-iot-device-sdks) para implementar o cliente do dispositivo.

### <a name="device-modeling"></a>Modelagem de dispositivo

Um [Construtor de dispositivos](#device-builder) ou [Construtor de módulos](#module-builder)usa a linguagem de definição de [gêmeos digital](#digital-twins-definition-language) para modelar os recursos de um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device). Um [Solution Builder](#solution-builder) pode configurar uma solução de IOT do modelo.

### <a name="desired-configuration"></a>Configuração desejada

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), a configuração desejada refere-se ao conjunto completo de propriedades e de metadados no dispositivo gêmeo que deve ser sincronizado com o dispositivo.

### <a name="desired-properties"></a>Propriedades desejadas

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), as propriedades desejadas compõem uma subseção do dispositivo gêmeo usado com as [porpriedades relatadas](#reported-properties) para sincronizar a configuração ou a condição do dispositivo. As propriedades desejadas só podem ser definidas por um [aplicativo de back-end](#back-end-app) e são observadas pelo [aplicativo de dispositivo](#device-app).

### <a name="device-to-cloud"></a>Dispositivo para nuvem

Refere-se às mensagens enviadas de um dispositivo conectado ao [Hub IoT](#iot-hub). Essas mensagens podem ser do tipo [ponto de dados](#data-point-message) ou [interativas](#interactive-message). Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="device"></a>Dispositivo

No contexto do IoT, um dispositivo é normalmente um dispositivo de computação autônomo de pequena escala que pode coletar dados ou controlar outros dispositivos. Um dispositivo pode ser, por exemplo, um dispositivo de monitoramento ambiental ou um controlador de sistemas de rega e ventilação em uma estufa. O [catálogo de dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para o trabalho com o [Hub IoT](#iot-hub).

### <a name="device-app"></a>Aplicativo do dispositivo

Um aplicativo de dispositivo é executado em seu [dispositivo](#device) e gerencia a comunicação com o [hub IoT](#iot-hub). Normalmente, você usa um dos [SDKs de dispositivo IoT do Azure](#azure-iot-device-sdks) ao implementar um aplicativo de dispositivo. Em muitos dos tutoriais de IoT, você deve usar um [dispositivo simulado](#simulated-device) para sua conveniência.

### <a name="device-condition"></a>Condição do dispositivo

Refere-se às informações de estado do dispositivo, como o método de conectividade em uso no momento, conforme relatado por um [aplicativo de dispositivo](#device-app). Os [aplicativos de dispositivo](#device-app) também podem relatar suas funcionalidades. Você pode consultar informações de condição e de funcionalidade usando dispositivos gêmeos.

### <a name="device-data"></a>Dados do dispositivo

Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registro de identidade](#identity-registry) do Hub IoT. É possível importar e exportar esses dados.

### <a name="device-identity"></a>Identidade do dispositivo

A identidade do dispositivo é o identificador exclusivo atribuído a cada dispositivo registrado no [registro de identidade](#identity-registry).

### <a name="device-management"></a>Gerenciamento de dispositivo

O gerenciamento de dispositivos engloba o ciclo de vida completo associado ao gerenciamento dos dispositivos na sua solução de IoT incluindo planejamento, provisionamento, configuração, monitoramento e desativação.

### <a name="device-management-patterns"></a>Padrões de gerenciamento de dispositivos

O [hub IoT](#iot-hub) permite que os padrões comuns de gerenciamento de dispositivos, incluindo reinicialização, execução de redefinições de fábrica e execução de atualizações de firmware nos seus dispositivos.

### <a name="device-rest-api"></a>APIs REST do dispositivo

Você pode usar a [API REST de Dispositivo](/rest/api/iothub/device) de um dispositivo para enviar mensagens de dispositivo para a nuvem a um hub IoT e receber mensagens da [nuvem para o dispositivo](#cloud-to-device) de um hub IoT. Normalmente, você deve usar um dos [SDKs de dispositivo](#azure-iot-device-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

### <a name="device-provisioning"></a>Provisionamento de dispositivos

O provisionamento de dispositivos é o processo de adicionar os dados iniciais do [dispositivo](#device-data) às lojas em sua solução. Para permitir que um novo dispositivo se conecte ao seu hub, você deve adicionar uma ID de dispositivo e chaves ao [registro de identidade](#identity-registry)do Hub IOT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução.

### <a name="device-twin"></a>Dispositivo gêmeo

Um dispositivo gêmeo é um documento JSON que armazena informações de estado do dispositivo, como metadados, configurações e condições. O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você provisiona no hub IoT. Os dispositivos gêmeos permitem sincronizar condições de dispositivo e configurações entre o dispositivo e o back-end da solução. Você pode consultar dispositivos gêmeos para localizar dispositivos específicos e o status de operações de longa execução.

### <a name="direct-method"></a>Método direto

Um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) é uma maneira de disparar um método a fim de executá-lo em um dispositivo chamando uma API em seu hub IoT.

### <a name="digital-twin"></a>Gêmeo digital

Um FileUp digital é uma coleção de dados digitais que representa um objeto físico. As alterações no objeto físico são refletidas no digital. Em alguns cenários, você pode usar a pesquisa digital para manipular o objeto físico. O [serviço de gêmeos digital do Azure](../digital-twins/index.yml) usa modelos expressos na [linguagem de definição de gêmeos digital](#digital-twins-definition-language) para habilitar uma ampla gama de soluções baseadas em nuvem que usam o gêmeos digital. Um dispositivo de [plug and Play de IOT](../iot-pnp/index.yml) tem uma DTDL digital, descrita por um [modelo de dispositivo](#device-model).

### <a name="digital-twin-change-events"></a>Eventos de alteração de gêmeo digital

Quando um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) é conectado a um hub IOT, o Hub pode usar sua capacidade de roteamento para enviar notificações de alterações de conexão digital. Por exemplo, sempre que um valor de [Propriedade](#properties) é alterado em um dispositivo, o Hub IOT pode enviar uma notificação para um ponto de extremidade, como um hub de eventos.

### <a name="digital-twins-definition-language"></a>Linguagem de definição de gêmeos digital

Uma linguagem para descrever modelos e interfaces para [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Use a [linguagem de definição de gêmeos digital versão 2](https://github.com/Azure/opendigitaltwins-dtdl) para descrever os recursos de uma [teledigital](#digital-twin) e habilitar a plataforma IOT e as soluções de IOT para usar a semântica da entidade.

### <a name="digital-twin-route"></a>Rota do gêmeo digital

Uma rota configurada em um hub IoT para entregar [eventos de alteração de troca digital](#digital-twin-change-events) e ponto de extremidade, como um hub de eventos.

## <a name="e"></a>E

### <a name="endpoint"></a>Ponto de extremidade

Um hub IoT expõe vários [pontos de extremidade](../iot-hub/iot-hub-devguide-endpoints.md) que permitem que seus aplicativos se conectem ao hub IoT. Há pontos de extremidade voltados para dispositivos que permitem que dispositivos executem operações como o envio de mensagens do [dispositivo para a nuvem](#device-to-cloud) e o recebimento de mensagens da [nuvem para o dispositivo](#cloud-to-device). Há pontos de extremidade de gerenciamento de voltados para serviço que permitem que [aplicativos de back-end](#back-end-app) executem operações como o gerenciamento de [identidade de dispositivo](#device-identity) e o gerenciamento de dispositivos gêmeos. Há voltado para o serviço [pontos de extremidade internos](#built-in-endpoints) para ler mensagens de dispositivo para a nuvem. Você pode criar [pontos de extremidade personalizados](#custom-endpoints) para receber mensagens de dispositivo para nuvem enviadas por uma [regra de roteamento](#routing-rules).

### <a name="event-hub-compatible-endpoint"></a>Ponto de extremidade compatível com o Hub de Eventos

Para ler as mensagens do [dispositivo para a nuvem](#device-to-cloud) enviadas ao seu hub IOT, você pode se conectar a um ponto de extremidade em seu hub e usar qualquer método compatível com o Hub de eventos para ler essas mensagens. Entre os métodos compatíveis com o Hub de Eventos estão o uso de [SDKs dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md) e o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md).

## <a name="f"></a>F

### <a name="field-gateway"></a>Gateway de campo

Um gateway de campo permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub) e normalmente é implantado localmente com seus dispositivos. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Um gateway permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Veja também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway) e [Gateway personalizado](#custom-gateway).

## <a name="i"></a>I

### <a name="identity-registry"></a>Registro de identidade

O [registro de identidade](../iot-hub/iot-hub-devguide-identity-registry.md) é o componente interno de um hub IoT que armazena informações sobre os dispositivos individuais que têm permissão para se conectar a um hub IoT.

### <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem da [nuvem para o dispositivo](#cloud-to-device) que dispara uma ação imediata no back-end da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser registrada automaticamente em um sistema de CRM.

### <a name="interface"></a>Interface

No Plug and Play de IoT, uma interface descreve os recursos relacionados que são implementados por um [dispositivo IoT plug and Play](#iot-plug-and-play-device) ou em uma [teledigital](#digital-twin). Você pode reutilizar interfaces em diferentes [modelos de dispositivo](#device-model). Quando uma interface é usada em um modelo de dispositivo, ela define um [componente](#component) do dispositivo. Um dispositivo simples contém apenas uma interface padrão.

### <a name="iot-edge"></a>IoT Edge

O Azure IoT Edge permite a implantação orientada a nuvem do código específico da solução e de serviços do Azure para dispositivos locais. [IOT Edge dispositivos](#iot-edge-device) podem agregar dados de outros dispositivos para executar a computação e análise antes de enviar os dados para a nuvem. Para saber mais, consulte [Azure IOT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agente do IoT Edge

A parte do runtime do IoT Edge responsável por implantar e monitorar módulos.

### <a name="iot-edge-device"></a>Dispositivo do IoT Edge

Um dispositivo IoT Edge usa [módulos IOT Edge](#iot-edge-module) em contêineres para executar serviços do Azure, serviços de terceiros ou seu próprio código. No dispositivo IoT Edge, o [tempo de execução do IOT Edge](#iot-edge-runtime) gerencia os módulos. Você pode monitorar e gerenciar remotamente um dispositivo IoT Edge da nuvem.

### <a name="iot-edge-automatic-deployment"></a>Implantação automática do IoT Edge

Uma implantação automática do IoT Edge configura um conjunto de destino de dispositivos IoT Edge para executar um conjunto de módulos do IoT Edge. Cada implantação continuamente garante que todos os dispositivos que correspondem à sua condição de destino estejam executando o conjunto de módulos especificado, mesmo quando novos dispositivos são criados ou modificados para corresponder à condição de destino. Cada dispositivo IoT Edge recebe apenas a implantação de prioridade mais alta cuja condição de destino ele atende. Saiba mais sobre [implantação automática do IoT Edge](../iot-edge/module-deployment-monitoring.md).

### <a name="iot-edge-deployment-manifest"></a>Manifesto de implantação do IoT Edge

Um documento Json contendo as informações a serem copiadas em um ou mais módulos de dispositivos da IoT Edge para implantar um conjunto de módulos, rotas e propriedades desejadas do módulo associado.

### <a name="iot-edge-gateway-device"></a>Dispositivo de gateway IoT Edge

Um dispositivo IoT Edge com dispositivo downstream. O dispositivo downstream pode ser um dispositivo IoT Edge ou não.

### <a name="iot-edge-hub"></a>Hub do IoT Edge

A parte do runtime do IoT Edge responsável pelas comunicações de módulo para módulo e comunicações upstream (para o Hub IoT) e downstream (para fora do Hub IoT).

### <a name="iot-edge-leaf-device"></a>Dispositivo de folha IoT Edge

Um dispositivo IoT Edge sem nenhum dispositivo downstream.

### <a name="iot-edge-module"></a>Módulo do IoT Edge

Um módulo do IoT Edge é um contêiner do Docker que você pode implantar para dispositivos IoT Edge. Ele executa uma tarefa específica, como a ingestão de uma mensagem de um dispositivo, a transformação de uma mensagem ou o envio de uma mensagem para um Hub IoT. Ele se comunica com outros módulos e envia dados para o runtime do IoT Edge. [Entenda os requisitos e as ferramentas para o desenvolvimento de módulos IOT Edge](../iot-edge/module-development.md).

### <a name="iot-edge-module-identity"></a>Identidade do módulo do IoT Edge

Um registro no Registro de identidade do módulo do Hub IoT detalhando as credenciais de segurança e existência a serem usadas por um módulo para a autenticação com um hub de borda ou Hub IoT.

### <a name="iot-edge-module-image"></a>Imagem do módulo do IoT Edge

A imagem do docker que é usada pelo runtime do IoT Edge para instanciar as instâncias do módulo.

### <a name="iot-edge-module-twin"></a>Módulo gêmeo do IoT Edge

Um documento Json persistido no Hub IoT que armazena as informações de estado para uma instância de módulo.

### <a name="iot-edge-priority"></a>Prioridade do IoT Edge

Quando duas implantações do IoT Edge visam o mesmo dispositivo, a implantação com a prioridade mais alta é aplicada. Se duas implantações tiverem a mesma prioridade, a implantação com a criação mais recente será aplicada. Saiba mais sobre a [prioridade](../iot-edge/module-deployment-monitoring.md#priority).

### <a name="iot-edge-runtime"></a>runtime do IoT Edge

O runtime do IoT Edge inclui tudo que a Microsoft distribui para ser instalado em um dispositivo IoT Edge. Inclui o agente Edge, o hub Edge e o daemon de segurança IoT Edge.

### <a name="iot-edge-set-modules-to-a-single-device"></a>Módulos de definição do IoT Edge para um único dispositivo

Uma operação que copia o conteúdo de um manifesto IoT Edge em um módulo de dispositivo. A API subjacente é uma configuração de aplicação genérica, que simplesmente usa um manifesto IoT Edge como uma entrada.

### <a name="iot-edge-target-condition"></a>Condição de destino do IoT Edge

Em uma implantação de IoT Edge, a condição de destino é qualquer condição booliana nas marcas do dispositivo gêmeos para selecionar os dispositivos de destino da implantação, por exemplo, **tag. Environment = prod**. A condição de destino é avaliada continuamente para incluir novos dispositivos que atendam aos requisitos ou remova os dispositivos que não fazem mais. Saiba mais sobre a [condição de destino](../iot-edge/module-deployment-monitoring.md#target-condition)

### <a name="iot-hub"></a>Hub IoT

O Hub IoT é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md). Usando a assinatura do Azure, você pode criar hubs IoT para lidar com suas cargas de trabalho de mensagens IoT.

### <a name="iot-hub-metrics"></a>Métricas do Hub IoT

As métricas do Hub IoT fornecem dados sobre o estado dos hubs IoT em sua assinatura do Azure. As métricas do Hub IoT permitem avaliar a integridade geral do serviço e dos dispositivos conectados a ele. As métricas do Hub IoT podem ajudar você a ver o que está acontecendo com o hub IoT e a investigar a causa raiz de problemas sem precisar entrar em contato com o suporte do Azure. Para saber mais, confira [Monitorar o Hub IoT](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) é uma linguagem semelhante a SQL que permite consultar seu [trabalho](#job), digital gêmeos e dispositivo gêmeos.

### <a name="iot-hub-resource-rest-api"></a>API REST de Recursos do Hub IoT

Você pode usar a [API REST dos recursos do Hub IoT](/rest/api/iothub/iothubresource) para gerenciar os hubs IoT em sua assinatura do Azure que executam operações como criar, atualizar e excluir hubs.

### <a name="iot-solution-accelerators"></a>Aceleradores de solução do IoT

Os aceleradores de solução do Azure IoT incluem vários serviços do Azure para oferecer diferentes soluções. Essas soluções permitem que você realize rapidamente implementações de ponta a ponta de cenários comuns de IoT. Para obter mais informações, consulte [O que são os aceleradores de solução do Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

### <a name="the-iot-extension-for-azure-cli"></a>A extensão IoT para o CLI do Azure 

[A extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando de plataforma cruzada. A ferramenta permite que você gerencie seus dispositivos no [registro de identidade](#identity-registry), envie e receba mensagens e arquivos de seus dispositivos e monitore as operações do hub IoT.

### <a name="iot-plug-and-play-bridge"></a>Ponte do IoT Plug and Play

O IoT Plug and Play Bridge é um aplicativo de software livre que permite que os sensores e periféricos existentes conectados aos gateways do Windows ou do Linux se conectem como [dispositivos de IoT plug and Play](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo de Plug and Play IoT normalmente é um dispositivo de computação independente de pequena escala que coleta dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de dispositivo](#device-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação de agricultura inteligente. Um dispositivo IoT Plug and Play pode ser implementado diretamente ou como um módulo IoT Edge. É possível escrever uma solução de IoT hospedada na nuvem para comandar, controlar e receber dados de dispositivos IoT Plug and Play.

### <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os [dispositivos](#iot-plug-and-play-device) de plug and Play IOT devem seguir um conjunto de convenções quando trocam dados com uma solução.

## <a name="j"></a>J

### <a name="job"></a>Trabalho

O back-end da solução pode usar [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) para agendar e acompanhar atividades em um conjunto de dispositivos registrados com o Hub IOT. As atividades incluem a atualização de [propriedades desejadas](#desired-properties)do dispositivo, atualizando as [marcas](#tags)do dispositivo e invocando [métodos diretos](#direct-method). O [Hub IoT](#iot-hub) também usa  para [importar e exportar](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do [registro de identidade](#identity-registry).

## <a name="m"></a>M

### <a name="model-id"></a>ID do Modelo

Quando um dispositivo de Plug and Play IoT se conecta a um hub IoT, ele envia a **ID de modelo** do modelo [DTDL](#digital-twins-definition-language) que ele implementa. Essa ID permite que a solução Localize o modelo do dispositivo.

### <a name="model-repository"></a>Repositório de modelos

Um repositório de modelos armazena modelos e [interfaces](#interface)de [dispositivo](#device-model) .

### <a name="model-repository-rest-api"></a>API REST do repositório de modelos

Uma API para gerenciar e interagir com o repositório de modelos. Por exemplo, você pode usar a API para adicionar e Pesquisar [modelos de dispositivo](#device-model).

### <a name="module-builder"></a>Construtor de módulo

Um construtor de módulo usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os construtores de módulo implementam o código como um módulo ou um módulo IoT Edge para implantar no tempo de execução de IoT Edge em um dispositivo.

### <a name="modules"></a>Módulos

No lado do dispositivo, os SDKs do dispositivo do Hub IoT permitem criar [módulos](../iot-hub/iot-hub-devguide-module-twins.md) em que cada um abre uma conexão independente com o Hub IoT. Essa funcionalidade permite usar namespaces separados para diferentes componentes em seu dispositivo.

A identidade do módulo e o módulo gêmeo fornecem os mesmos recursos que a [identidade do dispositivo](#device-identity) e o [dispositivo gêmeo](#device-twin), mas com granularidade mais precisa. Essa granularidade mais fina permite que dispositivos capazes, como dispositivos baseados em sistema operacional ou dispositivos de firmware que gerenciam vários componentes, isolem a configuração e as condições de cada um desses componentes.

### <a name="module-identity"></a>Identidade de módulo

A identidade do módulo é o identificador exclusivo atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também é registrada no [registro de identidade](#identity-registry).

### <a name="module-twin"></a>Módulo Gêmeo

Semelhante a um dispositivo gêmeo, um módulo gêmeo é um documento JSON que armazena informações de estado do módulo, como metadados, configurações e condições. O Hub IoT persiste um módulo gêmeo para cada identidade de módulo que você provisionar com uma identidade do dispositivo no seu Hub IoT. Os módulos gêmeos permitem sincronizar condições de módulo e configurações entre o módulo e o back-end da solução. Você pode consultar módulos gêmeos para localizar módulos específicos e consultar o status de operações de longa duração.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) é um dos protocolos de mensagens que o [Hub IOT](#iot-hub) dá suporte para comunicação com dispositivos. Para saber mais sobre os protocolos de mensagens com suporte do Hub IoT, consulte [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Monitoramento de operações

O [monitoramento de operações](../iot-hub/iot-hub-operations-monitoring.md) do Hub IOT permite monitorar o status das operações no Hub IOT em tempo real. O [Hub IOT](#iot-hub) rastreia eventos em várias categorias de operações. Você pode aceitar o envio de eventos de uma ou mais categorias para um ponto de extremidade de Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

## <a name="p"></a>P

### <a name="physical-device"></a>Dispositivo físico

Um dispositivo físico é um dispositivo real como um Raspberry Pi que se conecta a um hub IoT. Para sua conveniência, muitos dos tutoriais do Hub IoT usam [dispositivos simulados](#simulated-device) para que você possa executar os exemplos em seu computador local.

### <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias

Quando você se conecta a um ponto de extremidade voltado para o dispositivo ou voltado para o serviço em um hub IoT, sua [cadeia de conexão](#connection-string) inclui a chave para conceder acesso. Quando você adiciona um dispositivo ao [registro de identidade](#identity-registry) ou adiciona uma [política de acesso compartilhado](#shared-access-policy) para o hub, o serviço gera uma chave primária e secundária. Ter duas chaves permite que você passe de uma chave para outra quando você atualiza uma chave sem perder o acesso ao hub IoT.

### <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de uma gêmeo digital. É possível declarar propriedades como somente leitura ou graváveis. Propriedades somente leitura, como número de série, são definidas pelo código em execução no próprio dispositivo [IoT Plug and Play](#iot-plug-and-play-device).  As propriedades graváveis, como um limite de alarme, normalmente são definidas com base na solução de IoT baseada em nuvem.

### <a name="protocol-gateway"></a>Gateway de protocolo

Um gateway de protocolo normalmente é implantado na nuvem e fornece serviços de conversão de protocolo para dispositivos que se conectam ao [Hub IOT](#iot-hub). Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="reported-configuration"></a>Configuração relatada

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), a configuração relatada refere-se ao conjunto completo de propriedades e de metadados no dispositivo gêmeo que deve ser relatado para o back-end da solução.

### <a name="reported-properties"></a>Propriedades reportadas

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), as propriedades relatadas compõem uma subseção do dispositivo gêmeo usado com [propriedades desejadas](#desired-properties) para sincronizar a configuração ou a condição do dispositivo. As propriedades relatadas só podem ser definidas pelo [aplicativo de dispositivo](#device-app) e podem ser lidas e consultadas pelo [aplicativo de back-end](#back-end-app).

### <a name="retry-policy"></a>Política de Repetição

Use uma política de repetição para tratar [erros transitórios](/azure/architecture/best-practices/transient-faults) quando você se conectar a um serviço de nuvem.

### <a name="routing-rules"></a>Regras de roteamento

Configurar [as regras de roteamento](../iot-hub/iot-hub-devguide-messages-read-custom.md) em seu hub IoT para rotear mensagens de dispositivo para a nuvem para um [ponto de extremidade interno](#built-in-endpoints) ou [pontos de extremidade personalizados](#custom-endpoints) para processamento pelo back-end da sua solução.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL SIMPLES

SASL PLAIN é um protocolo que o protocolo AMQP usa para transferir tokens de segurança.

### <a name="service-rest-api"></a>API REST do Serviço

Você pode usar a [API REST de Serviço](/rest/api/iothub/service/configuration) da solução de back-end para gerenciar seus dispositivos. A API permite recuperar e atualizar as propriedades de [dispositivos gêmeos](#device-twin) e chamar [métodos diretos](#direct-method) e [trabalhos](#job) programados. Normalmente, você deve usar um dos [SDKs de serviço](#azure-iot-service-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As SAS (Assinaturas de Acesso Compartilhado) são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. A autenticação de SAS tem dois componentes: uma _Política de acesso Compartilhado_ e uma _Assinatura de Acesso Compartilhado_ (normalmente chamada de token). Um dispositivo usa SAS para se autenticar em um hub IoT. Os [aplicativos de back-end](#back-end-app) também usam SAS para se autenticar com os pontos de extremidade voltados para o serviço em um hub IoT. Normalmente, você pode incluir o token SAS na [cadeia de conexão](#connection-string) que um aplicativo usa para estabelecer uma conexão com um hub IoT.

### <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma política de acesso compartilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Você pode gerenciar as chaves e as políticas de acesso compartilhadas para seu hub no portal.

### <a name="simulated-device"></a>Dispositivo simulado

Para sua conveniência, muitos dos tutoriais do Hub IoT usam dispositivos simulados para que você possa executar os exemplos em seu computador local. Por outro lado, um [dispositivo físico](#physical-device) é um dispositivo real como um Raspberry Pi que se conecta a um hub IoT.

### <a name="solution"></a>Solução

Uma _solução_ pode se referir a uma solução do Visual Studio que inclua um ou mais projetos. Uma _solução_ também pode se referir a uma solução IoT que inclua elementos como dispositivos, [aplicativos de dispositivo](#device-app), um hub IoT, outros serviços do Azure e [aplicativos de back-end](#back-end-app).

### <a name="solution-builder"></a>Solution Builder

Um Solution Builder cria o back-end da solução. Um Solution Builder normalmente funciona com recursos do Azure, como o Hub IoT e [repositórios de modelo](#model-repository).

### <a name="system-properties"></a>Propriedades do sistema

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), as propriedades do sistema são somente leitura em incluem informações sobre o uso do dispositivo, por exemplo, a hora da última atividade e o estado da conexão.

## <a name="t"></a>T

### <a name="tags"></a>Marcas

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), as marcas são metadados do dispositivo armazenadas e recuperadas pelo back-end da solução na forma de um documento JSON. As marcas não são visíveis para os aplicativos em um dispositivo.

### <a name="telemetry"></a>Telemetria

Os dispositivos coletam dados de telemetria, como a velocidade do vento ou a temperatura, e usam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

No Plug and Play IoT, os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor enviadas pelo [dispositivo IoT Plug and Play](#iot-plug-and-play-device) como um fluxo de dados.

### <a name="token-service"></a>Serviço de token

Você pode usar um serviço de token para implementar um mecanismo de autenticação para seus dispositivos. Ele usa uma [política de acesso compartilhado](#shared-access-policy) do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped*. Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT. Um dispositivo usa um mecanismo de autenticação personalizado para se autenticar no serviço de token. Se o dispositivo for autenticado com êxito, o serviço de token emitirá um token SAS para o dispositivo a ser usado para acessar o hub IoT.

### <a name="twin-queries"></a>Consultas de gêmeos

As [consultas de dispositivo e módulo gêmeo](../iot-hub/iot-hub-devguide-query-language.md) usam a linguagem de consulta do Hub IoT semelhante ao SQL para recuperar informações dos seus dispositivos e módulos gêmeos. Você pode usar a mesma linguagem de consulta do Hub IoT para recuperar informações sobre um [trabalho](#job) em execução no Hub IOT.

### <a name="twin-synchronization"></a>Sincronização gêmea

A sincronização gêmea usa as [propriedades desejadas](#desired-properties) em dispositivos ou módulos gêmeos para configurar seus dispositivos e recuperar as [propriedades relatadas](#reported-properties) de dispositivos ou módulos para armazenar no gêmeo.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certificado de cliente X.509

Um dispositivo pode usar um certificado X.509 para se autenticar no [Hub IoT](#iot-hub). O uso de um certificado x.509 é uma alternativa ao uso de um [token SAS](#shared-access-signature).

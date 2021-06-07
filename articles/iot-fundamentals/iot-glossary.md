---
title: Glossário de termos do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor – um glossário explicando alguns dos termos comuns usados nos artigos do Azure IoT.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: d7ae1e72dee28509c1338a1b56cf42a5293af9bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670249"
---
# <a name="glossary-of-iot-terms"></a>Glossário de termos de IoT

Este artigo lista alguns dos termos comuns usados nos artigos de IoT.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Procolo AMQP

O [AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) é um dos protocolos de mensagens com suporte do [Hub IoT](#iot-hub) para comunicação com dispositivos. Para saber mais sobre os protocolos de mensagens com suporte do Hub IoT, consulte [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Política de alocação

No [serviço de provisionamento de dispositivos](#device-provisioning-service), a política de alocação determina como o serviço atribui dispositivos a [hubs IOT vinculados](#linked-iot-hub).

### <a name="attestation-mechanism"></a>Mecanismo de atestado

No [serviço de provisionamento de dispositivos](#device-provisioning-service), o mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado é configurado em um [registro](#enrollment).

Os mecanismos de atestado incluem certificados X. 509, módulos de plataforma confiáveis e chaves simétricas.

### <a name="automatic-deployment"></a>Implantação automática

No IoT Edge, uma implantação automática configura um conjunto de destino de dispositivos de IoT Edge para executar um conjunto de módulos de IoT Edge. Cada implantação continuamente garante que todos os dispositivos que correspondem à sua condição de destino estejam executando o conjunto de módulos especificado, mesmo quando novos dispositivos são criados ou modificados para corresponder à condição de destino. Cada dispositivo IoT Edge recebe apenas a implantação de prioridade mais alta cuja condição de destino ele atende. Saiba mais sobre [implantação automática do IoT Edge](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Configuração de dispositivo automática

O back-end da sua solução pode usar [configurações de dispositivo automáticas](../iot-hub/iot-hub-automatic-device-management.md) para atribuir as propriedades desejadas a um conjunto de [dispositivos gêmeos](#device-twin) e relatar o status usando métricas do sistema e métricas personalizadas.

### <a name="automatic-device-management"></a>Gerenciamento automático de dispositivo

O gerenciamento automático do dispositivo no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Com o gerenciamento automático do dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo.  Consiste em [configurações de dispositivo automáticas](../iot-hub/iot-hub-automatic-device-management.md) e [implantações automáticas do IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Gêmeos Digitais do Azure

O Azure digital gêmeos é uma oferta de PaaS (plataforma como serviço) para a criação de representações digitais de coisas, locais, processos de negócios e pessoas do mundo real. Crie grafos de conhecimento que representam ambientes inteiros e use-os para obter informações para impulsionar produtos melhores, otimizar operações e custos e criar experiências de clientes inovadoras. Para saber mais, confira [Azure digital gêmeos](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Instância de gêmeos digital do Azure

Uma única instância do serviço gêmeos do Azure digital na assinatura de um cliente. Enquanto o [Azure digital gêmeos](#azure-digital-twins) refere-se ao serviço do Azure como um todo, sua **instância** do gêmeos digital do Azure é seu recurso de gêmeos digital do Azure individual.

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

Um tipo de [ponto de extremidade](#endpoint) que é incorporado ao Hub IOT. Cada hub IoT inclui um [ponto de extremidade](../iot-hub/iot-hub-devguide-endpoints.md) interno que é compatível com o Hub de Eventos. Você pode usar qualquer mecanismo que funciona com os Hubs de Eventos para ler mensagens de dispositivo para nuvem desse ponto de extremidade.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Gateway de nuvem

Um gateway de nuvem permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Um gateway de nuvem é hospedado na nuvem em comparação a um [gateway de campo](#field-gateway) executado no local para seus dispositivos. Um caso de uso típico para um gateway de nuvem é implementar a conversão de protocolo para seus dispositivos.

### <a name="cloud-to-device"></a>Nuvem para o dispositivo

Refere-se às mensagens enviadas de um hub IoT para um dispositivo conectado. Geralmente, essas mensagens são comandos que instruem o dispositivo a executar uma ação. Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Comandos

No Plug and Play IoT, os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [digital](#digital-twin). Por exemplo, um comando para reinicializar um dispositivo.

### <a name="component"></a>Componente

No Plug and Play IoT e no Azure digital gêmeos, os componentes permitem criar uma [interface](#interface) de modelo como um assembly de outras interfaces. Um [modelo de dispositivo](#device-model) pode combinar várias interfaces como componentes. Por exemplo, um modelo pode incluir um componente de switch e um componente termostato. Vários componentes em um modelo também podem usar o mesmo tipo de interface. Por exemplo, um modelo pode incluir dois componentes termostato.

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

### <a name="deployment-manifest"></a>Manifesto de implantação

No [IOT Edge](#iot-edge), o manifesto de implantação é um documento JSON que contém as informações a serem copiadas em um ou mais conjuntos de módulos de IOT Edge de dispositivos de um ou mais para implantar um conjunto de módulo, rotas e propriedades desejadas do módulo associado.

### <a name="desired-configuration"></a>Configuração desejada

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), a configuração desejada refere-se ao conjunto completo de propriedades e de metadados no dispositivo gêmeo que deve ser sincronizado com o dispositivo.

### <a name="desired-properties"></a>Propriedades desejadas

No contexto de um [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md), as propriedades desejadas compõem uma subseção do dispositivo gêmeo usado com as [porpriedades relatadas](#reported-properties) para sincronizar a configuração ou a condição do dispositivo. As propriedades desejadas só podem ser definidas por um [aplicativo de back-end](#back-end-app) e são observadas pelo [aplicativo de dispositivo](#device-app).

### <a name="device"></a>Dispositivo

No contexto do IoT, um dispositivo é normalmente um dispositivo de computação autônomo de pequena escala que pode coletar dados ou controlar outros dispositivos. Um dispositivo pode ser, por exemplo, um dispositivo de monitoramento ambiental ou um controlador de sistemas de rega e ventilação em uma estufa. O [catálogo de dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para o trabalho com o [Hub IoT](#iot-hub).

### <a name="device-app"></a>Aplicativo do dispositivo

Um aplicativo de dispositivo é executado em seu [dispositivo](#device) e gerencia a comunicação com o [hub IoT](#iot-hub). Normalmente, você usa um dos [SDKs de dispositivo IoT do Azure](#azure-iot-device-sdks) ao implementar um aplicativo de dispositivo. Em muitos dos tutoriais de IoT, você deve usar um [dispositivo simulado](#simulated-device) para sua conveniência.

### <a name="device-builder"></a>Construtor de dispositivos

Um construtor de dispositivos usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os criadores de dispositivos normalmente usam um dos [SDKs do dispositivo IOT do Azure](#azure-iot-device-sdks) para implementar o cliente do dispositivo.

### <a name="device-certification"></a>Certificado de dispositivo

O programa de certificação de dispositivo do IoT Plug and Play verifica se um dispositivo atende aos requisitos de certificação do IoT Plug and Play. Você pode adicionar um dispositivo certificado ao [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/devicecatalog).

### <a name="device-condition"></a>Condição do dispositivo

Refere-se às informações de estado do dispositivo, como o método de conectividade em uso no momento, conforme relatado por um [aplicativo de dispositivo](#device-app). Os [aplicativos de dispositivo](#device-app) também podem relatar suas funcionalidades. Você pode consultar informações de condição e de funcionalidade usando dispositivos gêmeos.

### <a name="device-data"></a>Dados do dispositivo

Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registro de identidade](#identity-registry) do Hub IoT. É possível importar e exportar esses dados.

### <a name="device-identity"></a>Identidade do dispositivo

A identidade do dispositivo (ou ID do dispositivo) é o identificador exclusivo atribuído a cada dispositivo registrado no [registro de identidade](#identity-registry)do Hub IOT.

### <a name="device-management"></a>Gerenciamento de dispositivo

O gerenciamento de dispositivos engloba o ciclo de vida completo associado ao gerenciamento dos dispositivos na sua solução de IoT incluindo planejamento, provisionamento, configuração, monitoramento e desativação.

### <a name="device-management-patterns"></a>Padrões de gerenciamento de dispositivos

O [hub IoT](#iot-hub) permite que os padrões comuns de gerenciamento de dispositivos, incluindo reinicialização, execução de redefinições de fábrica e execução de atualizações de firmware nos seus dispositivos.

### <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo é um tipo de [modelo](#model) que usa a [linguagem de definição de gêmeos digital](#digital-twins-definition-language-dtdl) para descrever os recursos de um dispositivo de plug and Play de IOT. Um modelo de dispositivo simples usa uma única interface para descrever os recursos do dispositivo. Um modelo de dispositivo mais complexo inclui vários componentes, cada um deles descreve um conjunto de recursos. Para saber mais, confira [componentes de IoT plug and Play em modelos](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Modelagem de dispositivo

Um [Construtor de dispositivos](#device-builder) ou [Construtor de módulos](#module-builder)usa a linguagem de definição de [gêmeos digital](#digital-twins-definition-language-dtdl) para modelar os recursos de um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device). Um [Solution Builder](#solution-builder) pode configurar uma solução de IOT do modelo.

### <a name="device-provisioning"></a>Provisionamento de dispositivos

O provisionamento de dispositivos é o processo de adicionar os dados iniciais do [dispositivo](#device-data) às lojas em sua solução. Para permitir que um novo dispositivo se conecte ao seu hub, você deve adicionar uma ID de dispositivo e chaves ao [registro de identidade](#identity-registry)do Hub IOT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução.

### <a name="device-provisioning-service"></a>Serviço de Provisionamento de Dispositivos

O DPS (serviço de provisionamento de dispositivos) do Hub IoT é um serviço auxiliar para o [Hub IOT](#iot-hub) que você usa para configurar o provisionamento de dispositivos sem toque para um hub IOT especificado. Com o DPS, você pode provisionar milhões de dispositivos de maneira segura e escalonável.

### <a name="device-rest-api"></a>APIs REST do dispositivo

Você pode usar a [API REST de Dispositivo](/rest/api/iothub/device) de um dispositivo para enviar mensagens de dispositivo para a nuvem a um hub IoT e receber mensagens da [nuvem para o dispositivo](#cloud-to-device) de um hub IoT. Normalmente, você deve usar um dos [SDKs de dispositivo](#azure-iot-device-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

### <a name="device-twin"></a>Dispositivo gêmeo

Um dispositivo gêmeo é um documento JSON que armazena informações de estado do dispositivo, como metadados, configurações e condições. O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você provisiona no hub IoT. Os dispositivos gêmeos permitem sincronizar condições de dispositivo e configurações entre o dispositivo e o back-end da solução. Você pode consultar dispositivos gêmeos para localizar dispositivos específicos e o status de operações de longa execução.

### <a name="device-to-cloud"></a>Dispositivo para nuvem

Refere-se às mensagens enviadas de um dispositivo conectado ao [Hub IoT](#iot-hub). Essas mensagens podem ser do tipo [ponto de dados](#data-point-message) ou [interativas](#interactive-message). Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Gêmeo digital

Um FileUp digital é uma coleção de dados digitais que representa um objeto físico. As alterações no objeto físico são refletidas no digital. Em alguns cenários, você pode usar a pesquisa digital para manipular o objeto físico. O [serviço de gêmeos digital do Azure](../digital-twins/index.yml) usa [modelos](#model) expressos na [DTDL (digital gêmeos Definition Language)](#digital-twins-definition-language-dtdl) para representar a gêmeos digital de dispositivos físicos ou conceitos de negócios abstratos de nível superior, permitindo uma ampla gama de soluções de alta bits digitais baseadas em nuvem. Um dispositivo de [plug and Play de IOT](../iot-pnp/index.yml) tem uma DTDL digital, descrita por um [modelo de dispositivo](#device-model).

### <a name="digital-twin-change-events"></a>Eventos de alteração de gêmeo digital

Quando um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) é conectado a um hub IOT, o Hub pode usar sua capacidade de roteamento para enviar notificações de alterações de conexão digital. Por exemplo, sempre que um valor de [Propriedade](#properties) é alterado em um dispositivo, o Hub IOT pode enviar uma notificação para um ponto de extremidade, como um hub de eventos.

### <a name="digital-twin-route"></a>Rota do gêmeo digital

Uma rota configurada em um hub IoT para fornecer [eventos de alteração de troca digital](#digital-twin-change-events) para um ponto de extremidade, como um hub de eventos.

### <a name="digital-twins-definition-language-dtdl"></a>DTDL (Linguagem de Definição de Gêmeos Digitais)

Uma linguagem JSON-LD para descrever [modelos](#model) e [interfaces](#interface) para [dispositivos IOT plug and Play](#iot-plug-and-play-device) e entidades [do Azure digital gêmeos](../digital-twins/index.yml) . Use a [linguagem de definição de gêmeos digital versão 2](https://github.com/Azure/opendigitaltwins-dtdl) para descrever os recursos de uma [teledigital](#digital-twin) e habilitar a plataforma IOT e as soluções de IOT para usar a semântica da entidade. A linguagem de definição de gêmeos digital geralmente é abreviada como DTDL.

### <a name="direct-method"></a>Método direto

Um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) é uma maneira de disparar um método a fim de executá-lo em um dispositivo chamando uma API em seu hub IoT.

### <a name="downstream-services"></a>Serviços downstream

Um termo relativo que descreve os serviços que recebem dados do contexto atual. Por exemplo, se você estiver pensando no contexto do Azure digital gêmeos, [Time Series insights](../time-series-insights/index.yml) seria considerado um serviço downstream se você configurar seus dados para fluir do Azure digital gêmeos para Time Series insights.

## <a name="e"></a>E

### <a name="endpoint"></a>Ponto de extremidade

Uma representação nomeada de um serviço de roteamento de dados que pode receber dados de outros serviços.

Um hub IoT expõe vários [pontos de extremidade](../iot-hub/iot-hub-devguide-endpoints.md) que permitem que seus aplicativos se conectem ao hub IoT. Há pontos de extremidade voltados para dispositivos que permitem que dispositivos executem operações como o envio de mensagens do [dispositivo para a nuvem](#device-to-cloud) e o recebimento de mensagens da [nuvem para o dispositivo](#cloud-to-device). Há pontos de extremidade de gerenciamento de voltados para serviço que permitem que [aplicativos de back-end](#back-end-app) executem operações como o gerenciamento de [identidade de dispositivo](#device-identity) e o gerenciamento de dispositivos gêmeos. Há voltado para o serviço [pontos de extremidade internos](#built-in-endpoints) para ler mensagens de dispositivo para a nuvem. Você pode criar [pontos de extremidade personalizados](#custom-endpoints) para receber mensagens de dispositivo para nuvem enviadas por uma [regra de roteamento](#routing-rules).

### <a name="enrollment"></a>Registro

No [serviço de provisionamento de dispositivos](#device-provisioning-service), um registro é o registro de dispositivos individuais ou grupos de dispositivos que podem ser registrados com um [Hub IOT vinculado](#linked-iot-hub) por meio de provisionamento automático.

### <a name="enrollment-group"></a>Grupo de registro

No [serviço de provisionamento de dispositivos](#device-provisioning-service), um grupo de registro identifica um grupo de dispositivos que compartilham um mecanismo de [atestado](#attestation-mechanism)de chave simétrica X. 509 ou simétrico.

### <a name="event-handlers"></a>Manipuladores de eventos

Isso pode se referir a qualquer processo que é disparado pela chegada de um evento e realiza alguma ação de processamento. Uma maneira de criar manipuladores de eventos é adicionar o código de processamento de eventos a uma função do Azure e enviar dados por meio dele usando [pontos de extremidade](#endpoint) e [Roteamento de eventos](#event-routing).

### <a name="event-hub-compatible-endpoint"></a>Ponto de extremidade compatível com o Hub de Eventos

Para ler as mensagens do [dispositivo para a nuvem](#device-to-cloud) enviadas ao seu hub IOT, você pode se conectar a um ponto de extremidade em seu hub e usar qualquer método compatível com o Hub de eventos para ler essas mensagens. Entre os métodos compatíveis com o Hub de Eventos estão o uso de [SDKs dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md) e o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md).

### <a name="event-routing"></a>Roteamento de eventos

O processo de envio de eventos e seus dados de um dispositivo ou serviço para o [ponto de extremidade](#endpoint) de outro. 

No Hub IOT, você pode definir [regras de roteamento](#routing-rules) para descrever como as mensagens devem ser enviadas. No Azure digital gêmeos, as rotas de eventos são entidades criadas para essa finalidade. As rotas de eventos do gêmeos digital do Azure podem conter filtros para limitar quais tipos de eventos são enviados para cada ponto de extremidade.

## <a name="f"></a>F

### <a name="field-gateway"></a>Gateway de campo

Um gateway de campo permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub) e normalmente é implantado localmente com seus dispositivos. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Um gateway permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Veja também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway) e [Gateway personalizado](#custom-gateway).

### <a name="gateway-device"></a>Dispositivos de gateway

Um dispositivo é um exemplo de um [Gateway de campo](#field-gateway). Um dispositivo de gateway pode ser um [dispositivo IOT padrão](#device) ou um [dispositivo IOT Edge](#iot-edge-device).

Um dispositivo de Gateway permite a conectividade para dispositivos downstream que não podem se conectar diretamente ao [Hub IOT](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Módulo de segurança de hardware

Um HSM (módulo de segurança de hardware) é usado para armazenamento seguro baseado em hardware de segredos de dispositivo. É a forma mais segura de armazenamento secreto para um dispositivo. Tanto os certificados X. 509 quanto as chaves simétricas podem ser armazenados em um HSM. No [serviço de provisionamento de dispositivos](#device-provisioning-service), um mecanismo de [atestado](#attestation-mechanism) pode usar um HSM.

## <a name="i"></a>I

### <a name="id-scope"></a>Escopo da ID

O escopo de ID é um valor exclusivo atribuído a uma instância do [DPS (serviço de provisionamento de dispositivos)](#device-provisioning-service) quando ele é criado.

IoT Central aplicativos fazem uso de instâncias do DPS e disponibilizam o escopo da ID por meio da interface do usuário do IoT Central.

### <a name="identity-registry"></a>Registro de identidade

O [registro de identidade](../iot-hub/iot-hub-devguide-identity-registry.md) é o componente interno de um hub IoT que armazena informações sobre os dispositivos individuais que têm permissão para se conectar a um hub IoT.

### <a name="individual-enrollment"></a>Registro individual

No [serviço de provisionamento de dispositivos](#device-provisioning-service), um registro individual identifica um único dispositivo que usa um certificado de folha X. 509 ou uma chave simétrica como um mecanismo de [atestado](#attestation-mechanism).

### <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem da [nuvem para o dispositivo](#cloud-to-device) que dispara uma ação imediata no back-end da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser registrada automaticamente em um sistema de CRM.

### <a name="interface"></a>Interface

No Plug and Play de IoT, uma interface descreve os recursos relacionados que são implementados por um [dispositivo IoT plug and Play](#iot-plug-and-play-device) ou em uma [teledigital](#digital-twin). Você pode reutilizar interfaces em diferentes [modelos de dispositivo](#device-model). Quando uma interface é usada em um modelo de dispositivo, ela define um [componente](#component) do dispositivo. Um dispositivo simples contém apenas uma interface padrão.

No Azure digital gêmeos, a *interface* pode ser usada para fazer referência ao item de código de nível superior em uma definição de modelo de [DTDL](#digital-twins-definition-language-dtdl) .

### <a name="iot-edge"></a>IoT Edge

O Azure IoT Edge permite a implantação orientada a nuvem do código específico da solução e de serviços do Azure para dispositivos locais. [IOT Edge dispositivos](#iot-edge-device) podem agregar dados de outros dispositivos para executar a computação e análise antes de enviar os dados para a nuvem. Para saber mais, consulte [Azure IOT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agente do IoT Edge

A parte do runtime do IoT Edge responsável por implantar e monitorar módulos.

### <a name="iot-edge-device"></a>Dispositivo do IoT Edge

Um dispositivo IoT Edge usa [módulos](#modules) IOT Edge em contêineres para executar serviços do Azure, serviços de terceiros ou seu próprio código. No dispositivo IoT Edge, o [tempo de execução do IOT Edge](#iot-edge-runtime) gerencia os módulos. Você pode monitorar e gerenciar remotamente um dispositivo IoT Edge da nuvem.

### <a name="iot-edge-hub"></a>Hub do IoT Edge

A parte do runtime do IoT Edge responsável pelas comunicações de módulo para módulo e comunicações upstream (para o Hub IoT) e downstream (para fora do Hub IoT).

### <a name="iot-edge-runtime"></a>runtime do IoT Edge

O runtime do IoT Edge inclui tudo que a Microsoft distribui para ser instalado em um dispositivo IoT Edge. Inclui o agente Edge, o hub Edge e o daemon de segurança IoT Edge.

### <a name="iot-extension-for-azure-cli"></a>Extensão de IoT para CLI do Azure

[A extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando de plataforma cruzada. A ferramenta permite que você gerencie seus dispositivos no [registro de identidade](#identity-registry), envie e receba mensagens e arquivos de seus dispositivos e monitore as operações do hub IoT.

### <a name="iot-hub"></a>Hub IoT

O Hub IoT é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md). Usando a assinatura do Azure, você pode criar hubs IoT para lidar com suas cargas de trabalho de mensagens IoT.

### <a name="iot-hub-metrics"></a>Métricas do Hub IoT

As métricas do Hub IoT fornecem dados sobre o estado dos hubs IoT em sua assinatura do Azure. As métricas do Hub IoT permitem avaliar a integridade geral do serviço e dos dispositivos conectados a ele. As métricas do Hub IoT podem ajudar você a ver o que está acontecendo com o hub IoT e a investigar a causa raiz de problemas sem precisar entrar em contato com o suporte do Azure. Para saber mais, confira [Monitorar o Hub IoT](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) é uma linguagem semelhante a SQL que permite consultar seu [trabalho](#job), digital gêmeos e dispositivo gêmeos.

### <a name="iot-hub-resource-rest-api"></a>API REST de Recursos do Hub IoT

Você pode usar a [API REST dos recursos do Hub IoT](/rest/api/iothub/iothubresource) para gerenciar os hubs IoT em sua assinatura do Azure que executam operações como criar, atualizar e excluir hubs.

### <a name="iot-plug-and-play-bridge"></a>Ponte do IoT Plug and Play

O IoT Plug and Play Bridge é um aplicativo de software livre que permite que os sensores e periféricos existentes conectados aos gateways do Windows ou do Linux se conectem como [dispositivos de IoT plug and Play](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os [dispositivos](#iot-plug-and-play-device) de plug and Play IOT devem seguir um conjunto de convenções quando trocam dados com uma solução.

### <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo de Plug and Play IoT normalmente é um dispositivo de computação independente de pequena escala que coleta dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de dispositivo](#device-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação de agricultura inteligente. Um dispositivo IoT Plug and Play pode ser implementado diretamente ou como um módulo IoT Edge. É possível escrever uma solução de IoT hospedada na nuvem para comandar, controlar e receber dados de dispositivos IoT Plug and Play.

### <a name="iot-solution-accelerators"></a>Aceleradores de solução do IoT

Os aceleradores de solução do Azure IoT incluem vários serviços do Azure para oferecer diferentes soluções. Essas soluções permitem que você realize rapidamente implementações de ponta a ponta de cenários comuns de IoT. Para obter mais informações, consulte [O que são os aceleradores de solução do Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>Trabalho

O back-end da solução pode usar [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) para agendar e acompanhar atividades em um conjunto de dispositivos registrados com o Hub IOT. As atividades incluem a atualização de [propriedades desejadas](#desired-properties)do dispositivo, atualizando as [marcas](#tags)do dispositivo e invocando [métodos diretos](#direct-method). O [Hub IoT](#iot-hub) também usa  para [importar e exportar](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do [registro de identidade](#identity-registry).

## <a name="l"></a>L

### <a name="leaf-device"></a>Dispositivo de folha

No [IOT Edge](#iot-edge), um dispositivo de folha é um dispositivo sem dispositivo downstream.

### <a name="lifecycle-event"></a>Evento do ciclo de vida

No Azure digital gêmeos, esse tipo de evento é acionado quando um item de dados — como uma cópia digital, uma relação ou um manipulador de eventos — é criado ou excluído da instância do gêmeos digital do Azure.

### <a name="linked-iot-hub"></a>Hub IoT vinculado

O [DPS (serviço de provisionamento de dispositivos)](#device-provisioning-service)pode provisionar dispositivos para hubs IOT que foram vinculados a ele. Vincular um hub IoT a uma instância de DPS permite que o serviço registre uma ID de dispositivo e defina a configuração inicial no dispositivo.

## <a name="m"></a>M

### <a name="model"></a>Modelo

Um modelo define um tipo de entidade em seu ambiente físico, incluindo suas propriedades, telemetrias, componentes e, às vezes, outras informações. Os modelos são usados para criar [gêmeos digitais](#digital-twin) que representam objetos físicos específicos desse tipo. Os modelos são escritos na [linguagem de definição de gêmeos digital](#digital-twins-definition-language-dtdl).

No [serviço de gêmeos digital do Azure](../digital-twins/index.yml), os modelos podem definir dispositivos ou conceitos de negócios abstratos de nível superior. No [plug and Play IOT](../iot-pnp/index.yml), os [modelos de dispositivo](#device-model) são usados para descrever os dispositivos especificamente.

### <a name="model-id"></a>ID do Modelo

Quando um dispositivo de Plug and Play IoT se conecta a um hub IoT, ele envia a **ID de modelo** do modelo [DTDL](#digital-twins-definition-language-dtdl) que ele implementa. Essa ID permite que a solução Localize o modelo do dispositivo.

### <a name="model-repository"></a>Repositório de modelos

Um repositório de modelos armazena modelos e [interfaces](#interface)de [dispositivo](#device-model) .

### <a name="model-repository-rest-api"></a>API REST do repositório de modelos

Uma API para gerenciar e interagir com o repositório de modelos. Por exemplo, você pode usar a API para adicionar e Pesquisar [modelos de dispositivo](#device-model).

### <a name="module-builder"></a>Construtor de módulo

Um construtor de módulo usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os construtores de módulo implementam o código como um módulo ou um módulo IoT Edge para implantar no tempo de execução de IoT Edge em um dispositivo.

### <a name="module-identity"></a>Identidade de módulo

A identidade do módulo é o identificador exclusivo atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também é registrada no [registro de identidade](#identity-registry).

O módulo identificar detalha as credenciais de segurança que o módulo usa para autenticar com o [Hub IOT](#iot-hub) ou, no caso de um módulo de IOT Edge para o [Hub de IOT Edge](#iot-edge-hub).

### <a name="module-image"></a>Imagem do módulo

A imagem do Docker que o [tempo de execução do IOT Edge](#iot-edge-runtime) usa para instanciar instâncias de módulo.

### <a name="module-twin"></a>Módulo Gêmeo

Semelhante a um dispositivo gêmeo, um módulo gêmeo é um documento JSON que armazena informações de estado do módulo, como metadados, configurações e condições. O Hub IoT persiste um módulo gêmeo para cada identidade de módulo que você provisionar com uma identidade do dispositivo no seu Hub IoT. Os módulos gêmeos permitem sincronizar condições de módulo e configurações entre o módulo e o back-end da solução. Você pode consultar módulos gêmeos para localizar módulos específicos e consultar o status de operações de longa duração.

### <a name="modules"></a>Módulos

No lado do dispositivo, os SDKs do dispositivo do Hub IoT permitem criar [módulos](../iot-hub/iot-hub-devguide-module-twins.md) em que cada um abre uma conexão independente com o Hub IoT. Essa funcionalidade permite usar namespaces separados para diferentes componentes em seu dispositivo.

A identidade do módulo e o módulo gêmeo fornecem os mesmos recursos que a [identidade do dispositivo](#device-identity) e o [dispositivo gêmeo](#device-twin), mas com granularidade mais precisa. Essa granularidade mais fina permite que dispositivos capazes, como dispositivos baseados em sistema operacional ou dispositivos de firmware que gerenciam vários componentes, isolem a configuração e as condições de cada um desses componentes.

No [IOT Edge](#iot-edge), um módulo é um contêiner do Docker que você pode implantar em dispositivos IOT Edge. Ele executa uma tarefa específica, como a ingestão de uma mensagem de um dispositivo, a transformação de uma mensagem ou o envio de uma mensagem para um Hub IoT. Ele se comunica com outros módulos e envia dados para o [tempo de execução de IOT Edge](#iot-edge-runtime).

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

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado persistente de um " [digital](#digital-twin)". É possível declarar propriedades como somente leitura ou graváveis. Propriedades somente leitura, como número de série, são definidas pelo código em execução no próprio dispositivo [IoT Plug and Play](#iot-plug-and-play-device). As propriedades graváveis, como um limite de alarme, normalmente são definidas com base na solução de IoT baseada em nuvem.

### <a name="property-change-event"></a>Evento de alteração de propriedade

Um evento que resulta de uma alteração de propriedade em uma troca [digital](#digital-twin).

### <a name="protocol-gateway"></a>Gateway de protocolo

Um gateway de protocolo normalmente é implantado na nuvem e fornece serviços de conversão de protocolo para dispositivos que se conectam ao [Hub IOT](#iot-hub). Para obter mais informações, consulte [o que é o Hub IOT do Azure?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="registration"></a>Registro

Um registro é o registro de um dispositivo no [registro de identidade](#identity-registry)do Hub IOT. Você pode registrar ou descadastrar diretamente ou usar o [serviço de provisionamento de dispositivos](#device-provisioning-service) para automatizar o registro de dispositivos.

### <a name="registration-id"></a>ID de registro

A ID de registro é usada para identificar exclusivamente um [registro](#registration) de dispositivo com o [serviço de provisionamento de dispositivos](#device-provisioning-service). A ID de registro pode ter o mesmo valor que a [identidade do dispositivo](#device-identity).

### <a name="relationship"></a>Relação

No serviço de [gêmeos digital do Azure](../digital-twins/index.yml) , as relações são usadas para conectar [gêmeos digital](#digital-twin) em grafos de conhecimento que representam digitalmente todo o seu ambiente físico. Os tipos de relações que seus gêmeos podem ter são definidos como parte das definições de [modelo](#model) de gêmeos; o modelo de [DTDL](#digital-twins-definition-language-dtdl) para um determinado tipo de texto inclui informações sobre quais relações ele pode ter com outras gêmeos.

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

### <a name="service-operations-endpoint"></a>Ponto de extremidade das operações de serviço

Um [ponto de extremidade](#endpoint) para gerenciar as configurações de serviço usadas por um administrador de serviço. Por exemplo, no [serviço de provisionamento de dispositivos](#device-provisioning-service) , você usa o ponto de extremidade de serviço para gerenciar registros.

### <a name="service-rest-api"></a>API REST do Serviço

Você pode usar a [API REST de Serviço](/rest/api/iothub/service/configuration) da solução de back-end para gerenciar seus dispositivos. A API permite recuperar e atualizar as propriedades de [dispositivos gêmeos](#device-twin) e chamar [métodos diretos](#direct-method) e [trabalhos](#job) programados. Normalmente, você deve usar um dos [SDKs de serviço](#azure-iot-service-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

### <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma política de acesso compartilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Você pode gerenciar as chaves e as políticas de acesso compartilhadas para seu hub no portal.

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As SAS (Assinaturas de Acesso Compartilhado) são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. A autenticação de SAS tem dois componentes: uma _Política de acesso Compartilhado_ e uma _Assinatura de Acesso Compartilhado_ (normalmente chamada de token). Um dispositivo usa SAS para se autenticar em um hub IoT. Os [aplicativos de back-end](#back-end-app) também usam SAS para se autenticar com os pontos de extremidade voltados para o serviço em um hub IoT. Normalmente, você pode incluir o token SAS na [cadeia de conexão](#connection-string) que um aplicativo usa para estabelecer uma conexão com um hub IoT.

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

### <a name="target-condition"></a>Condição de destino

Em uma implantação de IoT Edge, a condição de destino seleciona os dispositivos de destino da implantação, por exemplo, **tag. Environment = prod**. A condição de destino é avaliada continuamente para incluir novos dispositivos que atendam aos requisitos ou remova os dispositivos que não fazem mais.

### <a name="telemetry"></a>Telemetria

Os dispositivos coletam dados de telemetria, como a velocidade do vento ou a temperatura, e usam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

No Plug and Play IoT e no gêmeos digital do Azure, os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor que são enviadas por dispositivos, como [dispositivos IoT plug and Play](#iot-plug-and-play-device), como um fluxo de dados.

Ao contrário das [Propriedades](#properties), a telemetria não é armazenada em uma [teledigital](#digital-twin). é um fluxo de eventos de dados associados a tempo que precisam ser manipulados conforme eles ocorrem.

### <a name="telemetry-event"></a>Evento de telemetria

Um evento que indica a chegada de dados de telemetria.

### <a name="token-service"></a>Serviço de token

Você pode usar um serviço de token para implementar um mecanismo de autenticação para seus dispositivos. Ele usa uma [política de acesso compartilhado](#shared-access-policy) do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped*. Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT. Um dispositivo usa um mecanismo de autenticação personalizado para se autenticar no serviço de token. Se o dispositivo for autenticado com êxito, o serviço de token emitirá um token SAS para o dispositivo a ser usado para acessar o hub IoT.

### <a name="twin-graph-or-digital-twin-graph"></a>Gráfico de entrelaçamento (ou gráfico de entrelaçamento digital)

No serviço de [gêmeos digital do Azure](../digital-twins/index.yml) , você pode conectar o [gêmeos digital](#digital-twin) com [relações](#relationship) para criar grafos de conhecimento que representam digitalmente todo o seu ambiente físico. Uma única [instância de gêmeos digital do Azure](#azure-digital-twins-instance) pode hospedar muitos grafos desconectados ou um único grafo interconectado.

### <a name="twin-queries"></a>Consultas de gêmeos

As [consultas de dispositivo e módulo gêmeo](../iot-hub/iot-hub-devguide-query-language.md) usam a linguagem de consulta do Hub IoT semelhante ao SQL para recuperar informações dos seus dispositivos e módulos gêmeos. Você pode usar a mesma linguagem de consulta do Hub IoT para recuperar informações sobre um [trabalho](#job) em execução no Hub IOT.

### <a name="twin-synchronization"></a>Sincronização gêmea

A sincronização gêmea usa as [propriedades desejadas](#desired-properties) em dispositivos ou módulos gêmeos para configurar seus dispositivos e recuperar as [propriedades relatadas](#reported-properties) de dispositivos ou módulos para armazenar no gêmeo.

## <a name="u"></a>U

### <a name="upstream-services"></a>Serviços upstream

Um termo relativo que descreve os serviços que alimentam dados no contexto atual. Por exemplo, se você estiver pensando no contexto do Azure digital gêmeos, o Hub IoT será considerado um serviço upstream porque os dados fluem do Hub IoT para o gêmeos digital do Azure.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certificado de cliente X.509

Um dispositivo pode usar um certificado X.509 para se autenticar no [Hub IoT](#iot-hub). O uso de um certificado x.509 é uma alternativa ao uso de um [token SAS](#shared-access-signature).

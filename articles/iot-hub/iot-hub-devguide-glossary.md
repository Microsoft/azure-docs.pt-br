---
title: Glossário de termos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – um glossário explicando alguns dos termos comuns usados nos artigos do Hub IoT do Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
- devx-track-azurecli
ms.openlocfilehash: 3a75b3622ebc2085dac898e2a06e09f16466cbf5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746605"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário de termos do Hub IoT

Este artigo lista alguns dos termos comuns usados nos artigos do Hub IoT.

## <a name="advanced-message-queueing-protocol"></a>Procolo AMQP

O [AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) é um dos protocolos de mensagens com suporte do [Hub IoT](#iot-hub) para comunicação com dispositivos. Para saber mais sobre os protocolos de mensagens com suporte do Hub IoT, consulte [Enviar e receber mensagens com o Hub IoT](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gerenciamento de Dispositivo Automático

O gerenciamento automático do dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Com o gerenciamento automático do dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo.  Consiste em [configurações de dispositivo automáticas](./iot-hub-automatic-device-management.md) e [implantações automáticas do IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Configuração de dispositivo automática

O back-end da sua solução pode usar [configurações de dispositivo automáticas](./iot-hub-automatic-device-management.md) para atribuir as propriedades desejadas a um conjunto de [dispositivos gêmeos](#device-twin) e relatar o status usando métricas do sistema e métricas personalizadas. 

## <a name="azure-classic-cli"></a>CLI clássica do Azure

O [CLI clássico do Azure](/cli/azure/install-classic-cli) é uma ferramenta de comando baseada em shell, de plataforma cruzada e código aberto para criar e gerenciar recursos no Microsoft Azure. Esta versão da CLI deve ser usada apenas para implantações clássicas.

## <a name="azure-cli"></a>CLI do Azure

A [CLI do Azure](/cli/azure/install-az-cli2) é uma ferramenta de comando de plataforma cruzada, de software livre, baseada em shell para criar e gerenciar os recursos no Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure

Há _SDKs de dispositivo_ disponíveis para vários idiomas que permitem que você crie [aplicativos de dispositivo](#device-app) que interagem com um hub IoT. Os tutoriais do Hub IoT mostram como usar esses SDKs de dispositivo. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs de dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-explorer"></a>Gerenciador de IoT do Azure

O [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer) é usado para exibir a telemetria que o dispositivo está enviando, trabalhar com propriedades de dispositivo e chamar comandos. Você também pode usar o Explorer para interagir e testar seus dispositivos e para gerenciar dispositivos plug and Play.

## <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure

Há _SDKs de serviço_ disponíveis para vários idiomas que permitem que você crie [aplicativos de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais do Hub IoT mostram como usar esses SDKs de serviço. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-tools"></a>Ferramentas do Azure IoT

As [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão multiplataforma do Visual Studio Code, o código-fonte aberto que ajuda você a gerenciar dispositivos no VS Code e o Hub IoT do Azure. Com as Ferramentas do Azure IoT, os desenvolvedores de IoT podem desenvolver o projeto de IoT no VS Code com facilidade.

## <a name="azure-portal"></a>Portal do Azure

O [portal do Microsoft Azure](https://portal.azure.com) é um local central em que você pode provisionar e gerenciar seus recursos do Azure. Ele organiza seu conteúdo usando _folhas_ .

## <a name="azure-powershell"></a>Azure PowerShell

O [Azure PowerShell](/powershell/azure/) é uma coleção de cmdlets que você pode usar para gerenciar o Azure com o Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) permite que você trabalhe com os recursos em sua solução como um grupo. Você pode implantar, atualizar ou excluir os recursos da sua solução em uma única operação coordenada.

## <a name="azure-service-bus"></a>Barramento de Serviço do Azure

O [barramento de serviço](../service-bus-messaging/index.yml) fornece comunicação habilitada para nuvem com mensagens corporativas e comunicação retransmitida que ajuda você a conectar soluções locais com a nuvem. Alguns tutoriais do Hub IoT utilizam [filas](../service-bus-messaging/service-bus-messaging-overview.md) do Barramento de Serviço.

## <a name="azure-storage"></a>Armazenamento do Azure

O [Armazenamento do Azure](../storage/common/storage-introduction.md) é uma solução de armazenamento de nuvem. Ele inclui o serviço Armazenamento de Blobs que você pode usar para armazenar dados de objetos não estruturados. Alguns tutoriais do Hub IoT usam o armazenamento de blobs.

## <a name="back-end-app"></a>Aplicativo de back-end

No contexto do [Hub IoT](#iot-hub), um aplicativo de back-end é um aplicativo que se conecta a um dos pontos de extremidade voltados para o serviço em um hub IoT. Por exemplo, um aplicativo de back-end pode recuperar mensagens do [dispositivo para nuvem](#device-to-cloud) ou gerenciar o [registro de identidade](#identity-registry). Normalmente, um aplicativo de back-end é executado na nuvem, mas em muitos dos tutoriais dos aplicativos de back-end há aplicativos de console em execução no seu computador de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos de extremidade internos

Cada hub IoT inclui um [ponto de extremidade](iot-hub-devguide-endpoints.md) interno que é compatível com o Hub de Eventos. Você pode usar qualquer mecanismo que funciona com os Hubs de Eventos para ler mensagens de dispositivo para nuvem desse ponto de extremidade.

## <a name="cloud-gateway"></a>Gateway de nuvem

Um gateway de nuvem permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Um gateway de nuvem é hospedado na nuvem em comparação a um [gateway de campo](#field-gateway) executado no local para seus dispositivos. Um caso de uso típico para um gateway de nuvem é implementar a conversão de protocolo para seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem para o dispositivo

Refere-se às mensagens enviadas de um hub IoT para um dispositivo conectado. Geralmente, essas mensagens são comandos que instruem o dispositivo a executar uma ação. Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configuração

No contexto da [configuração de dispositivo automática](./iot-hub-automatic-device-management.md), uma configuração no Hub IoT define a configuração desejada para um conjunto de dispositivos gêmeos e fornece um conjunto de métricas para relatar o status e o progresso.

## <a name="connection-string"></a>Cadeia de conexão

Você pode usar cadeias de conexão no código do aplicativo para encapsular as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço das informações de segurança e de ponto de extremidade, mas os formatos da cadeia de conexão variam de acordo com os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- *Cadeias de conexão do dispositivo* permitem que os dispositivos se conectem aos pontos de extremidade voltados para o dispositivo em um Hub IoT.

- *Cadeias de conexão do Hub IoT* permitem que os aplicativos de back-end se conectem aos pontos de extremidade voltados para o serviço em um Hub IoT.

## <a name="custom-endpoints"></a>Pontos de extremidade personalizados

Você pode criar [pontos de extremidade](iot-hub-devguide-endpoints.md) em um hub IoT entregar as mensagens enviadas por uma [regra de roteamento](#routing-rules). Pontos de extremidade personalizados se conectar diretamente a um hub de eventos, uma fila do barramento de serviço ou um tópico do barramento de serviço.

## <a name="custom-gateway"></a>Gateway personalizado

Um gateway permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Você pode usar o Azure IoT Edge para criar gateways personalizados que implementam a lógica personalizada para lidar com mensagens, conversões de protocolo personalizado e outros processamentos no Edge.

## <a name="data-point-message"></a>Mensagem de ponto de dados

Uma mensagem de ponto de dados é uma mensagem do [dispositivo para nuvem](#device-to-cloud) que contém dados de [telemetria](#telemetry), como velocidade do vento ou temperatura.

## <a name="desired-configuration"></a>Configuração desejada

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), a configuração desejada refere-se ao conjunto completo de propriedades e de metadados no dispositivo gêmeo que deve ser sincronizado com o dispositivo.

## <a name="desired-properties"></a>Propriedades desejadas

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), as propriedades desejadas compõem uma subseção do dispositivo gêmeo usado com as [porpriedades relatadas](#reported-properties) para sincronizar a configuração ou a condição do dispositivo. As propriedades desejadas só podem ser definidas por um [aplicativo de back-end](#back-end-app) e são observadas pelo [aplicativo de dispositivo](#device-app).

## <a name="device-to-cloud"></a>Dispositivo para nuvem

Refere-se às mensagens enviadas de um dispositivo conectado ao [Hub IoT](#iot-hub). Essas mensagens podem ser do tipo [ponto de dados](#data-point-message) ou [interativas](#interactive-message). Para saber mais, confira [Enviar e receber mensagens com o Hub IoT](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo

No contexto do IoT, um dispositivo é normalmente um dispositivo de computação autônomo de pequena escala que pode coletar dados ou controlar outros dispositivos. Um dispositivo pode ser, por exemplo, um dispositivo de monitoramento ambiental ou um controlador de sistemas de rega e ventilação em uma estufa. O [catálogo de dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para o trabalho com o [Hub IoT](#iot-hub).

## <a name="device-app"></a>Aplicativo do dispositivo

Um aplicativo de dispositivo é executado em seu [dispositivo](#device) e gerencia a comunicação com o [hub IoT](#iot-hub). Normalmente, você usa um dos [SDKs de dispositivo IoT do Azure](#azure-iot-device-sdks) ao implementar um aplicativo de dispositivo. Em muitos dos tutoriais de IoT, você deve usar um [dispositivo simulado](#simulated-device) para sua conveniência.

## <a name="device-condition"></a>Condição do dispositivo

Refere-se às informações de estado do dispositivo, como o método de conectividade em uso no momento, conforme relatado por um [aplicativo de dispositivo](#device-app). Os [aplicativos de dispositivo](#device-app) também podem relatar suas funcionalidades. Você pode consultar informações de condição e de funcionalidade usando dispositivos gêmeos.

## <a name="device-data"></a>Dados do dispositivo

Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registro de identidade](#identity-registry) do Hub IoT. É possível importar e exportar esses dados.

## <a name="device-explorer"></a>Gerenciador de dispositivos

O Gerenciador de dispositivos foi substituído pelo [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer), que é usado para exibir a telemetria que o dispositivo está enviando, trabalhar com propriedades de dispositivo e chamar comandos. Você também pode usar o Explorer para interagir e testar seus dispositivos e para gerenciar dispositivos plug and Play.

## <a name="device-identity"></a>Identidade do dispositivo

A identidade do dispositivo é o identificador exclusivo atribuído a cada dispositivo registrado no [registro de identidade](#identity-registry).

## <a name="device-management"></a>Gerenciamento de dispositivo

O gerenciamento de dispositivos engloba o ciclo de vida completo associado ao gerenciamento dos dispositivos na sua solução de IoT incluindo planejamento, provisionamento, configuração, monitoramento e desativação.

## <a name="device-management-patterns"></a>Padrões de gerenciamento de dispositivos

O [hub IoT](#iot-hub) permite que os padrões comuns de gerenciamento de dispositivos, incluindo reinicialização, execução de redefinições de fábrica e execução de atualizações de firmware nos seus dispositivos.

## <a name="device-rest-api"></a>APIs REST do dispositivo

Você pode usar a [API REST de Dispositivo](/rest/api/iothub/device) de um dispositivo para enviar mensagens de dispositivo para a nuvem a um hub IoT e receber mensagens da [nuvem para o dispositivo](#cloud-to-device) de um hub IoT. Normalmente, você deve usar um dos [SDKs de dispositivo](#azure-iot-device-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

## <a name="device-provisioning"></a>Provisionamento de dispositivos

O provisionamento de dispositivos é o processo de adicionar os dados iniciais do [dispositivo](#device-data) às lojas em sua solução. Para permitir que um novo dispositivo se conecte ao seu hub, você deve adicionar uma ID de dispositivo e chaves ao [registro de identidade](#identity-registry)do Hub IOT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução.

## <a name="device-twin"></a>Dispositivo gêmeo

Um [dispositivo gêmeo](iot-hub-devguide-device-twins.md) é um documento JSON que armazena informações de estado do dispositivo, como metadados, configurações e condições. O [Hub IoT](#iot-hub) persiste um dispositivo gêmeo para cada dispositivo que você provisiona no hub IoT. Os dispositivos gêmeos permitem sincronizar [condições de dispositivo](#device-condition) e configurações entre o dispositivo e o back-end da solução. Você pode consultar dispositivos gêmeos para localizar dispositivos específicos e consultar o status de operações de longa duração.

## <a name="direct-method"></a>Método direto

Um [método direto](iot-hub-devguide-direct-methods.md) é uma maneira de disparar um método a fim de executá-lo em um dispositivo chamando uma API em seu hub IoT.

## <a name="endpoint"></a>Ponto de extremidade

Um hub IoT expõe vários [pontos de extremidade](iot-hub-devguide-endpoints.md) que permitem que seus aplicativos se conectem ao hub IoT. Há pontos de extremidade voltados para dispositivos que permitem que dispositivos executem operações como o envio de mensagens do [dispositivo para a nuvem](#device-to-cloud) e o recebimento de mensagens da [nuvem para o dispositivo](#cloud-to-device). Há pontos de extremidade de gerenciamento de voltados para serviço que permitem que [aplicativos de back-end](#back-end-app) executem operações como o gerenciamento de [identidade de dispositivo](#device-identity) e o gerenciamento de dispositivos gêmeos. Há voltado para o serviço [pontos de extremidade internos](#built-in-endpoints) para ler mensagens de dispositivo para a nuvem. Você pode criar [pontos de extremidade personalizados](#custom-endpoints) para receber mensagens de dispositivo para nuvem enviadas por uma [regra de roteamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço Hubs de Eventos

[Hubs de Eventos](../event-hubs/event-hubs-about.md) é um serviço de entrada de dados altamente dimensionável que pode incluir milhões de eventos por segundo. O serviço permite processar e analisar as grandes quantidades de dados produzidas por aplicativos e dispositivos conectados. Para obter uma comparação com o serviço Hub IoT, veja [Comparação do Hub IoT do Azure e Hubs de Eventos do Azure](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Ponto de extremidade compatível com o Hub de Eventos

Para ler as mensagens do [dispositivo para a nuvem](#device-to-cloud) enviadas ao seu hub IOT, você pode se conectar a um ponto de extremidade em seu hub e usar qualquer método compatível com o Hub de eventos para ler essas mensagens. Entre os métodos compatíveis com o Hub de Eventos estão o uso de [SDKs dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md) e o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Gateway de campo

Um gateway de campo permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub) e normalmente é implantado localmente com seus dispositivos. Para saber mais, confira [O que é o Hub IoT do Azure?](about-iot-hub.md)

## <a name="free-account"></a>Conta gratuita

Você pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) para concluir os tutoriais do Hub IoT e experimentar o serviço Hub IoT (e outros serviços do Azure).

## <a name="gateway"></a>Gateway

Um gateway permite a conectividade para os dispositivos que não podem se conectar diretamente ao [Hub IoT](#iot-hub). Veja também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway) e [Gateway personalizado](#custom-gateway).

## <a name="identity-registry"></a>Registro de identidade

O [registro de identidade](iot-hub-devguide-identity-registry.md) é o componente interno de um hub IoT que armazena informações sobre os dispositivos individuais que têm permissão para se conectar a um hub IoT.

## <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem da [nuvem para o dispositivo](#cloud-to-device) que dispara uma ação imediata no back-end da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser registrada automaticamente em um sistema de CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Hub IoT

O Hub IoT é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Para saber mais, confira [O que é o Hub IoT do Azure?](about-iot-hub.md) Usando a [assinatura do Azure](#subscription), você pode criar hubs IoT para lidar com suas cargas de trabalho de mensagens IoT.

## <a name="iot-hub-metrics"></a>Métricas do Hub IoT

As [métricas do Hub IoT](monitor-iot-hub-reference.md#metrics) fornecem dados sobre o estado dos hubs IoT em sua [assinatura do Azure](#subscription). As métricas do Hub IoT permitem avaliar a integridade geral do serviço e dos dispositivos conectados a ele. As métricas do Hub IoT podem ajudar você a ver o que está acontecendo com o hub IoT e a investigar a causa raiz de problemas sem precisar entrar em contato com o suporte do Azure. Para saber mais, consulte [monitorar o Hub IOT](monitor-iot-hub.md).

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A [linguagem de consulta do Hub IOT](iot-hub-devguide-query-language.md) é uma linguagem semelhante a SQL que permite consultar seu [trabalho](#job) e dispositivo gêmeos.

## <a name="iot-hub-resource-rest-api"></a>API REST de Recursos do Hub IoT

Você pode usar a [API REST dos recursos do Hub IoT](/rest/api/iothub/iothubresource) para gerenciar os hubs IoT em sua [assinatura do Azure](#subscription) que executam operações como criar, atualizar e excluir hubs.

## <a name="iot-solution-accelerators"></a>Aceleradores de solução do IoT

Os aceleradores de solução do Azure IoT incluem vários serviços do Azure para oferecer diferentes soluções. Essas soluções permitem que você realize rapidamente implementações de ponta a ponta de cenários comuns de IoT. Para obter mais informações, consulte [O que são os aceleradores de solução do Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>A extensão IoT para o CLI do Azure 

[A extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando de plataforma cruzada. A ferramenta permite que você gerencie seus dispositivos no [registro de identidade](#identity-registry), envie e receba mensagens e arquivos de seus dispositivos e monitore as operações do hub IoT.

## <a name="job"></a>Trabalho

O back-end da solução pode usar [trabalhos](iot-hub-devguide-jobs.md) para agendar e acompanhar atividades em um conjunto de dispositivos registrados com o Hub IOT. As atividades incluem a atualização de [propriedades desejadas](#desired-properties)do dispositivo, atualizando as [marcas](#tags)do dispositivo e invocando [métodos diretos](#direct-method). O [Hub IoT](#iot-hub) também usa  para [importar e exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do [registro de identidade](#identity-registry).

## <a name="modules"></a>Módulos

No lado do dispositivo, os SDKs do dispositivo do Hub IoT permitem criar [módulos](iot-hub-devguide-module-twins.md) em que cada um abre uma conexão independente com o Hub IoT. Essa funcionalidade permite usar namespaces separados para diferentes componentes em seu dispositivo.

A identidade do módulo e o módulo gêmeo fornecem os mesmos recursos que a [identidade do dispositivo](#device-identity) e o [dispositivo gêmeo](#device-twin), mas com granularidade mais precisa. Essa granularidade mais fina permite que dispositivos capazes, como dispositivos baseados em sistema operacional ou dispositivos de firmware que gerenciam vários componentes, isolem a configuração e as condições de cada um desses componentes.

## <a name="module-identity"></a>Identidade de módulo

A identidade de módulo é o identificador exclusivo atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também é registrada no [registro de identidade](#identity-registry).

## <a name="module-twin"></a>Módulo Gêmeo

Semelhante a um dispositivo gêmeo, um módulo gêmeo é um documento JSON que armazena informações de estado do módulo, como metadados, configurações e condições. O Hub IoT persiste um módulo gêmeo para cada identidade de módulo que você provisionar com uma identidade do dispositivo no seu Hub IoT. Os módulos gêmeos permitem sincronizar condições de módulo e configurações entre o módulo e o back-end da solução. Você pode consultar módulos gêmeos para localizar módulos específicos e consultar o status de operações de longa duração.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) é um dos protocolos de mensagens que o [Hub IOT](#iot-hub) dá suporte para comunicação com dispositivos. Para saber mais sobre os protocolos de mensagens com suporte do Hub IoT, consulte [Enviar e receber mensagens com o Hub IoT](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitoramento de operações

O [monitoramento de operações](iot-hub-operations-monitoring.md) do Hub IOT permite monitorar o status das operações no Hub IOT em tempo real. O [Hub IOT](#iot-hub) rastreia eventos em várias categorias de operações. Você pode aceitar o envio de eventos de uma ou mais categorias para um ponto de extremidade de Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

## <a name="physical-device"></a>Dispositivo físico

Um dispositivo físico é um dispositivo real como um Raspberry Pi que se conecta a um hub IoT. Para sua conveniência, muitos dos tutoriais do Hub IoT usam [dispositivos simulados](#simulated-device) para que você possa executar os exemplos em seu computador local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias

Quando você se conecta a um ponto de extremidade voltado para o dispositivo ou voltado para o serviço em um hub IoT, sua [cadeia de conexão](#connection-string) inclui a chave para conceder acesso. Quando você adiciona um dispositivo ao [registro de identidade](#identity-registry) ou adiciona uma [política de acesso compartilhado](#shared-access-policy) para o hub, o serviço gera uma chave primária e secundária. Ter duas chaves permite que você passe de uma chave para outra quando você atualiza uma chave sem perder o acesso ao hub IoT.

## <a name="protocol-gateway"></a>Gateway de protocolo

Um gateway de protocolo normalmente é implantado na nuvem e fornece serviços de conversão de protocolo para dispositivos que se conectam ao [Hub IOT](#iot-hub). Para saber mais, confira [O que é o Hub IoT do Azure?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Cotas e limitação

Há várias [cotas](iot-hub-devguide-quotas-throttling.md) que se aplicam ao uso do [Hub IoT](#iot-hub), muitas delas variam de acordo com a camada do hub IoT. O [Hub IoT](#iot-hub) também aplica [limitações](iot-hub-devguide-quotas-throttling.md) ao uso do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração relatada

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), a configuração relatada refere-se ao conjunto completo de propriedades e de metadados no dispositivo gêmeo que deve ser relatado para o back-end da solução.

## <a name="reported-properties"></a>Propriedades reportadas

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), as propriedades relatadas compõem uma subseção do dispositivo gêmeo usado com [propriedades desejadas](#desired-properties) para sincronizar a configuração ou a condição do dispositivo. As propriedades relatadas só podem ser definidas pelo [aplicativo de dispositivo](#device-app) e podem ser lidas e consultadas pelo [aplicativo de back-end](#back-end-app).

## <a name="resource-group"></a>Resource group

O [Azure Resource Manager](#azure-resource-manager) usa grupos de recursos para agrupar recursos relacionados. Você pode usar um grupo de recursos para executar operações em todos os recursos no grupo simultaneamente.

## <a name="retry-policy"></a>Política de Repetição

Use uma política de repetição para tratar [erros transitórios](/azure/architecture/best-practices/transient-faults) quando você se conectar a um serviço de nuvem.

## <a name="routing-rules"></a>Regras de roteamento

Configurar [as regras de roteamento](iot-hub-devguide-messages-read-custom.md) em seu hub IoT para rotear mensagens de dispositivo para a nuvem para um [ponto de extremidade interno](#built-in-endpoints) ou [pontos de extremidade personalizados](#custom-endpoints) para processamento pelo back-end da sua solução.

## <a name="sasl-plain"></a>SASL SIMPLES

SASL PLAIN é um protocolo que o protocolo AMQP usa para transferir tokens de segurança.

## <a name="service-rest-api"></a>API REST do Serviço

Você pode usar a [API REST de Serviço](/rest/api/iothub/service/configuration) da solução de back-end para gerenciar seus dispositivos. A API permite recuperar e atualizar as propriedades de [dispositivos gêmeos](#device-twin) e chamar [métodos diretos](#direct-method) e [trabalhos](#job) programados. Normalmente, você deve usar um dos [SDKs de serviço](#azure-iot-service-sdks) de nível mais alto, como mostrado nos tutoriais de Hub IoT.

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As SAS (Assinaturas de Acesso Compartilhado) são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. A autenticação de SAS tem dois componentes: uma _Política de acesso Compartilhado_ e uma _Assinatura de Acesso Compartilhado_ (normalmente chamada de token). Um dispositivo usa SAS para se autenticar em um hub IoT. Os [aplicativos de back-end](#back-end-app) também usam SAS para se autenticar com os pontos de extremidade voltados para o serviço em um hub IoT. Normalmente, você pode incluir o token SAS na [cadeia de conexão](#connection-string) que um aplicativo usa para estabelecer uma conexão com um hub IoT.

## <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma política de acesso compartilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Você pode gerenciar as chaves e as políticas de acesso compartilhadas para seu hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulado

Para sua conveniência, muitos dos tutoriais do Hub IoT usam dispositivos simulados para que você possa executar os exemplos em seu computador local. Por outro lado, um [dispositivo físico](#physical-device) é um dispositivo real como um Raspberry Pi que se conecta a um hub IoT.

## <a name="solution"></a>Solução
Uma _solução_ pode se referir a uma solução do Visual Studio que inclua um ou mais projetos. Uma _solução_ também pode se referir a uma solução IoT que inclua elementos como dispositivos, [aplicativos de dispositivo](#device-app), um hub IoT, outros serviços do Azure e [aplicativos de back-end](#back-end-app).

## <a name="subscription"></a>Subscription

Uma assinatura do Azure é onde ocorre a cobrança. Cada recurso do Azure que você cria ou serviço do Azure que usa está associado a uma única assinatura. Muitas cotas também se aplicam ao nível de uma assinatura.

## <a name="system-properties"></a>Propriedades do sistema

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), as propriedades do sistema são somente leitura em incluem informações sobre o uso do dispositivo, por exemplo, a hora da última atividade e o estado da conexão.

## <a name="tags"></a>Marcas

No contexto de um [dispositivo gêmeo](iot-hub-devguide-device-twins.md), as marcas são metadados do dispositivo armazenadas e recuperadas pelo back-end da solução na forma de um documento JSON. As marcas não são visíveis para os aplicativos em um dispositivo.

## <a name="telemetry"></a>Telemetria

Os dispositivos coletam dados de telemetria, como a velocidade do vento ou a temperatura, e usam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

## <a name="token-service"></a>Serviço de token

Você pode usar um serviço de token para implementar um mecanismo de autenticação para seus dispositivos. Ele usa uma [política de acesso compartilhado](#shared-access-policy) do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped* . Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT. Um dispositivo usa um mecanismo de autenticação personalizado para se autenticar no serviço de token. Se o dispositivo for autenticado com êxito, o serviço de token emitirá um token SAS para o dispositivo a ser usado para acessar o hub IoT.

## <a name="twin-queries"></a>Consultas de gêmeos

As [consultas de dispositivo e módulo gêmeo](iot-hub-devguide-query-language.md) usam a linguagem de consulta do Hub IoT semelhante ao SQL para recuperar informações dos seus dispositivos e módulos gêmeos. Você pode usar a mesma linguagem de consulta do Hub IoT para recuperar informações sobre um [trabalho](#job) em execução no Hub IOT.

## <a name="twin-synchronization"></a>Sincronização gêmea

A sincronização gêmea usa as [propriedades desejadas](#desired-properties) em dispositivos ou módulos gêmeos para configurar seus dispositivos e recuperar as [propriedades relatadas](#reported-properties) de dispositivos ou módulos para armazenar no gêmeo.

## <a name="x509-client-certificate"></a>Certificado de cliente X.509

Um dispositivo pode usar um certificado X.509 para se autenticar no [Hub IoT](#iot-hub). O uso de um certificado x.509 é uma alternativa ao uso de um [token SAS](#shared-access-signature).

---
title: Visão geral do acelerador de solução de monitoramento remoto | Microsoft Docs
description: Implantar o acelerador da solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442522"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Visão geral do acelerador de solução de Monitoramento Remoto

O [acelerador de solução](../iot-accelerators/about-iot-accelerators.md) de Monitoramento Remoto implementa uma solução de monitoramento de ponta a ponta para vários computadores em locais remotos. A solução combina os principais serviços do Azure para fornecer uma implementação genérica de cenário de negócios. Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizá-la](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) para atender às suas próprias necessidades de negócios específicas.

Este artigo explica alguns dos principais elementos da solução de monitoramento remoto para que você possa entender como ela funciona. Esse conhecimento ajuda a:

* Solucionar problemas na solução.
* Planejar como personalizar a solução para atender a seus próprios requisitos específicos.
* Criar sua própria solução IoT que usa os serviços do Azure.

O código do acelerador de solução de monitoramento remoto está disponível no GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes lógicos do acelerador de solução de monitoramento remoto sobreposta na [arquitetura da IoT](../iot-fundamentals/iot-introduction.md):

![Arquitetura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que os microsserviços?

A arquitetura de nuvem evoluiu desde que a Microsoft lançou os primeiros aceleradores de solução. Os [microsserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) foram lançados como uma prática comprovada para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Vários serviços da Microsoft usam esse padrão de arquitetura internamente, com resultados de escalabilidade e confiabilidade excelentes. Os aceleradores de solução atualizados colocam estas lições em prática para que você também possa se beneficiar delas.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviço, confira [Arquitetura do Aplicativo .NET](https://www.microsoft.com/net/learn/architecture) e [Microsserviços: Uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Conectividade do dispositivo

A solução inclui os seguintes componentes na parte de conectividade do dispositivo da arquitetura lógica:

### <a name="real-devices"></a>Dispositivos reais

Você pode conectar dispositivos reais à solução. Você pode implementar o comportamento de seus dispositivos simulados usando as SDKs do dispositivo IoT do Azure.

Você pode provisionar dispositivos reais pelo dashboard no portal da solução.

### <a name="device-simulation-microservice"></a>Microsserviço de simulação de dispositivo

A solução inclui o [microsserviço de simulação de dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) que permite que você gerencie um pool de dispositivos simulados do portal de solução para testar o fluxo de ponta a ponta na solução. Os dispositivos simulados:

* Geram telemetria do dispositivo para a nuvem.
* Respondem a chamadas de método da nuvem para o dispositivo do Hub IoT.

O microsserviço fornece um ponto de extremidade RESTful para criar, iniciar e interromper as simulações. Cada simulação consiste em um conjunto de dispositivos virtuais de tipos diferentes, que enviam a telemetria e respondem a chamadas de método.

Você pode provisionar os dispositivos simulados do painel no portal de solução.

### <a name="iot-hub"></a>Hub IoT

O [Hub IoT](../iot-hub/index.yml) absorve os dados de telemetria enviados dos dispositivos reais e simulados para a nuvem. O hub IoT disponibiliza a telemetria para os serviços no back-end na solução de IoT para processamento.

O hub IoT na solução também:

* Mantém um registro de identidade que armazena as IDs e as chaves de autenticação de todos os dispositivos permitidos para se conectar ao portal.
* Chama métodos em seus dispositivos em nome do acelerador de solução.
* Mantém dispositivos gêmeos para todos os dispositivos registrados. Um dispositivo gêmeo armazena os valores de propriedade relatados por um dispositivo. Um dispositivo gêmeo também armazena propriedades desejadas, definidas no portal de solução para o dispositivo recuperar ao lado se conectar.
* Agenda trabalhos para definir propriedades para vários dispositivos ou chama métodos em vários dispositivos.

## <a name="data-processing-and-analytics"></a>Processamento de dados e análise

A solução inclui os seguintes componentes na parte de análise da arquitetura lógica e processamento de dados:

### <a name="iot-hub-manager-microservice"></a>Microsserviço de Gerenciador de Hub IoT

A solução inclui o [microsserviço do gerente de Hub IoT](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) para manipular as interações com o Hub IoT como:

* Criação e gerenciamento de dispositivos de IoT.
* Gerenciamento de dispositivos gêmeos.
* Invocação de métodos em dispositivos.
* Gerenciamento de credenciais de IoT.

Este serviço também executa consultas do Hub IoT para recuperar os dispositivos que pertencem a grupos definidos pelo usuário.

O microsserviço fornece um ponto de extremidade RESTful para gerenciar os dispositivos e os dispositivos gêmeos, invocar métodos e executar consultas do Hub IoT.

### <a name="device-telemetry-microservice"></a>Microsserviço de telemetria do dispositivo

O [microsserviço de telemetria do dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fornece um ponto de extremidade RESTful para acesso de leitura a telemetria do dispositivo armazenada no Time Series Insights. O ponto de extremidade RESTful também permite as operações CRUD em regras e acesso de leitura/gravação para definições de alarme do armazenamento.

### <a name="storage-adapter-microservice"></a>Armazenar o microsserviço de adaptador de armazenamento

O [microsserviço do adaptador de armazenamento](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) gerencia pares de chave-valor, abstraindo a semântica do serviço de armazenamento e apresentar uma interface simples para armazenar dados de qualquer formato usando o Azure Cosmos DB.

Os valores são organizados em coleções. Você pode trabalhar em valores individuais ou buscar coleções inteiras. Estruturas de dados complexas são serializadas pelos clientes e gerenciadas como conteúdo de texto simples.

O serviço fornece um ponto de extremidade RESTful para operações CRUD em pares chave-valor. valores

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

As implantações do acelerador de solução usam o [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para armazenar definições de configuração, alertas, regras e todos os outros armazenamentos frios.

### <a name="azure-stream-analytics-manager-microservice"></a>Microsserviço de Gerenciador de Stream Analytics do Azure

O [microsserviço do gerente do Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gerencia trabalhos do Azure Stream Analytics (ASA), incluindo a configuração de sua configuração, iniciando e interrompendo-os e monitorando seu status.

O trabalho ASA é compatível com dois conjuntos de dados de referência. Um conjunto de dados define as regras e um deles define grupos de dispositivos. Os dados de referência de regras são gerados a partir das informações gerenciadas pelo microsserviço de telemetria do dispositivo. O microsserviço do gerente do Azure Stream Analytics transforma as regras de telemetria em lógica de processamento de fluxo.

Os dados de referência de grupos de dispositivo são usados para identificar qual grupo de regras aplicar a uma mensagem de telemetria de entrada. Os grupos de dispositivos são gerenciados pelo microsserviço de configuração e usam consultas idênticas de dispositivo do Hub IoT do Azure.

Os trabalhos ASA entregam a telemetria dos dispositivos conectados ao Time Series Insights para análise e armazenamento.

### <a name="azure-stream-analytics"></a>Stream Analytics do Azure

O [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) é um mecanismo de processamento de eventos que permite examinar grandes volumes de fluxo de dados de dispositivos.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

O [Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) armazena a telemetria dos dispositivos conectada ao acelerador da solução. Ele também permite visualizar e consultar a telemetria do dispositivo na web de solução da interface do usuário.

> [!NOTE]
> Os Time Series Insights não estão disponíveis atualmente na nuvem do Azure China. As novas implantações do acelerador de solução de monitoramento remoto na nuvem do Azure China usam o Cosmos DB para todo o armazenamento.

### <a name="configuration-microservice"></a>Microsserviço de configuração

A [configuração do microsserviço ](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornece um ponto de extremidade RESTful para operações CRUD nos grupos de dispositivos, as configurações da solução e configurações de usuário do acelerador de solução. Funciona com o microsserviço de adaptador de armazenamento para manter os dados de configuração.

### <a name="authentication-and-authorization-microservice"></a>Detalhes de autenticação e autorização do microsserviço

O [microsserviço de autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gerencia os usuários autorizados a acessar o acelerador da solução. Gerenciamento de usuário pode ser feito usando qualquer provedor de serviços de identidade que dá suporte a [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Implantações de acelerador de solução usam o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) como um provedor do OpenID Connect. O Azure Active Directory armazena informações de usuário e fornece certificados para validar assinaturas de Token JWT.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes na parte de apresentação da arquitetura lógica:

A [interface do usuário da web é um aplicativo do React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). O aplicativo:

* Usa somente o Javascript React e é executado totalmente no navegador.
* É projetado com CSS.
* Interage com microsserviços voltados ao público por meio de chamadas AJAX.

A interface do usuário apresenta todas as funcionalidades do acelerador de solução e interage com outros microsserviços, como:

* O microsserviço de autenticação e autorização para proteger os dados de usuário.
* Microsserviço do gerenciador Hub IoT para listar e gerenciar os dispositivos de IoT.

A interface do usuário integra-se ao Azure Time Series Insights Explorer para habilitar a análise de telemetria do dispositivo e consulta.

O microsserviço de configuração habilita a interface do usuário para armazenar e recuperar as definições de configuração.

## <a name="next-steps"></a>Próximas etapas

Se você quiser explorar a documentação do desenvolvedor e do código-fonte, inicie com um os dois repositórios GitHub:

* [Acelerador de solução para monitoramento remoto com o Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Acelerador de solução para monitoramento remoto com o Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas detalhados de arquitetura de solução:
* [ Acelerador de solução para arquitetura de monitoramento remoto ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Para obter informações mais conceituais sobre o acelerador de solução de monitoramento remoto, consulte [ Personalizar o acelerador de solução ](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).

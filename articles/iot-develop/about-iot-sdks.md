---
title: Visão geral das opções de SDK do dispositivo IoT do Azure
description: Saiba qual SDK do dispositivo IoT do Azure usar com base na função e nas tarefas de desenvolvimento.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c9624e9a23d005185429c82199324ac570cbd63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607723"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Visão geral de SDKs do dispositivo IoT do Azure

Os SDKs do dispositivo IoT do Azure são um conjunto de bibliotecas de clientes, guias do desenvolvedor, exemplos e documentações do dispositivo. Os SDKs do dispositivo ajudam você a conectar dispositivos aos serviços IoT do Azure de modo programático.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Um diagrama mostrando vários SDKs de IoT do Azure":::

Conforme mostrado no diagrama, há vários SDKs do dispositivo disponíveis para atender às suas necessidades de dispositivo e linguagem de programação. Diretrizes sobre como selecionar o SDK do dispositivo apropriado estão disponíveis em [Qual SDK devo usar](#which-sdk-should-i-use). Também há SDKs do serviço IoT do Azure disponíveis para conectar seu aplicativo baseado em nuvem aos serviços IoT do Azure no back-end. Os SDKs do dispositivo são o foco deste artigo, porém é possível saber mais sobre os SDKs do serviço do Azure [aqui](#service-sdks).

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Por que devo usar SDKs do dispositivo IoT do Azure?

É possível criar uma camada de conexão personalizada ou usar SDKs do dispositivo IoT do Azure para conectar dispositivos à IoT do Azure. Usar SDKs do dispositivo IoT do Azure oferece várias vantagens:

| Custo de desenvolvimento &nbsp; &nbsp; &nbsp; &nbsp; | Camada de conexão personalizada | SDKs do dispositivo IoT do Azure |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Suporte | Precisa dar suporte ao que for criado, bem como documentar tudo | Tem acesso ao suporte da Microsoft (GitHub, Microsoft Q&A, Microsoft Docs e equipe de suporte ao cliente) |
| Novos recursos | Precisa adicionar recursos do Azure ao middleware personalizado | Pode aproveitar de modo imediato os novos recursos que a Microsoft adiciona de modo constante aos SDKs de IoT |
| Investimento | Investe centenas de horas de desenvolvimento integrado para projetar, criar, testar e manter uma versão personalizada | Pode aproveitar ferramentas gratuitas de software livre. O único custo associado aos SDKs é a curva de aprendizagem. |

## <a name="which-sdk-should-i-use"></a>Qual SDK devo usar?

Os SDKs do dispositivo IoT do Azure estão disponíveis em linguagens de programação populares, incluindo: C, C#, Java, Node.js e Python. É necessário fazer duas considerações básicas ao escolher um SDK: as funcionalidades do dispositivo e a familiaridade da equipe com a linguagem de programação.

### <a name="device-capabilities"></a>Funcionalidades de dispositivo

Ao escolher um SDK, será preciso considerar os limites dos dispositivos que você está usando. Um dispositivo restrito tem um MCU (microcontrolador) único e memória limitada. Caso esteja usando um dispositivo restrito, recomendamos usar um [SDK do C inserido](#embedded-c-sdk). Esse SDK foi projetado para fornecer um conjunto mínimo de funcionalidades para se conectar à IoT do Azure. Também é possível selecionar componentes (cliente MQTT, TLS e bibliotecas de soquete) que são mais otimizados para um dispositivo inserido. Caso o dispositivo restrito também execute o Azure RTOS, será possível usar o middleware do Azure RTOS para se conectar à IoT do Azure. O middleware do Azure RTOS encapsula o SDK do C inserido usando uma funcionalidade adicional para simplificar a conexão do dispositivo do Azure RTOS com a nuvem.

Um dispositivo irrestrito tem uma CPU mais robusta, compatível com a execução de um sistema operacional para dar suporte a um runtime de linguagem, como o .NET ou o Python. Caso esteja usando um dispositivo irrestrito, a principal consideração será a familiaridade com a linguagem.

### <a name="your-teams-familiarity-with-the-programming-language"></a>A familiaridade de sua equipe com uma linguagem de programação

Os SDKs do dispositivo IoT do Azure são implementados em várias linguagens para que seja possível escolher a linguagem de sua preferência. Os SDKs do dispositivo também se integram a outras ferramentas conhecidas e específicas a um idioma. Poder trabalhar com linguagens e ferramentas de desenvolvimento conhecidas, permite que sua equipe otimize o ciclo de desenvolvimento de pesquisa, os protótipos, o desenvolvimento de produtos e a manutenção contínua.

Sempre que possível, selecione um SDK que seja familiar para sua equipe de desenvolvimento. Todos os SDKs de IoT do Azure são de software livre e têm vários exemplos disponíveis para avaliação e teste antes de sua equipe fazer commit de um SDK específico.

## <a name="how-can-i-get-started"></a>Como posso começar?

Comece explorando repositórios GitHub dos SDKs do dispositivo do Azure. Também será possível experimentar um [guia de início rápido](quickstart-send-telemetry-python.md) que mostrará de que modo usar um SDK de maneira rápida para enviar telemetria à IoT do Azure.

As opções para começar dependerão do seu tipo de dispositivo:
- Use o [SDK do C inserido](#embedded-c-sdk) para dispositivos restritos. 
- É possível desenvolver usando o [middleware do Azure RTOS](#azure-rtos-middleware) para dispositivos que são executados no Azure RTOS. 
- É possível [escolher um SDK](#unconstrained-device-sdks) em uma linguagem de sua preferência para dispositivos irrestritos. 

### <a name="constrained-device-sdks"></a>SDKs de um dispositivo restrito
Esses SDKs são especializados em promover a execução em dispositivos com recursos limitados de computação ou memória. Para saber mais sobre tipos de dispositivos comuns, confira a [Visão geral dos tipos de dispositivos IoT do Azure](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>SDK do C inserido
* [Repositório GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Amostras](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Documentação de referência](https://azure.github.io/azure-sdk-for-c/)
* [Como criar um SDK do C inserido](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Gráfico de tamanhos para dispositivos restritos](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Middleware do Azure RTOS

* [Repositório GitHub](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Guias de Introdução](https://github.com/azure-rtos/getting-started) e [mais exemplos](https://github.com/azure-rtos/samples)
* [Documentação de referência](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDKs de um dispositivo irrestrito
Esses SDKs podem ser executados em dispositivos que dão suporte a um runtime de linguagem de ordem superior. Isso inclui dispositivos como PCs, Raspberry Pis e smartphones. Eles são diferenciados principalmente por linguagem para que seja possível escolher a biblioteca mais adequada para a equipe e o cenário.

#### <a name="c-device-sdk"></a>SDK do dispositivo C
* [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pacotes](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Documentação de referência](/azure/iot-hub/iot-c-sdk-ref/)
* [Documentação de referência do módulo do Edge](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Compilar o SDK do dispositivo C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Portabilidade do SDK C para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do desenvolvedor](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para obter informações sobre compilação cruzada e como começar em diferentes plataformas
* [Informações sobre o consumo de recursos do SDK do C do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>SDK do dispositivo C#

* [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Amostras](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pacote](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Documentação de referência](/dotnet/api/microsoft.azure.devices)
* [Documentação de referência do módulo do Edge](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>SDK do dispositivo Java

* [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pacote](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Documentação de referência](/java/api/com.microsoft.azure.sdk.iot.device)
* [Documentação de referência do módulo do Edge](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>SDK do dispositivo Node.js

* [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pacote](https://www.npmjs.com/package/azure-iot-device)
* [Documentação de referência](/javascript/api/azure-iot-device/)
* [Documentação de referência do módulo do Edge](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>SDK do dispositivo Python

* [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pacote](https://pypi.org/project/azure-iot-device/)
* [Documentação de referência](/python/api/azure-iot-device)
* [Documentação de referência do módulo do Edge](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>SDKs do Serviço
A IoT do Azure também oferece SDKs de serviços que permitem criar aplicativos em uma solução para gerenciar dispositivos, obter insights, visualizar dados e muito mais. Esses SDKs são específicos para cada serviço IoT do Azure. Além disso, eles estão disponíveis nas linguagens C#, Java, JavaScript e Python para simplificar sua experiência de desenvolvimento. 

#### <a name="iot-hub"></a>Hub IoT

Os SDKs do serviço Hub IoT permitem criar aplicativos que podem interagir de modo fácil com o Hub IoT para gerenciar dispositivos e a segurança. É possível usar esses SDKs para enviar mensagens da nuvem para dispositivo, invocar métodos diretos nos dispositivos, atualizar propriedades do dispositivo e muito mais.

[**Saiba mais sobre o Hub IoT**](https://azure.microsoft.com/services/iot-hub/) | [**Experimente controlar um dispositivo**](../iot-hub/quickstart-control-device-python.md)

**SDK do Serviço do Hub IoT no C#** : [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) | [Pacote](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [Exemplos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples) | [Documentação de Referência](/dotnet/api/microsoft.azure.devices)

**SDK do Serviço do Hub IoT no Java**: [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/service) | [Pacote](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) | [Exemplos](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples) | [Documentação de Referência](/java/api/com.microsoft.azure.sdk.iot.service)

**SDK do Serviço do Hub IoT no JavaScript**: [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [Pacote](https://www.npmjs.com/package/azure-iothub) | [Exemplos](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Documentação de Referência](/javascript/api/azure-iothub/)

**SDK do Serviço do Hub IoT no Python**: [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [Pacote](https://pypi.python.org/pypi/azure-iot-hub/) | [Exemplos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Documentação de Referência](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure são uma oferta de PaaS (plataforma como serviço) que permite a criação de grafos de conhecimento baseados em modelos digitais de ambientes inteiros. Esses ambientes podem ser edifícios, fábricas, fazendas, redes de energia, ferrovias, estádios e muito mais – até mesmo cidades inteiras. Esses modelos digitais podem ser usados para obter insights que impulsionam melhores produtos, operações otimizadas, custos reduzidos e experiências de clientes inovadoras. A IoT do Azure oferece SDKs de serviços para facilitar a criação de aplicativos que usam a potência dos Gêmeos Digitais do Azure.

[**Saiba mais sobre os Gêmeos Digitais do Azure**](https://azure.microsoft.com/services/digital-twins/) | [**Codificar um aplicativo do ADT**](../digital-twins/tutorial-code.md)

**SDK do Serviço do ADT no C#** : [Repositório GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [Pacote](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [Documentação de Referência](/dotnet/api/overview/azure/digitaltwins/client)

**SDK do Serviço do ADT no Java**: [Repositório GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Pacote](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [Exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [Documentação de Referência](/java/api/overview/azure/digitaltwins/client)

**SDK do Serviço do ADT na linguagem Node.js**: [Repositório GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [Pacote](https://www.npmjs.com/package/@azure/digital-twins-core) | [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [Documentação de Referência](/javascript/api/@azure/digital-twins-core/)

**SDK do Serviço do ADT no Python**: [Repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Pacote](https://pypi.org/project/azure-digitaltwins-core/) | [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [Documentação de Referência](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Serviço de Provisionamento de Dispositivos

O DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT é um serviço auxiliar para Hub IoT que permite o provisionamento Just-In-Time e sem toque no hub IoT correto sem exigir intervenção humana. O DPS permite obter um provisionamento de milhões de dispositivos de modo seguro e escalonável. Os SDKs do serviço DPS permitem criar aplicativos que podem gerenciar seus dispositivos com segurança, criando grupos de registro e executando operações em massa.

[**Saiba mais sobre o Serviço de Provisionamento de Dispositivos**](../iot-dps/index.yml) | [**Experimente criar um registro em grupo para dispositivos X.509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**SDK do Serviço de Provisionamento de Dispositivos no C#** : [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service) | [Pacote](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [Exemplos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples) | [Documentação de Referência](/dotnet/api/microsoft.azure.devices.provisioning.service)

**SDK do Serviço de Provisionamento de Dispositivos no Java**: [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src) | [Pacote](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [Exemplos](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [Documentação de Referência](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**SDK do Serviço de Provisionamento de Dispositivos Node.js**: [Repositório GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [Pacote](https://www.npmjs.com/package/azure-iot-provisioning-service) | [Exemplos](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [Documentação de Referência](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Próximas etapas

* [Guia de início rápido: conectar um dispositivo à IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Guia de início rápido: conectar um dispositivo ao Hub IoT (Python)](quickstart-send-telemetry-cli-python.md)
* [Introdução ao desenvolvimento inserido](quickstart-device-development.md)
* Saiba mais sobre os [benefícios de usar SDKs de IoT do Azure no desenvolvimento](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)
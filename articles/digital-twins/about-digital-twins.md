---
title: Visão geral dos Gêmeos Digitais do Azure | Microsoft Docs
description: Saiba mais sobre os Gêmeos Digitais do Azure, uma solução de IoT do Azure para inteligência espacial.
author: julieseto
ms.author: jseto
ms.date: 05/31/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 0bbbb924cace93e44af84fd96926a2d7153dd5f8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459015"
---
# <a name="overview-of-azure-digital-twins"></a>Visão geral dos Gêmeos Digitais do Azure

A versão prévia dos Gêmeos Digitais do Azure são um serviço de IoT do Azure que cria modelos abrangentes do ambiente físico. Eles podem criar grafos de inteligência espacial para modelar relações e interações entre pessoas, espaços e dispositivos.

Com os Gêmeos Digitais do Azure, você pode consultar dados de um espaço físico em vez de vários sensores diferentes. Esse serviço ajuda você a criar experiências reutilizáveis, altamente escalonáveis e com reconhecimento espacial que vinculam os dados de streaming entre o mundo digital e o mundo físico. Seus aplicativos são aprimorados por esses recursos contextuais exclusivamente relevantes. 

Os Gêmeos Digitais do Azure se aplicam a todos os tipos de ambientes, como, depósitos, escritórios, escolas, hospitais e bancos. Eles ainda podem ser usados em estádios, fábricas, estacionamentos, parques, grades inteligentes e cidades. Veja a seguir alguns cenários nos quais os Gêmeos Digitais do Azure podem ser úteis:

- Prever as necessidades de manutenção de uma fábrica.
- Analisar os requisitos de energia de uma rede elétrica em tempo real.
- Otimizar o uso de espaço disponível de um escritório.
- Controlar a temperatura diária em vários estados.
- Monitorar caminhos de drone ocupados.
- Identificar veículos autônomos.
- Analisar os níveis de ocupação de um prédio.
- Localizar a caixa registradora mais usada na sua loja.

Seja qual for seu cenário comercial, é provável que uma instância digital correspondente possa ser provisionada com os Gêmeos Digitais do Azure.

O vídeo a seguir examina mais detalhadamente os Gêmeos Digitais do Azure.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Principais recursos

Os Gêmeos Digitais do Azure tem estes recursos principais.

### <a name="spatial-intelligence-graph"></a>Grafo de inteligência espacial

O [*grafo de inteligência espacial*](./concepts-objectmodel-spatialgraph.md#graph), ou *grafo espacial*, é uma representação virtual do ambiente físico. Você pode usá-lo para modelar as relações entre pessoas, lugares e dispositivos.

Pense em um aplicativo de serviços essenciais inteligente que envolva vários medidores de uso de energia conectados em uma vizinhança. A empresa de serviços essenciais inteligente deve monitorar e prever o uso e a cobrança de uso de energia com precisão. Cada dispositivo e sensor deve ser modelado com contexto sobre o local e o cliente que está sendo cobrado. Você pode usar o grafo de inteligência espacial para modelar esses tipos de relações complexas.

### <a name="digital-twin-object-models"></a>Modelos de objeto dos Gêmeos Digitais

Os [modelos de objeto dos Gêmeos Digitais](./concepts-objectmodel-spatialgraph.md#model) são protocolos de dispositivo e esquema de dados predefinidos. Eles se alinham às necessidades específicas do domínio de sua solução para acelerar e simplificar o desenvolvimento.

Por exemplo, um aplicativo de ocupação de espaço pode usar tipos predefinidos de espaço, como campus, prédio, andar e sala.

### <a name="multiple-and-nested-tenants"></a>Locatários múltiplos e aninhados

Você pode criar soluções que podem ser dimensionadas com segurança e reutilizadas para vários locatários. Você também pode criar vários sublocatários que podem ser acessados e usados de maneira segura e isolada.

Um exemplo é um aplicativo de utilização de espaço que está configurado para isolar dados de um locatário de dados de outros locatários em um mesmo prédio. O aplicativo também pode ser usado para combinar dados de um mesmo locatário em vários prédios.

### <a name="advanced-compute-capabilities"></a>Recursos de computação avançada

Com as [funções definidas pelo usuário](./concepts-user-defined-functions.md), você pode definir e executar funções personalizadas em relação a [dados de dispositivo](./concepts-device-ingress.md) recebidos para enviar sinais a pontos de extremidade predefinidos. Esse recurso avançado melhora a personalização e a automação de tarefas de dispositivos.

Um exemplo é um aplicativo de agricultura inteligente que inclui uma função definida pelo usuário para avaliar a previsão do tempo e as leituras do sensor de umidade do solo. O aplicativo, em seguida, envia sinais sobre as necessidades de irrigação.

### <a name="built-in-access-control"></a>Controle de acesso interno

Usando recursos de gerenciamento de acesso e identidade, como o [controle de acesso baseado em função](./security-role-based-access-control.md) e o [Azure Active Directory](./security-authenticating-apis.md), você pode controlar o acesso de pessoas e dispositivos de forma segura.

Um exemplo é um aplicativo de gerenciamento de recursos que está configurado para permitir que os ocupantes de uma sala definam a temperatura dentro de um intervalo específico. Os gerentes das instalações têm permissão para definir a temperatura de qualquer sala com qualquer valor.

### <a name="ecosystem"></a>Ecossistema

Você pode conectar uma instância dos Gêmeos Digitais do Azure a muitos serviços do Azure avançados. Esses serviços incluem o Armazenamento do Azure, a IA do Azure e o Azure Stream Analytics. Eles também incluem o Azure Mapas, a Realidade Misturada da Microsoft, o Dynamics 365 ou o Office 365.

Por exemplo, um aplicativo de prédio inteligente pode usar os Gêmeos Digitais do Azure para representar equipes e dispositivos localizados em vários andares. Como os dispositivos transmitem dados dinâmicos para a instância de Gêmeos Digitais provisionada, o Stream Analytics processa esses dados para fornecer insights acionáveis importantes. Os dados são armazenados no Armazenamento do Azure e convertidos em um formato de arquivo que pode ser compartilhado. O arquivo é distribuído a toda a organização por meio do Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Soluções que se beneficiam dos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure são úteis para representar o mundo físico e suas muitas relações. Ele simplifica a modelagem, o processamento de dados, a manipulação de eventos e o controle de dispositivos IoT. Considere alguns dos cenários abaixo, em vários setores. Eles se beneficiam de seu uso para:

* Mostrar a uma empresa de gestão de propriedades os níveis de ocupação de um espaço ao longo do tempo para obter insights sobre as melhores maneiras de configurar o prédio comercial.
* Disparar tíquetes de ordem de trabalho para um aplicativo móvel. Use-o para expedir seguranças e agendar serviços de manutenção e outros em uma loja ou em uma arena esportiva.
* Mostrar a um ocupante do prédio quais salas estão ocupadas no prédio em tempo real. Em seguida, ajudar o ocupante a reservar espaços de trabalho que atendam às suas necessidades.
* Controlar onde os ativos estão localizados em determinado espaço.
* Otimizar a carga de um veículo elétrico modelando as preferências do usuário e as restrições da rede elétrica.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Gêmeos Digitais do Azure no contexto de outros serviços de IoT

Os Gêmeos Digitais do Azure usam o Hub IoT do Azure para conectar os dispositivos de IoT e os sensores que mantêm tudo atualizado com o mundo físico. O diagrama abaixo mostra como os Gêmeos Digitais do Azure se relacionam com outros serviços de IoT do Azure.

![Os Gêmeos Digitais do Azure são um serviço baseado no Hub IoT do Azure][1]

Para obter mais informações sobre o IoT, confira [Tecnologias e soluções de IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Próximas etapas

Vá para uma breve demonstração sobre os Gêmeos Digitais do Azure:

>[!div class="nextstepaction"]
>[Início Rápido: Encontrar salas disponíveis usando os Gêmeos Digitais do Azure](./quickstart-view-occupancy-dotnet.md)

Examine em detalhes um aplicativo de gerenciamento de instalações usando os Gêmeos Digitais do Azure:

>[!div class="nextstepaction"]
>[Tutorial: Implantar os Gêmeos Digitais do Azure e configurar um grafo espacial](./tutorial-facilities-setup.md)

Saiba mais sobre os principais conceitos dos Gêmeos Digitais do Azure:

>[!div class="nextstepaction"]
>[Noções básicas sobre modelo de objeto e grafo de inteligência espacial dos Gêmeos Digitais](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png
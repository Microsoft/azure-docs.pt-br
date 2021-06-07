---
title: O que são Gêmeos Digitais do Azure?
titleSuffix: Azure Digital Twins
description: Visão geral do que pode ser feito com os Gêmeos Digitais do Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481660"
---
# <a name="what-is-azure-digital-twins"></a>O que são Gêmeos Digitais do Azure?

Os **Gêmeos Digitais do Azure** são uma oferta de PaaS (plataforma como serviço) que permite a criação de grafos de conhecimento baseados em modelos digitais de ambientes inteiros. Esses ambientes podem ser edifícios, fábricas, fazendas, redes de energia, ferrovias, estádios e muito mais – até mesmo cidades inteiras. Esses modelos digitais podem ser usados para obter insights que impulsionam melhores produtos, operações otimizadas, custos reduzidos e experiências de clientes inovadoras.

Aproveite o seu conhecimento de domínio sobre os Gêmeos Digitais do Azure para criar soluções conectadas e personalizadas que:
* Modelem qualquer ambiente e deem vida aos Gêmeos Digitais de maneira escalonável e segura
* Conectem ativos como dispositivos IoT e sistemas de negócios existentes
* Usem um sistema de eventos robusto para criar uma lógica de negócios e um processamento de dados dinâmicos
* Integrem-se com os serviços de dados, análise e IA do Azure para ajudar a acompanhar o passado e, em seguida, prever o futuro

## <a name="azure-digital-twins-capabilities"></a>Funcionalidades dos Gêmeos Digitais do Azure

Aqui está um resumo dos recursos fornecidos pelos Gêmeos Digitais do Azure.

### <a name="open-modeling-language"></a>Linguagem de modelagem aberta

Nos Gêmeos Digitais do Azure, você define as entidades digitais que representam as pessoas, os lugares e as coisas do seu ambiente físico usando tipos de gêmeos personalizados chamados de [**modelos**](concepts-models.md). 

Você pode considerar essas definições de modelo como um vocabulário especializado para descrever seus negócios. Para uma solução de gerenciamento de edifício, por exemplo, você pode definir modelos como "edifício", "andar" e "elevador". Em seguida, você pode criar **gêmeos digitais** com base nesses modelos a fim de representar seu ambiente específico.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Os modelos são definidos em uma linguagem semelhante à JSON chamada [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) e descrevem os gêmeos em termos de suas propriedades de estado, eventos de telemetria, comandos, componentes e relações.
* Os modelos definem **relações** semânticas entre as entidades para que você possa conectar os gêmeos a um grafo de conhecimento que reflita as interações deles. Em uma analogia com o mundo real, você pode considerar os modelos como substantivos e as relações como verbos.
* Você também pode especializar os gêmeos usando a herança entre modelos. Um modelo pode herdar características de outro.

A DTDL é usada para modelos de dados em todos os outros serviços de IoT do Azure, incluindo o [IoT PnP (IoT Plug and Play)](../iot-pnp/overview-iot-plug-and-play.md) e o [TSI (Time Series Insights)](../time-series-insights/overview-what-is-tsi.md). Isso ajuda a manter a solução dos Gêmeos Digitais do Azure conectada e compatível com as demais partes do ecossistema do Azure.

### <a name="live-execution-environment"></a>Ambiente de execução ao vivo

Os modelos digitais dos Gêmeos Digitais do Azure são representações ao vivo e atualizadas do mundo real. Usando as relações dos modelos DTDL personalizados, você conectará os gêmeos a um **grafo ao vivo** que representa o seu ambiente.

Você pode exibir uma visualização do seu grafo de Gêmeos Digitais do Azure com a ajuda de um aplicativo de exemplo, o [**Explorador dos Gêmeos Digitais do Azure**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Veja abaixo qual é a aparência da visualização de exemplo:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Captura de tela do aplicativo de exemplo do explorador dos Gêmeos Digitais do Azure que mostra um grafo de nós representando os gêmeos digitais" lightbox="media/includes/azure-digital-twins-explorer.png":::

Os Gêmeos Digitais do Azure fornecem um **sistema de eventos** avançado para manter esse grafo atualizado com o processamento de dados e a lógica de negócios. Você poderá conectar recursos externos de computação, como o [Azure Functions](../azure-functions/functions-overview.md), a fim de impulsionar esse processamento de dados de maneiras flexíveis e personalizadas.

Você também poderá extrair insights do ambiente de execução ao vivo usando a poderosa **API de consulta** dos Gêmeos Digitais do Azure. A API permite consultas com critérios de pesquisa avançados, incluindo valores de propriedade, relações, propriedades de relações, informações sobre modelos e muito mais. Você também poderá combinar consultas, reunindo um amplo intervalo de insights sobre o ambiente e respondendo a perguntas personalizadas que são importantes para você.

### <a name="input-from-iot-and-business-systems"></a>Entrada da IoT e de sistemas de negócio

Para manter o ambiente de execução ao vivo dos Gêmeos Digitais do Azure atualizado com o mundo real, você poderá usar o [Hub IoT](../iot-hub/about-iot-hub.md) a fim de conectar a solução a dispositivos IoT e IoT Edge. Esses dispositivos gerenciados por hub são representados como parte do grafo de gêmeos e fornecem os dados que orientam o modelo.

Você poderá criar um Hub IoT para essa finalidade com os Gêmeos Digitais do Azure ou conectar um Hub IoT existente juntamente com os dispositivos que ele já gerencia.

Você também poderá obter os Gêmeos Digitais do Azure de outras fontes de dados, usando APIs REST ou conectores para outros serviços, como os [Aplicativos Lógicos](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Saída para TSI, armazenamento e análise

Os dados do modelo Gêmeos Digitais do Azure podem ser roteados para serviços downstream do Azure para análise ou armazenamento adicional. Isso é fornecido por meio de **rotas de eventos**, que usam o [Hub de Eventos](../event-hubs/event-hubs-about.md), a [Grade de Eventos](../event-grid/overview.md) ou o [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md) para impulsionar os fluxos de dados desejados.

Dentre as coisas que você pode fazer com as rotas de eventos estão:
* Armazenar dados dos Gêmeos Digitais do Azure no [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analisar dados dos Gêmeos Digitais do Azure com o [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ou com outras ferramentas de análise de dados da Microsoft
* Integrar fluxos de trabalho maiores com os Aplicativos Lógicos
* Conectar os Gêmeos Digitais do Azure ao Time Series Insights a fim de acompanhar o histórico de séries temporais de cada gêmeo
* Alinhar um modelo de série temporal no Time Series Insights com origem nos Gêmeos Digitais do Azure

Essa é outra maneira pela qual os Gêmeos Digitais do Azure podem se conectar a uma solução mais ampla e dar suporte às suas necessidades personalizadas para trabalho contínuo com esses insights.

## <a name="azure-digital-twins-in-a-solution-context"></a>Gêmeos Digitais do Azure no contexto de uma solução

Os Gêmeos Digitais do Azure normalmente são usados em combinação com outros serviços do Azure, como parte de uma solução de IoT mais ampla. 

Uma solução completa usando os Gêmeos Digitais do Azure pode conter as seguintes partes:
* A instância de serviço dos Gêmeos Digitais do Azure. Ela armazena os modelos gêmeos e o grafo gêmeo com seu respectivo estado e orquestra o processamento de eventos.
* Um ou mais aplicativos cliente que impulsionam a instância dos Gêmeos Digitais do Azure configurando modelos, criando topologia e extraindo insights do grafo gêmeo.
* Um ou mais recursos externos de computação para processar eventos gerados pelos Gêmeos Digitais do Azure ou fontes de dados conectadas, como dispositivos. Uma forma comum de fornecer recursos de computação é por meio do [Azure Functions](../azure-functions/functions-overview.md).
* Um Hub IoT para proporcionar o gerenciamento de dispositivos e funcionalidades de fluxo de dados de IoT.
* Serviços downstream para gerenciar tarefas como a integração do fluxo de trabalho (como [Aplicativos Lógicos](../logic-apps/logic-apps-overview.md), armazenamento frio, integração de série temporal ou análise).

O diagrama a seguir mostra onde se situam os Gêmeos Digitais do Azure no contexto de uma solução mais ampla de IoT do Azure.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagrama mostrando fontes de entrada, serviços de saída e comunicação bidirecional com aplicativos cliente e recursos externos de computação." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Limites de serviço

Você pode ler sobre os **limites de serviço** dos Gêmeos Digitais do Azure aqui: [limites do serviço dos Gêmeos Digitais do Azure](reference-service-limits.md). Isso pode ser útil ao trabalhar com o serviço para entender as limitações funcionais e de taxa do serviço, bem como quais limites podem ser ajustados, se necessário.

## <a name="terminology"></a>Terminologia

Você pode exibir uma lista de **termos comuns de IoT** e seus usos nos serviços de IoT do Azure, incluindo os Gêmeos Digitais do Azure, aqui: [Glossário de IoT do Azure](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). Essa pode ser uma referência útil enquanto você começa a usar os Gêmeos Digitais do Azure e cria uma solução de IoT.

## <a name="next-steps"></a>Próximas etapas

* Aprofunde-se no trabalho com os Gêmeos Digitais do Azure com o auxílio do guia de início rápido: [*Início Rápido: explorar um cenário de exemplo*](quickstart-azure-digital-twins-explorer.md).

* Alternativamente, comece a ler sobre conceitos dos Gêmeos Digitais do Azure com [*Conceitos: modelos personalizados*](concepts-models.md).
---
title: Visão geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço de nuvem gerenciado que ajuda a analisar dados de streaming de Internet das Coisas (IoT) em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: c50ad2b045f32daf53033318123b68e4b2d58db5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329329"
---
# <a name="what-is-azure-stream-analytics"></a>O que é o Azure Stream Analytics?

O Azure Stream Analytics é um mecanismo de processamento de eventos complexos e análise em tempo real desenvolvido para analisar e processar simultaneamente altos volumes de dados de streaming rápido a partir de várias fontes. É possível identificar padrões e relações em informações extraídas de diversas fontes de entrada, incluindo dispositivos, sensores, sequências de cliques, feeds de mídias sociais e aplicativos. Esses padrões podem ser usados para disparar outras ações e iniciar fluxos de trabalho, como criação de alertas, informações de feed para uma ferramenta de relatórios, ou para armazenar dados transformados para uso posterior. O Stream Analytics também está disponível no tempo de execução do IoT Edge do Azure e é compatível com a mesma linguagem ou sintaxe que a nuvem. 

Os cenários a seguir são exemplos de quando você pode usar o Azure Stream Analytics:

* Analisar streams de telemetria em tempo real de dispositivos IoT
* Análise de sequência de cliques/blogs
* Análise geoespacial para gerenciamento de frotas e veículos sem motoristas
* Monitoramento remoto e manutenção preditiva de ativos de alto valor
* Análise em tempo real em dados de pontos de venda para detecção de anomalias e controle de estoque

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Um trabalho do Azure Stream Analytics consiste em uma entrada, uma consulta e uma saída. O Stream Analytics ingere dados dos Hubs de Eventos do Azure, do Hub IoT do Azure ou do Armazenamento de Blobs do Azure. A consulta, que tem como base a linguagem de consulta SQL, pode ser usada para filtrar, classificar, agregar e associar dados de streaming com facilidade por um período de tempo. Também é possível estender essa linguagem SQL com UDFs (funções definidas pelo usuário) de JavaScript e C#. Você pode ajustar com facilidade as opções de ordenação de eventos e a duração das janelas de tempo na execução de operações de agregação por configurações e/ou constructos de linguagem.

Cada trabalho tem uma saída para os dados transformados e você pode controlar o que deve ser feito em resposta às informações analisadas. Por exemplo, você pode:

* Enviar dados para serviços como Azure Functions, Filas ou Tópicos do Barramento de Serviço para disparar comunicações ou de fluxos de trabalho personalizados downstream.
* Enviar dados a um painel do Power BI para painéis em tempo real.
* Armazenar dados em outros serviços de armazenamento do Azure para treinar um modelo de machine learning com base em dados históricos ou para executar análise em lote.

A imagem a seguir mostra como os dados são enviados ao Stream Analytics, analisados e enviados para outras ações, como armazenamento ou apresentação:

![Pipeline de introdução do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais recursos e benefícios

O Azure Stream Analytics foi projetado para ser fácil de usar, flexível, confiável e escalonável para qualquer tamanho de trabalho. Ele está disponível em várias regiões do Azure. A imagem abaixo ilustra os principais recursos do Azure Stream Analytics:

![Principais recursos do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade de uso do guia de introdução

É fácil começar a usar o Azure Stream Analytics. Bastam alguns cliques para se conectar a várias fontes e coletores, assim criando um pipeline de ponta a ponta. O Stream Analytics pode se conectar a [Hubs de Eventos do Azure](/azure/event-hubs/) e a [Hub IoT do Azure](/azure/iot-hub/) para a ingestão de dados de fluxo, e ao [Armazenamento de Blobs do Azure](/azure/storage/storage-introduction) para ingerir dados históricos. A entrada de trabalho também pode incluir dados de referência estáticos ou com alteração lenta do Armazenamento de Blobs do Azure ou do [Banco de Dados SQL](stream-analytics-use-reference-data.md#azure-sql-database), que você pode unir a dados de streaming para executar operações de pesquisa.

O Stream Analytics pode rotear a saída de trabalho para vários sistemas de armazenamento, como o [Armazenamento de Blob do Azure](/azure/storage/storage-introduction), o [Banco de Dados SQL do Azure](/azure/sql-database/), o [Azure Data Lake Storage](/azure/data-lake-store/) ou o [Azure CosmosDB](/azure/cosmos-db/introduction). Você pode executar a análise em lotes em saídas armazenadas com o Azure HDInsight ou enviar a saída para outro serviço, como os Hubs de Eventos para consumo ou para o [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real.

Para acessar toda a lista de saídas do Stream Analytics, confira [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade do programador

O Azure Stream Analytics usa uma linguagem de consulta baseada em SQL simples que foi aumentada com restrições temporais avançadas para analisar os dados em movimento. Para definir as transformações de trabalho, use uma [Linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simples e declarativa que permite criar consultas temporais e análises complexas usando constructos SQL simples. Como a linguagem de consulta do Stream Analytics é consistente com a linguagem SQL, basta estar familiarizado com a linguagem SQL para começar a criar trabalhos. Você também pode criar trabalhos usando ferramentas de desenvolvedor, como o Azure PowerShell, as [ferramentas do Visual Studio para o Stream Analytics](stream-analytics-tools-for-visual-studio-install.md), a [extensão do Visual Studio Code no Stream Analytics](quick-create-vs-code.md) ou modelos do Azure Resource Manager. O uso de ferramentas de desenvolvedor permite a você criar consultas de transformação offline e usar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para enviar trabalhos ao Azure.

A linguagem de consulta do Stream Analytics oferece uma ampla variedade de funções para analisar e processar os dados de streaming. Essa linguagem de consulta dá suporte a funções de manipulação de dados e de agregação simples e a funções geoespaciais complexas. Você pode editar consultas no portal e testá-las usando os dados de exemplo que são extraídos do fluxo ativo.

Você pode estender os recursos da linguagem de consulta definindo e chamando funções adicionais. Você pode definir as chamadas de função no Serviço do Azure Machine Learning para aproveitar as soluções desta e integrar UDFs (funções definidas pelo usuário) de JavaScript ou C#, ou agregações definidas pelo usuário para executar cálculos complexos como parte de uma consulta do Stream Analytics.

## <a name="fully-managed"></a>Totalmente gerenciado

O Azure Stream Analytics é uma oferta sem servidor (PaaS) totalmente gerenciada no Azure. Você não precisa provisionar hardware ou gerenciar clusters para executar seus trabalhos. O Azure Stream Analytics gerencia seu trabalho integralmente, configurando clusters de computação complexos na nuvem e cuidando do ajuste do desempenho necessário para executar o trabalho. A integração com os Hubs de Eventos do Azure e o Hub IoT do Azure permite que seu trabalho ingira milhões de eventos por segundo, provenientes de diversas fontes, incluindo dispositivos conectados, cliques em fluxos e arquivos de log. Usando o recurso de partição dos Hubs de Eventos, você pode dividir os cálculos em etapas lógicas, cada um com a capacidade de ser mais particionado para aumentar a escalabilidade.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Executar na nuvem ou na borda inteligente

O Azure Stream Analytics pode ser executado na nuvem, para uma análise em larga escala, ou no IoT Edge, para análise de latência extremamente baixa. O Azure Stream Analytics usa a mesma linguagem de consulta na nuvem e na borda, permitindo que os desenvolvedores criem arquiteturas realmente híbridas para processamento de fluxo. 

## <a name="low-total-cost-of-ownership"></a>Custo total de propriedade baixo

Como um serviço de nuvem, o Stream Analytics é otimizado para que você possa economizar custos. Não há nenhum custo inicial envolvido, você só paga pelas [unidades de streaming que consumir](stream-analytics-streaming-unit-consumption.md) e pela quantidade de dados processados. Não há nenhum compromisso ou provisionamento de cluster necessário e você pode aumentar ou reduzir verticalmente o trabalho com base em suas necessidades de negócios.

## <a name="mission-critical-ready"></a>Pronto para necessidades críticas

O Azure Stream Analytics está disponível em várias regiões em todo o mundo e foi projetado para executar cargas de trabalho críticas, dando suporte a requisitos de confiabilidade, segurança e conformidade.

### <a name="reliability"></a>Confiabilidade

O Azure Stream Analytics garante o processamento de eventos exatamente uma vez e pelo menos uma entrega de eventos; portanto, os eventos nunca são perdidos. O processamento exatamente uma vez é garantido com a saída selecionada, conforme descrito em [Garantias de entrega de evento](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

O Azure Stream Analytics tem funcionalidades internas de recuperação no caso de falha na entrega de um evento. Além disso, o Stream Analytics fornece também pontos de verificação internos para manter o estado do trabalho e fornece resultados reproduzíveis.

Como um serviço gerenciado, o Stream Analytics garante o processamento de eventos com 99,9% de disponibilidade ao nível de granularidade do minuto. Para saber mais, confira a página do [SLA do Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/). 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics criptografa todas as comunicações de entrada e de saída e dá suporte ao TLS 1.2. Os pontos de verificação internos também são criptografados. O Stream Analytics não armazena os dados de entrada, pois todo o processamento é feito na memória.

### <a name="compliance"></a>Conformidade

O Azure Stream Analytics segue várias certificações de conformidade, conforme descrito na [Visão geral de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

O Stream Analytics pode processar milhões de eventos por segundo e fornecer resultados com latência ultrabaixa. Ele permite que você escale verticalmente e expanda para lidar com aplicativos grandes de processamento de eventos complexos e em tempo real. O Stream Analytics é compatível com um desempenho mais alto por meio do particionamento, permitindo que consultas complexas sejam colocadas em paralelo e executadas em vários nós de streaming. O Azure Stream Analytics é criado no [Trill](https://github.com/Microsoft/Trill), um mecanismo de análise de streaming na memória de alto desempenho desenvolvido em colaboração com a Microsoft Research.

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral do Azure Stream Analytics. Em seguida, você pode se aprofundar e criar seu primeiro trabalho do Stream Analytics:

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md).
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).
* [Criar um trabalho do Stream Analytics usando o Visual Studio Code](quick-create-vs-code.md).

---
title: Capturar eventos de streaming – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do recurso de Captura que permite que você capture eventos de streaming por meio dos Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: cc74bfe9bf9e5f33b7cf05ebb19b44ab8b3bea43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822457"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capture eventos por meio dos Hubs de Eventos do Azure no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage
Os Hubs de Eventos do Azure permitem que você capture automaticamente dados de streaming em seus Hubs de Eventos em uma conta de [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou do [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) de sua escolha, com maior flexibilidade para especificar uma hora ou um período. A configuração da Captura é rápida, não há custos administrativos para executá-la e ela é dimensionada automaticamente com as [unidades de produtividade](event-hubs-features.md#throughput-units) dos Hubs de Eventos. A Captura de Hubs de Eventos é a maneira mais fácil de carregar dados de streaming no Azure e permite que você se concentre no processamento de dados em vez de se concentrar na captura de dados.

A Captura de Hubs de Eventos permite processar pipelines em tempo real e baseados em lote no mesmo fluxo. Isso significa que você pode criar soluções que crescem com suas necessidades ao longo do tempo. Se você estiver criando sistemas baseados em lote com o objetivo de processá-los em tempo real no futuro ou para adicionar um caminho frio eficiente para uma solução em tempo real, a Captura de Hubs de Eventos facilita o trabalho de transmissão de dados.

> [!NOTE]
> Atualmente, o recurso de captura de Hubs de eventos dá suporte a apenas Gen 1 do Azure Data Lake Store, não Gen 2. 

## <a name="how-event-hubs-capture-works"></a>Como a Captura de Hubs de Eventos funciona

Os Hubs de Eventos são um buffer de tempo de retenção durável para a entrada de telemetria, semelhante a um log distribuído. A chave para reduzir os Hubs de Eventos é o [modelo de consumidor particionado](event-hubs-features.md#partitions). Cada partição é um segmento independente de dados e é consumido de forma independente. Ao longo do tempo, esses dados expiram com base no período de retenção configurável. Assim, um Hub de Eventos específico nunca fica “cheio demais”.

A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta do Azure Data Lake Store, que será usado para armazenar os dados capturados. Essa conta pode estar na mesma região que o hub de eventos ou em outra região, concedendo flexibilidade ao recurso de Captura de Hubs de Eventos.

Os dados capturados são gravados no formato [Apache Avro][Apache Avro]: um formato compacto, rápido e binário que fornece estruturas de dados avançados com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory. Mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Janelas da Captura

A Captura de Hubs de Eventos permite que você configure uma janela para controlar a captura. Essa janela tem configuração mínima de tamanho e tempo com uma “política de ganha quem vem primeiro”, o que significa que o primeiro gatilho encontrado causa uma operação de captura. Se você tiver uma janela de captura de quinze minutos/100 MB e enviar 1 MB por segundo, a janela de tamanho será disparada antes da janela de tempo. Cada partição captura de forma independente e grava um blob de blocos completo durante o tempo de captura, nomeado com a hora em que o intervalo de captura foi encontrado. A convenção de nomenclatura de armazenamento é a seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Observe que os valores de data estão preenchidos com zeros; um nome de arquivo de exemplo poderia ser:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento de unidades de produtividade

O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-features.md#throughput-units). Uma única unidade de produtividade permite o ingresso de 1 MB por segundo ou 1.000 eventos por segundo e duas vezes essa quantidade de saída. Os Hubs de Evento Standard podem ser configurados com 1 a 20 unidades de produtividade e outras podem ser adquiridas por meio de uma [solicitação de suporte][support request] para aumento de cota. O uso além das unidades de produtividade adquiridas é restringido. A Captura de Hubs de Eventos copia os dados diretamente do armazenamento interno dos Hubs de Eventos, ignorando as cotas de saída das unidades de produtividade e salvando a saída de outros leitores de processamento, como o Stream Analytics ou o Spark.

Uma vez configurado, a Captura de Hubs de Eventos é executada automaticamente quando você envia o primeiro evento e continua em execução. Para que o seu processamento downstream saiba mais facilmente que o processo está funcionando, os Hubs de Eventos gravam arquivos vazios quando não há nenhum dado. Esse processo fornece cadência e marcador previsíveis que podem alimentar os processadores em lotes.

## <a name="setting-up-event-hubs-capture"></a>Configurando a Captura de Hubs de Eventos

Você pode configurar a Captura na hora da criação do hub de eventos usando o [Portal do Azure](https://portal.azure.com) ou usando modelos do Azure Resource Manager. Para obter mais informações, consulte os seguintes artigos:

- [Habilitar a Captura de Hubs de Evento usando o Portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um namespace de Hubs de Eventos com o hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

A Captura de Hubs de Eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurada. Você pode exibir esses arquivos em qualquer ferramenta, como o [Gerenciador de Armazenamento do Azure][Azure Storage Explorer]. Você pode baixar os arquivos localmente para trabalhar com eles.

Os arquivos produzidos pela Captura de Hubs de Eventos têm o seguinte esquema Avro:

![Esquema Avro][3]

Uma maneira fácil de explorar arquivos do Avro é usando o jar [Ferramentas Avro][Avro Tools] do Apache. Você também pode usar o [Apache Drill][Apache Drill] para ter uma experiência leve controlada por SQL ou o [Apache Spark][Apache Spark] para executar um processamento distribuído complexo nos dados ingeridos. 

### <a name="use-apache-drill"></a>Usar o Apache Drill

O [Apache Drill][Apache Drill] é um "mecanismo de consulta SQL de software livre para exploração de Big Data" que pode consultar dados estruturados e semiestruturados onde estiver. O mecanismo pode ser executado como um nó autônomo ou como um grande cluster para ter um excelente desempenho.

Suporte nativo para o armazenamento de Blobs do Azure está disponível, o que torna mais fácil consultar dados em um arquivo Avro, conforme descrito na documentação:

[Apache Drill: Plug-in do Armazenamento de Blobs do Azure][Apache Drill: Azure Blob Storage Plugin]

Para consultar facilmente arquivos capturados, você pode criar e executar uma VM com o Apache Drill habilitado por meio de um contêiner para acessar o armazenamento de Blobs do Azure:

https://github.com/yorek/apache-drill-azure-blob

Um exemplo de ponta a ponta completo está disponível no repositório de Streaming em escala:

[Streaming em escala: captura dos Hubs de Eventos]

### <a name="use-apache-spark"></a>Usar o Apache Spark

O [Apache Spark][Apache Spark] é um “mecanismo de análise unificado para processamento de dados em grande escala”. Ele dá suporte a diferentes linguagens, incluindo SQL, e pode acessar facilmente o Armazenamento de Blobs do Azure. Há duas opções para executar o Apache Spark no Azure e as duas fornecem acesso fácil para o Armazenamento de Blobs do Azure:

- [HDInsight: arquivos de endereços no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: Armazenamento de Blobs do Azure][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Usar Ferramentas Avro

As [Ferramentas Avro][Avro Tools] estão disponíveis como um pacote jar. Depois de baixar o arquivo jar, você pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Esse comando retorna

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Você também pode usar as Ferramentas Avro para converter o arquivo em formato JSON e executar outros tipos de processamento.

Para executar um processamento mais avançado, baixe e instale o Avro na plataforma escolhida. No momento da redação deste artigo, há implementações disponíveis para C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

O Apache Avro tem guias de Introdução completos para [Java][Java] e [Python][Python]. Você também pode ler o artigo [Introdução à Captura de Hubs de Eventos](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Como a Captura de Hubs de Eventos é cobrada

A Captura de Hubs de Eventos é medida da mesma forma que as unidades de produtividade, com uma taxa por hora. A cobrança é diretamente proporcional ao número de unidades de produtividade adquiridas para o namespace. Conforme as unidades de produtividade são aumentadas ou diminuídas, a Captura de Hubs de Eventos aumenta e diminui para ter o desempenho correspondente. Os medidores ocorrem em tandem. Para detalhes de preços, consulte [Preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="integration-with-event-grid"></a>Integração com a Grade de Eventos 

Você pode criar uma assinatura da Grade de Eventos do Azure com um namespace de Hubs de Eventos como sua fonte. O tutorial a seguir mostra como criar uma assinatura da Grade de Eventos com um hub de eventos como origem e um aplicativo do Azure Functions como coletor: [Processar e migrar dados dos Hubs de Eventos capturados para um SQL Data Warehouse usando a Grade de Eventos e o Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Próximas etapas

A Captura de Hubs de Eventos é a forma mais fácil de obter dados para o Azure. Usando o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, você pode executar processamento em lotes e outras análises usando ferramentas familiares e plataformas de sua escolha, na escala que precisar.

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Começar a enviar e receber eventos](event-hubs-dotnet-framework-getstarted-send.md)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming em escala: captura dos Hubs de Eventos]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture

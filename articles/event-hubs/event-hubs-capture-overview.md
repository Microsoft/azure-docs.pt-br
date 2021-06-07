---
title: Capturar eventos de streaming – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do recurso de Captura que permite que você capture eventos de streaming por meio dos Hubs de Eventos do Azure.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595962"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capture eventos por meio dos Hubs de Eventos do Azure no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage
Os Hubs de Eventos do Azure permitem que você capture automaticamente dados de streaming em seus Hubs de Eventos em uma conta de [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou do [Azure Data Lake Storage Gen1 ou Gen2](https://azure.microsoft.com/services/data-lake-store/) de sua escolha, com maior flexibilidade para especificar uma hora ou um período. A configuração da Captura é rápida, não há custos administrativos para executá-la e ela é dimensionada automaticamente com as [unidades de produtividade](event-hubs-scalability.md#throughput-units) dos Hubs de Eventos. A Captura de Hubs de Eventos é a maneira mais fácil de carregar dados de streaming no Azure e permite que você se concentre no processamento de dados em vez de se concentrar na captura de dados.

> [!NOTE]
> Configurar a Captura de Hubs de Eventos para usar o Azure Data Lake Storage **Gen 2** é o mesmo que configurá-la para usar um armazenamento de Blobs do Azure. Para obter detalhes, consulte [Configurar a Captura de Hubs de Eventos](event-hubs-capture-enable-through-portal.md). 

A Captura de Hubs de Eventos permite processar pipelines em tempo real e baseados em lote no mesmo fluxo. Isso significa que você pode criar soluções que crescem com suas necessidades ao longo do tempo. Se você estiver criando sistemas baseados em lote com o objetivo de processá-los em tempo real no futuro ou para adicionar um caminho frio eficiente para uma solução em tempo real, a Captura de Hubs de Eventos facilita o trabalho de transmissão de dados.

> [!IMPORTANT]
> A conta de armazenamento de destino (Armazenamento do Azure ou Azure Data Lake Storage) precisa estar na mesma assinatura do hub de eventos. 

## <a name="how-event-hubs-capture-works"></a>Como a Captura de Hubs de Eventos funciona

Os Hubs de Eventos são um buffer de tempo de retenção durável para a entrada de telemetria, semelhante a um log distribuído. A chave para reduzir os Hubs de Eventos é o [modelo de consumidor particionado](event-hubs-scalability.md#partitions). Cada partição é um segmento independente de dados e é consumido de forma independente. Ao longo do tempo, esses dados expiram com base no período de retenção configurável. Assim, um Hub de Eventos específico nunca fica “cheio demais”.

A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta do Azure Data Lake Storage, que será usado para armazenar os dados capturados. Essa conta pode estar na mesma região que o hub de eventos ou em outra região, concedendo flexibilidade ao recurso de Captura de Hubs de Eventos.

Os dados capturados são gravados no formato [Apache Avro][Apache Avro]: um formato compacto, rápido e binário que fornece estruturas de dados avançados com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory. Mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Janelas da Captura

A Captura de Hubs de Eventos permite que você configure uma janela para controlar a captura. Essa janela tem configuração mínima de tamanho e tempo com uma “política de ganha quem vem primeiro”, o que significa que o primeiro gatilho encontrado causa uma operação de captura. Se você tiver uma janela de captura de quinze minutos/100 MB e enviar 1 MB por segundo, a janela de tamanho será disparada antes da janela de tempo. Cada partição captura de forma independente e grava um blob de blocos completo durante o tempo de captura, nomeado com a hora em que o intervalo de captura foi encontrado. A convenção de nomenclatura de armazenamento é a seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Os valores de data estão preenchidos com zeros. Um nome de arquivo de exemplo poderia ser:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Caso o seu blob de armazenamento do Azure esteja temporariamente indisponível, a Captura de Hubs de Eventos manterá seus dados para o período de retenção de dados configurado em seu hub de eventos e preencherá os dados assim que sua conta de armazenamento estiver disponível novamente.

### <a name="scaling-to-throughput-units"></a>Dimensionamento de unidades de produtividade

O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-scalability.md#throughput-units). Uma única unidade de produtividade permite o ingresso de 1 MB por segundo ou 1.000 eventos por segundo e duas vezes essa quantidade de saída. Os Hubs de Eventos Standard podem ser configurados com 1 a 20 unidades de produtividade e outras podem ser adquiridas por meio de uma [solicitação de suporte][support request] para aumento de cota. O uso além das unidades de produtividade adquiridas é restringido. A Captura de Hubs de Eventos copia os dados diretamente do armazenamento interno dos Hubs de Eventos, ignorando as cotas de saída das unidades de produtividade e salvando a saída de outros leitores de processamento, como o Stream Analytics ou o Spark.

Uma vez configurado, a Captura de Hubs de Eventos é executada automaticamente quando você envia o primeiro evento e continua em execução. Para que o seu processamento downstream saiba mais facilmente que o processo está funcionando, os Hubs de Eventos gravam arquivos vazios quando não há nenhum dado. Esse processo fornece cadência e marcador previsíveis que podem alimentar os processadores em lotes.

## <a name="setting-up-event-hubs-capture"></a>Configurando a Captura de Hubs de Eventos

Você pode configurar a Captura na hora da criação do hub de eventos usando o [Portal do Azure](https://portal.azure.com) ou usando modelos do Azure Resource Manager. Para obter mais informações, consulte os seguintes artigos:

- [Habilitar a Captura de Hubs de Evento usando o Portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um namespace de Hubs de Eventos com o hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Se você habilitar o recurso de captura para um hub de eventos existente, o recurso capturará eventos que chegam ao Hub de eventos **depois** que o recurso é ativado. Ele não captura eventos que existiam no Hub de eventos antes de o recurso ter sido ativado. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

A Captura de Hubs de Eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurada. Você pode exibir esses arquivos em qualquer ferramenta, como o [Gerenciador de Armazenamento do Azure][Azure Storage Explorer]. Você pode baixar os arquivos localmente para trabalhar com eles.

Os arquivos produzidos pela Captura de Hubs de Eventos têm o seguinte esquema Avro:

![Esquema Avro][3]

Uma maneira fácil de explorar os arquivos do Avro é usando o jar [Ferramentas Avro][Avro Tools] do Apache. Você também pode usar o [Apache Drill][Apache Drill] para ter uma experiência leve controlada por SQL ou o [Apache Spark][Apache Spark] para executar um processamento distribuído complexo nos dados ingeridos. 

### <a name="use-apache-drill"></a>Usar o Apache Drill

O [Apache Drill][Apache Drill] é um “mecanismo de consulta SQL de software livre para exploração de Big Data” que pode consultar dados estruturados e semiestruturados em qualquer lugar. O mecanismo pode ser executado como um nó autônomo ou como um grande cluster para ter um excelente desempenho.

Suporte nativo para o armazenamento de Blobs do Azure está disponível, o que torna mais fácil consultar dados em um arquivo Avro, conforme descrito na documentação:

[Apache Drill: Plug-in do Armazenamento de Blobs do Azure][Apache Drill: Azure Blob Storage Plugin]

Para consultar facilmente os arquivos capturados, você pode criar e executar uma VM com o Apache Drill habilitado por meio de um contêiner para acessar o armazenamento de BLOBs do Azure. Consulte o exemplo a seguir: [streaming em escala com captura de hubs de eventos](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Usar o Apache Spark

O [Apache Spark][Apache Spark] é um “mecanismo de análise unificado para processamento de dados em grande escala”. Ele dá suporte a diferentes linguagens, incluindo SQL, e pode acessar facilmente o Armazenamento de Blobs do Azure. Há algumas opções para executar o Apache Spark no Azure e as duas fornecem acesso fácil para o Armazenamento de Blobs do Azure:

- [HDInsight: Arquivos de endereços no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: Armazenamento de Blobs do Azure][Azure Databricks: Azure Blob Storage]
- [Serviço de Kubernetes do Azure](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Usar Ferramentas Avro

As [Ferramentas Avro][Avro Tools] estão disponíveis como um pacote jar. Depois de baixar o arquivo jar, você pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
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

A Captura não consome a cota de saída, pois é cobrada separadamente. 

## <a name="integration-with-event-grid"></a>Integração com a Grade de Eventos 

Você pode criar uma assinatura da Grade de Eventos do Azure com um namespace de Hubs de Eventos como sua fonte. O tutorial a seguir mostra como criar uma assinatura de grade de eventos com um hub de eventos como uma fonte e um aplicativo Azure Functions como um coletor: [processar e migrar dados de hubs de eventos capturados para uma análise de Synapse do Azure usando a grade de eventos e Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Próximas etapas
A Captura de Hubs de Eventos é a forma mais fácil de obter dados para o Azure. Usando o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, você pode executar processamento em lotes e outras análises usando ferramentas familiares e plataformas de sua escolha, na escala que precisar.

Saiba como habilitar esse recurso usando o portal do Azure e o modelo do Azure Resource Manager:

- [Usar o portal do Azure para habilitar a Captura de Hubs de Evento](event-hubs-capture-enable-through-portal.md)
- [Usar um modelo do Azure Resource Manager para habilitar a Captura de Hubs de Eventos](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture

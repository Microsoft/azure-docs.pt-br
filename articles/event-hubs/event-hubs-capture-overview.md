---
title: "Visão geral da Captura de Hubs de Eventos do Azure | Microsoft Docs"
description: "Capturar dados telemétricos com a Captura de Hubs de Eventos"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: sethm;darosa
ms.openlocfilehash: c4fd365ec8eeb389f0df9f53cd2f2a18f4c9b52a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-hubs-capture"></a>Captura de Hubs de Eventos do Azure

A Captura de Hubs de Eventos do Azure permite que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para a conta de [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) de sua escolha, com maior flexibilidade para especificar uma hora ou um período de tempo. A configuração da Captura é rápida, não há custos administrativos para executá-la e ela é dimensionada automaticamente com as [unidades de produtividade](event-hubs-features.md#capacity) dos Hubs de Eventos. A Captura de Hubs de Eventos é a maneira mais fácil de carregar dados de streaming no Azure e permite que você se concentre no processamento de dados em vez de se concentrar na captura de dados.

A Captura de Hubs de Eventos permite processar pipelines em tempo real e baseados em lote no mesmo fluxo. Isso significa que você pode criar soluções que crescem com suas necessidades ao longo do tempo. Se você estiver criando sistemas baseados em lote com o objetivo de processá-los em tempo real no futuro ou para adicionar um caminho frio eficiente para uma solução em tempo real, a Captura de Hubs de Eventos facilita o trabalho de transmissão de dados.

## <a name="how-event-hubs-capture-works"></a>Como a Captura de Hubs de Eventos funciona

Os Hubs de Eventos são um buffer de tempo de retenção durável para a entrada de telemetria, semelhante a um log distribuído. A chave para reduzir os Hubs de Eventos é o [modelo de consumidor particionado](event-hubs-features.md#partitions). Cada partição é um segmento independente de dados e é consumido de forma independente. Ao longo do tempo, esses dados expiram com base no período de retenção configurável. Assim, um Hub de Eventos específico nunca fica “cheio demais”.

A Captura de Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner, ou conta do Azure Data Lake Store, que será usado para armazenar os dados capturados. Essa conta pode estar na mesma região que o hub de eventos ou em outra região, concedendo flexibilidade ao recurso de Captura de Hubs de Eventos.

Os dados capturados são gravados no formato [Apache Avro][Apache Avro]: um formato compacto, rápido e binário que fornece estruturas de dados avançados com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory. Mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Janelas da Captura

A Captura de Hubs de Eventos permite que você configure uma janela para controlar a captura. Essa janela tem configuração mínima de tamanho e tempo com uma “política de ganha quem vem primeiro”, o que significa que o primeiro gatilho encontrado causa uma operação de captura. Se você tiver uma janela de captura de quinze minutos/100 MB e enviar 1 MB por segundo, a janela de tamanho será disparada antes da janela de tempo. Cada partição captura de forma independente e grava um blob de blocos completo durante o tempo de captura, nomeado com a hora em que o intervalo de captura foi encontrado. A convenção de nomenclatura de armazenamento é a seguinte:

```
[namespace]/[event hub]/[partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento de unidades de produtividade

O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-features.md#capacity). Uma única unidade de produtividade permite o ingresso de 1 MB por segundo ou 1.000 eventos por segundo e duas vezes essa quantidade de saída. Os Hubs de Evento Standard podem ser configurados com 1 a 20 unidades de produtividade e outras podem ser adquiridas por meio de uma [solicitação de suporte][support request] para aumento de cota. O uso além das unidades de produtividade adquiridas é restringido. A Captura de Hubs de Eventos copia os dados diretamente do armazenamento interno dos Hubs de Eventos, ignorando as cotas de saída das unidades de produtividade e salvando a saída de outros leitores de processamento, como o Stream Analytics ou o Spark.

Uma vez configurado, a Captura de Hubs de Eventos é executada automaticamente quando você envia o primeiro evento e continua em execução. Para que o seu processamento downstream saiba mais facilmente que o processo está funcionando, os Hubs de Eventos gravam arquivos vazios quando não há nenhum dado. Esse processo fornece cadência e marcador previsíveis que podem alimentar os processadores em lotes.

## <a name="setting-up-event-hubs-capture"></a>Configurando a Captura de Hubs de Eventos

Você pode configurar a Captura na hora da criação do hub de eventos usando o [Portal do Azure](https://portal.azure.com) ou usando modelos do Azure Resource Manager. Para obter mais informações, consulte os seguintes artigos:

- [Habilitar a Captura de Hubs de Evento usando o Portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um namespace de Hubs de Eventos com o hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

A Captura de Hubs de Eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurada. Você pode exibir esses arquivos em qualquer ferramenta, como o [Gerenciador de Armazenamento do Azure][Azure Storage Explorer]. Você pode baixar os arquivos localmente para trabalhar com eles.

Os arquivos produzidos pela Captura de Hubs de Eventos têm o seguinte esquema Avro:

![][3]

Uma maneira fácil de explorar arquivos do Avro é usando o jar [Ferramentas Avro][Avro Tools] do Apache. Depois de baixar o jar, você pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Esse comando retorna

```
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

## <a name="next-steps"></a>Próximas etapas

A Captura de Hubs de Eventos é a forma mais fácil de obter dados para o Azure. Usando o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, você pode executar processamento em lotes e outras análises usando ferramentas familiares e plataformas de sua escolha, na escala que precisar.

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Começar a enviar e receber eventos](event-hubs-dotnet-framework-getstarted-send.md)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md

---
title: Visão geral do armazenamento-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados no Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676844"
---
# <a name="data-storage"></a>Armazenamento de dados

Este artigo descreve o armazenamento de dados no Azure Time Series Insights Gen2. Ele cobre aquecimento e frio, disponibilidade de dados e práticas recomendadas.

## <a name="provisioning"></a>Provisionamento

Ao criar um ambiente de Azure Time Series Insights Gen2, você tem as seguintes opções:

* Armazenamento de dados frio:
  * Crie um novo recurso de armazenamento do Azure na assinatura e na região que você escolheu para o seu ambiente.
  * Anexe uma conta de armazenamento do Azure já existente. Essa opção só está disponível por meio da implantação de um [modelo](/azure/templates/microsoft.timeseriesinsights/allversions)de Azure Resource Manager e não é visível na portal do Azure.
* Armazenamento de dados quente:
  * Uma loja morna é opcional e pode ser habilitada ou desabilitada durante ou após o provisionamento. Se você decidir habilitar a loja a quente em um momento posterior e já houver dados em sua loja fria, examine [esta](concepts-storage.md#warm-store-behavior) seção abaixo para entender o comportamento esperado. O tempo de retenção de dados de armazenamento quente pode ser configurado para 7 a 31 dias, e isso também pode ser ajustado conforme necessário.

Quando um evento é ingerido, ele é indexado na loja a quente (se habilitada) e na loja fria.

[![Visão geral do armazenamento](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Como o proprietário da conta de armazenamento de Blobs do Azure, na qual os dados de armazenamento cold residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que Azure Time Series Insights gravações Gen2 porque isso pode causar perda de dados.

## <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Gen2 partições e índices de dados para obter o desempenho de consulta ideal. Os dados tornam-se disponíveis para consulta de uma loja passiva (se habilitada) e fria após sua indexação. A quantidade de dados que está sendo ingerida e a taxa de transferência por partição pode afetar a disponibilidade. Examine as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) da origem do evento e [as práticas recomendadas](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) para melhor desempenho. Você também pode configurar um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de atraso para ser notificado se o seu ambiente estiver enfrentando problemas de processamento de dados.

> [!IMPORTANT]
> Você pode experimentar um período de até 60 segundos antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa que dure mais de 60 segundos, envie um tíquete de suporte pelo portal do Azure.

## <a name="warm-store"></a>Armazenamento quente

Os dados em sua loja a quente estão disponíveis apenas por meio das [APIs de consulta de série temporal](./concepts-query-overview.md), do [Azure Time Series insights Explorer TSI](./concepts-ux-panels.md)ou do [conector de Power bi](./how-to-connect-power-bi.md). As consultas de armazenamento quente são gratuitas e não há nenhuma cota, mas há um [limite de 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) solicitações simultâneas.

### <a name="warm-store-behavior"></a>Comportamento de armazenamento quente

* Quando habilitado, todos os dados transmitidos para seu ambiente serão roteados para a sua loja passiva, independentemente do carimbo de data/hora do evento. Observe que o pipeline de ingestão de streaming é criado para streaming quase em tempo real e ingestão de eventos históricos [não é suportado](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* O período de retenção é calculado com base em quando o evento foi indexado na loja passiva, não no carimbo de data/hora do evento. Isso significa que os dados não estarão mais disponíveis na loja a quente depois que o período de retenção tiver decorrido, mesmo que o carimbo de data/hora do evento seja para o futuro.
  * Exemplo: um evento com previsões de tempo de 10 dias é ingerido e indexado em um contêiner de armazenamento quente configurado com um período de retenção de 7 dias. Após 7 dias, a previsão não é mais acessível na loja a quente, mas pode ser consultada de frio.
* Se você habilitar o armazenamento quente em um ambiente existente que já tenha dados recentes indexados no armazenamento frio, observe que a sua loja a quente não será preenchida de volta com esses dados.
* Se você acabou de habilitar a loja a quente e estiver enfrentando problemas para exibir os dados recentes no Gerenciador, você pode desativar temporariamente as consultas de loja passivas:

   [![Desabilitar consultas quentes](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Armazenamento frio

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights Gen2.

Para obter uma descrição completa do armazenamento de Blobs do Azure, leia a [Introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>Sua conta de armazenamento frio

Azure Time Series Insights Gen2 retém até duas cópias de cada evento em sua conta de armazenamento do Azure. Uma cópia armazena eventos ordenados pelo tempo de ingestão, sempre permitindo o acesso a eventos em uma sequência ordenada por tempo. Ao longo do tempo, Azure Time Series Insights Gen2 também cria uma cópia reparticionada dos dados para otimizar para consultas de alto desempenho.

Todos os seus dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

#### <a name="writing-and-editing-blobs"></a>Gravando e editando BLOBs

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite nem exclua nenhum blob que Azure Time Series Insights Gen2 cria.

#### <a name="accessing-cold-store-data"></a>Acessando dados de armazenamento frio

Além de acessar seus dados do [Azure Time Series insights Explorer](./concepts-ux-panels.md) e das [APIs de consulta de série temporal](./concepts-query-overview.md), você também pode querer acessar seus dados diretamente dos arquivos parquet armazenados na Cold Store. Por exemplo, você pode ler, transformar e limpar dados em um notebook Jupyter e, em seguida, usá-lo para treinar seu modelo do Azure Machine Learning no mesmo fluxo de trabalho do Spark.

Para acessar dados diretamente da sua conta de armazenamento do Azure, você precisa de acesso de leitura à conta usada para armazenar seus dados do Azure Time Series Insights Gen2. Você pode ler os dados selecionados com base na hora de criação do arquivo Parquet localizado na pasta `PT=Time` descrita abaixo na seção [Formato de arquivo Parquet](#parquet-file-format-and-folder-structure).  Para obter mais informações sobre como habilitar o acesso de leitura para sua conta de armazenamento, consulte [Gerenciar o acesso aos recursos de sua conta de armazenamento](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos do Azure Time Series Insights Gen2. Gerenciar dados relacionados somente no Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo Parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna open-source projetado para armazenamento e desempenho eficientes. Azure Time Series Insights Gen2 usa parquet para habilitar o desempenho de consulta baseado em ID de série temporal em escala.  

Para obter mais informações sobre o tipo de arquivo Parquet, leia a [documentação do Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 armazena cópias de seus dados da seguinte maneira:

* Na primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Esses dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Na segunda, a cópia reparticionada é agrupada por IDs da série temporal e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

O carimbo de data/hora nos nomes de blob na `PT=Time` pasta corresponde à hora de chegada dos dados para Azure Time Series insights Gen2 e não ao carimbo de data/hora dos eventos.

Os dados na pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não são estáticos. Durante o reparticionamento, alguns eventos podem estar presentes em vários BLOBs. Não há garantia de que a nomenclatura dos BLOBs nesta pasta permaneça a mesma.

Em geral, se você precisar acessar dados diretamente por meio de arquivos parquet, use a `PT=Time` pasta.  A funcionalidade futura permitirá o acesso eficiente à `PT=TsId` pasta.

> [!NOTE]
>
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundos de três dígitos (`fff`).

Azure Time Series Insights eventos Gen2 são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna de **carimbo de data/hora** com um carimbo de data/hora do evento. A propriedade de carimbo de data/hora nunca é nula. O padrão será o **tempo de enfileiramento do evento** se a propriedade de carimbo de data/hora não for especificada na origem do evento. O carimbo de data/hora armazenado sempre está no fuso horário UTC.
* Cada linha inclui as colunas de ID da série temporal (TSID) conforme definido quando o ambiente de Azure Time Series Insights Gen2 é criado. O nome da propriedade TSID inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de coluna que terminam com `_bool` (booliano), `_datetime` (carimbo de data/hora), `_long` (longo), `_double` (duplo), `_string` (cadeia de caracteres) ou `dynamic` (dinâmico), dependendo do tipo de propriedade.  Para obter mais informações, leia sobre [tipos de dados com suporte](./concepts-supported-data-types.md).
* Esse esquema de mapeamento se aplica à primeira versão do formato de arquivo, referenciado como **V=1** e armazenado na pasta base de mesmo nome. Conforme esse recurso evolui, esse esquema de mapeamento pode ser alterado e o nome de referência é incrementado.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre [modelagem de dados](./concepts-model-overview.md).

* Planeje seu [ambiente de Azure Time Series insights Gen2](./how-to-plan-your-environment.md).

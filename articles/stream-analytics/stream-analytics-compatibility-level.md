---
title: Níveis de compatibilidade Azure Stream Analytics
description: Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recente
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8105988cd2036f44d214befad3365cf9ee96ee0d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015973"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para trabalhos do Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. O Stream Analytics é um serviço gerenciado, com atualizações de recursos regulares e melhorias de desempenho. A maioria das atualizações de tempos de execução do serviço é disponibilizada automaticamente para os usuários finais. 

No entanto, algumas novas funcionalidades do serviço podem introduzir uma alteração importante, como uma alteração no comportamento de um trabalho existente ou uma alteração na maneira como os dados são consumidos em trabalhos em execução. Você pode manter os trabalhos de Stream Analytics existentes em execução sem alterações importantes, deixando a configuração de nível de compatibilidade reduzida. Quando estiver pronto para os comportamentos de tempo de execução mais recentes, você poderá optar por gerar o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolher um nível de compatibilidade

O nível de compatibilidade controla o comportamento de runtime de um trabalho do Stream Analytics. 

O Azure Stream Analytics atualmente dá suporte a três níveis de compatibilidade:

* 1,0-nível de compatibilidade original, introduzido durante a disponibilidade geral de Azure Stream Analytics há vários anos.
* 1,1-comportamento anterior
* 1,2-comportamento mais recente com melhorias mais recentes

Quando você cria um novo trabalho de Stream Analytics, é uma prática recomendada criá-lo usando o nível de compatibilidade mais recente. Inicie seu design de trabalho contando com os comportamentos mais recentes, para evitar alterações e complexidade adicionais posteriormente.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Você pode definir o nível de compatibilidade para um trabalho de Stream Analytics no portal do Azure ou usando a [chamada de API REST de trabalho Create](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel).

Para atualizar o nível de compatibilidade do trabalho no portal do Azure:

1. Use o [portal do Azure](https://portal.azure.com) para localizar em seu trabalho de Stream Analytics.
2. **Pare** o trabalho antes de atualizar o nível de compatibilidade. Você não poderá atualizar o nível de compatibilidade se seu trabalho estiver em um estado de execução.
3. No cabeçalho **Configurar** , selecione **nível de compatibilidade**.
4. Escolha o valor do nível de compatibilidade desejado.
5. Selecione **salvar** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando você atualiza o nível de compatibilidade, o compilador T valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Nível de compatibilidade 1,2

As seguintes alterações principais são introduzidas no nível de compatibilidade 1,2:

###  <a name="amqp-messaging-protocol"></a>Protocolo de mensagens AMQP

**nível 1,2**: o Azure Stream Analytics usa o protocolo de mensagens [do protocolo AMQP](../service-bus-messaging/service-bus-amqp-overview.md) para gravar em filas e tópicos do barramento de serviço. O AMQP permite que você crie várias plataformas, aplicativos híbridos usando um protocolo de padrão aberto.

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis anteriores:** Azure Stream Analytics os cálculos de Geografia usados.

**nível de 1,2:** Azure Stream Analytics permite computar coordenadas geográficas projetadas geométricas. Não há nenhuma alteração na assinatura das funções geoespaciais. No entanto, sua semântica é um pouco diferente, permitindo uma computação mais precisa do que antes.

O Azure Stream Analytics dá suporte à indexação de dados de referência geoespacial. Os dados de referência contendo elementos geoespaciais podem ser indexados para uma computação de junção mais rápida.

As funções geoespaciais atualizadas trazem a expressividade completa do formato geoespacial de texto bem conhecido (WKT). Você pode especificar outros componentes geoespaciais que anteriormente não eram compatíveis com o geojson.

Para obter mais informações, consulte [atualizações para recursos geoespaciais no Azure Stream Analytics – Cloud e IOT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução de consulta paralela para fontes de entrada com várias partições

**Níveis anteriores:** Azure Stream Analytics consultas exigiam o uso da cláusula PARTITION BY para paralelizar o processamento de consulta nas partições de origem de entrada.

**nível de 1,2:** Se a lógica de consulta puder ser paralelizada entre as partições de origem de entrada, Azure Stream Analytics criar instâncias de consulta separadas e executar cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração de API em massa nativa com saída de CosmosDB

**Níveis anteriores:** O comportamento de Upsert foi *INSERT ou Merge*.

**nível de 1,2:** A integração de API em massa nativa com saída CosmosDB maximiza a taxa de transferência e manipula com eficiência as solicitações de limitação. Para obter mais informações, consulte [a página Azure Stream Analytics saída para Azure Cosmos DB](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12).

O comportamento de Upsert é *Inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao gravar na saída do SQL

**Níveis anteriores: os** tipos de [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql) foram ajustados para UTC.

**nível de 1,2:** DateTimeOffset não está mais ajustado.

### <a name="long-when-writing-to-sql-output"></a>Longo ao gravar na saída do SQL

**Níveis anteriores:** Os valores foram truncados com base no tipo de destino.

**nível de 1,2:** Os valores que não se ajustam ao tipo de destino são tratados de acordo com a política de erro de saída.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Registro e serialização de matriz ao gravar na saída do SQL

**Níveis anteriores:** Os registros foram gravados como "registro" e as matrizes foram escritas como "matriz".

**nível de 1,2:** Os registros e as matrizes são serializados no formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita do prefixo das funções

**Níveis anteriores:** Não havia validação estrita dos prefixos de função.

**nível de 1,2:** Azure Stream Analytics tem uma validação estrita dos prefixos de função. A adição de um prefixo a uma função interna causa um erro. Por exemplo, `myprefix.ABS(…)` não tem suporte.

Adicionar um prefixo a agregações internas também resulta em erro. Por exemplo, `myprefix.SUM(…)` não tem suporte.

O uso do prefixo "System" para qualquer função definida pelo usuário resulta em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Não permitir matriz e objeto como propriedades de chave em Cosmos DB adaptador de saída

**Níveis anteriores:** Os tipos de objeto e de matriz eram suportados como uma propriedade de chave.

**nível de 1,2:** Os tipos de objeto e de matriz não têm mais suporte como uma propriedade de chave.

## <a name="compatibility-level-11"></a>Nível de compatibilidade 1,1

As alterações principais a seguir são apresentadas no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato XML do Barramento de Serviço

**nível de 1,0:** Azure Stream Analytics o DataContractSerializer usado, portanto, o conteúdo da mensagem incluía marcas XML. Por exemplo:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**nível de 1,1:** O conteúdo da mensagem contém o fluxo diretamente sem marcas adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Diferenciação de maiúsculas e minúsculas persistente para nomes de campos

**nível de 1,0:** Os nomes de campo foram alterados para minúsculas quando processados pelo mecanismo de Azure Stream Analytics.

**nível 1,1:** a diferenciação de maiúsculas e minúsculas é persistida para nomes de campo quando eles são processados pelo mecanismo de Azure Stream Analytics.

> [!NOTE]
> Diferenciação de maiúsculas e minúsculas persistente ainda não está disponível para trabalhos de Análise de Fluxo hospedados usando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas se o trabalho estiver hospedado no Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**nível de 1,0:** CREATE TABLE comando não filtrou eventos com NaN (não é um número. Por exemplo, Infinity, -Infinity) em um tipo de coluna FLOAT porque eles estão fora do intervalo documentado para esses números.

**nível de 1,1:** CREATE TABLE permite que você especifique um esquema forte. O mecanismo do Stream Analytics valida que os dados estão em conformidade com este esquema. Com esse modelo, o comando pode filtrar eventos com valores NaN.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Desabilitar a conversão automática de cadeias de caracteres DateTime para o tipo DateTime na entrada para JSON

**nível de 1,0:** O analisador JSON converteria automaticamente os valores de cadeia de caracteres com informações de data/hora/zona no tipo DATETIME na entrada para que o valor perca imediatamente sua formatação original e as informações de fuso horário. Como isso é feito na entrada, mesmo que esse campo não tenha sido usado na consulta, ele é convertido em DateTime UTC.

**nível de 1,1:** Não há conversão automática de valores de cadeia de caracteres com informações de data/hora/zona para o tipo DATETIME. Como resultado, as informações de fuso horário e a formatação original são mantidas. No entanto, se o campo NVARCHAR (MAX) for usado na consulta como parte de uma expressão DATETIME (função DATEADD, por exemplo), ele será convertido para o tipo DATETIME para executar a computação e perderá seu formato original.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics o Resource Health](./stream-analytics-troubleshoot-query.md)
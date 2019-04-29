---
title: Reconhecer o nível de compatibilidade para trabalhos do Azure Stream Analytics
description: Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: e4bbfdbcf7a295089570d4c8b77b07fd7270b3fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771679"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para trabalhos do Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. Stream Analytics é um serviço gerenciado, com atualizações do recurso normal e melhorias de desempenho. A maioria das atualizações de tempos de execução do serviço é automaticamente disponibilizada aos usuários finais. 

No entanto, algumas novas funcionalidades no serviço podem introduzir uma alteração importante, como uma alteração no comportamento de um trabalho existente, ou uma alteração na forma que os dados é consumida em trabalhos em execução. Você pode manter seus trabalhos do Stream Analytics existentes em execução sem grandes alterações, deixando o nível de compatibilidade definindo reduzida. Quando você estiver pronto para os comportamentos de tempo de execução mais recente, você pode participar ao aumentar o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolha um nível de compatibilidade

O nível de compatibilidade controla o comportamento de tempo de execução de um trabalho do Stream Analytics. 

Atualmente, o Azure Stream Analytics dá suporte a três níveis de compatibilidade:

* nível de 1.0 - padrão
* 1.1 - comportamento de liberação atual
* 1.2 (visualização) – um comportamento mais recente com os aprimoramentos mais recentes na avaliação

O nível de compatibilidade original 1.0 foi introduzido durante a disponibilidade geral do Azure Stream Analytics vários anos atrás.

Quando você cria um novo trabalho de Stream Analytics, é uma prática recomendada criá-la usando o nível de compatibilidade mais recente. Inicie seu design de trabalho após os comportamentos de mais recente da terceira parte confiável para evitar a alteração adicionadas e complexidade mais tarde.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Você pode definir o nível de compatibilidade para um trabalho do Stream Analytics no portal do Azure ou usando o [criar a chamada à API REST trabalho](/rest/api/streamanalytics/stream-analytics-job).

Para atualizar o nível de compatibilidade do trabalho no portal do Azure:

1. Use o [portal do Azure](https://portal.azure.com) localizar ao trabalho do Stream Analytics.
2. **Parar** o trabalho antes de atualizar o nível de compatibilidade. Não será possível atualizar o nível de compatibilidade se o trabalho estiver em um estado de execução.
3. Sob o **configurar** título, selecione **nível de compatibilidade**.
4. Escolha o valor de nível de compatibilidade que você deseja.
5. Selecione **salvar** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando você atualizar o nível de compatibilidade, o compilador do T-SQL valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Nível de compatibilidade 1.2

As principais alterações a seguir são apresentadas no nível de compatibilidade 1.2:

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis anteriores:** O Azure Stream Analytics usados cálculos geográficos.

**nível de 1,2:** O Azure Stream Analytics permite que você calcular as coordenadas geográficas projetada geométrico. Não há nenhuma alteração na assinatura das funções geoespaciais. No entanto, sua semântica é ligeiramente diferente, permitindo que a computação mais precisa do que antes.

O Azure Stream Analytics dá suporte à indexação de dados de referência geoespacial. Dados de referência que contém elementos geoespaciais podem ser indexados para uma computação de junção mais rápida.

As funções geoespaciais atualizado trazem a expressividade completa do formato do texto WKT (Well Known) geoespacial. Você pode especificar outros componentes geoespaciais que anteriormente não eram suportados com GeoJson.

Para obter mais informações, consulte [atualiza a recursos geoespaciais no Azure Stream Analytics – nuvem e borda de IoT](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução paralela da consulta para fontes de entrada com várias partições

**Níveis anteriores:** Consultas de Stream Analytics do Azure exigiam o uso da cláusula PARTITION BY paralelizar o processamento de consulta em partições de origem de entrada.

**nível de 1,2:** Se a lógica de consulta pode ser paralelizada entre partições de origem de entrada, o Azure Stream Analytics cria instâncias de consulta separada e executa cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração nativa de API em massa com a saída do CosmosDB

**Níveis anteriores:** O comportamento de upsert era *inserir ou mesclar*.

**nível de 1,2:** Integração nativa de API em massa com a saída do CosmosDB maximiza a taxa de transferência e com eficiência lida com solicitações de limitação. Para obter mais informações, consulte [a saída do Azure Stream Analytics para a página do Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

É o comportamento de upsert *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao gravar a saída do SQL

**Níveis anteriores:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipos foram ajustados ao UTC.

**nível de 1,2:** DateTimeOffset não é ajustado.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita de prefixo de funções

**Níveis anteriores:** Não havia nenhuma validação estrita de prefixos de função.

**nível de 1,2:** O Azure Stream Analytics tem uma validação estrita de prefixos de função. Adicionar um prefixo para uma função interna causa um erro. Por exemplo,`myprefix.ABS(…)` não tem suporte.

Adicionar um prefixo para agregações internas também resulta em erro. Por exemplo, `myprefix.SUM(…)` não tem suporte.

Usando o prefixo "system" para quaisquer resultados de funções definidas pelo usuário em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Não permitir a matriz e objeto como propriedades de chave no adaptador de saída do Cosmos DB

**Níveis anteriores:** Tipos de matriz e objeto tinham suporte como uma propriedade de chave.

**nível de 1,2:** Não há suporte para tipos de objeto e matriz como uma propriedade de chave.

## <a name="compatibility-level-11"></a>Nível de compatibilidade 1.1

As alterações principais a seguir são apresentadas no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato XML do barramento de serviço

**1.0 nível:** O Azure Stream Analytics usava o DataContractSerializer e, por isso, o conteúdo da mensagem incluía marcas XML. Por exemplo: 

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**nível de 1.1:** O conteúdo da mensagem contém o fluxo diretamente sem marcas adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Persistindo diferenciação de maiusculas e minúsculas para nomes de campo

**1.0 nível:** Os nomes de campos foram alterados para letras minúsculas quando processados pelo mecanismo do Azure Stream Analytics.

**nível 1.1:** maiusculas e minúsculas é persistida para nomes de campos quando eles são processados pelo mecanismo do Azure Stream Analytics.

> [!NOTE]
> Diferenciação de maiúsculas e minúsculas persistente ainda não está disponível para trabalhos de Análise de Fluxo hospedados usando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas se o trabalho estiver hospedado no Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 nível:** O comando CREATE TABLE não filtrava eventos com NaN (não é um número. Por exemplo, Infinity, -Infinity) em um tipo de coluna FLOAT porque eles estão fora do intervalo documentado para esses números.

**nível de 1.1:** CREATE TABLE permite especificar um esquema forte. O mecanismo do Stream Analytics valida que os dados estão em conformidade com este esquema. Com esse modelo, o comando pode filtrar eventos com valores NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Desabilite a elevação automática para cadeias de caracteres de datetime em JSON

**1.0 nível:** O analisador JSON realiza upcast automaticamente dos valores de cadeia de caracteres com informações de data/hora/fuso horário para o tipo DateTime e, em seguida, converte-os em UTC. Esse comportamento resulta na perda de informações de fuso horário.

**nível de 1.1:** Não há mais nenhum upcast automático de valores de cadeia de caracteres com informações de data/hora/fuso horário para o tipo DateTime. Dessa forma, as informações de fuso horário são mantidas.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Integridade de recursos de análise de Stream](stream-analytics-resource-health.md)

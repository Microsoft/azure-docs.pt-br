---
title: Executar a atividade de fluxo de dados no Azure Data Factory | Microsoft Docs
description: Como executar dados flui de dentro de um pipeline do data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 24b27c16573a35b1d8749d7ff381fbef970f4bd0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471651"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Executar a atividade de fluxo de dados no Azure Data Factory
Use a atividade de fluxo de dados execute para executar seu fluxo de dados ADF em execuções de depuração (área restrita) do pipeline em execuções de pipeline disparada.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Propriedades de tipo

* ```dataflow``` é o nome da entidade de fluxo de dados que você deseja executar
* ```compute``` Descreve o ambiente de execução do Spark
* ```coreCount``` é o número de núcleos para atribuir a esta execução de atividade do fluxo de dados

![Executar o fluxo de dados](media/data-flow/activity-data-flow.png "executar o fluxo de dados")

### <a name="debugging-pipelines-with-data-flows"></a>Depurar pipelines com fluxos de dados

![Botão de depuração](media/data-flow/debugbutton.png "no botão depurar")

Use os dados de fluxo de depuração para utilizar um cluster warmed para seus fluxos de dados de teste interativamente em uma execução de depuração do pipeline. Use a opção de Pipeline de depuração para testar seus fluxos de dados dentro de um pipeline.

### <a name="run-on"></a>Executar em

Este é um campo obrigatório que define qual tempo de execução de integração a ser usado para a execução de atividade de fluxo de dados. Por padrão, o Data Factory usará o tempo de execução de integração do Azure do padrão de resolução automática. No entanto, você pode criar seus próprios tempos de execução de integração do Azure que definem a regiões específicas, TTL, contagens de núcleos e tipo de computação para a execução de atividade de fluxo de dados.

A configuração padrão para execuções de fluxo de dados é de 8 núcleos de computação geral com um TTL de 60 minutos.

Escolha o ambiente de computação para essa execução do fluxo de dados. O padrão é o tempo de execução de integração do Azure de resolução automática padrão. Essa opção executará o fluxo de dados no ambiente do Spark na mesma região do seu data factory. O tipo de computação será um cluster de trabalho, o que significa que o ambiente de computação pode levar vários minutos para inicialização.

Você tem controle sobre o ambiente de execução do Spark para suas atividades de fluxo de dados. No [tempo de execução de integração do Azure](concepts-integration-runtime.md) são configurações para definir o tipo de computação (uso geral, com otimização de memória e otimizado para computação), número de núcleos de trabalhador e time-to-live para coincidir com o mecanismo de execução com a computação de fluxo de dados requisitos. Além disso, a definir o TTL permitirá manter um cluster passiva que está imediatamente disponível para as execuções de trabalho.

![Tempo de execução de integração do Azure](media/data-flow/ir-new.png "tempo de execução de integração do Azure")

> [!NOTE]
> A seleção de tempo de execução de integração na atividade de fluxo de dados se aplica somente aos *disparado execuções* do seu pipeline. Depurando seu pipeline com os dados fluem com depuração serão executadas em cluster do Spark padrão de 8 núcleos.

### <a name="staging-area"></a>Área de preparação

Se você está afundando seus dados no Data Warehouse do Azure, você deve escolher um local de preparo para sua carga de lote do Polybase. As configurações de preparo só são aplicáveis a cargas de trabalho de Data Warehouse do Azure.

## <a name="parameterized-datasets"></a>Conjuntos de dados com parâmetros

Se você estiver usando conjuntos de dados com parâmetros, certifique-se de definir os valores de parâmetro.

![Execute os parâmetros de fluxo de dados](media/data-flow/params.png "parâmetros")

## <a name="parameterized-data-flows"></a>Fluxos de dados com parâmetros

Se você tiver parâmetros dentro de seu fluxo de dados, você definirá os valores dinâmicos dos seus parâmetros de fluxo de dados aqui na seção de parâmetros da atividade de execução de fluxo de dados. Você pode usar a linguagem de expressão de Pipeline do ADF (somente para tipos de parâmetro de cadeia de caracteres) ou a linguagem de expressão de fluxo de dados para definir os valores de parâmetro com expressões dinâmicas ou literais valores estáticos.

![Executar o exemplo de parâmetro de fluxo de dados](media/data-flow/parameter-example.png "exemplo de parâmetro")

### <a name="debugging-data-flows-with-parameters"></a>Depuração de fluxos de dados com parâmetros

No momento atual, só é possível depurar fluxos de dados com parâmetros de Pipeline depurar executados usando a atividade de fluxo de dados execute. As sessões de depuração interativa no fluxo de dados de ADF estará disponível em breve. Execuções de pipeline e execuções de depuração, no entanto, trabalhará com parâmetros.

É uma boa prática compilar seu fluxo de dados com conteúdo estático para que você tenha a propagação de coluna de metadados completos disponível em tempo de design para a solução de problemas. Em seguida, substitua o conjunto de dados estático com um conjunto de dados com parâmetros dinâmico quando você operacionaliza seu pipeline de fluxo de dados.

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)

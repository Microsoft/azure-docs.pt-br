---
title: Executar a atividade de fluxo de dados no Azure Data Factory | Microsoft Docs
description: A atividade de fluxo de dados do execute executa fluxos de dados.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557510"
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

### <a name="run-on"></a>Executar em

Escolha o ambiente de computação para essa execução do fluxo de dados. O padrão é o tempo de execução de integração do Azure de resolução automática padrão. Essa opção executará o fluxo de dados no ambiente do Spark na mesma região do seu data factory. O tipo de computação será um cluster de trabalho, o que significa que o ambiente de computação pode levar vários minutos para inicialização.

### <a name="debugging-pipelines-with-data-flows"></a>Depurar pipelines com fluxos de dados

![Botão de depuração](media/data-flow/debugbutton.png "no botão depurar")

Use os dados de fluxo de depuração para utilizar um cluster warmed para seus fluxos de dados de teste interativamente em uma execução de depuração do pipeline. Use a opção de depuração Pipleine para testar seus fluxos de dados dentro de um pipeline.

### <a name="compute-type"></a>Tipo de computação

Você pode escolher o uso geral, computação otimizada ou com otimização de memória, dependendo dos requisitos do fluxo de dados.

### <a name="core-count"></a>Contagem de núcleos

Escolha quantos núcleos que você deseja atribuir ao trabalho. Para trabalhos menores, menos núcleos funcionará melhor.

### <a name="staging-area"></a>Área de preparação

Se você está afundando seus dados no Data Warehouse do Azure, você deve escolher um local de preparo para sua carga de lote do Polybase.

## <a name="parameterized-datasets"></a>Conjuntos de dados com parâmetros

Se você estiver usando conjuntos de dados com parâmetros, certifique-se de definir os valores de parâmetro.

![Execute os parâmetros de fluxo de dados](media/data-flow/params.png "parâmetros")

### <a name="debugging-parameterized-data-flows"></a>Depuração de fluxos de dados com parâmetros

Só é possível depurar fluxos de dados com conjuntos de dados com parâmetros de Pipeline depurar executados usando a atividade de fluxo de dados execute. Atualmente, as sessões de depuração interativa no fluxo de dados de ADF não funcionam com conjuntos de dados com parâmetros. Execuções de pipeline e execuções de depuração funcionará com parâmetros.

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)

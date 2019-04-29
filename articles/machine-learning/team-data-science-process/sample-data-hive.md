---
title: Dados de exemplo nas tabelas Hive do Azure HDInsight - Processo da Ciência de Dados da Equipe
description: Reduzir dados armazenados nas tabelas Hive do Azure HDInsight usando consultas de Hive para reduzir os dados a um tamanho mais gerenciável para análise.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042876"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo nas tabelas Hive do Azure HDInsight
Este artigo descreve como reduzir os dados de amostra armazenados nas tabelas Hive do Azure HDInsight usando consultas de Hive para reduzir isso a um tamanho mais gerenciável para análise. Abordaremos três métodos de amostragem popularmente usados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem estratificada

**Por que fazer amostragem dos dados?**
Se o conjunto de dados que você deseja analisar for grande, geralmente, é uma boa ideia reduzir os dados para um tamanho menor, mas representativo e mais gerenciável. Facilitando a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Ciência de Dados de Equipe é permitir a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Como enviar consultas de Hive
Consultas de Hive podem ser enviadas do console de Linha de Comando do Hadoop no nó principal do cluster do Hadoop. Para isso, faça logon no nó principal do cluster do Hadoop, abra o Console de Linha de Comando do Hadoop e envie as consultas de Hive de lá. Para obter instruções sobre como enviar consultas de Hive no console Linha de Comando do Hadoop, confira [Como enviar consultas de Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Amostragem aleatória uniforme
A amostragem aleatória uniforme significa que cada linha no conjunto de dados tem a mesma chance de amostra. Isso pode ser implementado adicionando um campo rand() extra ao conjunto de dados na consulta interna "select" e na consulta externa “select”, esta condição estará nesse campo aleatório.

Veja um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` especifica a proporção de registros que os usuários desejam como amostra.

## <a name="group"></a> Amostragem aleatória por grupos
Ao realizar amostragem de dados categóricos, convém incluir ou excluir todas as instâncias de um determinado valor de uma variável categórica. Esse tipo de amostragem é chamado de "amostragem pelo grupo". Por exemplo, se você tiver uma variável categórica "*Estado*", que tem como valores NY, MA, CA, NJ, PA, etc. (siglas de estados dos EUA), o ideal é que registros do mesmo estado estejam sempre juntos, presentes ou não como amostra.

Veja um exemplo de consulta com amostragem por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Amostragem estratificada
A amostragem aleatória é estratificada em relação a uma variável categórica quando as amostras obtidas têm valores da categoria em questão que estão na mesma proporção como a população-mãe. Usando o mesmo exemplo acima, vamos supor que os dados têm subpopulações por estados: NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se você especificar a taxa de amostragem estratificada para ser 0,5, a amostra obtida deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respectivamente.

Veja um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre os métodos de amostragem mais avançados disponíveis no Hive, consulte [Amostragem LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).


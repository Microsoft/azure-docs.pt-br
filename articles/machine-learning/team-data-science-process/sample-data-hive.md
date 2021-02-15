---
title: Dados de exemplo nas tabelas Hive do Azure HDInsight - Processo da Ciência de Dados da Equipe
description: Reduzir dados armazenados nas tabelas Hive do Azure HDInsight usando consultas de Hive para reduzir os dados a um tamanho mais gerenciável para análise.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321888"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo nas tabelas Hive do Azure HDInsight
Este artigo descreve como reduzir os dados de amostra armazenados nas tabelas Hive do Azure HDInsight usando consultas de Hive para reduzir isso a um tamanho mais gerenciável para análise. Ele aborda três métodos de amostragem populares usados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem estratificada

**Por que fazer amostragem dos dados?**
Se o conjunto de dados que você deseja analisar for grande, geralmente, é uma boa ideia reduzir os dados para um tamanho menor, mas representativo e mais gerenciável. Facilitando a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Ciência de Dados de Equipe é permitir a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Como enviar consultas de Hive
Consultas de Hive podem ser enviadas do console de Linha de Comando do Hadoop no nó principal do cluster do Hadoop.  Faça logon no nó principal do cluster do Hadoop, abra o console de Command-Line do Hadoop e envie as consultas de Hive a partir daí. Para obter instruções sobre como enviar consultas de Hive no console Linha de Comando do Hadoop, confira [Como enviar consultas de Hive](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Amostragem aleatória uniforme
A amostragem aleatória uniforme significa que cada linha no conjunto de dados tem a mesma chance de amostra. Isso pode ser implementado adicionando um campo rand() extra ao conjunto de dados na consulta interna "select" e na consulta externa “select”, esta condição estará nesse campo aleatório.

Veja um exemplo de consulta:

```python
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
```

Aqui, `<sample rate, 0-1>` especifica a proporção de registros que os usuários desejam como amostra.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Amostragem aleatória por grupos
Ao realizar amostragem de dados categóricos, convém incluir ou excluir todas as instâncias de um determinado valor de uma variável categórica. Esse tipo de amostragem é chamado de "amostragem pelo grupo". Por exemplo, se você tiver uma variável categórica " *Estado* ", que tem como valores NY, MA, CA, NJ, PA, etc. (siglas de estados dos EUA), o ideal é que registros do mesmo estado estejam sempre juntos, presentes ou não como amostra.

Veja um exemplo de consulta com amostragem por grupo:

```python
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
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Amostragem estratificada
A amostragem aleatória é estratificada em relação a uma variável categórica quando as amostras obtidas têm valores da categoria em questão que estão na mesma proporção como a população-mãe. Usando o mesmo exemplo anterior, suponha que os dados têm subpopulações por estados: NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se você especificar a taxa de amostragem estratificada para ser 0,5, a amostra obtida deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respectivamente.

Veja um exemplo de consulta:

```hiveql
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
```

Para obter informações sobre os métodos de amostragem mais avançados disponíveis no Hive, consulte [Amostragem LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
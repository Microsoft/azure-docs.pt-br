---
title: Operadores úteis nas consultas de log no Azure Monitor | Microsoft Docs
description: Funções comuns a serem usadas em diferentes cenários nas consultas do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424131"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Operadores úteis nas consultas de log no Azure Monitor

A tabela abaixo fornece algumas funções comuns a serem usadas em diferentes cenários nas consultas de log do Azure Monitor.

## <a name="useful-operators"></a>Operadores úteis

Category                                |Função de Análise Relevante
----------------------------------------|----------------------------------------
Aliases de Seleção e Coluna            |`project`, `project-away`, `extend`
Constantes e tabelas temporárias          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Operadores de Comparação e Cadeia de Caracteres         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Funções de cadeia de caracteres comuns                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Funções matemáticas comuns                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Análise de texto                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitação de saída                         |`take`, `limit`, `top`, `sample`
Funções de data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Agrupamento e agregação                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Junções e Uniões                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Classificação, ordem                             |`sort`, `order` 
Objeto dinâmico (JSON e matriz)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operadores lógicos                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Aprendizado de máquina                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Próximas etapas

- Faça uma lição sobre como [escrever consultas de log no Azure Monitor](get-started-queries.md).

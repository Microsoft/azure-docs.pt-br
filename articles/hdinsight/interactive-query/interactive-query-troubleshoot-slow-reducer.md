---
title: O redutor está lento no Azure HDInsight
description: O redutor está lento no Azure HDInsight a partir de uma possível distorção de dados
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930816"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Cenário: o redutor está lento no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta como `insert into table1 partition(a,b) select a,b,c from table2` o plano de consulta inicia um monte de redutores, mas os dados de cada partição vão para um único redutor. Isso faz com que a consulta seja tão lenta quanto o tempo gasto pelo redutor da partição maior.

## <a name="cause"></a>Causa

Abra [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verifique o valor de Set `hive.optimize.sort.dynamic.partition` .

O valor dessa variável deve ser definido como true/false com base na natureza dos dados.

Se as partições na tabela de entrada forem menores (digamos menos de 10) e, portanto, for o número de partições de saída e a variável for definida como `true` , isso fará com que os dados sejam classificados globalmente e gravados usando um único redutor por partição. Mesmo que o número de redutores disponível seja maior, alguns redutores podem estar atrasados devido à distorção de dados e o paralelismo máximo não pode ser obtido. Quando alterado para `false` , mais de um redutor pode manipular uma única partição e vários arquivos menores serão gravados, resultando em uma inserção mais rápida. Isso pode afetar outras consultas, embora devido à presença de arquivos menores.

Um valor `true` faz sentido quando o número de partições é maior e os dados não são distorcidos. Nesses casos, o resultado da fase de mapa será escrito de forma que cada partição será tratada por um único redutor, resultando em melhor desempenho de consulta subsequente.

## <a name="resolution"></a>Resolução

1. Tente reparticionar os dados para normalizar em várias partições.

1. Se #1 não for possível, defina o valor da configuração como false na sessão beeline e tente a consulta novamente. `set hive.optimize.sort.dynamic.partition=false`. Não é recomendável definir o valor como false em um nível de cluster. O valor de `true` é ideal e define o parâmetro conforme necessário com base na natureza dos dados e da consulta.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
---
title: Dimensionar automaticamente instâncias de Apache Spark
description: Usar o recurso de autoescala do Azure Synapse para dimensionar automaticamente as instâncias de Apache Spark
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: f34bcfa8b743fbee6ee3b78fc1a042d1df0abfde
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313630"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Dimensionar automaticamente pools do Apache Spark do Azure Synapse Analytics

O recurso Dimensionamento automático do pool do Apache Spark para Azure Synapse Analytics escala ou reduz verticalmente de maneira automática o número de nós em uma instância de cluster. Durante a criação de um pool do Apache Spark para Azure Synapse Analytics, um número mínimo e máximo de nós pode ser definido quando o Dimensionamento automático é selecionado. O dimensionamento automático monitora os requisitos do recurso da carga e escala ou reduz verticalmente o número de nós. Não há nenhum custo adicional para esse recurso.

## <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora a instância do Spark continuamente e coleta as seguintes métricas:

|Métrica|Descrição|
|---|---|
|Total de CPU pendente|O número total de núcleos necessário para iniciar a execução de todos os nós pendentes.|
|Total de memória pendente|O total de memória (em MB) necessário para iniciar a execução de todos os nós pendentes.|
|Total de CPU livre|A soma de todos os núcleos não utilizados em nós ativos.|
|Total de memória livre|A soma de memória não usada (em MB) em nós ativos.|
|Memória usada por nó|A carga em um nó. Um nó no qual 10 GB de memória são usados é considerado estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.|

As métricas acima são verificadas a cada 30 segundos. O dimensionamento automático escala ou reduz verticalmente as decisões com base nessas métricas.

## <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando as seguintes condições forem detectadas, o Dimensionamento automático emitirá uma solicitação de dimensionamento:

|Escalar verticalmente|Reduzir verticalmente|
|---|---|
|O total de CPU pendente é maior que o total de CPU livre por mais de 1 minuto.|O total de CPU pendente é menor que o total de CPU livre por mais de 2 minutos.|
|O total de memória pendente é maior que o total de memória livre por mais de 1 minuto.|O total de memória pendente é menor que o total de memória livre por mais de 2 minutos.|

Para escalar verticalmente, o serviço Dimensionamento automático do Azure Synapse calcula quantos novos nós são necessários para atender aos requisitos atuais de CPU e memória e emite uma solicitação de escala vertical para adicionar o número necessário de nós.

Para reduzir verticalmente, com base no número de executores, mestres de aplicativo por nó e os requisitos atuais de CPU e memória, o Dimensionamento automático emite uma solicitação para remover um determinado número de nós. O serviço também detecta quais nós são candidatos para remoção com base na execução do trabalho atual. A operação de redução encerra primeiro os nós e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>Criar um pool de Apache Spark sem servidor com dimensionamento automático

Para habilitar o recurso Dimensionamento automático, execute as seguintes etapas como parte do processo normal de criação de pool:

1. Na guia **Noções básicas** , marque a caixa de seleção **Habilitar dimensionamento automático**.
1. Insira os valores desejados para as seguintes propriedades:  

    * Número **mínimo** de nós.
    * Número **máximo** de nós.

O número inicial de nós será o mínimo. Esse valor define o tamanho inicial da instância quando ela é criada. O número mínimo de nós não pode ser menor que três.

## <a name="best-practices"></a>Práticas recomendadas

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerar a latência de operações de aumento ou redução

Pode levar de 1 a 5 minutos para que uma operação de dimensionamento seja concluída.

### <a name="prepare-for-scaling-down"></a>Preparar para reduzir verticalmente

Durante o processo de redução de escala da instância, o dimensionamento automático colocará os nós no estado de encerramento para que nenhum executor novo possa ser iniciado nesse nó.

Os trabalhos em execução continuarão sendo executados e concluídos. Os trabalhos pendentes aguardarão para serem agendados normalmente com menos nós disponíveis.

## <a name="next-steps"></a>Próximas etapas

Início rápido para configurar um novo pool do Spark [Criar um pool do Spark](../quickstart-create-apache-spark-pool-portal.md)

---
title: Conceitos de pool de Apache Spark
description: Introdução à Apache Spark de tamanhos e configurações de pool no Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606150"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Configurações de pool de Apache Spark no Azure Synapse Analytics

Um pool do Spark é um conjunto de metadados que define os requisitos de recursos de computação e as características de comportamento associadas quando uma instância do Spark é instanciada. Essas características incluem, mas não se limitam a nome, número de nós, tamanho do nó, comportamento de dimensionamento e vida útil. Um pool do Spark em si não consome nenhum recurso. Não há custos incorridos com a criação de pools do Spark. Os encargos só são incorridos quando um trabalho do Spark é executado no pool do Spark de destino e a instância do Spark é instanciada sob demanda.

Você pode ler como criar um Pool do Spark e ver todas as suas propriedades em [Introdução aos Pools do Spark no Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Nós

A instância do pool de Apache Spark consiste em um nó de cabeçalho e dois ou mais nós de trabalho com um mínimo de três nós em uma instância do Spark.  O nó de cabeçalho executa serviços de gerenciamento adicionais, como Livy, yarn Resource Manager, Zookeeper e o driver Spark.  Todos os nós executam serviços como o node Agent e o Gerenciador de nó do yarn. Todos os nós de trabalho executam o serviço de executor do Spark.

## <a name="node-sizes"></a>Tamanhos de nó

Um pool do Spark pode ser definido com tamanhos de nó que variam de um pequeno nó de computação com 8 vCore e 64 GB de memória até um nó de computação XXLarge com 64 vCore e 432 GB de memória por nó. Os tamanhos de nó podem ser alterados após a criação do pool, embora a instância precise ser reiniciada.

|Tamanho | vCore | Memória|
|-----|------|-------|
|Small|4|32 GB|
|Médio|8|64 GB|
|Grande|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Autoscale

Os pools de Apache Spark fornecem a capacidade de aumentar e reduzir automaticamente os recursos de computação com base na quantidade de atividade.  Quando o recurso de dimensionamento automático estiver habilitado, você poderá definir o número mínimo e máximo de nós a serem dimensionados.
Quando o recurso de dimensionamento automático estiver desabilitado, o número de nós definido permanecerá fixo.  Essa configuração pode ser alterada após a criação do pool, embora a instância precise ser reiniciada.

## <a name="automatic-pause"></a>Pausar automaticamente

O recurso de pausa automática libera recursos após um período de ociosidade definido, reduzindo o custo geral de um pool de Apache Spark.  O número de minutos de tempo ocioso pode ser definido quando esse recurso estiver habilitado.  O recurso de pausa automática é independente do recurso de dimensionamento automático. Os recursos podem ser pausados se o dimensionamento automático estiver habilitado ou desabilitado.  Essa configuração pode ser alterada após a criação do pool, embora a instância precise ser reiniciada.

## <a name="next-steps"></a>Próximas etapas

* [Azure Synapse Analytics](../index.yml)
* [Documentação do Apache Spark](https://spark.apache.org/docs/2.4.5/)
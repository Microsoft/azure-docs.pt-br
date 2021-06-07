---
title: Conceitos principais do Apache Spark
description: Introdução aos conceitos principais do Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120301"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Conceitos principais do Apache Spark no Azure Synapse Analytics

O Apache Spark é uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análise de Big Data. O Apache Spark no Azure Synapse Analytics é uma das implementações da Microsoft do Apache Spark na nuvem. 

O Azure Synapse facilita a criação e a configuração de recursos do Spark no Azure. O Azure Synapse fornece uma implementação diferente dos recursos do Spark que estão documentados aqui.

## <a name="spark-pools"></a>Pools do Spark

Um Pool do Apache Spark sem servidor foi criado no portal do Azure. É a definição de um Pool do Spark que, após a criação de uma instância, é usada para criar uma instância do Spark que processa dados. Quando um Pool do Spark é criado, ele existe somente como metadados e nenhum recurso é consumido, executado nem cobrado. Um Pool do Spark tem uma série de propriedades que controlam as características de uma instância do Spark. Essas características incluem, sem limitações, o nome, o tamanho, o comportamento de dimensionamento e a vida útil.

Como não há nenhum custo monetário nem de recurso associado à criação de um pool do Spark, qualquer quantidade pode ser criada, com qualquer quantidade de configurações diferentes. Permissões também podem ser aplicadas a Pools do Spark, permitindo que os usuários tenham acesso apenas a alguns pools específicos.

A melhor prática é criar Pools do Spark menores que possam ser usados para desenvolvimento e depuração e, em seguida, criar maiores para executar cargas de trabalho de produção.

Você pode ler como criar um Pool do Spark e ver todas as suas propriedades em [Introdução aos pools do Spark no Azure Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Instâncias do Spark

As instâncias do Spark são criadas quando você se conecta a um Pool do Spark, cria uma sessão e executa um trabalho. Como vários usuários podem ter acesso a um Pool do Spark, uma instância do Spark é criada para cada usuário que se conecta. 

Quando você envia um segundo trabalho, se houver capacidade no pool, a instância existente do Spark também terá capacidade. Em seguida, a instância existente processará o trabalho. Caso contrário, se a capacidade estiver disponível no nível do pool, uma instância do Spark será criada.

## <a name="examples"></a>Exemplos

### <a name="example-1"></a>Exemplo 1

- Você cria um Pool do Spark chamado SP1; ele tem um tamanho de cluster fixo de 20 nós.
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Então você envia outro trabalho, J2, que usa 10 nós porque ainda há capacidade no pool e na instância; o J2, é processado pela SI1.
- Se o J2 solicitasse 11 nós, não haveria capacidade no SP1 nem na SI1. Nesse caso, se o J2 vier de um notebook, o trabalho será rejeitado; se o J2 vier de um trabalho em lotes, ele será enfileirado.

### <a name="example-2"></a>Exemplo 2

- Você cria uma chamada ao Pool do Spark SP2; ele tem um dimensionamento automático habilitado de 10 a 20 nós
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Agora você envia outro trabalho, J2, que usa 10 nós, porque ainda há capacidade no pool que a instância aumenta automaticamente para 20 nós e processa o J2.

### <a name="example-3"></a>Exemplo 3

- Você cria um Pool do Spark chamado SP1; ele tem um tamanho de cluster fixo de 20 nós.
- Você envia um trabalho de notebook J1 que usa 10 nós, uma instância do Spark, SI1, é criada para processar o trabalho.
- Outro usuário, U2, envia um trabalho, J3, que usa 10 nós; uma instância do Spark, SI2, é criada para processar o trabalho.
- Agora você envia outro trabalho, J2, que usa dez nós porque ainda há capacidade no pool e na instância, e J2 é processado por SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Cotas e restrições de recursos no Apache Spark para o Azure Synapse

### <a name="workspace-level"></a>Nível do workspace

Cada workspace do Azure Synapse é fornecido com uma cota padrão de vCores que pode ser usada no Spark. A cota é dividida entre a cota do usuário e a cota de fluxo de trabalho para que nenhum padrão de uso ocupe todos os vCores no workspace. A cota é diferente dependendo do tipo de assinatura, mas é simétrica entre o usuário e o fluxo de dados. No entanto, se você solicitar mais vCores do que o restante no workspace, receberá o seguinte erro:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

O link na mensagem indica este artigo.

O artigo a seguir descreve como solicitar um aumento na cota do vCore do workspace.

- Selecione "Azure Synapse Analytics" como o tipo de serviço.
- Na janela Detalhes da cota, selecione Apache Spark (vCore) por workspace

[Solicitar um aumento de capacidade por meio do portal do Azure](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Nível do Pool do Spark

Quando você define um Pool do Spark, está efetivamente definindo uma cota por usuário para esse pool; se você executar vários notebooks, trabalhos ou uma combinação dos dois, é possível esgotar a cota do pool. Se você fizer isso, será gerada uma mensagem de erro como a que está a seguir

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Para resolver esse problema, você precisa reduzir o uso dos recursos do pool antes de enviar uma nova solicitação de recurso executando um bloco de anotações ou um trabalho.

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../index.yml)
- [Documentação do Apache Spark](https://spark.apache.org/docs/2.4.5/)
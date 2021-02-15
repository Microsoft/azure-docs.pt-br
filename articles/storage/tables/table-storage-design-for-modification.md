---
title: Projetar o armazenamento de tabelas do Azure para modificação de dados | Microsoft Docs
description: Projetar tabelas para modificação de dados no armazenamento de tabelas do Azure. Otimizar as operações de inserção, atualização e exclusão. Garanta a consistência em suas entidades armazenadas.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236753"
---
# <a name="design-for-data-modification"></a>Design para modificação de dados
Este artigo concentra-se nas considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, você precisará avaliar a compensação entre designs que otimizam para consulta em relação a designs que otimizam para modificação de dados, da mesma forma que em designs de bancos de dados relacionais (embora as técnicas para gerenciar vantagens e desvantagens do design sejam diferentes em um banco de dados relacional). A seção Padrões de design de tabela descreve alguns padrões de design detalhados para o serviço Tabela e destaca algumas compensações. Na prática, você descobrirá que muitos designs otimizados para entidades de consulta também funcionam bem para modificar entidades.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho das operações de inserção, atualização e exclusão
Para atualizar ou excluir uma entidade, você deve ser capaz de identificá-la usando os valores de **PartitionKey** e **RowKey**. Nesse sentido, a escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhantes à escolha para dar suporte a consultas de ponto, a fim identificar entidades do modo mais eficiente possível. Não convém usar uma verificação ineficiente de tabela ou de partição para localizar uma entidade, a fim de descobrir os valores de **PartitionKey** e **RowKey** necessários para atualizá-la ou excluí-la.  

Os padrões a seguir na seção Padrões de design de tabela abordam a otimização do desempenho ou as operações de inserção, atualização e exclusão:  

* [Padrão de exclusão de alto volume](table-storage-design-patterns.md#high-volume-delete-pattern) - Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em suas próprias tabelas separadas; exclua as entidades por meio da exclusão da tabela.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) - Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  
* [Padrão de entidades longas](table-storage-design-patterns.md#wide-entities-pattern) - Use várias entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de entidades grandes](table-storage-design-patterns.md#large-entities-pattern) - Use o armazenamento de blobs para armazenar grandes valores de propriedade.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Garantir a consistência nas entidades armazenadas
Outro fator-chave que influencia sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência usando transações atômicas. Você só pode usar uma EGT para operar em entidades armazenadas na mesma partição.  

Os padrões a seguir no artigo [Padrões de design de tabela](table-storage-design-patterns.md) endereçam a consistência de gerenciamento:  

* [Padrão de índice secundário intrapartição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores de **RowKey** diferentes.  
* [Padrão de índice secundário entre partições](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições separadas ou em tabelas separadas, a fim de permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey**.  
* [Padrão de transações eventualmente consistentes](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.
* [Padrão de entidades de índice](table-storage-design-patterns.md#index-entities-pattern) – manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  
* [Padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) - Combine dados relacionados juntos em uma única entidade, para que você possa recuperar todos os dados de que precisa com uma única consulta de ponto.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) - Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  

Para obter informações sobre transações de grupo de entidades, consulte a seção [transações do grupo de entidades](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir o design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas você sempre deve avaliar se esse é o caso para seu cenário específico. Alguns dos padrões no artigo [Padrões de design de tabela](table-storage-design-patterns.md) avaliam explicitamente as compensações entre a consulta e a modificação de entidades, e você deve sempre levar em consideração o número de cada tipo de operação.  

Os padrões a seguir, no artigo [Padrões de design de tabela](table-storage-design-patterns.md), abordam as compensações entre o design para consultas eficientes e o design para modificação eficiente de dados:  

* [Padrão de chave composta](table-storage-design-patterns.md#compound-key-pattern) - Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma consulta de único ponto.  
* [Padrão de rastro do log](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* adicionadas recentemente em uma partição, usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

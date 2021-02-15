---
title: Criar o armazenamento de tabelas do Azure para consultas | Microsoft Docs
description: Projetar tabelas para consultas no armazenamento de tabelas do Azure. Escolha uma chave de partição apropriada, Otimize as consultas e classifique os dados para o serviço tabela.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316156"
---
# <a name="design-for-querying"></a>Design para consulta
Soluções de serviço Tabela podem ser de leitura intensiva, gravação intensiva ou uma combinação dos dois. Este artigo aborda as coisas a serem lembradas ao criar seu serviço Tabela para dar suporte a operações de leitura com eficiência. Normalmente, um design que dá suporte a operações de leitura com eficiência também é eficiente para operações de gravação. No entanto, neste artigo, [Design para modificação de dados](table-storage-design-for-modification.md), existem e são discutidas considerações adicionais para se ter em mente ao projetar de modo a dar suporte a operações de gravação.

Um bom ponto de partida para criar a solução serviço Tabela para que você possa ler os dados com eficiência é perguntar "Quais consultas meu aplicativo precisará executar para recuperar os dados necessários do serviço Tabela?"  

> [!NOTE]
> Com o serviço Tabela, é importante obter o design correto com antecedência porque é difícil e caro alterá-lo depois. Por exemplo, em um banco de dados relacional é frequentemente possível solucionar problemas de desempenho simplesmente adicionando índices a um banco de dados: isso não é uma opção com o serviço Tabela.  
> 
> 

Esta seção se concentra nos principais problemas que você deve abordar ao projetar suas tabelas para consulta. Os tópicos abordados nesta seção incluem:

* [Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolhendo uma PartitionKey apropriada](#choosing-an-appropriate-partitionkey)
* [Otimizando consultas para o serviço Tabela](#optimizing-queries-for-the-table-service)
* [Armazenando dados no serviço Tabela](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta
Os exemplos a seguir pressupõem que o serviço Tabela é armazenar entidades de funcionário com a seguinte estrutura (a maioria dos exemplos omite a propriedade **Timestamp** por motivos de clareza):  

| *Nome da coluna* | *Data type* |
| --- | --- |
| **PartitionKey** (nome de departamento) |String |
| **RowKey** (ID do funcionário) |String |
| **Nome** |String |
| **Sobrenome** |String |
| **Age** |Integer |
| **EmailAddress** |String |

O artigo [Visão geral do armazenamento de Tabela do Azure](table-storage-overview.md) descreve alguns dos principais recursos do serviço Tabela do Azure que têm uma influência direta no design para consulta. Isso resulta nas seguintes diretrizes gerais para a criação de consultas do serviço Tabela. Observe que a sintaxe de filtro usada nos exemplos a seguir é proveniente da API REST do serviço Tabela. Para obter mais informações, veja [Query Entities](/rest/api/storageservices/Query-Entities) (Consultar entidades).  

* Uma consulta do * **Point** _ é a pesquisa mais eficiente a ser usada e é recomendável usá-la para pesquisas de alto volume ou pesquisas que exigem menor latência. Essa consulta pode usar os índices para localizar uma entidade individual com muita eficiência, especificando os valores _ *PartitionKey* * e **RowKey** . Por exemplo: $filter=(PartitionKey eq 'Sales') and (RowKey eq '2')  
* O segundo melhor é uma **consulta de intervalo** * que usa o _ *PartitionKey* * e os filtros em um intervalo de valores de **RowKey** para retornar mais de uma entidade. O valor de **PartitionKey** identifica uma partição específica e os valores de **RowKey** identificam um subconjunto das entidades na partição. Por exemplo: $filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'  
* A terceira melhor é uma * **verificação de partição** _ que usa o _ *PartitionKey* * e os filtros em outra propriedade que não seja de chave e que pode retornar mais de uma entidade. O valor **PartitionKey** identifica uma partição específica e os valores de propriedades selecionados para um subconjunto das entidades nessa partição. Por exemplo: $filter=PartitionKey eq 'Sales' and LastName eq 'Smith'  
* Uma * **verificação de tabela** _ não inclui o _ *PartitionKey* * e é muito ineficiente porque pesquisa todas as partições que compõem sua tabela por vez para qualquer entidade correspondente. A verificação da tabela será realizada, independentemente de o filtro usar ou não a **RowKey**. Por exemplo: $filter=LastName eq 'Dias'  
* As consultas que retornam várias entidades as retornam classificadas na ordem **PartitionKey** e **RowKey**. Para evitar reclassificar as entidades no cliente, escolha uma **RowKey** que define a ordem de classificação mais comum.  

Observe que o uso de um operador " **or** " para especificar um filtro com base em valores de **RowKey** resulta em uma verificação de partição, e não é tratado como uma consulta de intervalo. Portanto, você deve evitar consultas que usam filtros, como: $filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')  

Para obter exemplos de código de cliente que usam a Biblioteca de Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Executar uma consulta de ponto usando a Biblioteca de Cliente de Armazenamento](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Recuperar várias entidades usando LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projeção do lado do servidor](table-storage-design-patterns.md#server-side-projection)  

Para obter exemplos de código do lado do cliente que pode lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogênea](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Escolhendo uma PartitionKey apropriada
Sua escolha de **PartitionKey** deve equilibrar a necessidade de habilitar o uso de EGTs (para garantir a consistência) com a necessidade de distribuir suas entidades por várias partições (para garantir uma solução escalonável).  

Por um lado, você pode armazenar todas as suas entidades em uma única partição, mas isso pode limitar a escalabilidade da solução e impedir que o serviço Tabela seja capaz de solicitar balanceamento de carga. Por outro lado, você pode armazenar uma entidade por partição, o que seria altamente escalonável e permitiria que o serviço Tabela balanceasse a carga das solicitações, mas impediria o uso de transações em grupo de entidades.  

Uma **PartitionKey** ideal é aquela que permite que você use consultas eficientes e que tenha partições suficientes para garantir que sua solução seja escalonável. Normalmente, você descobrirá que as entidades terão uma propriedade adequada que distribui suas entidades em partições suficientes.

> [!NOTE]
> Por exemplo, em um sistema que armazena informações sobre usuários ou funcionários, UserID pode ser uma boa PartitionKey. Você pode ter várias entidades que usam uma determinada UserID como a chave da partição. Cada entidade que armazena dados sobre um usuário é agrupada em uma única partição, e pode ser acessada por meio de transações do grupo de entidades, continuando altamente escalonável.
> 
> 

Há considerações adicionais na sua escolha de **PartitionKey** relacionadas a como você vai inserir, atualizar e excluir entidades. Para obter mais informações, consulte [Criando tabelas de modificação de dados](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Otimizando consultas para o serviço Tabela
O serviço Tabela indexa automaticamente suas entidades usando os valores de **PartitionKey** e **RowKey** em um único índice clusterizado; por isso as consultas de ponto são as mais eficientes. No entanto, não há qualquer índice além daquele no índice clusterizado em **PartitionKey** e **RowKey**.

Muitos designs devem atender aos requisitos para habilitar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionário com base em email, ID de funcionário ou sobrenome. Os padrões descritos em [Padrões de design de tabela](table-storage-design-patterns.md) abordam esses tipos de requisito e descrevem maneiras de contornar o fato de que o serviço Tabela não fornece índices secundários:  

* [Padrão de índice secundário intrapartição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores de **RowKey** diferentes.  
* [Padrão de índice secundário entre partições](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições separadas ou em tabelas separadas, a fim de permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey**.  
* [Padrão de entidades de índice](table-storage-design-patterns.md#index-entities-pattern) - Mantenha entidades de índice para permitir pesquisas eficientes que retornem listas de entidades.  

## <a name="sorting-data-in-the-table-service"></a>Armazenando dados no serviço Tabela
O serviço Tabela retorna entidades classificadas em ordem crescente com base em **PartitionKey** e, em seguida, **RowKey**. Essas chaves são valores de cadeia de caracteres e para garantir que os valores numéricos sejam classificados corretamente, você deve convertê-los em um comprimento fixo e preenchê-los com zeros. Por exemplo, se o valor de ID de funcionário usado como **RowKey** for um valor inteiro, você deverá converter a ID de funcionário **123** em **00000123**.  

Muitos aplicativos têm requisitos para usar dados classificados em ordens diferentes: por exemplo, classificação de funcionários por nome ou por data de ingresso. Os seguintes padrões abordam como alternar as ordens de classificação para suas entidades:  

* [Padrão de índice secundário intrapartição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - armazene várias cópias de cada entidade usando valores diferentes de RowKey (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores de RowKey diferentes.  
* [Padrão de índice secundário entre partições](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições e tabelas separadas, para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de RowKey.
* [Padrão de rastro do log](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* adicionadas recentemente em uma partição, usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Criptografar dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
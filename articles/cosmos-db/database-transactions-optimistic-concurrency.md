---
title: Controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
description: Este artigo descreve o controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 96652b2a1eb35668bd8a810b309ab31cec5afdb7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967218"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Controle de transações e simultaneidade otimista
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As transações do banco de dados oferecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. Os bancos de dados relacionais tradicionais, como SQL Server, permitem que você grave a lógica de negócios usando procedimentos armazenados e/ou gatilhos, envie-o para o servidor para execução diretamente no mecanismo de banco de dados. Com os bancos de dados relacionais tradicionais, você precisa lidar com duas linguagens de programação diferentes a linguagem de programação de aplicativo (não transacional), como JavaScript, Python, C#, Java, etc., e a linguagem de programação transacional (como o T-SQL) que é executada nativamente pelo banco de dados.

O mecanismo de banco de dados no Azure Cosmos DB dá suporte completo a transações em conformidade ACID (atomicidade, consistência, isolamento, durabilidade) com isolamento de instantâneo. Todas as operações de banco de dados no escopo da [partição lógica](partitioning-overview.md) de um contêiner são executadas de forma transacional no mecanismo de banco de dados hospedado pela réplica da partição. Essas operações incluem operações de gravação (atualização de um ou mais itens dentro da partição lógica) e de leitura. A tabela a seguir ilustra diferentes operações e tipos de transação:

| **Operação**  | **Tipo de operação** | **Transação de item único ou de vários itens** |
|---------|---------|---------|
| Inserir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Inserir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Substituir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Substituir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Upsert (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Upsert (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Excluir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Excluir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Executar procedimento armazenado | Gravar e ler | Transação de vários itens |
| O sistema iniciou a execução de um procedimento de mesclagem | Gravar | Transação de vários itens |
| O sistema iniciou a execução da exclusão de itens com base na expiração (vida útil) de um item | Gravar | Transação de vários itens |
| Ler | Ler | Transação de item único |
| Feed de alterações | Ler | Transação de vários itens |
| Leitura paginada | Ler | Transação de vários itens |
| Consulta paginada | Ler | Transação de vários itens |
| Executar UDF como parte da consulta paginada | Ler | Transação de vários itens |

## <a name="multi-item-transactions"></a>Transações de vários itens

Azure Cosmos DB permite que você escreva [procedimentos armazenados, gatilhos pre/post, UDFs (funções definidas pelo usuário)](stored-procedures-triggers-udfs.md) e procedimentos de mesclagem em JavaScript. O Azure Cosmos DB dá suporte nativo à execução de JavaScript dentro de seu mecanismo de banco de dados. É possível registrar procedimentos armazenados, pré/pós-gatilhos, UDFs (funções definidas pelo usuário) e procedimentos de mesclagem em um contêiner e executá-los posteriormente de maneira transacional dentro do mecanismo de banco de dados do Azure Cosmos. Gravar uma lógica de aplicativo em JavaScript permite a expressão natural do fluxo de controle, escopo de variáveis, atribuição e integração de primitivos de manuseio de exceções dentro de transações do banco de dados diretamente na linguagem JavaScript.

Os procedimentos armazenados baseados em JavaScript, gatilhos, UDFs e procedimentos de mesclagem são encapsulados dentro de uma transação ACID ambiente com isolamento de instantâneos em todos os itens dentro da partição lógica. No decorrer de sua execução, se o programa JavaScript gerar uma exceção, toda a transação será abortada e revertida. O modelo de programação resultante é simples, porém, poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação durável, ao mesmo tempo em que utilizam os construtores de linguagem e primitivas de biblioteca com os quais estão familiarizados.

A capacidade de executar JavaScript diretamente dentro do mecanismo de banco de dados oferece desempenho e execução transacional de operações do banco de dados com relação aos itens de um contêiner. Além disso, como o mecanismo de banco de dados do Azure Cosmos dá suporte nativo a JSON e JavaScript, não há incompatibilidade de impedância entre os sistemas do tipo de um aplicativo e o banco de dados.

## <a name="optimistic-concurrency-control"></a>Controle de simultaneidade otimista

O controle de simultaneidade otimista permite que você evite a perda de atualizações e exclusões. Operações simultâneas e conflitantes estão sujeitas ao bloqueio pessimista regular do mecanismo de banco de dados hospedado pela partição lógica da qual o item faz parte. Quando duas operações simultâneas tentarem atualizar a última versão de um item em uma partição lógica, uma delas vencerá e a outra falhará. No entanto, se uma ou duas operações tentando atualizar simultaneamente o mesmo item tivessem lido anteriormente um valor mais antigo do item, o banco de dados não saberia se o valor lido anteriormente por qualquer uma ou por ambas as operações conflitantes era de fato o valor mais recente do item. Felizmente, essa situação pode ser detectada com o **controle de simultaneidade otimista (OCC)** antes de permitir que as duas operações insiram o limite da transação dentro do mecanismo de banco de dados. O OCC protege seus dados contra alterações de substituição acidental feitas por outras pessoas. Ele também impede outras pessoas de substituir acidentalmente suas próprias alterações.

As atualizações simultâneas de um item estão sujeitas ao OCC pela camada de protocolo de comunicação do Azure Cosmos DB. Para contas do Azure Cosmos configuradas para **gravações de região única**, Azure Cosmos DB garante que a versão do lado do cliente do item que você está atualizando (ou excluindo) seja igual à versão do item no contêiner Cosmos do Azure. Isso garante que suas gravações sejam protegidas acidentalmente por gravações de outros e vice-versa. Em um ambiente multiusuário, o controle de simultaneidade otimista protege você contra a exclusão ou a atualização acidental da versão incorreta de um item. Dessa forma, os itens são protegidos contra os problemas infamosos de "atualização perdida" ou "exclusão perdida".

Em uma conta do Azure Cosmos configurada com **gravações de várias regiões**, os dados podem ser confirmados independentemente em regiões secundárias se ele `_etag` corresponder aos dados na região local. Depois que novos dados são confirmados localmente em uma região secundária, eles são mesclados no Hub ou na região primária. Se a política de resolução de conflitos mescla os novos dados na região do Hub, esses dados serão replicados globalmente com o novo `_etag` . Se a política de resolução de conflitos rejeitar os novos dados, a região secundária será revertida para os dados originais e `_etag` .

Cada item armazenado em um contêiner do Azure Cosmos tem uma propriedade `_etag` definida por sistema. O valor do `_etag` é gerado e atualizado automaticamente pelo servidor toda vez que o item é atualizado. `_etag` pode ser usado com o cabeçalho de solicitação fornecido pelo cliente `if-match` para permitir que o servidor decida se um item pode ser atualizado condicionalmente. O valor do `if-match` cabeçalho corresponde ao valor de `_etag` no servidor, o item é atualizado. Se o valor do `if-match` cabeçalho de solicitação não for mais atual, o servidor rejeitará a operação com uma mensagem de resposta "falha na pré-condição HTTP 412". Em seguida, o cliente pode buscar novamente o item para adquirir a versão atual do item no servidor ou substituir a versão do item no servidor pelo seu próprio `_etag` valor para o item. Além disso, `_etag` o pode ser usado com o `if-none-match` cabeçalho para determinar se uma rebusca de um recurso é necessária.

O valor do item `_etag` é alterado toda vez que o item é atualizado. Para operações de substituição de item, `if-match` deve ser expresso explicitamente como parte das opções de solicitação. Para obter um exemplo, confira o código de exemplo no [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). `_etag` os valores são verificados implicitamente para todos os itens gravados tocadas pelo procedimento armazenado. Se qualquer conflito for detectado, o procedimento armazenado reverterá a transação e lançará uma exceção. Com esse método, toda ou nenhuma gravação dentro do procedimento armazenado é aplicada de maneira atômica. Isso é um sinal para o aplicativo reaplicar atualizações e repetir a solicitação original do cliente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre controle de transações e simultaneidade otimista do banco de dados nos seguintes artigos:

- [Trabalhando com bancos de dados, contêineres e itens do Azure Cosmos](account-databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos e políticas de resolução de conflitos](conflict-resolution-policies.md)
- [Usando TransactionalBatch](transactional-batch.md)
- [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md)

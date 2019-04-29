---
title: Controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
description: Este artigo descreve o controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 568f47aacf39793d4c2da46798682abc002ca33b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889348"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Controle de transações e simultaneidade otimista

As transações do banco de dados oferecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. Tradicionais bancos de dados relacionais como o SQL Server, permitem que você escreva a lógica de negócios usando procedimentos armazenados e/ou gatilhos, enviá-lo para o servidor para execução diretamente no mecanismo de banco de dados. Com bancos de dados relacionais tradicionais, será necessário lidar com duas linguagens de programação diferentes o linguagem como JavaScript, Python, de programação de aplicativo (não transacional) C#, Java, etc. e (linguagem de programação transacional como o T-SQL) que é executada nativamente pelo banco de dados.

O mecanismo de banco de dados no Azure Cosmos DB dá suporte completo a transações em conformidade ACID (atomicidade, consistência, isolamento, durabilidade) com isolamento de instantâneo. Todas as operações de banco de dados dentro do escopo de um contêiner [partição lógica](partition-data.md) executada de forma transacional dentro do mecanismo de banco de dados que é hospedado pela réplica da partição. Essas operações incluem operações de gravação (atualização de um ou mais itens dentro da partição lógica) e de leitura. A tabela a seguir ilustra diferentes operações e tipos de transação:

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

O Azure Cosmos DB permite que você escreva [procedimentos armazenados, gatilhos de pré/pós-funções definidas pelo usuário-(UDFs)](stored-procedures-triggers-udfs.md) e mesclar os procedimentos em JavaScript. O Azure Cosmos DB dá suporte nativo à execução de JavaScript dentro de seu mecanismo de banco de dados. É possível registrar procedimentos armazenados, pré/pós-gatilhos, UDFs (funções definidas pelo usuário) e procedimentos de mesclagem em um contêiner e executá-los posteriormente de maneira transacional dentro do mecanismo de banco de dados do Azure Cosmos. Gravar uma lógica de aplicativo em JavaScript permite a expressão natural do fluxo de controle, escopo de variáveis, atribuição e integração de primitivos de manuseio de exceções dentro de transações do banco de dados diretamente na linguagem JavaScript.

Os procedimentos armazenados baseados em JavaScript, gatilhos, UDFs e procedimentos de mesclagem são encapsulados dentro de uma transação ACID ambiente com isolamento de instantâneos em todos os itens dentro da partição lógica. No decorrer de sua execução, se o programa JavaScript gerar uma exceção, toda a transação será abortada e revertida. O modelo de programação resultante é simples, porém, poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação durável, ao mesmo tempo em que utilizam os construtores de linguagem e primitivas de biblioteca com os quais estão familiarizados.

A capacidade de executar JavaScript diretamente dentro do mecanismo de banco de dados oferece desempenho e execução transacional de operações do banco de dados com relação aos itens de um contêiner. Além disso, como o mecanismo de banco de dados do Azure Cosmos dá suporte nativo a JSON e JavaScript, não há incompatibilidade de impedância entre os sistemas do tipo de um aplicativo e o banco de dados.

## <a name="optimistic-concurrency-control"></a>Controle de simultaneidade otimista 

O controle de simultaneidade otimista permite que você evite a perda de atualizações e exclusões. Operações simultâneas e conflitantes estão sujeitas ao bloqueio pessimista regular do mecanismo de banco de dados hospedado pela partição lógica da qual o item faz parte. Quando duas operações simultâneas tentarem atualizar a última versão de um item em uma partição lógica, uma delas vencerá e a outra falhará. No entanto, se uma ou duas operações tentando atualizar simultaneamente o mesmo item tivessem lido anteriormente um valor mais antigo do item, o banco de dados não saberia se o valor lido anteriormente por qualquer uma ou por ambas as operações conflitantes era de fato o valor mais recente do item. Felizmente, essa situação pode ser detectada com a **controle de simultaneidade otimista (OCC)** antes de deixar as duas operações insira o limite de transação dentro do mecanismo de banco de dados. O OCC protege seus dados contra alterações de substituição acidental feitas por outras pessoas. Ele também impede outras pessoas de substituir acidentalmente suas próprias alterações.

As atualizações simultâneas de um item estão sujeitas ao OCC pela camada de protocolo de comunicação do Azure Cosmos DB. O banco de dados do Azure Cosmos garante que a versão do lado do cliente do item que você está atualizando (ou excluindo) é a mesma que a versão do item no contêiner do Azure Cosmos. Isso garante que suas gravações sejam protegidas contra substituições acidentais realizadas pelas gravações de outras pessoas e vice-versa. Em um ambiente multiusuário, o controle de simultaneidade otimista protege você contra a exclusão ou a atualização acidental da versão incorreta de um item. Como tal, os itens são protegidos contra problemas infames de "atualização perdida" ou "exclusão perdida".

Cada item armazenado em um contêiner do Azure Cosmos tem uma propriedade `_etag` definida por sistema. O valor do `_etag` é gerado e atualizado automaticamente pelo servidor toda vez que o item é atualizado. `_etag` pode ser usado com fornecido pelo cliente `if-match` cabeçalho de solicitação para permitir que o servidor decida se um item pode ser atualizado condicionalmente. O valor da `if-match` cabeçalho corresponde ao valor do `_etag` no servidor, o item é atualizado. Se o valor da `if-match` cabeçalho de solicitação não é mais atual, o servidor rejeitará a operação com um "HTTP 412 Falha de pré-condição" mensagem de resposta. O cliente, em seguida, pode buscar novamente o item para adquirir a versão atual do item no servidor ou substituir a versão do item no servidor com sua própria `_etag` valor para o item. Além disso, `_etag` pode ser usado com o `if-none-match` cabeçalho para determinar se um buscar novamente de um recurso é necessária. 

O item `_etag` o valor é alterado toda vez que o item é atualizado. Para operações de item de substituição, `if-match` devem ser expressos explicitamente como parte das opções de solicitação. Para obter um exemplo, confira o código de exemplo no [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` valores implicitamente são verificados para todos os itens escritos tocados pelo procedimento armazenado. Se qualquer conflito for detectado, o procedimento armazenado será reverter a transação e lançar uma exceção. Com esse método, toda ou nenhuma gravação dentro do procedimento armazenado é aplicada de maneira atômica. Isso é um sinal para o aplicativo reaplicar atualizações e repetir a solicitação original do cliente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre controle de transações e simultaneidade otimista do banco de dados nos seguintes artigos:

- [Trabalhando com bancos de dados, contêineres e itens do Azure Cosmos](databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos e políticas de resolução de conflitos](conflict-resolution-policies.md)
- [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md)

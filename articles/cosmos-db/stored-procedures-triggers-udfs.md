---
title: Trabalhar com procedimentos armazenados, gatilhos e UDFs no Azure Cosmos DB
description: Este artigo apresenta conceitos, como procedimentos armazenados, gatilhos e funções definidas pelo usuário, do Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ad9e6b99b396465c2cff95bd6ab340ef9d668085
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575950"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, gatilhos e funções definidas pelo usuário
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB fornece execução transacional e integrada à linguagem de JavaScript. Ao usar a API do SQL no Azure Cosmos DB, você pode gravar **procedimentos armazenados**, **gatilhos** e **UDFs (funções definidas pelo usuário)** na linguagem JavaScript. Escreva sua lógica em JavaScript que é executada dentro do mecanismo de banco de dados. Você pode criar e executar gatilhos, procedimentos armazenados e UDFs usando [portal do Azure](https://portal.azure.com/), a [API de consulta integrada da linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md) ou os [SDKs do cliente Cosmos DB API do SQL](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Benefícios do uso da programação do servidor

A escrita de procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário) em JavaScript permite que você crie aplicativos sofisticados e que eles tenham as seguintes vantagens:

* **Lógica de procedimento:** JavaScript como uma linguagem de programação de alto nível que fornece uma interface rica e familiar para expressar a lógica de negócios. Você pode executar uma sequência de operações complexas nos dados.

* **Transações atômicas:** Azure Cosmos DB operações de banco de dados executadas em um único procedimento armazenado ou um gatilho são atômicas. Essa funcionalidade atômica permite que um aplicativo combine operações relacionadas em um único lote, de modo que todas as operações ou nenhuma delas seja bem-sucedida.

* **Desempenho:** Os dados JSON são mapeados intrinsecamente para o sistema de tipos de linguagem JavaScript. Esse mapeamento permite uma série de otimizações, como a materialização lenta de documentos JSON no pool de buffers e sua disponibilização sob demanda para o código em execução. Há outros benefícios de desempenho associados ao envio da lógica de negócios ao banco de dados, que incluem:

   * *Envio em lote:* Você pode agrupar operações como inserções e enviá-las em massa. Os custos de latência do tráfego de rede e a sobrecarga de armazenamento para criar transações separadas são reduzidos significativamente.

   * *Pré-compilação:* Procedimentos armazenados, gatilhos e UDFs são implicitamente compilados para o formato de código de bytes para evitar o custo de compilação no momento de cada invocação de script. Devido à pré-compilação, a invocação de procedimentos armazenados é rápida e tem um volume baixo.

   * *Sequenciamento:* Às vezes, as operações precisam de um mecanismo de disparo que possa executar uma ou mais atualizações para os dados. Além da Atomicidade, também há benefícios de desempenho com a execução do servidor.

* **Encapsulamento:** Os procedimentos armazenados podem ser usados para agrupar lógica em um único local. O encapsulamento adiciona uma camada de abstração aos dados, o que permite que você desenvolva seus aplicativos de maneira independente dos dados. Essa camada de abstração é útil quando os dados são sem esquema e você não precisa gerenciar a adição de lógica extra diretamente no aplicativo. A abstração permite manter os dados seguros simplificando o acesso pelos scripts.

> [!TIP]
> Os procedimentos armazenados são mais adequados para operações que são de gravação pesada e exigem uma transação em um valor de chave de partição. Ao decidir se deseja usar procedimentos armazenados, otimize o encapsulamento da quantidade máxima de gravações possível. Em termos gerais, os procedimentos armazenados não são os meios mais eficientes para fazer grandes quantidades de operações de leitura ou consulta; portanto, o uso de procedimentos armazenados para enviar grandes quantidades de leituras em lotes para retornar ao cliente não produzirá o benefício desejado. Para obter o melhor desempenho, essas operações de leitura pesada devem ser feitas no lado do cliente, usando o SDK do cosmos. 

## <a name="transactions"></a>Transactions

A transação em um banco de dados típico pode ser definida como uma sequência de operações realizadas como uma única unidade lógica de trabalho. Cada transação fornece **garantias de propriedade ACID**. ACID é um acrônimo bem conhecido que significa: **uma** tomicity, **C** onsistency, **I** solation e **D** urability. 

* A atomicidade garante que todas as operações feitas dentro de uma transação sejam tratadas como uma única unidade e que nenhuma delas ou todas elas sejam confirmadas. 

* A consistência garante que os dados estejam sempre em um estado válido entre as transações. 

* O isolamento garante que duas transações não interfiram uma com a outra – muitos sistemas comerciais fornecem vários níveis de isolamento que podem ser usados com base nas necessidades do aplicativo. 

* A durabilidade garante que qualquer alteração confirmada em um banco de dados esteja sempre presente.

No Azure Cosmos DB, o runtime do JavaScript é hospedado dentro do mecanismo de banco de dados. Portanto, as solicitações feitas dentro dos procedimentos armazenados e dos gatilhos são executadas no mesmo escopo da sessão do banco de dados. Esse recurso permite que o Azure Cosmos DB garanta propriedades ACID para todas as operações que fazem parte de um procedimento armazenado ou um gatilho. Para obter exemplos, confira o artigo [Como implementar transações](how-to-write-stored-procedures-triggers-udfs.md#transactions).

### <a name="scope-of-a-transaction"></a>Escopo de uma transação

Os procedimentos armazenados são associados a um contêiner Cosmos do Azure e a execução do procedimento armazenado é delimitada a uma chave de partição lógica. Os procedimentos armazenados devem incluir um valor de chave de partição lógica durante a execução que define a partição lógica para o escopo da transação. Para obter mais informações, confira o artigo [Particionamento do Azure Cosmos DB](partitioning-overview.md).

### <a name="commit-and-rollback"></a>Confirmação e reversão

As transações são nativamente integradas ao modelo de programação do JavaScript do Azure Cosmos DB. Dentro de uma função do JavaScript, todas as operações são encapsuladas automaticamente em uma única transação. Se a lógica do JavaScript em um procedimento armazenado for concluída sem exceções, todas as operações dentro da transação serão confirmadas no banco de dados. Instruções como `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (conhecidas por bancos de dados relacionais) são implícitas no Azure Cosmos DB. Se houver exceções do script, o runtime do JavaScript do Azure Cosmos DB reverterá a transação inteira. Dessa forma, gerar uma exceção é efetivamente equivalente a um `ROLLBACK TRANSACTION` no Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência de dados

Os procedimentos armazenados e os gatilhos são sempre executados na réplica primária de um contêiner do Azure Cosmos. Esse recurso garante que as leituras dos procedimentos armazenados ofereçam [coerência forte](./consistency-levels.md). As consultas que usam funções definidas pelo usuário podem ser executadas na réplica primária ou em qualquer réplica secundária. Os procedimentos armazenados e os gatilhos se destinam a dar suporte a gravações transacionais – enquanto a lógica somente leitura é melhor implementada como a lógica do lado do aplicativo – e consultas usando os [SDKs da API do SQL do Azure Cosmos DB](sql-api-dotnet-samples.md) e isso ajudará você a saturar a taxa de transferência de banco de dados. 

> [!TIP]
> As consultas executadas em um procedimento armazenado ou gatilho podem não ver as alterações nos itens feitos pela mesma transação de script. Essa instrução se aplica a consultas SQL, como `getContent().getCollection.queryDocuments()` , bem como consultas de linguagem integradas, como `getContext().getCollection().filter()` .

## <a name="bounded-execution"></a>Execução vinculada

Todas as operações do Azure Cosmos DB precisam ser concluídas dentro da duração de tempo limite especificada. Os procedimentos armazenados têm um limite de tempo limite de 5 segundos. Essa restrição se aplica a funções do JavaScript – procedimentos armazenados, gatilhos e funções definidas pelo usuário. Se uma operação não for concluída dentro desse limite de tempo, a transação será revertida.

Garanta que as funções do JavaScript sejam concluídas dentro do limite de tempo ou implemente um modelo baseado em continuação para criar um lote/retomar a execução. A fim de simplificar o desenvolvimento de procedimentos armazenados e gatilhos para lidar com limites de tempo, todas as funções no contêiner do Azure Cosmos (por exemplo, criação, leitura, atualização e exclusão de itens) retornam um valor booliano que representa se a operação será concluída. Se esse valor for falso, ele será uma indicação de que o procedimento precisará encapsular a execução porque o script está consumindo mais tempo ou uma taxa de transferência provisionada maior do que o valor configurado. Operações colocadas em fila antes da primeira operação de armazenamento não aceita serão concluídas com certeza se o procedimento armazenado for concluído dentro do tempo e não colocar nenhuma outra solicitação em fila. Portanto, as operações devem ser enfileiradas uma de cada vez usando a Convenção de retorno de chamada do JavaScript para gerenciar o fluxo de controle do script. Como os scripts são executados em um ambiente do servidor, eles são estritamente controlados. Os scripts que violam repetidamente os limites de execução podem ser marcados como inativos e não podem ser executados e devem ser recriados para respeitar os limites de execução.

As funções do JavaScript também estão sujeitas à [capacidade de taxa de transferência provisionada](request-units.md). As funções do JavaScript podem acabar usando um grande número de unidades de solicitação em um curto período e podem ter a taxa limitada se o limite da capacidade de taxa de transferência provisionada é atingido. É importante observar que os scripts consomem uma taxa de transferência adicional, além da taxa de transferência gasta executando operações de banco de dados, embora essas operações de banco de dados sejam um pouco mais baratas do que a execução das mesmas operações no cliente.

## <a name="triggers"></a>Gatilhos

O Azure Cosmos DB é compatível com dois tipos de gatilho:

### <a name="pre-triggers"></a>Pré-gatilhos

O Azure Cosmos DB fornece gatilhos que podem ser invocados com a execução de uma operação em um item do Azure Cosmos. Por exemplo, você pode especificar um pré-gatilho ao criar um item. Nesse caso, o pré-gatilho será executado antes da criação do item. Pré-gatilhos não podem ter parâmetros de entrada. Se necessário, o objeto de solicitação pode ser usado para atualizar o corpo do documento na solicitação original. Quando os gatilhos são registrados, os usuários podem especificar as operações com as quais eles podem ser executados. Se um gatilho foi criado com `TriggerOperation.Create`, isso significa que o uso do gatilho em uma operação de substituição não será permitido. Para obter exemplos, confira o artigo [Como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers).

### <a name="post-triggers"></a>Pós-gatilhos

Semelhantes aos pré-gatilhos, pós-gatilhos, também são associados a uma operação em um item Cosmos do Azure e não exigem nenhum parâmetro de entrada. Eles são executados *após* a conclusão da operação e têm acesso à mensagem de resposta que é enviada ao cliente. Para obter exemplos, confira o artigo [Como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers).

> [!NOTE]
> Os gatilhos registrados não são executados automaticamente quando suas operações correspondentes (criar/excluir/substituir/atualizar) acontecem. Eles precisam ser chamados explicitamente ao executar essas operações. Para saber mais, consulte [o artigo como executar gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) .

## <a name="user-defined-functions"></a><a id="udfs"></a>Funções definidas pelo usuário

As UDFs ( [funções definidas pelo usuário](sql-query-udfs.md) ) são usadas para estender a sintaxe da linguagem de consulta da API do SQL e implementar a lógica de negócios personalizada facilmente. Elas podem ser chamadas somente em consultas. As UDFs não têm acesso ao objeto de contexto e devem ser usadas como JavaScript somente para cálculo. Portanto, as UDFs podem ser executadas em réplicas secundárias. Para obter exemplos, confira o artigo [Como escrever funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md#udfs).

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>API de consulta integrada à linguagem do JavaScript

Além de emitir consultas usando a sintaxe de consulta da API do SQL, o [SDK do servidor](https://azure.github.io/azure-cosmosdb-js-server) permite que você execute consultas usando uma interface JavaScript sem nenhum conhecimento de SQL. A API de consulta do JavaScript permite que você crie consultas de forma programática passando funções de predicado em uma sequência de chamadas de função. As consultas são analisadas pelo runtime do JavaScript e são executadas com eficiência no Azure Cosmos DB. Para saber mais sobre o suporte à API de consulta do JavaScript, confira o artigo [Trabalhando com a API de consulta integrada à linguagem do JavaScript](javascript-query-api.md). Para obter exemplos, confira o artigo [Como escrever procedimentos armazenados e gatilhos usando a API de Consulta do JavaScript](how-to-write-javascript-query-api.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md)

* [Como usar procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhando com a API de consulta integrada à linguagem do JavaScript](javascript-query-api.md)
---
title: Trabalhando com procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB
description: Este artigo apresenta conceitos, como procedimentos armazenados, gatilhos e funções definidas pelo usuário, do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 53ff318dcc034fb11e2d554f9ad8e8814eb32879
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672579"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimentos armazenados, gatilhos e funções definidas pelo usuário

O Azure Cosmos DB fornece execução transacional e integrada à linguagem de JavaScript. Ao usar a API do SQL no Azure Cosmos DB, você poderá escrever **procedimentos armazenados**, **gatilhos** e **UDFs (funções definidas pelo usuário)** na linguagem JavaScript. Escreva sua lógica em JavaScript que é executada dentro do mecanismo de banco de dados. Crie e execute gatilhos, procedimentos armazenados e UDFs usando o [portal do Azure](https://portal.azure.com/), a [API de consulta integrada à linguagem do JavaScript no Azure Cosmos DB](javascript-query-api.md) e os [SDKs de cliente da API do SQL do Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Benefícios do uso da programação do servidor

A escrita de procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário) em JavaScript permite que você crie aplicativos sofisticados e que eles tenham as seguintes vantagens:

* **Lógica de procedimento:** O JavaScript, como linguagem de programação de alto nível, oferece uma interface sofisticada e conhecida para expressar a lógica de negócios. Você pode executar uma sequência de operações complexas nos dados.

* **Transações atômicas:** O Azure Cosmos DB garante que as operações de banco de dados executadas em um único procedimento armazenado ou um gatilho sejam atômicas. Essa funcionalidade atômica permite que um aplicativo combine operações relacionadas em um único lote, de modo que todas as operações ou nenhuma delas seja bem-sucedida.

* **Desempenho:** Os dados JSON são intrinsecamente mapeados para o sistema de tipos de linguagem JavaScript. Esse mapeamento permite uma série de otimizações, como a materialização lenta de documentos JSON no pool de buffers e sua disponibilização sob demanda para o código em execução. Há outros benefícios de desempenho associados ao envio da lógica de negócios ao banco de dados, que incluem:

   * *Envio em lote:* Você pode agrupar operações como inserções e enviá-las em massa. Os custos de latência do tráfego de rede e a sobrecarga de armazenamento para criar transações separadas são reduzidos significativamente.

   * *Pré-compilação:* Os procedimentos armazenados, os gatilhos e as UDFs são pré-compilados implicitamente no formato de código de bytes, a fim de evitar o custo de compilação no momento da invocação de cada script. Devido à pré-compilação, a invocação de procedimentos armazenados é rápida e tem um volume baixo.

   * *Sequenciamento:* Às vezes, as operações precisam de um mecanismo de disparo que possa executar uma ou mais atualizações nos dados. Além da Atomicidade, também há benefícios de desempenho com a execução do servidor.

* **Encapsulamento:** Os procedimentos armazenados podem ser usados para agrupar a lógica em um só lugar. O encapsulamento adiciona uma camada de abstração aos dados, o que permite que você desenvolva seus aplicativos de maneira independente dos dados. Essa camada de abstração é útil quando os dados são sem esquema e você não precisa gerenciar a adição de lógica extra diretamente no aplicativo. A abstração permite manter os dados seguros simplificando o acesso pelos scripts.

> [!TIP]
> Procedimentos armazenados são mais adequados para operações de gravação intensa e exigem uma transação em um valor de chave de partição. Ao decidir se deseja usar procedimentos armazenados, otimize em torno de encapsular a quantidade máxima de possíveis de gravações. Em termos gerais, procedimentos armazenados não são o meio mais eficaz para fazer um grande número de operações de leitura ou consulta, usando armazenados, procedimentos para um grande número de lotes de leituras para retornar para o cliente não obterá o benefício desejado. Para obter melhor desempenho, essas operações de leitura pesada devem ser feitas no lado do cliente, usando o SDK do Cosmos. 

## <a name="transactions"></a>Transações

A transação em um banco de dados típico pode ser definida como uma sequência de operações realizadas como uma única unidade lógica de trabalho. Cada transação fornece **garantias de propriedade ACID**. ACID é um acrônimo conhecido que significa: **A**tomicidade, **C**onsistência, **I**solamento e **D**urabilidade. 

* A atomicidade garante que todas as operações feitas dentro de uma transação sejam tratadas como uma única unidade e que nenhuma delas ou todas elas sejam confirmadas. 

* A consistência garante que os dados estejam sempre em um estado válido entre as transações. 

* O isolamento garante que duas transações não interfiram uma com a outra – muitos sistemas comerciais fornecem vários níveis de isolamento que podem ser usados com base nas necessidades do aplicativo. 

* A durabilidade garante que qualquer alteração confirmada em um banco de dados esteja sempre presente.

No Azure Cosmos DB, o tempo de execução do JavaScript é hospedado dentro do mecanismo de banco de dados. Portanto, as solicitações feitas dentro dos procedimentos armazenados e dos gatilhos são executadas no mesmo escopo da sessão do banco de dados. Esse recurso permite que o Azure Cosmos DB garanta propriedades ACID para todas as operações que fazem parte de um procedimento armazenado ou um gatilho. Para obter exemplos, confira o artigo [Como implementar transações](how-to-write-stored-procedures-triggers-udfs.md#transactions).

### <a name="scope-of-a-transaction"></a>Escopo de uma transação

Se um procedimento armazenado estiver associado a um contêiner do Azure Cosmos, o procedimento armazenado será executado no escopo da transação de uma chave de partição lógica. Cada execução do procedimento armazenado precisa incluir um valor de chave de partição lógica que corresponda ao escopo da transação. Para obter mais informações, confira o artigo [Particionamento do Azure Cosmos DB](partition-data.md).

### <a name="commit-and-rollback"></a>Confirmação e reversão

As transações são nativamente integradas ao modelo de programação do JavaScript do Azure Cosmos DB. Dentro de uma função do JavaScript, todas as operações são encapsuladas automaticamente em uma única transação. Se a lógica do JavaScript em um procedimento armazenado for concluída sem exceções, todas as operações dentro da transação serão confirmadas no banco de dados. Instruções como `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (conhecidas por bancos de dados relacionais) são implícitas no Azure Cosmos DB. Se houver exceções do script, o tempo de execução do JavaScript do Azure Cosmos DB reverterá a transação inteira. Dessa forma, gerar uma exceção é efetivamente equivalente a um `ROLLBACK TRANSACTION` no Azure Cosmos DB.

### <a name="data-consistency"></a>Consistência de dados

Os procedimentos armazenados e os gatilhos são sempre executados na réplica primária de um contêiner do Azure Cosmos. Esse recurso garante que as leituras dos procedimentos armazenados ofereçam [coerência forte](consistency-levels-tradeoffs.md). As consultas que usam funções definidas pelo usuário podem ser executadas na réplica primária ou em qualquer réplica secundária. Os procedimentos armazenados e os gatilhos se destinam a dar suporte a gravações transacionais – enquanto a lógica somente leitura é melhor implementada como a lógica do lado do aplicativo – e consultas usando os [SDKs da API do SQL do Azure Cosmos DB](sql-api-dotnet-samples.md) e isso ajudará você a saturar a taxa de transferência de banco de dados. 

## <a name="bounded-execution"></a>Execução vinculada

Todas as operações do Azure Cosmos DB precisam ser concluídas dentro da duração de tempo limite especificada. Essa restrição se aplica a funções do JavaScript – procedimentos armazenados, gatilhos e funções definidas pelo usuário. Se uma operação não for concluída dentro desse limite de tempo, a transação será revertida.

Garanta que as funções do JavaScript sejam concluídas dentro do limite de tempo ou implemente um modelo baseado em continuação para criar um lote/retomar a execução. A fim de simplificar o desenvolvimento de procedimentos armazenados e gatilhos para lidar com limites de tempo, todas as funções no contêiner do Azure Cosmos (por exemplo, criação, leitura, atualização e exclusão de itens) retornam um valor booliano que representa se a operação será concluída. Se esse valor for falso, ele será uma indicação de que o procedimento precisará encapsular a execução porque o script está consumindo mais tempo ou uma taxa de transferência provisionada maior do que o valor configurado. Operações colocadas em fila antes da primeira operação de armazenamento não aceita serão concluídas com certeza se o procedimento armazenado for concluído dentro do tempo e não colocar nenhuma outra solicitação em fila. Portanto, as operações devem ser colocadas na fila uma por vez usando a convenção de retorno de chamada do JavaScript para gerenciar o fluxo de controle do script. Como os scripts são executados em um ambiente do servidor, eles são estritamente controlados. Os scripts que violam repetidamente os limites de execução podem ser marcados como inativos e não podem ser executados e devem ser recriados para respeitar os limites de execução.

As funções do JavaScript também estão sujeitas à [capacidade de taxa de transferência provisionada](request-units.md). As funções do JavaScript podem acabar usando um grande número de unidades de solicitação em um curto período e podem ter a taxa limitada se o limite da capacidade de taxa de transferência provisionada é atingido. É importante observar que os scripts consomem uma taxa de transferência adicional, além da taxa de transferência gasta executando operações de banco de dados, embora essas operações de banco de dados sejam um pouco mais baratas do que a execução das mesmas operações no cliente.

## <a name="triggers"></a>Gatilhos

O Azure Cosmos DB dá suporte a dois tipos de gatilhos:

### <a name="pre-triggers"></a>Pré-gatilhos

O Azure Cosmos DB fornece gatilhos que podem ser invocados com a execução de uma operação em um item do Azure Cosmos DB. Por exemplo, você pode especificar um pré-gatilho ao criar um item. Nesse caso, o pré-gatilho será executado antes da criação do item. Pré-gatilhos não podem ter parâmetros de entrada. Se necessário, o objeto de solicitação pode ser usado para atualizar o corpo do documento na solicitação original. Quando os gatilhos são registrados, os usuários podem especificar as operações com as quais eles podem ser executados. Se um gatilho foi criado com `TriggerOperation.Create`, isso significa que o uso do gatilho em uma operação de substituição não será permitido. Para obter exemplos, confira o artigo [Como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers).

### <a name="post-triggers"></a>Pós-gatilhos

Assim como os pré-gatilhos, os pós-gatilhos também estão associados a uma operação em um item do Azure Cosmos DB e não exigem nenhum parâmetro de entrada. Eles são executados *após* a conclusão da operação e têm acesso à mensagem de resposta enviada ao cliente. Para obter exemplos, confira o artigo [Como escrever gatilhos](how-to-write-stored-procedures-triggers-udfs.md#triggers).

> [!NOTE]
> Registrado gatilhos não são executados automaticamente quando as operações correspondentes (Criar / excluir / substituir / atualizar) ocorrer. Eles precisarão ser chamado explicitamente ao executar essas operações. Para obter mais informações, consulte [como executar gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) artigo.

## <a id="udfs"></a>Funções definidas pelo usuário

As UDFs (funções definidas pelo usuário) são usadas para estender a sintaxe da linguagem de consulta da API do SQL e implementar uma lógica de negócios personalizada com facilidade. Elas podem ser chamadas somente em consultas. As UDFs não têm acesso ao objeto de contexto e devem ser usadas como JavaScript somente para cálculo. Portanto, as UDFs podem ser executadas em réplicas secundárias. Para obter exemplos, confira o artigo [Como escrever funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md#udfs).

## <a id="jsqueryapi"></a>API de consulta integrada à linguagem do JavaScript

Além de emitir consultas usando a sintaxe de consulta da API do SQL, o [SDK do servidor](https://azure.github.io/azure-cosmosdb-js-server) permite que você execute consultas usando uma interface JavaScript sem nenhum conhecimento de SQL. A API de consulta do JavaScript permite que você crie consultas de forma programática passando funções de predicado em uma sequência de chamadas de função. As consultas são analisadas pelo tempo de execução do JavaScript e são executadas com eficiência no Azure Cosmos DB. Para saber mais sobre o suporte à API de consulta do JavaScript, confira o artigo [Trabalhando com a API de consulta integrada à linguagem do JavaScript](javascript-query-api.md). Para obter exemplos, confira o artigo [Como escrever procedimentos armazenados e gatilhos usando a API de Consulta do JavaScript](how-to-write-javascript-query-api.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB com os seguintes artigos:

* [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-write-stored-procedures-triggers-udfs.md)

* [Como usar procedimentos armazenados, gatilhos e funções definidas pelo usuário](how-to-use-stored-procedures-triggers-udfs.md)

* [Trabalhando com a API de consulta integrada à linguagem do JavaScript](javascript-query-api.md)

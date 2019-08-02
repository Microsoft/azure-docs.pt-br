---
title: Como modelar e particionar dados no Azure Cosmos DB usando um exemplo do mundo real
description: Saiba como modelar e particionar um exemplo do mundo real usando a API do núcleo do Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 4bb99c8cbec88d23f9297dcbe8b13cc69cd0006c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070678"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Como modelar e particionar dados no Azure Cosmos DB usando um exemplo do mundo real

Este artigo se baseia em vários conceitos do Azure Cosmos DB, tais como [modelagem de dados](modeling-data.md), [particionamento](partitioning-overview.md) e [taxa de transferência provisionada](request-units.md) para demonstrar como lidar com um exercício de criação de dados do mundo real.

Se normalmente trabalha com bancos de dados relacionais, você provavelmente criou hábitos e intuições sobre como criar um modelo de dados. Por causa das restrições específicas, mas também dos pontos fortes exclusivos do Azure Cosmos DB, a maioria dessas práticas recomendadas não geram bons resultados e podem arrastar você para soluções de qualidade inferior ao ideal. A meta deste artigo é orientá-lo pelo processo completo de modelagem de um caso de uso do mundo real no Azure Cosmos DB, desde a modelagem de itens à colocação de entidades e ao particionamento de contêineres.

## <a name="the-scenario"></a>O cenário

Para este exercício, vamos considerar o domínio de uma plataforma de blogs em que os *usuários* podem criar *posts*. Os usuários também podem *curtir* esses posts e adicionar *comentários* a elas.

> [!TIP]
> Destacamos algumas palavras em *itálico*; essas palavras identificam o tipo de "coisas" que nosso modelo precisará manipular.

Adicionando mais requisitos para nossa especificação:

- Uma página frontal exibe um feed de posts criados recentemente,
- Podemos buscar todos os posts de um usuário, todos os comentários para um post e todas as curtidas para um post,
- Os posts são retornados com o nome de usuário de seus autores e uma contagem de quantos comentários e curtidas eles têm,
- Comentários e curtidas também são retornadas com o nome de usuário dos usuários que os criaram,
- Quando exibidos como listas, os posts só precisam apresentar um resumo truncado de seu conteúdo.

## <a name="identify-the-main-access-patterns"></a>Identificar os principais padrões de acesso

Para começar, podemos dar alguma estrutura para nossa especificação inicial, identificando os padrões de acesso da nossa solução. Ao criar um modelo de dados do Azure Cosmos DB, é importante compreender quais solicitações nosso modelo precisará atender para assegurar que o modelo atenda a essas solicitações com eficiência.

Para facilitar o processo geral a seguir, podemos categorizar essas diferentes solicitações como comandos ou consultas, emprestando algum vocabulário de [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation), em que comandos são solicitações de gravação (ou seja, intenções de atualizar o sistema) e consultas são solicitações somente leitura.

Aqui está a lista de solicitações que nossa plataforma terá de expor:

- **[C1]** Criar/editar um usuário
- **[Q1]** Recuperar um usuário
- **[C2]** Criar/editar um post
- **[Q2]** Recuperar um post
- **[Q3]** Listar os posts de um usuário na forma abreviada
- **[C3]** Criar um comentário
- **[Q4]** Listar os comentários de um post
- **[C4]** Curtir um post
- **[Q5]**  Listar as curtidas de um post
- **[Q6]** Listar os *x* posts mais recentes criados na forma abreviada (feed)

Neste estágio, ainda não pensamos sobre os detalhes do que cada entidade (usuário, post etc.) conterá. Esta etapa está geralmente entre as primeiras a serem realizadas durante a criação em um armazenamento relacional, porque precisamos descobrir como essas entidades se traduzirão em termos de tabelas, colunas, chaves estrangeiras, etc. Isso é muito menos preocupante com um banco de dados de documentos que não impõe nenhum esquema na gravação.

O principal motivo pelo qual é importante identificar os nossos padrões de acesso desde o início é porque essa lista de solicitações vai ser o nosso conjunto de testes. Sempre que iteramos pelo nosso modelo de dados, percorremos cada uma das solicitações e verificamos os respectivos desempenho e escalabilidade.

## <a name="v1-a-first-version"></a>V1: Uma primeira versão

Começamos com dois contêineres: `users` e `posts`.

### <a name="users-container"></a>Contêiner de usuários

Esse contêiner armazena apenas os itens do usuário:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Particionamos esse contêiner por `id`, o que significa que cada partição lógica dentro do contêiner conterá apenas um item.

### <a name="posts-container"></a>Contêiner de posts

Este contêiner hospeda posts, comentários e afins:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Particionamos esse contêiner por `postId`, o que significa que cada partição lógica dentro do contêiner conterá um post, bem como todos os comentários e serviços para esse post.

Observe que apresentamos uma propriedade `type` nos itens armazenados nesse contêiner para distinguir entre os três tipos de entidades que esse contêiner hospeda.

Além disso, optamos por fazer referência a dados relacionados em vez de inseri-los (verifique [esta seção](modeling-data.md) para obter detalhes sobre esses conceitos) porque:

- não há nenhum limite superior de quantos posts um usuário pode criar;
- os posts podem ser arbitrariamente longos;
- não há limite superior para a quantidade de comentários e curtidas que um post pode ter;
- queremos adicionar um comentário ou uma curtida a um post sem precisar atualizar o post propriamente dito.

## <a name="how-well-does-our-model-perform"></a>Como é o desempenho do nosso modelo?

Agora é hora de avaliar o desempenho e escalabilidade de nossa primeira versão. Para cada uma das solicitações identificadas anteriormente, medimos o número de unidades de solicitação consumidas e a latência. Essa medida é feita em um conjunto de dados fictício que contém a 100.000 usuários com 5 a 50 posts por usuário e até 25 comentários e 100 curtidas por post.

### <a name="c1-createedit-a-user"></a>[C1] Criar/editar um usuário

Essa solicitação é muito fácil de implementar, podemos simplesmente criar ou atualizar um item no contêiner `users`. As solicitações se espalharão bem por todas as partições graças à chave de partição `id`.

![Gravar um único item no contêiner de usuários](./media/how-to-model-partition-example/V1-C1.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Recuperar um usuário

A recuperação de um usuário é realizada lendo-se o item correspondente do contêiner `users`.

![Recuperar um único item do contêiner de usuários](./media/how-to-model-partition-example/V1-Q1.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Criar/editar um post

Da mesma forma que **[C1]** , temos apenas que gravar o contêiner `posts`.

![Gravar um único item no contêiner de posts](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um post

Começamos recuperando o documento correspondente do contêiner `posts`. Mas isso não é suficiente, de acordo com nossa especificação também precisamos agregar o nome de usuário do autor do post e as contagens de quantos comentários e curtidas esse post tem, o que exige que 3 consultas SQL adicionais sejam realizadas.

![Recuperar um post e agregar dados adicionais](./media/how-to-model-partition-example/V1-Q2.png)

Cada uma das consultas adicionais filtra a chave de partição de seu respectivo contêiner, que é exatamente o que queremos para maximizar o desempenho e a escalabilidade. Mas eventualmente precisamos executar quatro operações para retornar um único post, portanto, melhoraremos isso em uma próxima iteração.

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Listar os posts de um usuário na forma abreviada

Primeiro, precisamos recuperar os posts desejados com uma consulta SQL que busca os posts correspondentes a esse usuário específico. Mas também precisamos emitir consultas adicionais para agregar o nome de usuário do autor e as contagens de comentários e curtidas.

![Recuperar todos os posts de um usuário e agregar seus dados adicionais](./media/how-to-model-partition-example/V1-Q3.png)

Essa implementação apresenta várias desvantagens:

- as consultas de agregação as contagens de comentários e curtidas precisam ser emitidos para cada post retornado pela primeira consulta,
- a consulta principal não filtra a chave de partição do contêiner `posts`, levando a um fan-out e a uma verificação de partição pelo contêiner.

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 130 ms | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Criar um comentário

Um comentário é criado escrevendo-se o item correspondente no contêiner `posts`.

![Gravar um único item no contêiner de posts](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Listar os comentários de um post

Vamos começar com uma consulta que busca todos os comentários para esse post e, mais uma vez, também precisamos agregar nomes de usuário separadamente para cada comentário.

![Recuperar todos os comentários de um post e agregar os dados adicionais deles](./media/how-to-model-partition-example/V1-Q4.png)

Embora a consulta principal filtre na chave de partição do contêiner, agregar os nomes de usuário separadamente causa prejuízo ao desempenho em geral. Aprimoraremos isso mais tarde.

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 23 ms | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Curtir um post

Assim como **[C3]** , criamos o item correspondente no contêiner `posts`.

![Gravar um único item no contêiner de posts](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 6 ms | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar as curtidas de um post

Assim como em **[Q4]** , podemos consultar as curtidas desse post e, em seguida, agregar os respectivos nomes de usuário.

![Recuperar todas as curtidas de um post e agregar os respectivos dados adicionais](./media/how-to-model-partition-example/V1-Q5.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 59 ms | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Listar os x posts mais recentes criados na forma abreviada (feed)

Buscamos os posts mais recentes consultando o contêiner `posts` classificado por data de criação decrescente, depois agregamos os nomes de usuário e contagens de comentários e curtidas para cada um dos posts.

![Recuperar os posts mais recentes e agregar seus dados adicionais](./media/how-to-model-partition-example/V1-Q6.png)

Mais uma vez, nossa consulta inicial não filtra na chave de partição do contêiner `posts`, que dispara um fan-out oneroso. Esse é ainda pior, pois direcionamos a um conjunto de resultados muito maior e classificamos os resultados com uma cláusula `ORDER BY`, o que o torna mais dispendioso em termos de unidades de solicitação.

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 306 ms | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refletindo sobre o desempenho do V1

Examinando os problemas de desempenho com os quais nos deparamos na seção anterior, podemos identificar duas classes principais de problemas:

- algumas solicitações exigem que várias consultas sejam emitidas para coletar todos os dados que precisamos retornar;
- algumas consultas não filtram a chave de partição dos contêineres aos quais se destinam, levando a um fan-out que impede nossa escalabilidade.

Resolveremos cada um desses problemas, começando com o primeiro deles.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introdução à desnormalização para otimização de consultas de leitura

O motivo por que temos que emitir solicitações adicionais em alguns casos é porque os resultados da solicitação inicial não contêm todos os dados que precisamos retornar. Ao trabalhar com um armazenamento de dados não relacionais como o Azure Cosmos DB, esse tipo de problema é normalmente resolvido com a desnormalização dos dados em nosso conjunto de dados.

Em nosso exemplo, podemos modificar itens de post para adicionar o nome de usuário do autor do post, a contagem de comentários e a contagem de curtidas:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Também podemos modificar itens de comentário e de curtida para adicionar o nome de usuário correspondente ao usuário que os criou:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Desnormalização de contagens de comentários e de curtidas

O que queremos alcançar é que, cada vez que adicionarmos um comentário ou uma curtida, possamos também aumentar o `commentCount` ou o `likeCount` no post correspondente. Como nosso contêiner `posts` é particionado por `postId`, o novo item (comentário ou curtida) e seu post correspondente ficam na mesma partição lógica. Como resultado, podemos usar um [procedimento armazenado](stored-procedures-triggers-udfs.md) para executar essa operação.

Agora, durante a criação de um comentário ( **[C3]** ), em vez de apenas adicionar um novo item ao contêiner `posts`, podemos chamar o seguinte procedimento armazenado nesse contêiner:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Esse procedimento armazenado usa a ID do post e o corpo do novo comentário como parâmetros, depois:

- recupera o post
- incrementa o `commentCount`
- substitui o post
- adiciona o novo comentário

Já que os procedimentos armazenados são executados como transações atômicas, é certo que o valor de `commentCount` e o número real de comentários permanecerão sempre em sincronia.

Obviamente, chamamos um procedimento armazenado semelhante ao adicionar novas curtidas para incrementar o `likeCount`.

### <a name="denormalizing-usernames"></a>Desnormalizar nomes de usuário

Os nomes de usuário exigem uma abordagem diferente, pois os usuários não só ficam em partições diferentes, mas também em um contêiner diferente. Quando precisamos desnormalizar os dados em partições e contêineres, podemos usar o [feed de alterações](change-feed.md) do contêiner de origem.

Em nosso exemplo, usamos o feed de alterações do contêiner `users` para reagir sempre que os usuários atualizam seus nomes de usuário. Quando isso acontece, podemos propagar a alteração chamando outro procedimento armazenado no contêiner `posts`:

![Desnormalizar nomes de usuário no contêiner de posts](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Esse procedimento armazenado usa a o novo nome de usuário e a ID do usuário como parâmetros, depois:

- busca todos os itens correspondentes ao `userId` (que podem ser posts, comentários ou curtidas)
- para cada um desses itens
  - substitui o `userUsername`
  - substitui o item

> [!IMPORTANT]
> Esta operação é cara porque requer que esse procedimento armazenado seja executado em todas as partições do contêiner `posts`. Vamos supor que a maioria dos usuários escolhe um nome de usuário adequado durante a inscrição e não o altera, então essa atualização será executada muito raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quais são os ganhos de desempenho do V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um post

Agora que nossa desnormalização está em vigor, precisamos apenas buscar um único item para lidar com essa solicitação.

![Recuperar um único item do contêiner de posts](./media/how-to-model-partition-example/V2-Q2.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Listar os comentários de um post

Aqui, novamente, podemos economizar as solicitações extras que buscaram os nomes de usuário e terminam com uma única consulta que filtra na chave de partição.

![Recuperando todos os comentários para um post](./media/how-to-model-partition-example/V2-Q4.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar as curtidas de um post

Exatamente a mesma situação ao listar as curtidas.

![Recuperar todas as curtidas de um post](./media/how-to-model-partition-example/V2-Q5.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Verificar se todas as solicitações são escalonáveis

Observando nossas melhorias de desempenho gerais, ainda existem duas solicitações que ainda não otimizamos totalmente: **[Q3]** e **[Q6]** . Elas são as solicitações que envolvem consultas que não filtram na chave de partição dos contêineres aos quais elas se destinam.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Listar os posts de um usuário na forma abreviada

Esta solicitação já se beneficia dos aperfeiçoamentos introduzidos na V2, o que poupa consultas adicionais.

![Recuperar todos os posts de um usuário](./media/how-to-model-partition-example/V2-Q3.png)

Mas a consulta restante ainda não está filtrando na chave de partição do contêiner `posts`.

A maneira de pensar sobre essa situação é na realidade simples:

1. Essa solicitação *precisa* filtrar no `userId` porque queremos buscar todos os posts de um usuário específico
1. O desempenho dela não é bom porque ela é executada direcionada ao contêiner `posts`, que não é particionado por `userId`
1. Dizendo o óbvio, resolveríamos nosso problema de desempenho executando essa solicitação direcionada a um contêiner que *é* particionado por `userId`
1. Acontece que já temos um contêiner assim: o contêiner `users`!

Portanto, apresentamos um segundo nível de desnormalização ao duplicarmos posts inteiros para o contêiner `users`. Fazendo isso, efetivamente obtemos uma cópia de nossos posts, apenas particionados em uma dimensão diferente, tornando a recuperação deles mais eficiente por seus `userId`.

O contêiner `users` agora contém dois tipos de itens:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Observe que:

- Apresentamos um campo `type` no item de usuário para diferenciar usuários de posts,
- Também adicionamos um campo `userId` no item de usuário, que é redundante com o campo `id`, mas é necessário já que o contêiner `users` agora é particionado por `userId` (e não por `id` como anteriormente)

Para alcançar essa desnormalização, podemos usar novamente o feed de alterações. Neste momento, podemos reagir no feed de alterações do contêiner `posts` para expedir qualquer post novo ou atualizado para o contêiner `users`. E já que a listagem de posts não exige que o conteúdo completo deles seja retornado, podemos truncá-los no processo.

![Desnormalizar posts no contêiner de usuários](./media/how-to-model-partition-example/denormalization-2.png)

Agora podemos encaminhar nossa consulta para o contêiner `users`, filtrando na chave de partição do contêiner.

![Recuperar todos os posts de um usuário](./media/how-to-model-partition-example/V3-Q3.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Listar os x posts mais recentes criados na forma abreviada (feed)

Temos de lidar com uma situação semelhante aqui: mesmo depois de poupar as consultas adicionais tornadas desnecessárias pela desnormalização introduzida na V2, a consulta restante não filtra na chave de partição do contêiner:

![Recuperar os posts mais recentes](./media/how-to-model-partition-example/V2-Q6.png)

Seguindo a mesma abordagem, maximizar o desempenho e a escalabilidade desta solicitação requer que ela atinja somente uma partição. Isso é concebível porque precisamos apenas retornar um número limitado de itens. Para preencher a home page da nossa plataforma de blog, é necessário apenas obter os 100 posts mais recentes, sem a necessidade de paginar através de todo o conjunto de dados.

Portanto, para otimizar essa última solicitação, introduzimos um terceiro contêiner em nosso design, totalmente dedicado ao atendimento dessa solicitação. Podemos desnormalizar nossos posts para esse novo contêiner `feed`:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Esse contêiner é particionado por `type`, que sempre será `post` em nossos itens. Fazer isso garante que todos os itens nesse contêiner fiquem na mesma partição.

Para alcançar a desnormalização, precisamos apenas nos conectar ao pipeline do feed de alterações que introduzimos anteriormente para expedir os posts para esse novo contêiner. Uma coisa importante para se ter em mente é que precisamos verificar se armazenamos apenas os 100 posts mais recentes, caso contrário, o conteúdo do contêiner pode crescer além do tamanho máximo de uma partição. Isso é feito chamando um [pós-gatilho](stored-procedures-triggers-udfs.md#triggers) toda vez que um documento é adicionado no contêiner:

![Desnormalizar posts no contêiner do feed](./media/how-to-model-partition-example/denormalization-3.png)

Aqui está o corpo do pós-gatilho que trunca a coleção:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

A etapa final é redirecionar a nossa consulta ao nosso novo contêiner `feed`:

![Recuperar os posts mais recentes](./media/how-to-model-partition-example/V3-Q6.png)

| **Latência** | **Preço da RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 16,97 RU | ✅ |

## <a name="conclusion"></a>Conclusão

Vamos dar uma olhada em aprimoramentos de desempenho e escalabilidade gerais que introduzimos nas diferentes versões do nosso projeto.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms/5,71 RU | 7 ms/5,71 RU | 7 ms/5,71 RU |
| **[Q1]** | 2 ms/1 RU | 2 ms/1 RU | 2 ms/1 RU |
| **[C2]** | 9 ms/8,76 RU | 9 ms/8,76 RU | 9 ms/8,76 RU |
| **[Q2]** | 9 ms/19,54 RU | 2 ms/1 RU | 2 ms/1 RU |
| **[Q3]** | 130 ms/619,41 RU | 28 ms/201,54 RU | 4 ms/6,46 RU |
| **[C3]** | 7 ms/8,57 RU | 7 ms/15,27 RU | 7 ms/15,27 RU |
| **[Q4]** | 23 ms/27,72 RU | 4 ms/7,72 RU | 4 ms/7,72 RU |
| **[C4]** | 6 ms/7,05 RU | 7 ms/14,67 RU | 7 ms/14,67 RU |
| **[Q5]** | 59 ms/58,92 RU | 4 ms/8,92 RU | 4 ms/8,92 RU |
| **[Q6]** | 306 ms/2.063,54 RU | 83 ms/532,33 RU | 9 ms/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Nós otimizamos um cenário de leitura intensa

Você talvez tenha notado que concentramos nossos esforços para melhorar o desempenho das solicitações de leitura (consultas), em detrimento das solicitações de gravação (comandos). Em muitos casos, as operações de gravação agora disparam uma desnormalização subsequente por meio de feeds de alterações, o que as torna mais onerosas computacionalmente e faz com que levem mais tempo para se materializar.

Isso se justifica pelo fato de que uma plataforma de blogs (como a maioria dos aplicativos sociais) é de leitura intensa, o que significa que a quantidade de solicitações de leitura que ela precisa atender normalmente tem ordens de grandeza maiores do que a quantidade de solicitações de gravação. Portanto, faz sentido fazer solicitações de gravação mais dispendiosas para permitir que solicitações de leitura sejam mais baratas e tenham melhor desempenho.

Se verificarmos a otimização mais extrema que fizemos, **[Q6]** passou de 2.000+ RUs para apenas 17 RUs; alcançamos isso desnormalizando posts a um custo de cerca de 10 RUs por item. Já que poderíamos atender a muito mais solicitações de feed do que a criação ou atualizações de posts, o custo dessa desnormalização é insignificante ao considerar a economia geral.

### <a name="denormalization-can-be-applied-incrementally"></a>A desnormalização pode ser aplicada de forma incremental

Os aprimoramentos de escalabilidade explorados neste artigo envolvem a desnormalização e duplicação de dados em todo o conjunto de dados. Deve-se observar que essas otimizações não precisam ser colocadas em vigor no primeiro dia. Consultas que filtram nas chaves de partição têm um desempenho melhor em grande escala, mas consultas entre partições podem ser totalmente aceitáveis se são chamadas raramente ou direcionadas a um conjunto de dados limitado. Se você estiver apenas criando um protótipo ou iniciando um produto com uma base de usuários pequena e controlada, você provavelmente poderá guardar essas melhorias para mais tarde. Depois, o que é importante é [monitorar](use-metrics.md) o desempenho do seu modelo para que você possa decidir se e quando é hora de incluí-las.

O feed de alterações que usamos para distribuir atualizações para outros contêineres armazena todas essas atualizações de maneira persistente. Isso torna possível solicitar todas as atualizações desde a criação do contêiner e inicializar exibições desnormalizadas como uma operação de atualização única, mesmo se o sistema já tem muitos dados.

## <a name="next-steps"></a>Próximas etapas

Após esta introdução ao particionamento e à modelagem de dados de caráter prático, você talvez queira verificar os artigos a seguir para analisar os conceitos abordados:

- [Como trabalhar com bancos de dados, contêineres e itens](databases-containers-items.md)
- [Particionamento no Azure Cosmos DB](partitioning-overview.md)
- [Feed de alterações no Azure Cosmos DB](change-feed.md)

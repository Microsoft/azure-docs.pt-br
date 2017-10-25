---
title: Modelando dados de documentos para um banco de dados NoSQL | Microsoft Docs
description: Saiba mais sobre a modelagem de dados para bancos de dados NoSQL
keywords: modelando dados
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig1
documentationcenter: 
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2016
ms.author: arramac
ms.openlocfilehash: 16c387fe574243544cf54cf283c7713ddcaa1942
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelando dados de documentos para bancos de dados NoSQL
Embora bancos de dados sem esquemas, como o Azure Cosmos DB, facilitem muito a adoção de mudanças em seu modelo de dados, ainda é recomendável dedicar algum tempo para considerar os dados. 

Como eles serão armazenados? Como seu aplicativo vai recuperá-los e consultá-los? O aplicativo realizará grandes volumes de leitura e gravação? 

Depois de ler este artigo, você poderá responder as seguintes perguntas:

* Como devo pensar em um documento num banco de dados de documentos?
* O que é modelagem de dados e como ele me afeta? 
* Como a modelagem de dados em um banco de dados de documentos difere da modelagem de dados em um banco de dados relacional?
* Como posso expressar relações de dados em um banco de dados não relacional?
* Quando eu insiro dados e quando vinculo a eles?

## <a name="embedding-data"></a>Inserindo dados
Ao começar a modelar dados em um repositório de documentos, como o Azure Cosmos DB, tente tratar as entidades como **documentos independentes** representados em JSON.

Antes de nos aprofundarmos demais, vamos voltar um pouco e ver como modelaríamos algo num banco de dados relacional, que é um processo que muitos de nós já conhecemos. O exemplo a seguir mostra como uma pessoa poderia ser armazenada em um banco de dados relacional. 

![Modelo de banco de dados relacional](./media/documentdb-modeling-data/relational-data-model.png)

Durante anos trabalhando com bancos de dados relacionais, aprendemos a normalizar, normalizar e normalizar.

Normalizar os dados geralmente envolve pegar uma entidade, como uma pessoa, e dividi-la em dados separados. No exemplo acima, uma pessoa pode ter vários registros de detalhes de contato, bem como vários registros de endereço. Nós ainda vamos além e dividimos os detalhes de contato extraindo campos comuns, como tipo. O mesmo vale para o endereço, cada registro tem um tipo, como *Residencial* ou *Comercial* 

A premissa que orienta a normalização de dados é **evitar armazenar dados redundantes** em cada registro e, em vez disso, referir-se a eles. Neste exemplo, para ler uma pessoa, com todos os endereços e detalhes de contato, você precisa usar junções para agregar os dados de modo eficaz em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Atualizar uma pessoa, com seus detalhes de contato e endereço, demanda várias operações de gravação em várias tabelas individuais. 

Agora, vejamos como nós modelaríamos os mesmos dados como uma entidade autossuficiente em um banco de dados de documentos.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

Usando a abordagem acima, nós **desnormalizamos** o registro da pessoa, no qual **inserimos** todas as informações relacionadas à pessoa, como seus detalhes de contato e endereços, criando um único documento JSON.
Além disso, como não estamos restritos a um esquema fixo, nós temos a flexibilidade de, por exemplo, ter detalhes de contato com formas totalmente diferentes. 

Agora, a recuperação do registro completo de uma pessoa do banco de dados é feita em somente uma operação de leitura, de uma única coleção e para um único documento. A atualização do registro de uma pessoa, com seus detalhes de contato e endereços, também é feita em uma única operação de gravação, de um único documento.

Com a desnormalização dos dados, seu aplicativo possivelmente precisará emitir menos consultas e atualizações para concluir operações comuns. 

### <a name="when-to-embed"></a>Quando inserir
De modo geral, use modelos de dados inseridos quando:

* Houver relações de **contém** entre entidades.
* Houver relações **de um para poucos** entre entidades.
* Houver dados inseridos que são **alterados com pouca frequência**.
* Houver dados inseridos que não crescerão **sem limite**.
* Houver dados inseridos que são **integrais** aos dados de um documento.

> [!NOTE]
> De modo geral, modelos de dados desnormalizados oferecem melhor desempenho de **leitura** .
> 
> 

### <a name="when-not-to-embed"></a>Quando não inserir
Embora o princípio básico do banco de dados de documentos seja desnormalizar tudo e inserir todos os dados em um único documento, isso pode levar a alguma situações que devem ser evitadas.

Veja este trecho de JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Uma entidade de postagem com comentários inseridos seria assim se estivéssemos modelando um sistema de blog comum, ou CMS. O problema com este exemplo é que a matriz de comentários é **ilimitada**, o que significa que não há limite (prático) para o número de comentários que qualquer postagem pode ter. Isso se tornará um problema, pois o tamanho do documento poderá aumentar significativamente.

Conforme o tamanho do documento aumenta, a capacidade de transmitir dados eletronicamente, bem como de ler e atualizar o documento, em escala, será afetada.

Nesse caso, seria melhor considerar o modelo a seguir.

    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Este modelo tem os três comentários mais recentes inseridos na postagem em si, que é uma matriz com limite fixo. Os outros comentários são agrupados em lotes de 100 e armazenados em documentos separados. O tamanho de 100 foi escolhido para o lote porque nosso aplicativo fictício permite ao usuário carregar 100 comentários por vez.  

Inserir dados também não é recomendado em casos em que os dados inseridos são usados em diferentes documentos e são alterados com frequência. 

Veja este trecho de JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Isto pode representar o portfólio de ações de alguém. Nós optamos por inserir as informações das ações em cada documento do portfólio. Em um ambiente onde dados relacionados mudam com frequência, como um aplicativo de corretagem de ações, inserir dados que mudam frequentemente significa que você atualiza constantemente cada documento do portfólio, sempre que uma ação for negociada.

A ação *zaza* pode ser negociada centenas de vezes em apenas um dia, e milhares de usuários podem ter a ação *zaza* em seus portfólios. Com um modelo de dados como o acima, teríamos que atualizar vários milhares de documentos de portfólio muitas vezes por dia, o que levaria a um sistema mal dimensionado. 

## <a id="Refer"></a>Fazendo referência a dados
Inserir dados funciona bem em muitos casos, mas claramente há situações em que desnormalizar seus dados trará mais problemas do que soluções. E o que podemos fazer? 

Bancos de dados relacionais não são o único lugar onde você pode criar relações entre entidades. Em um banco de dados de documentos, você pode ter informações em um documento que se relacionam a dados de outros documentos. Eu não estou, de modo algum, defendendo a criação de sistemas que seriam mais adequados a um banco de dados relacional no Azure Cosmos DB ou a qualquer outro banco de dados de documentos. O que estou dizendo é que relações simples são ótimas e podem ser muito úteis. 

No JSON abaixo, optamos por usar o exemplo do portfólio de ações, mas, dessa vez, fazemos referência ao item de estoque no portfólio em vez de inseri-lo. Dessa forma, quando o item de estoque mudar frequentemente ao longo do dia, o único documento que precisará ser atualizado será o documento de estoque. 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }


Uma desvantagem imediata dessa abordagem é se o aplicativo precisar mostrar informações sobre cada ação contida no portfólio de uma pessoa ao mostrar o portfólio. Nesse caso, você precisaria ir várias vezes ao banco de dados para carregar as informações de cada documento de ação. Aqui, nós decidimos aumentar a eficiência das operações de gravação, que acontecem com frequência ao longo do dia. Por outro lado, comprometemos as operações de leitura, que têm menor potencial de afetar o desempenho deste sistema em particular.

> [!NOTE]
> Modelos de dados normalizados **podem demandar mais viagens de ida e volta ao servidor** .
> 
> 

### <a name="what-about-foreign-keys"></a>E as chaves estrangeiras?
Como no momento não há o conceito de restrição e chave estrangeira, entre outros, qualquer relação interna que houver nos documentos será um "elo fraco" e não será verificada pelo banco de dados. Se você desejar garantir que os dados aos quais um documento está se referindo realmente existem, você precisa fazer isso no aplicativo, por meio do uso de gatilhos do lado do servidor ou de procedimentos armazenados no Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando fazer referência
De modo geral, use modelos de dados normalizados quando:

* Representar relações de **um para muitos** .
* Representar relações de **muitos para muitos** .
* Dados relacionados **mudarem com frequência**.
* Dados referenciados podem ser **ilimitados**.

> [!NOTE]
> De moro geral, normalizar traz melhor desempenho de **gravação** .
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Onde eu coloco a relação?
O crescimento da relação ajuda a determinar em qual documento armazenar a referência.

Observemos o JSON abaixo que modela editoras e livros.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

Se o número de livros por editora for pequeno, com crescimento limitado, armazenar a referência do livro no documento da editora pode ser útil. No entanto, se o número de livros por editora for ilimitado, esse modelo de dados levaria a matrizes crescentes e mutáveis, como no exemplo de documento de editora abaixo. 

Mudar um pouco as coisas resultaria em um modelo que ainda representa os mesmos dados, mas que agora evita essas grandes coleções mutáveis.

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents: 
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

No exemplo acima, tiramos a coleção ilimitada do documento da editora. Em vez disso, temos apenas uma referência à editora no documento de cada livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como eu modelo relações de muitos para muitos?
Em um banco de dados relacional, relações *muitos:muitos* frequentemente são modeladas com tabelas de junção, que simplesmente reúnem os registros de outras tabelas. 

![Associar tabelas](./media/documentdb-modeling-data/join-table.png)

Você pode ficar tentado a fazer a mesma coisa usando documentos e produzir um modelo de dados semelhante ao seguinte.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Isso funcionaria. No entanto, carregar um autor com seus livros ou carregar um livro com o autor iria sempre demandar pelo menos duas consultas adicionais no banco de dados. Uma consulta do documento de junção e outra consulta para obter o documento real que sofreu a junção. 

Se tudo o que a tabela de junção está fazendo é unir dois dados, por que não simplesmente removê-la?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Agora, se eu tivesse um autor, eu saberia imediatamente quais livros ele escreveu e, da mesma forma, se carregasse o documento de um livro, saberia quem é o autor. Isso anula a consulta intermediária, da tabela de junção, reduzindo o número de viagens de ida e volta ao servidor que o aplicativo precisa fazer. 

## <a id="WrapUp"></a>Modelos de dados híbridos
Nós vimos que inserir (ou desnormalizar) e referenciar (ou normalizar) dados têm suas vantagens e desvantagens. 

Nem sempre é necessário escolher um dos dois. Não tenha medo de misturar um pouco as coisas. 

Com base nas cargas de trabalho e nos padrões de uso específicos do aplicativo, pode haver casos em que misturar dados inseridos e referenciados faz sentido, levando a uma lógica mais simples do aplicativo, com menos viagens de ia e volta ao servidor e mantendo um bom nível de desempenho.

Considere o JSON a seguir. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Aqui, seguimos (principalmente) o modelo inserido, em que dados de outras entidades são inseridos no documento de nível superior, mas outros dados são referenciados. 

Olhando o documento do livro, vemos alguns campos interessantes na matriz de autores. Há um campo *id* que é o campo que usamos para fazer referência a um documento de autor, prática padrão em um modelo normalizado, mas também temos *nome* e *thumbnailUrl*. Nós poderíamos ter ficado somente com a *id* e deixado o aplicativo obter informações adicionais do documento do autor usando o "vínculo", mas como nosso aplicativo mostra o nome do autor e uma foto em miniatura com cada livro mostrado, podemos eliminar uma viagem de ida e volta ao servidor por livro da lista desnormalizando **alguns** dados do autor.

Claro, se o nome do autor mudasse ou se ele quisesse altera sua foto, teríamos que atualizar cada livro publicado. Mas para nosso aplicativo, com base no fato de que autores não mudam de nome com frequência, essa é uma decisão de design aceitável.  

No exemplo, há valores **agregados pré-calculados** para reduzir o processamento extensivo das operações de leitura. No exemplo, alguns dos dados inseridos no documento do autor são calculados em tempo de execução. Sempre que um novo livro é publicado, um documento de livro é criado **e** o campo countOfBooks, relativo à contagem de livros, é definido como um valor calculado com base no número de documentos de livros que existem para um dado autor. Essa otimização seria útil em sistemas com grandes volumes de leitura nos quais podemos computar as gravações para otimizar as leituras.

A capacidade de ter um modelo com campos pré-calculados é possibilitada porque o Azure Cosmos DB dá suporte a **transações de vários documentos**. Muitos repositórios NoSQL não podem fazer transações entre documentos e por isso defendem decisões de design, como "sempre inserir tudo", devido a essa limitação. Com o Azure Cosmos DB, você pode usar gatilhos do lado do servidor ou procedimentos armazenados, que inserem manuais e atualizam autores, tudo isso em uma transação ACID. Você não **precisa** inserir tudo em um documento para garantir que seus dados permaneçam consistentes.

## <a name="NextSteps"></a>Próximas etapas
O principal aspecto deste artigo é entender que modelar dados em um ambiente sem esquemas é tão importante quanto sempre foi. 

Assim como não há apenas uma forma de representar um dado em uma tela, não há apenas uma forma de modelar seus dados. Você precisa entender eu aplicativo e como ele vai produzir, consumir e processar dados. E então, aplicando algumas das diretrizes apresentadas aqui, você pode começar a criar um modelo que trata das necessidades imediatas do seu aplicativo. Quando seus aplicativos precisarem mudar, você pode tirar proveito da flexibilidade de um banco de dados sem esquemas para adotar as mudanças e desenvolver seu modelo de dados facilmente. 

Para saber mais sobre o Azure Cosmos DB, consulte a página de [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do serviço. 

Para entender como fragmentar seus dados em várias partições, consulte [Particionando dados no Azure Cosmos DB](documentdb-partition-data.md). 

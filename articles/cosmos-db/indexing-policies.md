---
title: "Políticas de indexação do Azure Cosmos DB | Microsoft Docs"
description: "Entenda como funciona a indexação no Azure Cosmos DB. Saiba como configurar e alterar a política de indexação para indexação automática e um melhor desempenho."
keywords: "como funciona a indexação, indexação automática, banco de dados de indexação"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: 30a21645831f0cfcb3b52c797dbddfa6b5283960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Como o Azure Cosmos DB indexa dados?

Por padrão, todos os dados do Azure Cosmos DB são indexados. Embora muitos clientes fiquem satisfeitos em deixar que o Azure Cosmos DB cuide automaticamente de todos os aspectos da indexação, o Azure Cosmos DB também dá suporte à especificação de uma **política de indexação** personalizada para coleções durante a criação. As políticas de indexação do Azure Cosmos DB são mais flexíveis e avançadas do que os índices secundários oferecidos em outras plataformas de banco de dados, pois elas permitem o design e a personalização da forma do índice sem comprometer a flexibilidade de esquemas. Para saber como a indexação funciona no Azure Cosmos DB, é necessário entender que, ao gerenciar a política de indexação, você poderá criar compensações refinadas entre a sobrecarga de armazenamento de índice, a produtividade de gravação e consulta e a consistência de consulta.  

Neste artigo, examinamos mais detalhadamente as políticas de indexação do Azure Cosmos DB, como é possível personalizar a política de indexação e as compensações associadas. 

Após ler este artigo, você poderá responder as perguntas a seguir:

* Como substituir as propriedades a serem incluídas ou excluídas da indexação?
* Como configurar o índice para eventuais atualizações?
* Como configurar a indexação para executar consultas Order By ou de intervalo?
* Como fazer alterações à política de indexação de uma coleção?
* Como posso comparar armazenamento e desempenho de políticas de indexação diferentes?

## <a id="CustomizingIndexingPolicy"></a> Personalizando a política de indexação de uma coleção
Os desenvolvedores podem personalizar as compensações entre armazenamento, desempenho de gravação/consulta e consistência de consulta, substituindo a política de indexação padrão em uma coleção do Azure Cosmos DB e configurando os aspectos a seguir.

* **Incluindo/excluindo documentos e caminhos no/do índice**. Os desenvolvedores podem escolher determinados documentos a serem excluídos ou incluídos no índice no momento da inserção ou da substituição deles na coleção. Os desenvolvedores também podem optar por incluir ou excluir determinadas propriedades JSON conhecidas como caminhos (incluindo padrões curingas) a serem indexadas em documentos incluídos em um índice.
* **Configurando diversos tipos de índice**. Para cada um dos caminhos incluídos, os desenvolvedores também podem especificar o tipo de índice exigido em uma coleção com base nos dados e na carga de trabalho de consulta esperada, além da “precisão” numérica ou de cadeia de caracteres de cada caminho.
* **Configurando modos de atualização de índice**. O Azure Cosmos DB dá suporte a três modos de indexação, que podem ser configurados por meio da política de indexação em uma coleção do Azure Cosmos DB: Consistente, Lento e Nenhum. 

O seguinte trecho de código .NET mostra como definir uma política de indexação personalizada durante a criação de uma coleção. Aqui, definimos a política com índice de intervalo de cadeias de caracteres e números à precisão máxima. Essa política nos permite executar consultas de Ordenar por com relação a cadeias de caracteres.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> O esquema JSON para política de indexação foi alterado com o lançamento da versão 2015-06-03 da API REST para dar suporte a índices de intervalo nas cadeias de caracteres. O SDK 1.2.0 do .NET e os SDKs 1.1.0 do Java, Python e Node.js suportam o novo esquema de política. SDKs mais antigos usam a API REST versão 2015-04-08 e dão suporte ao esquema mais antigo da política de indexação.
> 
> Por padrão, o Azure Cosmos DB indexa todas as propriedades de cadeia de caracteres nos documentos de forma consistente com um índice de Hash e as propriedades numéricas com um índice de Intervalo.  
> 
> 

### <a name="customizing-the-indexing-policy-using-the-portal"></a>Personalização da política de indexação usando o portal

Você pode alterar a política de indexação de uma coleção usando o portal do Azure. Abra sua conta do Azure Cosmos DB no portal do Azure, selecione a coleção na menu de navegação à esquerda, clique em **Configurações** e então clique em **Política de Indexação**. Na folha **Política de Indexação**, altere sua política de indexação e, em seguida, clique em **OK** para salvar suas alterações. 

### <a id="indexing-modes"></a>Modos de indexação do banco de dados
O Azure Cosmos DB dá suporte a três modos de indexação, que podem ser configurados por meio da política de indexação em uma coleção do Azure Cosmos DB – Consistente, Lento e Nenhum.

**Consistente**: se uma política da coleção do Azure Cosmos DB for designada como “consistente”, as consultas em determinada coleção do Azure Cosmos DB seguirão o mesmo nível de consistência especificado para as leituras de ponto (ou seja, forte, desatualização limitada, sessão ou eventual). O índice é atualizado de forma síncrona como parte da atualização do documento (ou seja, inserção, substituição, atualização e exclusão de um documento em uma coleção do Azure Cosmos DB).  A indexação consistente dá suporte a consultas consistentes ao custo de uma possível redução na taxa de transferência de gravação. Essa redução é uma função dos caminhos exclusivos que precisam ser indexados e do "nível de consistência". O modo de indexação consistente foi projetado para cargas de trabalho de "gravação rápida, consulta imediata”.

**Lento**: para permitir a produtividade máxima da ingestão de documentos, uma coleção do Azure Cosmos DB pode ser configurada com consistência lenta, o que significa que as consultas serão, em última análise, consistentes. O índice é atualizado de forma assíncrona quando uma coleção do Azure Cosmos DB está inativa, ou seja, quando a capacidade de produtividade da coleção não é totalmente utilizada para atender às solicitações do usuário. Para cargas de trabalho "ingerir agora, consultar depois" que exijam a ingestão ilimitada de documentos, o modo de indexação “lento” será mais adequado.

**Nenhum**: uma coleção marcada com o modo de índice "Nenhum" não tem nenhum índice associado a ela. Isso é geralmente usado se o Azure Cosmos DB é utilizado como um armazenamento de chave/valor e os documentos são acessados apenas pela sua propriedade de ID. 

> [!NOTE]
> A configuração da política de indexação com “Nenhum” tem o efeito colateral de remover qualquer índice existente. Use essa opção se os padrões de acesso forem somente exigir a “id” e/ou o “self-link”.
> 
> 

A tabela a seguir mostra a consistência para as consultas baseadas no modo de indexação (Consistente e Lento) configurado para a coleção e o nível de consistência especificado para a solicitação de consulta. Isso se aplica a consultas criadas com qualquer interface - API REST, SDKs ou de dentro de procedimentos armazenados e de gatilhos. 

|Consistência|Modo de indexação: Consistente|Modo de indexação: Lento|
|---|---|---|
|Strong|Strong|Eventual|
|Bounded staleness|Bounded staleness|Eventual|
|Session|Session|Eventual|
|Eventual|Eventual|Eventual|

O Azure Cosmos DB retorna um erro para consultas feitas em coleções com o modo de indexação Nenhum. As consultas podem ainda ser executadas como verificações por meio do cabeçalho explícito `x-ms-documentdb-enable-scan` na API REST ou da opção de solicitação `EnableScanInQuery` usando o SDK do .NET. Alguns recursos de consulta como ORDER BY não têm suporte como verificações com `EnableScanInQuery`.

A tabela a seguir mostra a consistência de consultas baseadas no modo de indexação (Consistente, Lento e Nenhum) quando EnableScanInQuery é especificado.

|Consistência|Modo de indexação: Consistente|Modo de indexação: Lento|Modo de indexação: Nenhum|
|---|---|---|---|
|Strong|Strong|Eventual|Strong|
|Bounded staleness|Bounded staleness|Eventual|Bounded staleness|
|Session|Session|Eventual|Session|
|Eventual|Eventual|Eventual|Eventual|

O exemplo de código a seguir mostra como criar uma coleção do Azure Cosmos DB usando o SDK do .NET com indexação consistente em todas as inserções de documentos.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Caminhos de índice
O Azure Cosmos DB modela documentos JSON e o índice como árvores e permite que você ajuste políticas de caminhos dentro da árvore. Nos documentos, você pode escolher quais caminhos devem ser incluídos ou excluídos da indexação. Isso pode oferecer um melhor desempenho de gravação e menor armazenamento de índice para situações onde os padrões de consulta são previamente conhecidos.

Caminhos de índice começam com a raiz (/) e geralmente terminam com o operador de curinga ?, indicando que há vários valores possíveis para o prefixo. Por exemplo, para servir SELECT * FROM Families F WHERE F.familyName = "Andersen", você deve incluir um caminho de índice para /familyName/? na política de índice da coleção.

Os caminhos de índice também podem usar o operador curinga * para especificar o comportamento de caminhos recursivamente no prefixo. Por exemplo, /payload/* pode ser usado para excluir tudo na propriedade de conteúdo da indexação.

Estes são os padrões comuns para especificar caminhos de índice:

| path                | Descrição/caso de uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Caminho padrão para coleta. Recursiva e aplica-se em toda árvore do documento inteiro.                                                                                                                                                                                                                                   |
| /prop/?             | Caminho de índice necessário para fazer consultas como as seguintes (com tipos hash e de intervalo respectivamente):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Caminho de índice para todos os caminhos sob o rótulo especificado. Funciona com as seguintes consultas<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de escalares como ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de objetos como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Caminho de índice necessário para fazer consultas (com tipos hash e de intervalo respectivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Ao definir caminhos de índice personalizados, é necessário especificar a regra de indexação padrão para a árvore de todo o documento indicada pelo caminho especial "/*". 
> 
> 

O exemplo a seguir configura um caminho específico com a indexação de intervalo e um valor personalizado de precisão de 20 bytes:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipos, modelos e precisões de dados
Agora que já vimos como especificar caminhos, vamos examinar as opções que podemos usar para configurar a política de indexação para um caminho. Você pode especificar uma ou mais definições de indexação para cada caminho:

* Tipo de dados: **String**, **Number**, **Point**, **Polygon** ou **LineString** (pode conter somente uma entrada por tipo de dados por caminho)
* Tipo de índice: **Hash** (consultas de igualdade), **Intervalo** (consultas de igualdade, de intervalo ou Order By) ou **Espacial** (consultas espaciais) 
* Precisão: para índice de hash, isso varia de 1 a 8 para cadeias de caracteres e números, com um padrão de 3. Para o índice de intervalo, esse valor pode ser -1 (precisão máxima) e variar entre 1 e 100 (precisão máxima) para a cadeia de caracteres ou valores numéricos.

#### <a name="index-kind"></a>Tipo de índice
O Azure Cosmos DB dá suporte a tipos de índice Hash e Intervalo em todos os caminhos (que podem ser configurados para cadeias de caracteres, números ou ambos).

* **Hash** dá suporte a consultas JOIN e de igualdade eficientes. Na maioria dos casos de uso, os índices de hash não precisam de uma precisão maior que o valor padrão de 3 bytes. DataType pode ser String ou Number.
* **Intervalo** dá suporte a consultas de igualdade eficientes, a consultas de intervalo (usando >, <, >=, <=, !=) e a consultas Order By. Por padrão, as consultas Ordenar por também exigem a precisão máxima de índice (-1). DataType pode ser String ou Number.

O Azure Cosmos DB também dá suporte ao tipo de índice Espacial em todos os caminhos, que podem ser especificados para os tipos de dados Point, Polygon ou LineString. O valor no caminho especificado deve ser um fragmento GeoJSON válido, como `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Espacial** dá suporte a consultas espaciais (interna e de distância) eficientes. DataType pode ser Point, Polygon ou LineString.

> [!NOTE]
> O Azure Cosmos DB dá suporte à indexação automática de Points, Polygons e LineStrings.
> 
> 

Estes são os tipos de índice com suporte e exemplos de consultas que eles podem usar para servir:

| Tipo de índice | Descrição/caso de uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | O hash em /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash em /props/[]/? (or / or /props/) pode ser usado para servir às consultas a seguir eficientemente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Intervalo      | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Espacial     | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --com indexação nos pontos habilitada<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --com indexação em polígonos habilitada              |

Por padrão, um erro será retornado para consultas com operadores de intervalo como >= se não houver nenhum índice de intervalo (de qualquer precisão) para sinalizar que uma verificação pode ser necessária para servir a consulta. Consultas de intervalo podem ser executadas sem um índice de intervalo usando o cabeçalho x-ms-documentdb-enable-scan na API REST ou na opção de solicitação EnableScanInQuery usando o SDK do .NET. Se houver outro filtro na consulta no qual o Azure Cosmos DB pode usar o índice para filtrar, nenhum erro será retornado.

As mesmas regras se aplicam a consultas espaciais. Por padrão, um erro retornará para consultas espaciais se não houver um índice espacial e se não houver outros filtros que possam ser atendidos a partir do índice. Elas podem ser executadas como um exame usando x-ms-documentdb-enable-scan/EnableScanInQuery.

#### <a name="index-precision"></a>Precisão de índice
A precisão de índice permite definir um equilíbrio entre a sobrecarga de armazenamento de índice e o desempenho da consulta. Para números, recomendamos usar a configuração de precisão padrão -1 (“máximo”). Como os números são 8 bytes em JSON, isso é equivalente a uma configuração de 8 bytes. Escolher um valor mais baixo para precisão, como 1 a 7, significa que os valores dentro de alguns intervalos podem ser mapeados para a mesma entrada de índice. Portanto, você reduzirá o espaço de armazenamento do índice, mas a execução da consulta talvez precise processar mais documentos e, como consequência, consumirá mais taxa de transferência, ou seja, unidades de solicitação.

A configuração de precisão do índice tem mais aplicação prática com intervalos de cadeia de caracteres. Como cadeias de caracteres podem ter qualquer comprimento arbitrário, a escolha de precisão do índice pode afetar o desempenho de consultas de intervalo de cadeia de caracteres e a quantidade de espaço de armazenamento de índice necessária. Os índices de intervalo de cadeia de caracteres podem ser configurados com 1-100 ou -1 (“máximo”). Se você quiser executar consultas Ordenar por em propriedades de cadeia de caracteres, então deverá especificar uma precisão de -1 para os caminhos correspondentes.

Os índices espaciais sempre usam a precisão de índice padrão para todos os tipos (Points, LineStrings e Polygons) e não podem ser substituídos. 

O exemplo a seguir mostra como aumentar a precisão de índices de intervalo em uma coleção usando o SDK do .NET. 

**Criar uma coleção com uma precisão de índice personalizada**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> O Azure Cosmos DB retorna um erro quando uma consulta usa Order By, mas não tem um índice de intervalo no caminho consultado com a precisão máxima. 
> 
> 

Da mesma forma, caminhos podem ser excluídos completamente da indexação. O exemplo a seguir mostra como excluir uma seção inteira de documentos (também conhecida como uma subárvore) de indexação usando o curinga "*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Aceitando e recusando a indexação
Você pode escolher se deseja que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode optar por desativá-lo. Quando a indexação estiver desativada, documentos podem ser acessados somente por meio de seus self links ou através de consultas usando um ID.

Com a indexação automática desativada, você ainda pode adicionar seletivamente somente documentos específicos para o índice. Por outro lado, você pode deixar a indexação automática e seletivamente optar por excluir apenas documentos específicos. As configurações de Ativar/desativar a indexação são úteis quando você tem apenas um subconjunto dos documentos que precisa ser consultado.

Por exemplo, a amostra a seguir explica como incluir um documento explicitamente usando o [SDK do .NET na API do DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-sdk-dotnet) e a propriedade [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Modificando a política de indexação de uma coleção
O Azure Cosmos DB permite que você faça alterações na política de indexação de uma coleção de forma dinâmica. Uma alteração na política de indexação em uma coleção do Azure Cosmos DB pode levar a uma alteração na forma do índice, incluindo a possibilidade de indexação dos caminhos, a precisão deles, bem como o modelo de consistência do próprio índice. Portanto, uma alteração na política de indexação requer, efetivamente, uma transformação do índice antigo em um novo. 

**Transformações de índice online**

![Como funciona a indexação – Transformações de índice online do Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

As transformações de índice são feitas online, o que significa que os documentos indexados pela política anterior são transformados com eficiência de acordo com a nova política **sem afetar a disponibilidade de gravação ou a taxa de transferência provisionada** da coleção. A consistência de operações de leitura e de gravação realizadas usando a API REST, SDKs ou em procedimentos armazenados e gatilhos não é afetada durante a transformação de índice. Isso significa que não haverá degradação de desempenho ou tempo de inatividade em seus aplicativos quando você alterar uma política de indexação.

No entanto, durante o tempo em que a transformação de índice estiver em andamento, as consultas por fim serão consistentes, independentemente da configuração do modo indexação (Consistente ou Lento). Isso também se aplica a consultas de todas as interfaces – API REST, SDKs ou de procedimentos armazenados e de gatilhos. Assim como acontece na indexação Lenta, a transformação de índice é executada de forma assíncrona em segundo plano nas réplicas usando os recursos disponíveis para uma determinada réplica. 

As transformações de índice também são feitas **in-situ** (no local), ou seja, o Azure Cosmos DB não mantém duas cópias do índice e troca o índice antigo pelo novo. Isso significa que o espaço em disco adicional não será necessário ou consumido em suas coleções durante a execução de transformações de índice.

Quando você alterar a política de indexação, a forma como as alterações serão aplicadas para a transformação do índice antigo no novo dependerão principalmente das configurações do modo de indexação do que de outros valores, como os caminhos incluídos/excluídos, os tipos de índice e as precisões. Caso a política antiga e a nova usem a indexação consistente, o Azure Cosmos DB executará uma transformação de índice online. Você não poderá aplicar outra alteração de política de indexação com o modo de indexação consistente enquanto a transformação estiver em andamento.

No entanto, você pode mudar para o modo de indexação Lento ou Nenhum enquanto uma transformação está em andamento. 

* Quando você muda para Lento, a alteração na política de indexação é efetivada imediatamente e o Azure Cosmos DB inicia a recriação do índice de forma assíncrona. 
* Quando você muda para Nenhum, então o índice é removido imediatamente. A mudança para Nenhum será útil quando você quiser cancelar uma transformação em andamento e iniciar uma nova com uma política de indexação diferente. 

Se você estiver usando o SDK do .NET, poderá iniciar uma alteração de política de indexação usando o novo método **ReplaceDocumentCollectionAsync** e acompanhar o progresso em percentual da transformação do índice transformação usando o a propriedade de resposta **IndexTransformationProgress** de uma chamada **ReadDocumentCollectionAsync**. Outros SDKs e a API REST dão suporte a propriedades e métodos equivalentes para alterações na política de indexação.

Veja um trecho de código que mostra como modificar a política de indexação de uma coleção do modo de indexação Consistente para o Lento.

**Modificar a política de indexação de Consistente para Lento**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Você pode verificar o andamento de uma transformação de índice chamando, por exemplo, ReadDocumentCollectionAsync, como mostrado abaixo.

**Acompanhar o andamento da transformação de índice**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Você pode remover o índice de uma coleção mudando para o modo de indexação Nenhum. Essa pode ser uma ferramenta operacional útil se você quiser cancelar uma transformação em andamento e iniciar uma nova imediatamente.

**Removendo o índice de uma coleção**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando você fará alterações na política de indexação de suas coleções do Azure Cosmos DB? A seguir, os casos de uso mais comuns:

* Fornecer resultados consistentes durante a operação normal, mas fazer fallback para a indexação lenta durante importações de dados em massa
* Começar a usar novos recursos de indexação em suas coleções atuais do Azure Cosmos DB, como consultas geoespaciais que exigem o tipo de índice Espacial ou consultas de intervalo Order By/cadeia de caracteres, que exigem o tipo de índice Intervalo de cadeia de caracteres
* Selecionar manualmente as propriedades a serem indexadas e alterá-las ao longo do tempo
* Ajustar a precisão da indexação para melhorar o desempenho da consulta ou reduzir o armazenamento consumido

> [!NOTE]
> Para modificar a política de indexação usando ReplaceDocumentCollectionAsync, você precisará de uma versão superior à 1.3.0 do SDK do .NET
> 
> Para que a transformação do índice seja concluída com êxito, você deve assegurar que exista espaço livre suficiente disponível na coleção. Se a coleção atingir sua cota de armazenamento, a transformação do índice será pausada. A transformação do índice será retomada automaticamente quando houver espaço em armazenamento disponível, por exemplo, se você excluir alguns documentos.
> 
> 

## <a name="performance-tuning"></a>Ajuste de desempenho
As APIs do DocumentDB fornecem informações sobre as métricas de desempenho, como o armazenamento de índice usado e o custo da taxa de transferência (unidades de solicitação) para cada operação. Essas informações podem ser usadas para comparar várias políticas de indexação e para ajuste de desempenho.

Para verificar se a cota de armazenamento e o uso de uma coleção, execute uma solicitação HEAD ou GET em relação ao recurso de coleção e inspecione os cabeçalhos x-ms-request-quota e x-ms-request-usage. No SDK do .NET, as propriedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) em [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contêm esses valores correspondentes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir a sobrecarga de indexação em cada operação de gravação (criar, atualizar ou excluir), inspecione o cabeçalho x-ms-request-charge (ou a propriedade equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) em [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) no SDK do .NET) para medir o número de unidades de solicitação consumidas por essas operações.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Alterações à especificação da política de indexação
Uma alteração no esquema da política de indexação foi introduzida em 7 de julho de 2015 com a versão 2015-06-03 da API REST. As classes correspondentes nas versões do SDK têm novas implementações para corresponder ao esquema. 

As seguintes alterações foram implementadas na especificação JSON:

* A política de indexação dá suporte a índices de intervalo para cadeias de caracteres
* Cada caminho pode ter várias definições de índice, um para cada tipo de dados
* A indexação de precisão dá suporte a 1-8 para números de 1-100 para cadeias de caracteres e -1 (precisão máxima)
* Segmentos de caminhos não exigem aspas duplas para cada caminho de escape. Por exemplo, você pode adicionar um caminho /title/? em vez de /"title"/?
* O caminho raiz representando "todos os caminhos" pode ser representado como /* (além de /)

Se você tiver o código que provisiona coleções com uma política de indexação personalizada gravada com versão 1.1.0 do SDK do .NET ou anterior, precisará alterar o código do aplicativo para lidar com essas alterações e mover para a versão 1.2.0 do SDK. Se você não tiver um código que configura a política de indexação ou planeja continuar usando uma versão anterior do SDK, nenhuma alteração será necessária.

Para uma comparação prática, aqui está um exemplo de política de indexação personalizada escrita usando a API REST versão 2015-06-03, bem como a versão 2015-04-08 anterior.

**Política de indexação anterior do JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }

**Política de indexação atual do JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## <a name="next-steps"></a>Próximas etapas
Siga os links abaixo para ver exemplos de gerenciamento de políticas de índice e saber mais sobre a linguagem de consulta do Azure Cosmos DB.

1. [Exemplos de código do Gerenciamento de Índice do .NET na API do DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Operações de coleção da API REST no DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Consulta com SQL](documentdb-sql-query.md)


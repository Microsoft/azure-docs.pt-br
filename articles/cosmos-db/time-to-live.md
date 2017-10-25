---
title: "Expirar os dados no Azure Cosmos DB com a vida útil | Microsoft Docs"
description: "Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do sistema após determinado período."
services: cosmos-db
documentationcenter: 
keywords: "vida útil"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 6213019131eec60263172f468ced516037a33c61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Expirar os dados em coleções do Azure Cosmos DB automaticamente com a vida útil
Os aplicativos podem gerar e armazenar grandes quantidades de dados. Alguns desses dados, como dados de evento, logs e informações da sessão do usuário gerados por computador, são úteis apenas por determinado período. Depois que os dados se tornam excedentes para as necessidades do aplicativo, é seguro limpar esses dados e reduzir as necessidades de armazenamento de um aplicativo.

Com a “vida útil” ou TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do banco de dados após determinado período. A vida útil padrão pode ser definida no nível de coleção e substituída conforme o documento. Depois de definir a TTL como um padrão de coleta ou em um nível de documento, o Cosmos DB removerá automaticamente os documentos existentes após esse período, em segundos, desde sua última modificação.

A vida útil no Cosmos DB usa um deslocamento em relação à data da última modificação do documento. Para fazer isso, ela usa o campo `_ts`, encontrado em todos os documentos. O campo _ts é um carimbo de data/hora de época estilo Unix que representa a data e a hora. O campo `_ts` é atualizado sempre que um documento é modificado. 

## <a name="ttl-behavior"></a>Comportamento de TTL
O recurso TTL é controlado pelas propriedades TTL em dois níveis: o nível de coleção e o nível de documento. Os valores são definidos em segundos e tratados como um delta no `_ts` em que o documento foi modificado pela última vez.

1. DefaultTTL da coleção
   
   * Se estiverem ausentes (ou definidos como nulos), os documentos não serão excluídos automaticamente.
   * Se estiverem presentes e o valor for "-1" = infinito, os documentos não expirarão por padrão
   * Se estiverem presentes e o valor for qualquer número ("n"), os documentos expirarão em "n" segundos após a última modificação
2. TTL dos documentos: 
   
   * A propriedade será aplicável somente se houver uma DefaultTTL para a coleção pai.
   * Substitui o valor de DefaultTTL da coleção pai.

Assim que o documento tiver expirado (`ttl` + `_ts` <= hora atual do servidor), o documento será marcado como "expirado". Nenhuma operação será permitida nesses documentos após esse período e elas serão excluídas dos resultados de todas as consultas executadas. Os documentos são excluídos fisicamente no sistema e são excluídos em segundo plano oportunamente em um momento posterior. Isso não consome nenhuma [RU (Unidade de Solicitação)](request-units.md) do orçamento da coleção.

A lógica acima pode ser mostrada na matriz a seguir:

|  | DefaultTTL ausente/não definida na coleção | DefaultTTL = -1 na coleção | DefaultTTL = “n” na coleção |
| --- |:--- |:--- |:--- |
| TTL Ausente no documento |Nada para substituir no nível de documento, pois o documento e a coleção não têm nenhum conceito de TTL. |Nenhum documento nesta coleção expirará. |Os documentos nessa coleção expirarão quando o intervalo de n expirar. |
| TTL = -1 no documento |Nada para substituir no nível de documento, pois a coleção não define a propriedade DefaultTTL que pode ser substituída por um documento. A TTL de um documento não é interpretada pelo sistema. |Nenhum documento nesta coleção expirará. |O documento com TTL = -1 nesta coleção nunca expirará. Todos os outros documentos expirarão após o intervalo de “n”. |
| TTL = n no documento |Nada para substituir no nível de documento. A TTL de um documento não é interpretada pelo sistema. |O documento com TTL = n expirará após o intervalo de n, em segundos. Os outros documentos herdarão o intervalo de -1 e nunca expirarão. |O documento com TTL = n expirará após o intervalo de n, em segundos. Os outros documentos herdarão o intervalo de “n” da coleção. |

## <a name="configuring-ttl"></a>Configurando a TTL
Por padrão, a vida útil é desabilitada por padrão em todas as coleções e todos os documentos do Cosmos DB. TTL pode ser definido programaticamente ou no Portal do Azure, na seção **Configurações** da coleção. 

## <a name="enabling-ttl"></a>Habilitando a TTL
Para habilitar a TTL em uma coleção, ou no documento em uma coleção, é necessário definir a propriedade DefaultTTL de uma coleção como -1 ou um número positivo diferente de zero. Definir a DefaultTTL como -1 significa que, por padrão, todos os documentos na coleção residirão para sempre, mas o serviço Cosmos DB deverá monitorar, nessa coleção, os documentos que substituíram esse padrão.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Configurando a TTL padrão em uma coleção
É possível configurar uma vida útil padrão em um nível de coleção. Para definir a TTL em uma coleção, é necessário fornecer um número positivo diferente de zero que indique o período, em segundos, para expirar todos os documentos na coleção após o último carimbo de data/hora modificado do documento (`_ts`). Outra opção é definir o padrão como -1, o que significa que, por padrão, todos os documentos inseridos na coleção residirão por tempo indeterminado.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Configurando a TTL em um documento
Além de definir uma TTL padrão em uma coleção, é possível definir uma TTL específica em um nível de documento. Isso substituirá o padrão da coleção.

* Para definir a TTL em um documento, é necessário fornecer um número positivo diferente de zero que indique o período, em segundos, para expirar todos os documentos após o último carimbo de data/hora modificado do documento (`_ts`).
* Se um documento não tiver um campo TTL, será aplicado o padrão da coleção.
* Se a TTL estiver desabilitada no nível de coleção, o campo TTL no documento será ignorado até que a TTL seja habilitada novamente na coleção.

Veja a seguir um trecho que mostra como definir a expiração de TTL em um documento:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Estendendo a TTL em um documento existente
É possível redefinir a TTL em um documento executando qualquer operação de gravação no documento. Isso definirá o `_ts` como a hora atual, e a contagem regressiva para a expiração do documento, conforme definido pela `ttl`, será iniciada novamente. Se deseja alterar a `ttl` de um documento, é possível atualizar o campo da mesma forma que qualquer outro campo configurável.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Removendo a TTL de um documento
Se uma TTL tiver sido definida em um documento e você não deseja mais que o documento expire, é possível recuperar o documento, remover campo TTL e substituir o documento no servidor. Quando o campo TTL for removido do documento, será aplicado o padrão da coleção. Para interromper a expiração de um documento e fazer com ele não herde da coleção, é necessário definir o valor de TTL como -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Desabilitando a TTL
Para desabilitar a TTL por completo em uma coleção e fazer com que o processo em segundo plano pare de procurar documentos expirados, a propriedade de DefaultTTL na coleção deve ser excluída. A exclusão dessa propriedade é diferente de defini-la como -1. A configuração como -1 significa que os novos documentos adicionados à coleção residirão para sempre, mas é possível substituí-la em documentos específicos da coleção. Remover esta propriedade por completo da coleção significa que os documentos não expirarão, mesmo que haja documentos que tenham explicitamente substituído um padrão anterior.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);


## <a name="faq"></a>Perguntas frequentes
**Qual o preço da TTL?**

Não há nenhum custo adicional para definição de uma TTL em um documento.

**Quanto tempo levará para que meu documento seja excluído após a habilitação da TTL?**

Os documentos expiram imediatamente depois da TTL ser ativada e não poderão ser acessados por meio do CRUD ou das APIs de consulta. 

**A TTL em um documento terá algum impacto sobre os encargos de RU?**

Não haverá nenhum impacto nos encargos de RU para as exclusões de documentos expirados por meio da TTL no Cosmos DB.

**O recurso TTL se aplica somente a documentos inteiros ou valores de propriedade de documentos individuais podem ser expirados?**

A TTL se aplica a todo o documento. Caso você queira que apenas uma parte de um documento expire, é recomendável extrair a parte do documento principal em um documento "vinculado" à parte e, em seguida, usar a TTL nesse documento extraído.

**O recurso TTL tem requisitos específicos de indexação?**

Sim. A coleção deve ter uma [política de indexação definida](indexing-policies.md) como Consistente ou Lenta. A tentativa de definir a DefaultTTL em uma coleção com a indexação definida como Nenhum resultará em um erro, assim como a tentativa de desativar a indexação em uma coleção que tenha uma DefaultTTL já definida.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Azure Cosmos DB, consulte a página de [*documentação*](https://azure.microsoft.com/documentation/services/cosmos-db/) do serviço.


---
title: "Várias arquiteturas de banco de dados mestre com o Azure Cosmos DB | Microsoft Docs"
description: "Saiba mais sobre como projetar arquiteturas de aplicativos com leituras e gravações locais em várias regiões geográficas com o Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf1482ae7b1070023703f5dbe861d151f5d64fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Arquiteturas de banco de dados replicadas globalmente de vários mestres com o Azure Cosmos DB
O Azure Cosmos DB dá suporte turnkey à [replicação global](distribute-data-globally.md), que permite que você distribua dados para várias regiões com acesso de baixa latência em qualquer lugar na carga de trabalho. Esse modelo é usado para cargas de trabalho do publisher/consumidor onde há um gravador em uma única região geográfica e leitores distribuídos globalmente em outras regiões (leitura). 

Você também pode usar o suporte de replicação global do Azure Cosmos DB para criar aplicativos em que os gravadores e leitores são distribuídos globalmente. Este documento descreve um padrão que permite obter gravação local e acesso de leitura local para gravadores distribuídos usando o Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publicação de conteúdo - um cenário de exemplo
Vamos examinar um cenário do mundo real para descrever como você pode usar padrões de leitura e gravação de várias regiões/vários mestres distribuídos globalmente com o Azure Cosmos DB. Considere uma plataforma de publicação de conteúdo criada no Azure Cosmos DB. Aqui estão alguns requisitos que essa plataforma deve atender para uma excelente experiência de usuário para editores e consumidores.

* Os autores e os assinantes estão espalhados pelo mundo 
* Os autores devem publicar artigos (gravação) em sua região local (mais próxima)
* Os autores têm leitores/assinantes de seus artigos distribuídos pelo mundo. 
* Os assinantes devem receber uma notificação quando novos artigos são publicados.
* Os assinantes devem ser capazes de ler artigos da sua região local. Também devem ser capazes de adicionar críticas a esses artigos. 
* Qualquer pessoa, incluindo o autor dos artigos, deve ser capaz de exibir todas as críticas anexados aos artigos de uma região local. 

Supondo que haja milhões de clientes e fornecedores com bilhões de artigos, assim temos de enfrentar os problemas de escala junto com a garantia de localidade de acesso. Assim como acontece com a maioria dos problemas de escalabilidade, a solução está em uma boa estratégia de particionamento. Em seguida, vamos examinar como modelar artigos, revisão e notificações como documentos, configurar contas do Azure Cosmos DB e implementar uma camada de acesso a dados. 

Se você quiser saber mais sobre particionamento e chaves de partição, consulte [Particionamento e dimensionamento no Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Notificações de modelagem
As notificações são feeds específicos de dados para um usuário. Portanto, os padrões de acesso para documentos de notificações são sempre no contexto de usuário único. Por exemplo, você poderia "lançar uma notificação a um usuário" ou "buscar todas as notificações para um determinado usuário". Portanto, a opção de chave para esse tipo de particionamento ideal seria `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Assinaturas de modelagem
As assinaturas podem ser criadas para vários critérios, como uma categoria específica de artigos de interesse ou uma editora específica. Portanto, o `SubscriptionFilter` é uma boa opção para a chave de partição.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Artigos de modelagem
Quando um artigo é identificado por meio de notificações, as consultas subsequentes são normalmente baseadas no `Article.Id`. Portanto, escolher `Article.Id` como chave de partição oferece a melhor distribuição para armazenar artigos dentro de uma coleção do Azure Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Análises de modelagem
Como artigos, resenhas principalmente são escritas e ler no contexto do artigo. Escolhendo `ArticleId` como uma partição chave fornece acesso eficiente das revisões associada ao artigo e melhor distribuição. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Métodos de camada de acesso de dados
Agora vamos examinar os dados principais, precisamos implementar os métodos de acesso. Aqui está a lista dos métodos que o `ContentPublishDatabase` precisa:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Configuração de conta do Azure Cosmos DB
Para garantir a local lê e grava, podemos deve particionar dados será não apenas na partição chave, mas também com base no padrão de acesso geográfica em regiões. O modelo se baseia em ter uma conta de banco de dados do Azure Cosmos DB replicada geograficamente para cada região. Por exemplo, com duas regiões, aqui está uma configuração para gravações de várias regiões:

| Nome da conta | Região de gravação | Região de leitura |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

O diagrama a seguir mostra como as leituras e gravações são executadas em um aplicativo típico com esta configuração:

![Arquitetura de vários mestres do Azure Cosmos DB](./media/multi-region-writers/multi-master.png)

Aqui está um trecho de código mostrando como inicializar os clientes em uma DAL em execução no `West US` região.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Com a configuração anterior, a camada de acesso a dados pode encaminhar todas as gravações para a conta local com base em onde ele é implantado. As leituras são executadas pela leitura de ambas as contas para obter a exibição global dos dados. Essa abordagem pode ser estendida para regiões tantos conforme necessário. Por exemplo, aqui está uma configuração com três regiões geográficas:

| Nome da conta | Região de gravação | Leitura região 1 | Região de leitura 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementação de camada de acesso de dados
Agora vamos examinar a implementação da camada de acesso a dados (DAL) para um aplicativo com duas regiões graváveis. A DAL deve implementar as seguintes etapas:

* Criar várias instâncias de `DocumentClient` para cada conta. Com duas regiões, cada instância DAL tem um `writeClient` e um `readClient`. 
* Com base na região de implantação do aplicativo, configure os pontos de extremidade `writeclient` e `readClient`. Por exemplo, a DAL implantado em `West US` usa `contentpubdatabase-usa.documents.azure.com` para executar gravações. A DAL implantado em `NorthEurope` usa `contentpubdatabase-europ.documents.azure.com` para gravações.

Com a configuração anterior, os métodos de acesso a dados podem ser implementados. Gravar operações encaminham a gravação correspondente `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Para ler notificações e revisões, você deve ler de regiões e união os resultados conforme mostrado no trecho a seguir:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Portanto, ao escolher uma boa chave de particionamento e particionamento estático baseado em conta, você pode obter leituras e gravações locais de várias regiões usando o Azure Cosmos DB.

## <a id="NextSteps"></a>Próximas etapas
Neste artigo, descrevemos como você pode usar padrões de leitura e gravação de várias regiões distribuídos globalmente com o Azure Cosmos DB usando a publicação de conteúdo como um cenário de exemplo.

* Saiba sobre como o Azure Cosmos DB dá suporte à [distribuição global](distribute-data-globally.md)
* Saiba mais sobre [failovers automáticos e manuais no Azure Cosmos DB](regional-failover.md)
* Saiba mais sobre [consistência global com o Azure Cosmos DB](consistency-levels.md)
* Desenvolver com várias regiões usando o [Azure Cosmos DB – API do DocumentDB](tutorial-global-distribution-documentdb.md)
* Desenvolver com várias regiões usando o [Azure Cosmos DB – API do MongoDB](tutorial-global-distribution-MongoDB.md)
* Desenvolver com várias regiões usando o [Azure Cosmos DB – API da Tabela](tutorial-global-distribution-table.md)

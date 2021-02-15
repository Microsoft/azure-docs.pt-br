---
title: Conectar a um repositório de conhecimento com o Power BI
titleSuffix: Azure Cognitive Search
description: Conectar um repositório de conhecimento da Pesquisa Cognitiva do Azure com o Power BI para análise e exploração.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289164"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Conectar um repositório de conhecimento com o Power BI

Neste artigo, você aprenderá a conectar e a explorar um repositório de conhecimento usando o Power Query no aplicativo Power BI Desktop. Você pode começar mais rápido com modelos ou criar um painel personalizado do zero. Este breve vídeo abaixo demonstra como você pode enriquecer sua experiência com seus dados usando o Azure Pesquisa Cognitiva em combinação com o Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Siga as etapas em [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md) ou [Criar um repositório de conhecimento do Azure Cognitive Search usando REST](knowledge-store-create-rest.md) para criar o repositório de conhecimento de exemplo usado neste passo a passo. Também será necessário o nome da conta de Armazenamento do Azure que você usou para criar o repositório de conhecimento, junto com sua chave de acesso do portal do Azure.

+ [Instalar Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Modelo do Power BI de exemplo – somente para o portal do Azure

Ao criar um [repositório de conhecimento usando o portal do Azure](knowledge-store-create-portal.md), você tem a opção de baixar um [modelo do Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na segunda página do assistente **Importar dados**. Esse modelo fornece várias visualizações, como WordCloud e Navegador de Rede, para conteúdo baseado em texto. 

Clique **Obter Modelo do Power BI** na página **Adicionar habilidades cognitivas** para recuperar e baixar o modelo de sua localização do GitHub público. O assistente modifica o modelo para adequar-se à forma de seus dados, conforme capturados nas projeções do repositório de conhecimento especificadas no assistente. Por esse motivo, o modelo baixado variará sempre que você executar o assistente, presumindo entradas de dados e seleções de habilidades diferentes.

![Exemplo de Modelo do Power BI do Azure Cognitive Search](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Exemplo de modelo do Power BI")

> [!NOTE]
> Embora o modelo seja baixado enquanto o assistente está sendo disponibilizado em versão piloto, você precisará esperar o repositório de conhecimento ter sido de fato criado no armazenamento de Tabela do Azure para poder usá-lo.

## <a name="connect-with-power-bi"></a>Conectar com Power BI

1. Inicie o Power BI Desktop e clique em **Obter dados**.

1. Na janela **Obter Dados**, selecione **Azure** e, em seguida, **Armazenamento de Tabelas do Azure**.

1. Clique em **Conectar**.

1. Em **Nome da Conta ou URL**, insira o nome de sua conta de Armazenamento do Azure (a URL completa será criada para você).

1. Se solicitado, insira a chave de conta de armazenamento.

1. Selecione as tabelas que contêm os dados de avaliações de hotel criados pelas instruções anteriores. 

   + Para obter instruções do portal, os nomes de tabela são *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases* e *hotelReviewsSsPages*. 
   
   + Para obter instruções do REST, os nomes de tabela são *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases* e *hotelReviewsSentiment*.

1. Clique em **Carregar**.

1. Na faixa de opções superior, clique em **Editar Consultas** para abrir o **Editor do Power Query**.

   ![Abra o Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Abrir o Power Query")

1. Selecione *hotelReviewsSsDocument* e remova as colunas *PartitionKey*, *RowKey* e *Timestamp*. 
   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tabelas")

1. Clique no ícone com setas opostas no lado superior direito da tabela para expandir o *Conteúdo*. Quando a lista de colunas for exibida, selecione todas elas e desmarque as colunas que começam com ‘metadata’. Clique em **OK** para mostrar as colunas selecionadas.

   ![Expandir conteúdo](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir conteúdo")

1. Altere o tipo de dados das seguintes colunas clicando no ícone ABC-123 na parte superior esquerda da coluna.

   + Para *content.latitude* e *Content.longitude*, selecione **Número Decimal**.
   + Para *Content.reviews_date* e *Content.reviews_dateAdded*, selecione **Data/Hora**.

   ![Alterar tipos de dados](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Alterar tipos de dados")

1. Selecione *hotelReviewsSsPages* e repita as etapas 9 e 10 para excluir as colunas e expanda o *Conteúdo*.
1. Altere o tipo de dados de *Content.SentimentScore* para **Decimal Number**.
1. Selecione *hotelReviewsSsKeyPhrases* e repita as etapas 9 e 10 para excluir as colunas e expanda o *Conteúdo*. Não há nenhuma modificação de tipo de dados para esta tabela.

1. Na barra de comandos, clique em **Fechar e Aplicar**.

1. Clique no bloco Modelo no painel de navegação esquerdo e confirme se o Power BI mostra relações entre todas as três tabelas.

   ![Validar relações](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relações")

1. Clique duas vezes em cada relação e verifique se a **Direção do filtro cruzado** foi definida como **Ambas**.  Isso permite que seus elementos visuais sejam atualizados quando um filtro é aplicado.

1. Clique no bloco Relatório no painel de navegação à esquerda para explorar dados por meio de visualizações. Para campos de texto, tabelas e cartões são visualizações úteis. Você pode escolher os campos de cada uma das três tabelas para preencher a tabela ou o cartão. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Limpeza

Quando já estiver trabalhando na sua assinatura, analise se você ainda precisa dos recursos criados no fim de um projeto. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

Para saber como explorar esse repositório de conhecimento usando o Gerenciador de Armazenamento, confira o artigo a seguir.

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)
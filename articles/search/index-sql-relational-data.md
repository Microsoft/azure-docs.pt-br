---
title: Modelar dados relacionais do SQL para importação e indexação
titleSuffix: Azure Cognitive Search
description: Saiba como modelar dados relacionais, desnormalizados em um conjunto de resultados simples, para indexação e pesquisa de texto completo no Azure Pesquisa Cognitiva.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924513"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Como modelar dados SQL relacionais para importação e indexação no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva aceita um conjunto de linhas simples como entrada para o [pipeline de indexação](search-what-is-an-index.md). Se os dados de origem forem provenientes de tabelas unidas em um banco de dados relacional SQL Server, este artigo explicará como construir o conjunto de resultados e como modelar uma relação pai-filho em um índice de Pesquisa Cognitiva do Azure.

Como ilustração, vamos nos referir a um banco de dados de hotéis hipotéticos, com base na [demonstração](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Suponha que o banco de dados consiste em um Hotéis $ Table com 50 hotéis e uma tabela Rooms $ com salas de tipos variados, taxas e comodidades, para um total de 750 salas. Há uma relação um-para-muitos entre as tabelas. Em nossa abordagem, uma exibição fornecerá a consulta que retorna 50 linhas, uma linha por Hotel, com detalhes de sala associado inseridos em cada linha.

   ![Tabelas e exibições no banco de dados de hotéis](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelas e exibições no banco de dados de hotéis")


## <a name="the-problem-of-denormalized-data"></a>O problema dos dados desnormalizados

Um dos desafios de trabalhar com relações um-para-muitos é que as consultas padrão criadas em tabelas unidas retornarão dados desnormalizados, o que não funciona bem em um cenário de Pesquisa Cognitiva do Azure. Considere o exemplo a seguir que une Hotéis e salas.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Os resultados dessa consulta retornam todos os campos de Hotel, seguidos por todos os campos de sala, com a repetição de informações preliminares do hotel para cada valor de sala.

   ![Dados desnormalizados, dados de Hotel redundantes quando campos de sala são adicionados](media/index-sql-relational-data/denormalize-data-query.png "Dados desnormalizados, dados de Hotel redundantes quando campos de sala são adicionados")


Embora essa consulta tenha êxito na superfície (fornecendo todos os dados em um conjunto de linhas simples), ela falha ao fornecer a estrutura de documentos correta para a experiência de pesquisa esperada. Durante a indexação, o Azure Pesquisa Cognitiva criará um documento de pesquisa para cada linha ingerida. Se os documentos de pesquisa parecerem com os resultados acima, você terá percebido duplicatas-sete documentos separados para o Hotel Dome. Uma consulta em "Hotéis na Flórida" retornaria sete resultados apenas para o Hotel Dome, enviando outros hotéis relevantes profundamente nos resultados da pesquisa.

Para obter a experiência esperada de um documento por Hotel, você deve fornecer um conjunto de linhas à granularidade correta, mas com informações completas. Felizmente, você pode fazer isso facilmente adotando as técnicas neste artigo.

## <a name="define-a-query-that-returns-embedded-json"></a>Definir uma consulta que retorna JSON inserido

Para fornecer a experiência de pesquisa esperada, seu conjunto de dados deve consistir em uma linha para cada documento de pesquisa no Azure Pesquisa Cognitiva. Em nosso exemplo, queremos uma linha para cada hotel, mas também queremos que nossos usuários sejam capazes de pesquisar outros campos relacionados à sala com os quais eles se preocupam, como a taxa noturna, o tamanho e o número de ambientes, ou uma exibição da praia, todas que fazem parte de um detalhe de sala.

A solução é capturar os detalhes da sala como JSON aninhado e, em seguida, inserir a estrutura JSON em um campo em uma exibição, conforme mostrado na segunda etapa. 

1. Suponha que você tenha duas tabelas unidas, hotéis $ e quartos $, que contenham detalhes de 50 hotéis e 750 quartos, e que sejam Unidas no campo Hotelid. Individualmente, essas tabelas contêm 50 hotéis e 750 salas relacionadas.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Crie uma exibição composta de todos os campos na tabela pai ( `SELECT * from dbo.Hotels$` ), com a adição de um novo campo de *salas* que contenha a saída de uma consulta aninhada. Uma cláusula **for JSON auto** em `SELECT * from dbo.Rooms$` estrutura a saída como JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A captura de tela a seguir mostra a exibição resultante, com o campo nvarchar *Rooms* na parte inferior. O campo de *salas* existe apenas na exibição HotelRooms.

   ![Exibição de HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Exibição de HoteRooms")

1. Execute `SELECT * FROM dbo.HotelRooms` para recuperar o conjunto de linhas. Essa consulta retorna 50 linhas, uma por Hotel, com informações de sala associadas como uma coleção JSON. 

   ![Conjunto de linhas da exibição HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Conjunto de linhas da exibição HotelRooms")

Este conjunto de linhas agora está pronto para importação para o Azure Pesquisa Cognitiva.

> [!NOTE]
> Essa abordagem pressupõe que o JSON inserido esteja abaixo dos [limites máximos de tamanho de coluna de SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Use uma coleção complexa para o lado "muitos" de uma relação um-para-muitos

No lado do Pesquisa Cognitiva do Azure, crie um esquema de índice que modela a relação um-para-muitos usando JSON aninhado. O conjunto de resultados criado na seção anterior geralmente corresponde ao esquema de índice fornecido abaixo (cortamos alguns campos para fins de brevidade).

O exemplo a seguir é semelhante ao exemplo em [como modelar tipos de dados complexos](search-howto-complex-data-types.md#creating-complex-fields). A estrutura de *salas* , que tem sido o foco deste artigo, está na coleção Fields de um índice chamado *Hotéis*. Este exemplo também mostra um tipo complexo para *endereço*, que difere de *salas* em que ele é composto de um conjunto fixo de itens, em oposição ao múltiplo, o número arbitrário de itens permitidos em uma coleção.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Considerando o conjunto de resultados anterior e o esquema de índice acima, você tem todos os componentes necessários para uma operação de indexação bem-sucedida. O conjunto de dados achatados atende aos requisitos de indexação, mas preserva as informações detalhadas. No índice de Pesquisa Cognitiva do Azure, os resultados da pesquisa ficarão facilmente em entidades baseadas em Hotel, preservando, ao mesmo tempo, o contexto de salas individuais e seus atributos.

## <a name="next-steps"></a>Próximas etapas

Usando seu próprio conjunto de dados, você pode usar o [Assistente para importar dados](search-import-data-portal.md) para criar e carregar o índice. O assistente detecta a coleção JSON inserida, como a contida em *salas*, e infere um esquema de índice que inclui uma coleção de tipos complexos. 

  ![Índice inferido pelo assistente de importação de dados](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Índice inferido pelo assistente de importação de dados")

Experimente o início rápido a seguir para aprender as etapas básicas do assistente de importação de dados.

> [!div class="nextstepaction"]
> [Início rápido: criar um índice de pesquisa usando portal do Azure](search-get-started-portal.md)
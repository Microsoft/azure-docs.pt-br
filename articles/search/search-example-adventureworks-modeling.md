---
title: 'Exemplo: Modelar o banco de dados de Inventário AdventureWorks - Azure Search'
description: Saiba como modelar dados relacionais, transformando-os em um conjunto de dados nivelado para indexação e pesquisa de texto completo no Azure Search.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291870"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Exemplo: Modelar o banco de dados de Inventário AdventureWorks para Azure Search

Modelar o conteúdo do banco de dados estruturado em um índice de pesquisa eficiente raramente é um exercício simples. Agendamento e gerenciamento de alterações à parte, há o desafio de desnormalizar as linhas de origem de seu estado ingressado na tabela em entidades de pesquisa amigável. Este artigo usa os dados de exemplo do AdventureWorks, disponíveis online, para destacar experiências comuns na transição do banco de dados para pesquisa. 

## <a name="about-adventureworks"></a>Sobre o AdventureWorks

Se você tiver uma instância do SQL Server, talvez esteja familiarizado com o banco de dados de exemplo AdventureWorks. Entre as tabelas incluídas nesse banco de dados estão cinco tabelas que expõem informações sobre o produto.

+ **ProductModel**: nome
+ **Produto**: nome, cor, custo, tamanho, peso, imagem, categoria (cada linha ingressa um ProductModel específico)
+ **ProductDescription**: descrição
+ **ProductModelProductDescription**: localidade (cada linha ingressa um ProductModel a um ProductDescription específico para uma linguagem específica)
+ **ProductCategory**: nome, categoria pai

Combinar todos esses dados em um conjunto de linhas bidimensional que possa ser ingerido em um índice de pesquisa é a tarefa em questão. 

## <a name="considering-our-options"></a>Considerando as opções

A abordagem mais simples seria indexar todas as linhas da tabela Produto (ingressadas quando apropriado), já que a tabela Produto possui as informações mais específicas. No entanto, essa abordagem poderia expor o índice de pesquisa para duplicatas percebidas em um conjunto de resultados. Por exemplo, o modelo Road-650 está disponível em duas cores e seis tamanhos. Uma consulta por "bicicletas de estradas" seria, portanto, dominada por doze instâncias do mesmo modelo, diferenciadas apenas por tamanho e cor. Os outros seis modelos específicos de estradas seriam todos relegados ao espaço inferior da pesquisa: página dois.

  ![Lista de produtos](./media/search-example-adventureworks/products-list.png "Lista de produtos")
 
Observe que o modelo Road-650 tem doze opções. Linhas de entidades um para muitos são melhor representadas como campos de vários valores ou campos de valor pré-agregado no índice de pesquisa.

Resolver esse problema não é tão simples quanto mover o índice de destino para a tabela ProductModel. Isso ignoraria os dados importantes na tabela Produto que ainda deveriam ser representados nos resultados da pesquisa.

## <a name="use-a-collection-data-type"></a>Usar um tipo de dados de Coleção

A "abordagem correta" é utilizar um recurso de esquema de pesquisa que não possui um paralelo direto no modelo de banco de dados: **Collection(Edm.String)**. Um tipo de dados de Coleção é usado quando há uma lista de cadeia de caracteres individuais, em vez de uma cadeia de caracteres muito longa (única). Se houver marcas ou palavras-chave, é possível usar um tipo de dados de Coleção para esse campo.

Ao definir campos de índice de vários valores de **Collection(Edm.String)** para "cor", "tamanho" e "imagem", as informações auxiliares são retidas para facetamento e filtragem sem poluir o índice com entradas duplicadas. Da mesma forma, aplique funções de agregação aos campos do Produto numérico, indexando **minListPrice** em vez de cada produto individual **listPrice**.

Dado um índice com essas estruturas, uma busca por "mountain bikes" mostraria modelos de bicicletas discretos, preservando metadados importantes como cor, tamanho e menores preços. A captura de tela a seguir fornece uma ilustração.

  ![Exemplo de pesquisa de mountain bike](./media/search-example-adventureworks/mountain-bikes-visual.png "Exemplo de pesquisa de mountain bike")

## <a name="use-script-for-data-manipulation"></a>Usar script para manipulação de dados

Infelizmente, esse tipo de modelagem não pode ser facilmente alcançado apenas por meio de instruções SQL. Em vez disso, use um script NodeJS simples para carregar os dados e, em seguida, mapeie-os em entidades JSON de pesquisa amigável.

O mapeamento de pesquisa de banco de dados final é semelhante a este:

+ modelo (Edm.String: pesquisável, filtrável, recuperável) de "ProductModel.Name"
+ description_en (Edm.String: pesquisável) de "ProductDescription" para o modelo em que culture=’en’
+ cor (Coleção (Edm.String): pesquisável, filtrável, com facetas, recuperável): valores únicos de "Product.Color" para o modelo
+ tamanho (Collection (Edm.String): pesquisável, filtrável, com facetas, recuperável): valores exclusivos de "Product.Size" para o modelo
+ imagem (Collection (Edm.String): recuperável): valores únicos de "Product.ThumbnailPhoto" para o modelo
+ minStandardCost (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.StandardCost" para o modelo
+ minListPrice (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.ListPrice" para o modelo
+ minWeight (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.Weight" para o modelo
+ produtos (Coleção (Edm.String): pesquisável, filtrável, recuperável): valores exclusivos de "Product.Name" para o modelo

Após ingressar a tabela ProductModel com Produto e ProductDescription, use [lodash](https://lodash.com/) (ou Linq em C#) para transformar rapidamente o conjunto de resultados:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

O JSON resultante tem este aspecto:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Finalmente, aqui está a consulta SQL para retornar o conjunto de registros inicial. O módulo npm [mssql](https://www.npmjs.com/package/mssql) foi usado para carregar os dados no aplicativo NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplo: Taxonomias facetadas de vários níveis no Azure Search](search-example-adventureworks-multilevel-faceting.md)



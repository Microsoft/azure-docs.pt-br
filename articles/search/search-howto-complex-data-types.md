---
title: Como modelar tipos de dados complexos – Azure Search
description: As estruturas de dados aninhadas e hierárquicas podem ser modeladas em um índice do Azure Search usando o conjunto de linhas nivelado e o tipo de dados Coleções.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076172"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como modelar os tipos de dados complexos no Azure Search
Os conjuntos de dados externos usados para preencher um índice do Azure Search, às vezes, incluem subestruturas hierárquicas ou aninhadas que não se dividem de modo organizado em um conjunto de linhas da tabela. Os exemplos dessas estruturas podem incluir vários locais e números de telefone para um único cliente, vários tamanhos e cores para um único SKU, vários autores de um único livro e assim por diante. Em termos de modelagem, você pode ver essas estruturas referidas como *tipos de dados complexos*, *tipos de dados compostos*, *tipos de dados combinados* ou *tipos de dados de agregação*, para citar alguns.

Os tipos de dados complexos não são suportados nativamente no Azure Search, mas uma solução comprovada inclui um processo de duas etapas de nivelamento da estrutura e uso de um tipo de dados **Coleção** para reconstituir a estrutura interna. Seguir a técnica descrita neste artigo permite que o conteúdo seja pesquisado, lapidado, filtrado e classificado.

## <a name="example-of-a-complex-data-structure"></a>Exemplo de uma estrutura de dados complexos
Normalmente, os dados em questão residem como um conjunto de documentos JSON ou XML, ou como itens em um repositório NoSQL, como o Azure Cosmos DB. Estruturalmente, o desafio é ter vários itens-filho que precisam ser pesquisados e filtrados.  Como ponto de partida para ilustrar a solução alternativa, veja o seguinte documento JSON que lista um conjunto de contatos como um exemplo:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Embora os campos denominados 'id', 'name' e 'empresa' possam ser mapeados facilmente um a um como campos em um índice do Azure Search, o campo 'locais' contém uma matriz de locais, com um conjunto de IDs de local, bem como descrições do local. Considerando que o Azure Search não tem um tipo de dados que oferece suporte, precisamos de uma maneira diferente de modelar isso no Azure Search. 

> [!NOTE]
> Essa técnica também é descrita por Kirk Evans em uma postagem de blog [Indexando o Azure Cosmos DB com o Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que mostra uma técnica denominada "nivelar os dados", por meio da qual você teria os campos chamados `locationsID` e `locationsDescription` que são [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Nivelar a matriz em campos individuais
Para criar um índice do Azure Search que aceita esse conjunto de dados, crie campos individuais para a subestrutura aninhada: `locationsID` e `locationsDescription` com um tipo de dados de [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres). Nesses campos, você deve indexar os valores '1' e '2' no campo `locationsID` para John Smith e os valores '3' e '4' no campo `locationsID` para Jen Campbell.  

Os dados no Azure Search ficariam assim: 

![dados de exemplo, 2 linhas](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Adicionar um campo de coleção à definição de índice
No esquema do índice, as definições do campo podem ser semelhantes a este exemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validar os comportamentos da pesquisa e, opcionalmente, estender o índice
Supondo que você criou o índice e carregou os dados, agora você pode testar a solução para verificar a execução da consulta de pesquisa no conjunto de dados. Cada campo da **coleção** deve ser **pesquisável**, **filtrável** e **lapidável**. Você deve ser capaz de executar consultas como:

* Localize todas as pessoas que trabalham na 'Matriz Adventureworks'.
* Obtenha uma contagem do número de pessoas que trabalham em um ‘Escritório Central’.  
* Das pessoas que trabalham em um ‘Escritório Central', mostre em quais outros escritórios elas trabalham, junto com uma contagem de pessoas em cada local.  

Onde essa técnica falha é quando você precisa fazer uma pesquisa que combina a identificação do local, bem como a descrição do local. Por exemplo: 

* Localize todas as pessoas onde elas têm um Escritório Central E uma Identificação de local 4.  

Se você se lembra, o conteúdo original era assim:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

No entanto, agora que dividimos os dados em campos separados, não temos nenhuma maneira de saber se o Escritório Central para Jen Campbell relaciona-se a `locationsID 3` ou `locationsID 4`.  

Para lidar com isso, defina outro campo no índice que combina todos os dados em uma única coleção.  Para nosso exemplo, chamaremos esse campo de `locationsCombined` e iremos separar o conteúdo com `||`, embora seja possível escolher qualquer separador que você acha que seria um conjunto exclusivo de caracteres para seu conteúdo. Por exemplo:  

![dados de exemplo, 2 linhas com separador](./media/search-howto-complex-data-types/sample-data-2.png)

Usando esse campo `locationsCombined` , agora podemos aceitar ainda mais consultas, como:

* Mostre uma contagem de pessoas que trabalham em um 'Escritório Central' com uma Id de local '4'.  
* Procure as pessoas que trabalham em um ‘Escritório Central' com uma Id de local '4'. 

## <a name="limitations"></a>Limitações
Essa técnica é útil para vários cenários, mas ela não é aplicável em todos os casos.  Por exemplo: 

1. Se você não tem um conjunto estático de campos em seu tipo de dados complexos e não foi possível mapear todos os tipos possíveis para um único campo. 
2. Atualizar os objetos aninhados requer um trabalho extra para determinar exatamente o que precisa ser atualizado no índice do Azure Search

## <a name="sample-code"></a>Exemplo de código
Você pode ver um exemplo sobre como indexar um conjunto de dados JSON complexo no Azure Search e executar várias consultas nesse conjunto de dados no [repositório GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Próxima etapa
[Escolha o suporte nativo para os tipos de dados complexos](https://feedback.azure.com/forums/263029-azure-search) na página UserVoice do Azure Search e forneça qualquer entrada adicional que você gostaria de considerar em relação à implementação do recurso. Você também pode me contatar diretamente no Twitter em @liamca.


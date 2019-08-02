---
title: Mapeamentos de campo para indexação automatizada usando indexadores – Azure Search
description: Configure mapeamentos de campo de indexador do Azure Search para esclarecer as diferenças em nomes de campo e representações de dados.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147802"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapeamentos de campo e transformações usando indexadores do Azure Search

Ao usar indexadores do Azure Search, você percebe que os dados de entrada não correspondem ao esquema de seu índice de destino. Nesses casos, você pode usar **mapeamentos de campo** de remodelar seus dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* Fonte de dados tem um campo chamado `_id`, mas o Azure Search não permite nomes de campo que começam com um sublinhado. Um mapeamento de campo permite que você efetivamente renomear um campo.
* Você deseja preencher vários campos no índice dos mesmos dados de origem de dados. Por exemplo, você talvez queira aplicar diferentes analisadores a esses campos.
* Você deseja preencher um campo de índice com os dados de mais de uma fonte de dados e as fontes de dados cada usarem nomes de campo diferente.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.

> [!NOTE]
> O recurso de mapeamento de campo de indexadores do Azure Search fornece uma maneira simples para mapear os campos de dados para campos de índice, com algumas opções para a conversão de dados. Dados mais complexos podem exigir pré-processamento a para reformatá-la em um formulário que é fácil de índice.
>
> Microsoft Azure Data Factory é uma solução poderosa baseada em nuvem para importar e transformar dados. Você também pode escrever código para transformar os dados de origem antes de indexação. Para obter exemplos de código, consulte [modelar dados relacionais](search-example-adventureworks-modeling.md) e [facetas de vários níveis de modelo](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo é composto por três partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Essa propriedade é obrigatória.
2. Um `targetFieldName`opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction`opcional, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Mapeamentos de campo são adicionados à `fieldMappings` matriz da definição do indexador.

## <a name="map-fields-using-the-rest-api"></a>Mapear campos usando a API REST

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) solicitação de API. Você pode gerenciar os mapeamentos de campo de um indexador existente usando o [atualizar indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) solicitação de API.

Por exemplo, aqui está como mapear um campo de origem para um campo de destino com um nome diferente:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como um campo, copiando o mesmo campo de origem para dois campos de índice diferente de "bifurcação":

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> O Azure Search usa a comparação que não diferencia maiúsculas de minúsculas para resolver os nomes de campo e a função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas corretas), mas isso significa que a fonte de dados ou o índice não pode ter campos que diferem somente maiúsculas e minúsculas.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapear campos usando o SDK do .NET

Definir mapeamentos de campo no SDK do .NET usando o [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) classe, que tem as propriedades `SourceFieldName` e `TargetFieldName`e um opcional `MappingFunction` referência.

Você pode especificar mapeamentos de campo ao construir o indexador ou posterior, definindo diretamente o `Indexer.FieldMappings` propriedade.

O seguinte C# exemplo define os mapeamentos de campo ao construir um indexador.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Uma função de mapeamento de campo transforma o conteúdo de um campo antes de ser armazenado no índice. Atualmente, há suporte para as seguintes funções de mapeamento:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - pesquisa de chave de documento

Caracteres de apenas a URL segura podem aparecer em uma chave de documento do Azure Search (porque os clientes devem ser capazes de resolver o documento usando o [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se o campo de origem para a sua chave contém caracteres não seguros URL, você pode usar o `base64Encode` função convertê-lo no momento da indexação.

Quando você recupera a chave codificada no tempo de pesquisa, você pode usar o `base64Decode` de função para obter o valor da chave original e usá-la para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Se você não incluir uma propriedade de parâmetros para a função de mapeamento, o padrão será o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Search dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros durante a codificação e decodificação o mesmo campo. Para obter mais informações, consulte [opções de codificação de base64](#base64details) para decidir quais parâmetros a serem usados.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a decodificação de Base64 da cadeia de caracteres de entrada. A entrada é considerada como uma *URL segura* cadeia de caracteres codificada em Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo - decodificar URLs ou os metadados de blob

Sua fonte de dados pode conter cadeias de caracteres codificada em Base64, como cadeias de caracteres de metadados de blob ou URLs da web, o que você deseja tornar pesquisáveis como texto sem formatação. Você pode usar o `base64Decode` função para transformar os dados codificados de volta em cadeias de caracteres regulares ao popular o índice de pesquisa.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Se você não incluir uma propriedade de parâmetros, o padrão será o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Search dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros durante a codificação e decodificação o mesmo campo. Para obter mais detalhes, consulte [opções de codificação de base64](#base64details) para decidir quais parâmetros a serem usados.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opções de codificação de Base64

O Azure Search dá suporte a duas codificações Base64 diferentes: **Token de URL HttpServerUtility**, e **codificação Base64 de URL segura sem preenchimento**. Uma cadeia de caracteres codificada em base64 durante a indexação deve ser decodificada posteriormente com as mesmas opções de codifica, caso contrário, o resultado não coincidir com o original.

Se o `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` parâmetros de codificação e decodificação respectivamente são definidos como `true`, em seguida, `base64Encode` se comporta como [HttpServerUtility. Urltokenencode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` se comporta como [Urltokendecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se você não estiver usando o .NET Framework completo (ou seja, você está usando .NET Core ou outra estrutura) para produzir os valores de chave para emular o comportamento de pesquisa do Azure, em seguida, você deve definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` para `false`. Dependendo da biblioteca que você use, base64 codificação e decodificação de funções podem ser diferentes daquelas usadas pelo Azure Search.

A tabela a seguir compara codificações diferentes de base64 da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para suas funções de base64, aplique sua função de codificação de biblioteca na cadeia de caracteres `00>00?00` e compare a saída com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificação Base64 | Processamento adicional após a codificação da biblioteca | Processamento adicional antes da decodificação da biblioteca |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Use caracteres com URL segura e remova o preenchimento | Use caracteres base64 padrão e adicione o preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Use caracteres com URL segura | Use caracteres base64 padrão |
| Base64 com preenchimento e URL segura | `MDA-MDA_MDA=` | Remover preenchimento | Adicionar preenchimento |
| Base64 sem preenchimento e URL segura | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função extractTokenAtPosition

Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Essa função usa os seguintes parâmetros:

* `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição baseada em zero do número inteiro do token para escolher depois que a cadeia de caracteres de entrada for dividida.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(espaço) e o `position` for 0, o resultado será `Jane`; se o `position` for 1, o resultado será `Doe`. Se a posição se refere a um token que não existe, um erro será retornado.

#### <a name="example---extract-a-name"></a>Exemplo – extrair um nome

Sua fonte de dados contém um campo `PersonName` e você deseja indexá-la como dois campos `FirstName` e `LastName` separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>função jsonArrayToStringCollection

Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher um campo `Collection(Edm.String)` no índice.

Por exemplo, se a cadeia de caracteres de entrada for `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white` e `blue`. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro retornará.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo - popular a coleção de dados relacionais

Banco de dados SQL do Azure não tem um tipo de dados interna que é mapeado naturalmente para `Collection(Edm.String)` campos no Azure Search. Para preencher os campos da coleção de cadeia de caracteres, você pode pré-processar os dados de origem como uma matriz de cadeia de caracteres JSON e, em seguida, usar o `jsonArrayToStringCollection` função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Para obter um exemplo detalhado que transforma dados relacionais em campos de coleção do índice, consulte [modelar dados relacionais](search-example-adventureworks-modeling.md).

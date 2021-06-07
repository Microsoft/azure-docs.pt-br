---
title: Mapeamentos de campo em indexadores
titleSuffix: Azure Cognitive Search
description: Configure mapeamentos de campo em um indexador para considerar as diferenças em nomes de campo e representações de dados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430438"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapeamentos de campo e transformações usando indexadores do Azure Pesquisa Cognitiva

![Estágios do indexador](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "estágios do indexador")

Ao usar os indexadores do Azure Pesquisa Cognitiva, às vezes, você descobre que os dados de entrada não correspondem exatamente ao esquema do índice de destino. Nesses casos, você pode usar **mapeamentos de campo** para remodelar os dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* Sua fonte de dados tem um campo chamado `_id` , mas o Azure pesquisa cognitiva não permite nomes de campo que começam com um sublinhado. Um mapeamento de campo permite que você renomeie efetivamente um campo.
* Você deseja preencher vários campos no índice a partir dos mesmos dados de fonte de dados. Por exemplo, talvez você queira aplicar analisadores diferentes a esses campos.
* Você deseja preencher um campo de índice com dados de mais de uma fonte de dados e as fontes de dados usam nomes de campos diferentes.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.

> [!NOTE]
> Os mapeamentos de campo nos indexadores são uma maneira simples de mapear campos de dados para campos de índice, com alguma capacidade de conversão de dados leves. Dados mais complexos podem exigir pré-processamento para reformatá-lo em um formulário que conduza à indexação. Uma opção que você pode considerar é [Azure data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo é composto por três partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Esta propriedade é necessária.
2. Um `targetFieldName`opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction`opcional, que pode transformar seus dados usando uma das várias funções predefinidas. Isso pode ser aplicado em ambos os mapeamentos de campo de entrada e saída. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de campo são adicionados à `fieldMappings` matriz da definição do indexador.

> [!NOTE]
> Se nenhum mapeamento de campo for adicionado, os indexadores presumirão que os campos de fonte de dados devem ser mapeados para campos de índice com o mesmo nome. A adição de um mapeamento de campo remove esses mapeamentos de campo padrão para o campo de origem e destino. Alguns indexadores, como o [indexador de armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md), adicionam mapeamentos de campo padrão para o campo chave de índice.

## <a name="map-fields-using-rest"></a>Mapear campos usando REST

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a solicitação criar API do [indexador](/rest/api/searchservice/create-Indexer) . Você pode gerenciar os mapeamentos de campo de um indexador existente usando a solicitação atualizar API do [indexador](/rest/api/searchservice/update-indexer) .

Por exemplo, veja como mapear um campo de origem para um campo de destino com um nome diferente:

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

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> O Azure Pesquisa Cognitiva usa a comparação que não diferencia maiúsculas de minúsculas para resolver os nomes de campo e função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas corretas), mas isso significa que a fonte de dados ou o índice não pode ter campos que diferem somente maiúsculas e minúsculas.  
>

## <a name="map-fields-using-net"></a>Mapear campos usando o .NET

Você define mapeamentos de campo no SDK do .NET usando a classe [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) , que tem as propriedades `SourceFieldName` e e `TargetFieldName` uma `MappingFunction` referência opcional.

Você pode especificar mapeamentos de campo ao construir o indexador, ou mais tarde, definindo diretamente a `Indexer.FieldMappings` propriedade.

O exemplo C# a seguir define os mapeamentos de campo ao construir um indexador.

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

Uma função de mapeamento de campos transforma o conteúdo de um campo antes que ele seja armazenado no índice. Atualmente, há suporte para as seguintes funções de mapeamento:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

Somente caracteres de URL segura podem aparecer em uma chave do documento de Pesquisa Cognitiva do Azure (porque os clientes devem ser capazes de resolver o documento usando a [API de pesquisa](/rest/api/searchservice/lookup-document) ). Se o campo de origem da chave contiver caracteres não seguros de URL, você poderá usar a `base64Encode` função para convertê-la no momento da indexação. No entanto, uma chave de documento (antes e depois da conversão) não pode ter mais de 1.024 caracteres.

Quando você recupera a chave codificada no momento da pesquisa, pode usar a `base64Decode` função para obter o valor de chave original e usá-lo para recuperar o documento de origem.

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

#### <a name="example---preserve-original-values"></a>Exemplo – preservar valores originais

O [indexador de armazenamento de blob](search-howto-indexing-azure-blob-storage.md) adiciona automaticamente um mapeamento de campo de `metadata_storage_path` , o URI do blob, ao campo chave de índice se nenhum mapeamento de campo for especificado. Esse valor é codificado em base64, portanto, é seguro usá-lo como uma chave de documento do Azure Pesquisa Cognitiva. O exemplo a seguir mostra como mapear simultaneamente uma versão codificada com base em Base64 de *URL* de `metadata_storage_path` para um `index_key` campo e preservar o valor original em um `metadata_storage_path` campo:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Se você não incluir uma propriedade Parameters para sua função de mapeamento, ela usa como padrão o valor `{"useHttpServerUtilityUrlTokenEncode" : true}` .

O Azure Pesquisa Cognitiva dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais informações, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a decodificação de Base64 da cadeia de caracteres de entrada. Pressupõe-se que a entrada seja uma cadeia de caracteres codificada em base64 *segura para URL* .

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo – decodificar metadados ou URLs de BLOB

Os dados de origem podem conter cadeias de caracteres codificadas em base64, como cadeias de caracteres de metadados de BLOB ou URLs da Web, que você deseja tornar pesquisáveis como texto sem formatação. Você pode usar a `base64Decode` função para transformar os dados codificados de volta em cadeias de caracteres regulares ao preencher o índice de pesquisa.

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

Se você não incluir uma propriedade Parameters, o valor padrão será `{"useHttpServerUtilityUrlTokenEncode" : true}` .

O Azure Pesquisa Cognitiva dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais detalhes, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação Base64

O Azure Pesquisa Cognitiva dá suporte à codificação Base64 de URL segura e à codificação Base64 normal. Uma cadeia de caracteres codificada em base64 durante a indexação deve ser decodificada posteriormente com as mesmas opções de codificação, caso contrário, o resultado não corresponderá ao original.

Se os `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` parâmetros ou para codificação e decodificação respectivamente forem definidos como `true` e, em seguida, se `base64Encode` comparecerem com [HttpServerUtility. UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) e `base64Decode` se comparecerem com [HttpServerUtility. UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Se `base64Encode` for usado para produzir valores de chave, `useHttpServerUtilityUrlTokenEncode` deverá ser definido como true. Somente a codificação Base64 segura para URL pode ser usada para valores de chave. Consulte [regras de nomenclatura &#40;pesquisa cognitiva do Azure&#41;](/rest/api/searchservice/naming-rules) para obter o conjunto completo de restrições em caracteres em valores de chave.

As bibliotecas do .NET no Azure Pesquisa Cognitiva assumem o .NET Framework completo, que fornece codificação interna. As `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` Opções e aproveitam essa funcionalidade interna. Se você estiver usando o .NET Core ou outra estrutura, é recomendável definir essas opções como `false` e chamar as funções de codificação e decodificação da estrutura diretamente.

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

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo – preencher a coleção de dados relacionais

O banco de dados SQL do Azure não tem um tipo de dado interno que é mapeado naturalmente para `Collection(Edm.String)` campos no Azure pesquisa cognitiva. Para preencher os campos de coleção de cadeia de caracteres, você pode pré-processar seus dados de origem como uma matriz de cadeia de caracteres JSON e, em seguida, usar a `jsonArrayToStringCollection` função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>função urlEncode

Essa função pode ser usada para codificar uma cadeia de caracteres de forma que ela seja "segura para a URL". Quando usado com uma cadeia de caracteres que contém caractere que não é permitido em uma URL, essa função converterá os caracteres "não seguros" em equivalentes de entidade de caracteres. Essa função usa o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

`urlEncode` a função pode ser usada como uma alternativa à `base64Encode` função, se apenas caracteres não seguros de URL forem convertidos, mantendo outros caracteres como estão.

Digamos que a cadeia de caracteres de entrada seja `<hello>` -então, o campo de destino do tipo `(Edm.String)` será preenchido com o valor `%3chello%3e`

Quando você recupera a chave codificada no momento da pesquisa, pode usar a `urlDecode` função para obter o valor de chave original e usá-lo para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>função urlDecode

 Essa função converte uma cadeia de caracteres codificada em URL em uma cadeia de caracteres decodificada usando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo – decodificar metadados de BLOB

 Alguns clientes de armazenamento do Azure URL automaticamente codificam metadados de BLOB se contiverem caracteres não ASCII. No entanto, se você quiser tornar esses metadados pesquisáveis (como texto sem formatação), poderá usar a `urlDecode` função para transformar os dados codificados em cadeias de caracteres regulares ao preencher o índice de pesquisa.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>função fixedLengthEncode
 
 Essa função converte uma cadeia de caracteres de qualquer tamanho em uma cadeia de caracteres de comprimento fixo.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exemplo – mapear chaves de documento que são muito longas
 
Quando os erros enfrentados reclamando sobre a chave do documento têm mais de 1024 caracteres, essa função pode ser aplicada para reduzir o tamanho da chave do documento.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
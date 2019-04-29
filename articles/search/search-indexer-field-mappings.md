---
title: Mapeamentos de campo para indexação automatizada usando indexadores – Azure Search
description: Configure mapeamentos de campo de indexador do Azure Search para esclarecer as diferenças em nomes de campo e representações de dados.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9bb8a168f54658ea14b174d19a1795e575646304
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749335"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapeamentos de campo em indexadores do Azure Search
Ao usar indexadores de Azure Search, ocasionalmente você pode se encontrar em situações em que seus dados de entrada não correspondem ao esquema de seu índice de destino. Nesses casos, você pode usar **mapeamentos de campo** para transformar seus dados na forma desejada.

Algumas situações em que os mapeamentos de campo são úteis:

* A fonte de dados tem um campo `_id`, mas o Azure Search não permite nomes de campo iniciados com um sublinhado. Um mapeamento de campo permite um campo "rename".
* Você deseja preencher vários campos no índice com os mesmos dados da fonte de dados, por exemplo, porque você deseja aplicar diferentes analisadores a esses campos. Mapeamentos de campo permitem a “bifurcação” de um campo de fonte de dados.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.   

## <a name="setting-up-field-mappings"></a>Configurar mapeamentos de campo
Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a API [Criar Indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx) . Você pode gerenciar mapeamentos de campo ao criar um novo indexador usando a API [Atualizar Indexador](https://msdn.microsoft.com/library/azure/dn946892.aspx) .

Um mapeamento de campo é composto por três partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Essa propriedade é obrigatória.
2. Um `targetFieldName`opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction`opcional, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Mapeamentos de campos são adicionados à matriz `fieldMappings` na definição do indexador.

Por exemplo, veja como você pode acomodar as diferenças nos nomes de campo:

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

Um indexador pode ter vários mapeamentos de campo. Por exemplo, aqui está como você pode "bifurcar" um campo:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo
Essas funções atualmente têm suporte:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Caso de uso de exemplo - pesquisa de chave de documento
Somente caracteres de URL segura podem aparecer em uma chave de documento do Azure Search (porque os clientes devem ser capazes de resolver o documento usando a [API de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document), por exemplo). Se seus dados contiverem caracteres de URL não seguros e você desejar usá-los para preencher um campo de chave em seu índice de pesquisa, use essa função. Após a codificação da chave, você pode usar a decodificação base64 para recuperar o valor original. Para obter detalhes, consulte a seção [Codificação e decodificação base64](#base64details).

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Caso de uso de exemplo - recuperar chave original
Você tem um indexador de blob que indexa os com os metadados de caminho de blob como a chave de documento. Depois de recuperar a chave de documento codificada, convém decodificar o caminho e baixar o blob.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Se você não precisar procurar documentos por chaves, e também não precisar decodificar o conteúdo codificado, basta deixar `parameters` para a função de mapeamento, que assume `useHttpServerUtilityUrlTokenEncode` como padrão para `true`. Caso contrário, consulte a seção [Detalhes de base64](#base64details) para decidir quais configurações usar.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Executa a decodificação de Base64 da cadeia de caracteres de entrada. A entrada é considerada uma cadeia de caracteres de codificação Base64 de *URL segura* .

### <a name="sample-use-case"></a>Casos de uso de exemplo
Valores de metadados personalizados de blob devem ser codificados em ASCII. Você pode usar a codificação Base64 para representar cadeias de caracteres UTF-8 arbitrárias em metadados personalizados do blob. No entanto, para que a pesquisa seja significativa, você pode usar essa função para transformar os dados codificados novamente em cadeias de caracteres "normais" ao popular o índice de pesquisa.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Se você não especificar qualquer `parameters`, o valor padrão de `useHttpServerUtilityUrlTokenDecode` será `true`. Consulte a seção [Detalhes de base64](#base64details) para decidir quais configurações usar.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Detalhes da codificação e decodificação base64
O Azure Search dá suporte a duas codificações base64: Token de URL de HttpServerUtility e codificação de Base 64 de URL segura sem preenchimento. Você precisa usar a mesma codificação que as funções de mapeamento se quiser codificar uma chave de documento para pesquisa, codificar um valor a ser decodificado pelo indexador ou decodificar um campo codificado pelo indexador.

Se os parâmetros `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` de codificação e decodificação forem respectivamente definidos como `true`, `base64Encode` se comportará como [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx), e `base64Decode`, como [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se não estiver usando o .NET Framework completo (ou seja, estiver usando .NET Core ou em outro ambiente de programação) para produzir os valores de chave para emular o comportamento de pesquisa do Azure Search, você deverá definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` como `false`. Dependendo da biblioteca usada, as funções do utilitário de codificação e decodificação base64 podem ser diferentes do Azure Search.

A tabela a seguir compara codificações diferentes de base64 da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para suas funções de base64, aplique sua função de codificação de biblioteca na cadeia de caracteres `00>00?00` e compare a saída com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificação Base64 | Processamento adicional após a codificação da biblioteca | Processamento adicional antes da decodificação da biblioteca |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Use caracteres com URL segura e remova o preenchimento | Use caracteres base64 padrão e adicione o preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Use caracteres com URL segura | Use caracteres base64 padrão |
| Base64 com preenchimento e URL segura | `MDA-MDA_MDA=` | Remover preenchimento | Adicionar preenchimento |
| Base64 sem preenchimento e URL segura | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(espaço) e o `position` for 0, o resultado será `Jane`; se o `position` for 1, o resultado será `Doe`. Se a posição se refere a um token que não existe, um erro será retornado.

### <a name="sample-use-case"></a>Casos de uso de exemplo
Sua fonte de dados contém um campo `PersonName` e você deseja indexá-la como dois campos `FirstName` e `LastName` separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

### <a name="parameters"></a>Parâmetros
* `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição baseada em zero do número inteiro do token para escolher depois que a cadeia de caracteres de entrada for dividida.    

### <a name="example"></a>Exemplo
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher um campo `Collection(Edm.String)` no índice.

Por exemplo, se a cadeia de caracteres de entrada for `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white` e `blue`. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro retornará.

### <a name="sample-use-case"></a>Casos de uso de exemplo
O banco de dados SQL do Azure não tem um tipo de dados interno que é mapeado naturalmente para campos `Collection(Edm.String)` no Azure Search. Para preencher os campos da coleção de cadeia de caracteres, formate seus dados de origem como uma matriz de cadeia de caracteres JSON e usar essa função.

### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar o Azure Search
Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

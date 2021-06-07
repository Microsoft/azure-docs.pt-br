---
title: Habilidade de pesquisa cognitiva de pesquisa de entidade personalizada
titleSuffix: Azure Cognitive Search
description: Extraia entidades personalizadas diferentes do texto em um pipeline de pesquisa cognitiva do Azure Pesquisa Cognitiva. Essa habilidade está atualmente em versão prévia pública.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97844948"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Habilidade cognitiva de pesquisa de entidade personalizada (versão prévia)

> [!IMPORTANT] 
> Essa habilidade está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). No momento, não há suporte para Portal ou SDK do .NET.

A habilidade de **pesquisa de entidade personalizada** procura texto de uma lista personalizada definida pelo usuário de palavras e frases. Usando essa lista, ela rotula todos os documentos com entidades correspondentes. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências semelhantes, mas não exatas.  

Essa habilidade não está associada a uma API de serviços cognitivas e pode ser usada gratuitamente durante o período de versão prévia. No entanto, você ainda deve [anexar um recurso de serviços cognitivas](./cognitive-search-attach-cognitive-services.md)para substituir o limite de enriquecimento diário. O limite diário se aplica a acesso gratuito aos serviços cognitivas quando acessados por meio do Azure Pesquisa Cognitiva.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Limites de dados
+ O tamanho máximo de registro de entrada com suporte é de 256 MB. Se você precisar dividir seus dados antes de enviá-los para a habilidade de pesquisa de entidade personalizada, considere usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).
+ A tabela de definição de entidades máxima com suporte é 10 MB se for fornecida usando o parâmetro *entitiesDefinitionUri* . 
+ Se as entidades forem definidas em linha, usando o parâmetro *inlineEntitiesDefinition* , o tamanho máximo com suporte será 10 KB.

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Caminho para um arquivo JSON ou CSV que contém todo o texto de destino para correspondência. Esta definição de entidade é lida no início de uma execução de indexador; as atualizações para esse arquivo mid-Run não serão realizadas até as execuções subsequentes. Essa configuração deve ser acessível via HTTPS. Consulte formato de [definição de entidade personalizada](#custom-entity-definition-format) "abaixo para obter o esquema CSV ou JSON esperado.|
|`inlineEntitiesDefinition` | Definições de entidade JSON embutida. Esse parâmetro substitui o parâmetro entitiesDefinitionUri, se presente. No máximo 10 KB de configuração podem ser fornecidos embutidos. Consulte a [definição de entidade personalizada](#custom-entity-definition-format) abaixo para obter o esquema JSON esperado. |
|`defaultLanguageCode` |    Adicional Código de idioma do texto de entrada usado para indexar e delinear o texto de entrada. Há suporte para os seguintes idiomas: `da, de, en, es, fi, fr, it, ko, pt` . O padrão é inglês ( `en` ). Se você passar um formato languagecode-countrycode, somente a parte languagecode do formato é usada.  |
|`globalDefaultCaseSensitive` | Adicional Valor padrão que diferencia maiúsculas de minúsculas para a habilidade. Se `defaultCaseSensitive` o valor de uma entidade não for especificado, esse valor se tornará o `defaultCaseSensitive` valor dessa entidade. |
|`globalDefaultAccentSensitive` | Adicional Valor padrão sensível a acentos para a habilidade. Se `defaultAccentSensitive` o valor de uma entidade não for especificado, esse valor se tornará o `defaultAccentSensitive` valor dessa entidade. |
|`globalDefaultFuzzyEditDistance` | Adicional Valor de distância de edição difusa padrão para a habilidade. Se `defaultFuzzyEditDistance` o valor de uma entidade não for especificado, esse valor se tornará o `defaultFuzzyEditDistance` valor dessa entidade. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| `text`          | O texto para analisar.          |
| `languageCode`    | Opcional. O padrão é `"en"`.  |


## <a name="skill-outputs"></a>Saídas de habilidades


| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| `entities` | Uma matriz de objetos que contém informações sobre as correspondências que foram encontradas e os metadados relacionados. Cada uma das entidades identificadas pode conter os seguintes campos:  <ul> <li> *nome*: a entidade de nível superior identificada. A entidade representa o formulário "normalizado". </li> <li> *ID*: um identificador exclusivo para a entidade, conforme definido pelo usuário no "formato de definição de entidade personalizada".</li> <li> *Descrição*: Descrição da entidade conforme definida pelo usuário no "formato de definição de entidade personalizada". </li> <li> *tipo:* Tipo de entidade, conforme definido pelo usuário no "formato de definição de entidade personalizada".</li> <li> *subtipo:* Subtipo de entidade, conforme definido pelo usuário no "formato de definição de entidade personalizada".</li>  <li> *corresponde*: coleção que descreve cada uma das correspondências para essa entidade no texto de origem. Cada correspondência terá os seguintes membros: </li> <ul> <li> *texto*: a correspondência de texto bruto do documento de origem. </li> <li> *offset*: o local onde a correspondência foi encontrada no texto. </li> <li> *comprimento*: o comprimento do texto correspondente. </li> <li> *matchDistance*: o número de caracteres diferente dessa correspondência era do nome ou alias da entidade original.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato de definição de entidade personalizada

Há 3 maneiras diferentes de fornecer a lista de entidades personalizadas para a habilidade de pesquisa de entidade personalizada. Você pode fornecer a lista em um. Arquivo CSV, a. Arquivo JSON ou como uma definição embutida como parte da definição de habilidade.  

Se o arquivo de definição for um. CSV ou. Arquivo JSON, o caminho do arquivo precisa ser fornecido como parte do parâmetro *entitiesDefinitionUri* . Nesse caso, o arquivo é baixado uma vez no início de cada execução do indexador. O arquivo deve estar acessível contanto que o indexador tenha a finalidade de ser executado. Além disso, o arquivo deve ser codificado em UTF-8.

Se a definição for fornecida em linha, ela deverá ser fornecida como embutida como o conteúdo do parâmetro de habilidade *inlineEntitiesDefinition* . 

### <a name="csv-format"></a>Formato CSV

Você pode fornecer a definição das entidades personalizadas a serem procuradas em um arquivo de valor Comma-Separated (CSV) fornecendo o caminho para o arquivo e definindo-o no parâmetro de habilidade *entitiesDefinitionUri*  . O caminho deve estar em um local HTTPS. O arquivo de definição pode ter até 10 MB de tamanho.

O formato CSV é simples. Cada linha representa uma entidade exclusiva, como mostrado abaixo:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Nesse caso, há três entidades que podem ser retornadas conforme as entidades encontradas (Bill Gates, Satya Nadella, Microsoft), mas elas serão identificadas se qualquer um dos termos na linha (aliases) for correspondido no texto. Por exemplo, se a cadeia de caracteres "William H. Gates" for encontrada em um documento, uma correspondência para a entidade "Bill Gates" será retornada.

### <a name="json-format"></a>Formato JSON

Você também pode fornecer a definição das entidades personalizadas a serem procuradas em um arquivo JSON. O formato JSON proporciona um pouco mais de flexibilidade, pois permite que você defina regras de correspondência por termo. Por exemplo, você pode especificar a distância de correspondência difusa (distância Damerau-Levenshtein) para cada termo ou se a correspondência deve diferenciar maiúsculas de minúsculas ou não. 

 Assim como ocorre com arquivos CSV, você precisa fornecer o caminho para o arquivo JSON e defini-lo no parâmetro de habilidade *entitiesDefinitionUri* . O caminho deve estar em um local HTTPS. O arquivo de definição pode ter até 10 MB de tamanho.

A definição da lista de entidades personalizadas JSON mais básicas pode ser uma lista de entidades para correspondência:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Um exemplo mais complexo de uma definição de JSON pode, opcionalmente, fornecer a ID, a descrição, o tipo e o subtipo de cada entidade, bem como outros *aliases*. Se um termo de alias for correspondido, a entidade também será retornada:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

As tabelas a seguir descrevem mais detalhadamente os diferentes parâmetros de configuração que você pode definir ao definir as entidades para correspondência:

|  Nome do campo  |        Descrição  |
|--------------|----------------------|
| `name` | O descritor de entidade de nível superior. As correspondências na saída da habilidade serão agrupadas por esse nome e deverão representar a forma "normalizada" do texto que está sendo encontrado.  |
| `description`  | Adicional Esse campo pode ser usado como uma passagem para metadados personalizados sobre os textos correspondentes. O valor desse campo será exibido com cada correspondência de sua entidade na saída da habilidade. |
| `type` | Adicional Esse campo pode ser usado como uma passagem para metadados personalizados sobre os textos correspondentes. O valor desse campo será exibido com cada correspondência de sua entidade na saída da habilidade. |
| `subtype` | Adicional Esse campo pode ser usado como uma passagem para metadados personalizados sobre os textos correspondentes. O valor desse campo será exibido com cada correspondência de sua entidade na saída da habilidade. |
| `id` | Adicional Esse campo pode ser usado como uma passagem para metadados personalizados sobre os textos correspondentes. O valor desse campo será exibido com cada correspondência de sua entidade na saída da habilidade. |
| `caseSensitive` | Adicional O padrão é false. Valor booliano que indica se as comparações com o nome da entidade devem ser sensíveis à capitalização de caracteres. As correspondências que não diferenciam maiúsculas e minúsculas de "Microsoft" podem ser: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Adicional O padrão é false. Valor booliano indicando se letras acentuadas e não acentuadas, como ' T' e ' e ', devem ser idênticas. |
| `fuzzyEditDistance` | Adicional O padrão é 0. Valor máximo de 5. Denota o número aceitável de caracteres divergentes que ainda constituem uma correspondência com o nome da entidade. A menor possibilidade de fuzzização possível para qualquer correspondência fornecida é retornada.  Por exemplo, se a distância de edição for definida como 3, "Windows 10" ainda corresponderia a "Windows", "Windows10" e "Windows 7". <br/> Quando a sensibilidade de maiúsculas e minúsculas é definida como false, as diferenças de maiúsculas e minúsculas não contam para a tolerância de fuzzing, mas sim. |
| `defaultCaseSensitive` | Adicional Altera o valor de sensibilidade do caso padrão para esta entidade. Ele pode ser usado para alterar o valor padrão de todos os valores de aliases caseSensitive. |
| `defaultAccentSensitive` | Adicional Altera o valor padrão de sensibilidade a acentos para esta entidade. Ele pode ser usado para alterar o valor padrão de todos os aliases accentSensitive valores.|
| `defaultFuzzyEditDistance` | Adicional Altera o valor de distância de edição difusa padrão para esta entidade. Ele pode ser usado para alterar o valor padrão de todos os aliases fuzzyEditDistance valores. |
| `aliases` | Adicional Uma matriz de objetos complexos que pode ser usada para especificar grafias alternativas ou sinônimos para o nome da entidade raiz. |

| Propriedades do alias | Descrição |
|------------------|-------------|
| `text`  | A grafia alternativa ou a representação de algum nome de entidade de destino.  |
| `caseSensitive` | Adicional Age da mesma forma que o parâmetro "caseSensitive" da entidade raiz acima, mas aplica-se somente a esse alias. |
| `accentSensitive` | Adicional Age da mesma forma que o parâmetro "accentSensitive" da entidade raiz acima, mas aplica-se somente a esse alias. |
| `fuzzyEditDistance` | Adicional Age da mesma forma que o parâmetro "fuzzyEditDistance" da entidade raiz acima, mas aplica-se somente a esse alias. |


### <a name="inline-format"></a>Formato embutido

Em alguns casos, pode ser mais conveniente fornecer a lista de entidades personalizadas para fazer a correspondência embutida diretamente na definição de habilidades. Nesse caso, você pode usar um formato JSON semelhante ao descrito acima, mas ele é embutido na definição de habilidade.
Somente as configurações com menos de 10 KB de tamanho (tamanho serializado) podem ser definidas em linha. 

##    <a name="sample-definition"></a>Definição de exemplo

Uma definição de habilidade de exemplo usando um formato embutido é mostrada abaixo:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Como alternativa, se você decidir fornecer um ponteiro para o arquivo de definição de entidades, uma definição de habilidade de exemplo usando o `entitiesDefinitionUri` formato será mostrada abaixo:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Saída de exemplo

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Erros e avisos

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Aviso: capacidade máxima atingida para correspondências, ignorando todas as correspondências duplicadas adicionais.

Esse aviso será emitido se o número de correspondências detectadas for maior que o máximo permitido. Nesse caso, vamos parar de incluir correspondências duplicadas. Se isso for inaceitável para você, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que possamos ajudá-lo com seu caso de uso individual.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade (para pesquisar entidades bem conhecidas)](cognitive-search-skill-entity-recognition.md)

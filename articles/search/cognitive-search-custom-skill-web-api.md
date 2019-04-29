---
title: Habilidade de pesquisa cognitiva personalizada – Azure Search
description: Estender os recursos dos conjuntos de habilidades de pesquisa cognitiva chamando APIs da Web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: 1fcb12fc2cfae98376210e1924a670cce444f4f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61343332"
---
# <a name="custom-web-api-skill"></a>Habilidade da API Web personalizada

A habilidade de **API Web Personalizada** permite que você estenda pesquisa cognitiva ao chamar um ponto de extremidade de API Web fornecendo operações personalizadas. Semelhante a habilidades internas, uma habilidade **API Web Personalizada** tem entradas e saídas. Dependendo das entradas, sua API Web recebe um conteúdo JSON quando o indexador é executado e gera um conteúdo JSON como uma resposta, juntamente com um código de status de êxito. A resposta deve ter as saídas especificadas pela sua habilidade personalizada. Qualquer outra resposta é considerada um erro e nenhum aprimoramento é executado.

A estrutura das payloads JSON é descrita mais detalhadamente abaixo neste documento.

> [!NOTE]
> O indexador tentará novamente duas vezes determinados códigos de status HTTP padrão retornados da API Web. Esses códigos de status HTTP são: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| uri | O URI da API Web para o qual o conteúdo _JSON_ será enviado. Somente o esquema do URI **https** é permitido |
| httpMethod | O método a ser usado ao enviar o conteúdo. Os métodos permitidos são `PUT` ou `POST` |
| httpHeaders | Uma coleção de pares chave-valor em que as chaves representam os nomes de cabeçalho e os valores representam valores de cabeçalho que serão enviados para sua API Web, juntamente com o conteúdo. Os seguintes cabeçalhos são proibidos de estarem nesta coleção: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| Tempo limite | (Opcional) Quando especificado, indica o tempo limite para o cliente http que fez a chamada à API. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Por exemplo, `PT60S` por 60 segundos. Se não for definido, um valor padrão de 30 segundos será escolhido. O tempo limite pode ser definido para um máximo de 90 segundos e um mínimo de 1 segundo. |
| batchSize | (Opcional) Indica quantos "registros de dados" (veja estrutura de conteúdos _JSON_ abaixo) serão enviados por chamada à API. Se não for definido, um padrão de 1.000 será escolhido. É recomendável que você faça uso desse parâmetro para alcançar um equilíbrio adequado entre a taxa de transferência de indexação e de carga em sua API |

## <a name="skill-inputs"></a>Entradas de habilidades

Não há nenhuma entrada "predefinida" para essa habilidade. Você pode escolher um ou mais campos que já estariam disponíveis no momento da execução dessa habilidade como entradas e o conteúdo _JSON_ enviada para a API Web terá campos diferentes.

## <a name="skill-outputs"></a>Saídas de habilidades

Não há nenhuma saída "predefinida" para essa habilidade. Dependendo da resposta que sua API Web retornará, adicione campos de saída para que eles possam ser retirados da resposta _JSON_.


## <a name="sample-definition"></a>Definição de exemplo

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Estrutura JSON de entrada de exemplo

Essa estrutura _JSON_ representa o conteúdo que será enviado para sua API Web.
Ele sempre segue estas restrições:

* A entidade de nível superior é chamada `values` e será uma matriz de objetos. O número desses objetos será no máximo o `batchSize`
* Cada objeto na matriz `values` terá
    * Uma propriedade `recordId` que é uma cadeia de caracteres **exclusiva** usada para identificar esse registro.
    * Uma propriedade `data` que é um objeto _JSON_. Os campos da propriedade `data` corresponderão a "nomes" especificados na seção `inputs` da definição de habilidade. O valor desses campos será da `source` desses campos (que poderia ser de um campo no documento ou potencialmente de outra habilidade)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Estrutura JSON de saída de exemplo

A "saída" corresponde à resposta retornada da sua API Web. A API Web deve retornar apenas um conteúdo _JSON_ (verificada examinando o cabeçalho de resposta `Content-Type`) e deve atender às restrições a seguir:

* Deve haver uma entidade de nível superior chamada `values`, que deve ser uma matriz de objetos.
* O número de objetos na matriz deve ser o mesmo que o número de objetos enviados para a API Web.
* Cada objeto deve ter:
   * Uma propriedade `recordId`
   * Uma propriedade `data`, que é um objeto no qual os campos são aprimoramentos correspondendo aos "nomes" no `output` e cujo valor é considerado o aprimoramento.
   * Uma propriedade `errors`, uma matriz listando os erros encontrados que serão adicionados ao histórico de execução do indexador. Essa propriedade é necessária, mas pode ter um valor `null`.
   * Uma propriedade `warnings`, uma matriz listando os avisos encontrados que serão adicionados ao histórico de execução do indexador. Essa propriedade é necessária, mas pode ter um valor `null`.
* Os objetos na matriz `values` não precisam estar na mesma ordem que os objetos na matriz `values` enviada como uma solicitação para a API Web. No entanto, o `recordId` é usado para correlação, de modo que qualquer registro na resposta que contém um `recordId` que não faça parte da solicitação original à API Web será descartado.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Casos de erro
Além de sua API Web não estar disponível ou enviar códigos de status sem êxito, os seguintes são considerados casos incorretos:

* Se a API Web retornar um código de status de êxito, mas a resposta indicar que não é `application/json`, a resposta será considerada inválida e nenhum aprimoramento será executado.
* Se houver registros **inválidos** (com `recordId` ausente na solicitação original ou com valores duplicados) na matriz `values` de resposta, nenhum aprimoramento será executado para **esses** registros.

Para casos em que a API Web não está disponível ou retorna um erro HTTP, um erro amigável com todos os detalhes disponíveis sobre o erro de HTTP será adicionado ao histórico de execução do indexador.

## <a name="see-also"></a>Consulte também

+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Adicionar habilidade personalizada para pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Criar uma habilidade personalizada usando a API de Tradução de Texto](cognitive-search-create-custom-skill-example.md)
---
title: Manipular tipos de conteúdo-aplicativos lógicos do Azure
description: Saiba como os Aplicativos Lógicos manipulam tipos de conteúdo em tempo de design e tempo de execução
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 97897da13c70c29834b1fc276829b316416efd8d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868925"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Manipular tipos de conteúdo em Aplicativos Lógicos do Azure

Vários tipos de conteúdo podem fluir por meio de um aplicativo lógico, por exemplo, JSON, XML, arquivos simples e dados binários. Embora os Aplicativos Lógicos deem suporte a todos os tipos de conteúdo, alguns têm suporte nativo e não exigem transmissão ou conversão nos aplicativos lógicos. Outros tipos podem exigir transmissão ou conversão conforme necessário. Este artigo descreve como os Aplicativos Lógicos manipulam tipos de conteúdo e como é possível transmitir ou converter corretamente esses tipos quando necessário.

Para determinar a maneira apropriada de manipular tipos de conteúdo, os Aplicativos Lógicos dependem do valor de cabeçalho `Content-Type` em chamadas HTTP, por exemplo:

* [aplicativo/json](#application-json) (tipo nativo)
* [texto/simples](#text-plain) (tipo nativo)
* [Fluxo de dados de aplicativo/xml e aplicativo/octet](#application-xml-octet-stream)
* [Outros tipos de conteúdo](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Os Aplicativos Lógicos armazenam e manipulam qualquer solicitação com o tipo de conteúdo *aplicativo/json* como um objeto JSON (JavaScript Object Notation). Por padrão, é possível analisar o conteúdo do JSON sem qualquer conversão. Para analisar uma solicitação que tenha um cabeçalho com o tipo de conteúdo "application/json", é possível usar uma expressão. Este exemplo retorna o valor `dog` da matriz `animal-type` sem conversão: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se estiver trabalhando com dados JSON que não especificam um cabeçalho, você poderá converter manualmente esses dados para JSON usando a função [json()](../logic-apps/workflow-definition-language-functions-reference.md#json), por exemplo: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Criar tokens para propriedades JSON

Os Aplicativos Lógicos possibilitam gerar tokens amigáveis que representam as propriedades no conteúdo JSON para que você possa referenciar e usar essas propriedades com mais facilidade no fluxo de trabalho do aplicativo lógico.

* **Gatilho de solicitação**

  Quando você usa esse gatilho no Designer do Aplicativo Lógico, é possível fornecer um esquema JSON que descreva o conteúdo que espera receber. 
  O designer analisa o conteúdo JSON usando esse esquema e gera tokens amigáveis que representam as propriedades no conteúdo JSON. 
  É possível então facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho do aplicativo lógico. 
  
  Se não tiver um esquema, você pode gerar o esquema. 
  
  1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.  
  
  2. Em **Inserir ou colar um conteúdo JSON de amostra**, forneça um conteúdo de exemplo e, em seguida, escolha **Concluído**. Por exemplo: 

     ![Fornecer conteúdo JSON de amostra](./media/logic-apps-content-type/request-trigger.png)

     O esquema gerado agora aparece no gatilho.

     ![Fornecer conteúdo JSON de amostra](./media/logic-apps-content-type/generated-schema.png)

     Aqui está a definição subjacente para o gatilho de Solicitação no editor de exibição de código:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. Na solicitação, certifique-se de incluir um cabeçalho `Content-Type` e defina o valor do cabeçalho como `application/json`.

* **Ação analisar JSON**

  Ao usar essa ação no Designer do Aplicativo Lógico, será possível analisar a saída JSON e gerar tokens amigáveis que representem as propriedades no conteúdo JSON. 
  É possível então facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho do aplicativo lógico. Semelhante ao gatilho Solicitar, é possível fornecer ou gerar um esquema JSON que descreva o conteúdo JSON que você quer analisar. 
  Dessa forma, será mais fácil consumir dados do Barramento de Serviço do Azure, Azure Cosmos DB e assim por diante.

  ![Analisar o JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>texto/sem formatação

Quando o aplicativo lógico recebe as mensagens HTTP que tenham o cabeçalho `Content-Type` definido como `text/plain`, o aplicativo lógico armazena essas mensagens em forma bruta. Se essas mensagens forem incluídas em ações subsequentes sem conversão, essas solicitações saem com o cabeçalho `Content-Type` definido como `text/plain`. 

Por exemplo, ao trabalhar com um arquivo simples, você poderá receber uma solicitação HTTP com o cabeçalho `Content-Type` definido como tipo de conteúdo `text/plain`:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se você então enviar essa solicitação em uma ação subsequente como o corpo para outra solicitação, por exemplo, `@body('flatfile')`, a segunda solicitação também terá um cabeçalho `Content-Type` definido como `text/plain`. Caso esteja trabalhando com dados de texto sem formatação, mas não especificar um cabeçalho, será possível converter manualmente esses dados para texto usando a [função string()](../logic-apps/workflow-definition-language-functions-reference.md#string) como esta expressão: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Fluxo de dados de aplicativo/xml e aplicativo/octet

Os aplicativos lógicos sempre mantêm o `Content-Type` em uma resposta ou solicitação HTTP recebida. Portanto, se o aplicativo lógico recebe o conteúdo com `Content-Type` definido como `application/octet-stream`, e você inclui esse conteúdo em uma ação subsequente sem conversão, a solicitação de saída também terá `Content-Type` definido como `application/octet-stream`. Dessa forma, os Aplicativos Lógicos podem garantir que os dados não sejam perdidos ao movê-los através do fluxo de trabalho. No entanto, o estado de ação, ou entradas e saídas, é armazenado em um objeto JSON conforme o estado é movido no fluxo de trabalho. 

## <a name="converter-functions"></a>Funções de conversor

Para preservar alguns tipos de dados, os Aplicativos Lógicos convertem o conteúdo em uma cadeia de caracteres codificada em base64 binária com os metadados apropriados que preservam o conteúdo `$content` e `$content-type`, que são convertidos automaticamente. 

Esta lista descreve como os Aplicativos Lógicos convertem o conteúdo quando você usa as seguintes [funções](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Converte dados em`application/json`
* `xml()`: Converte dados em`application/xml`
* `binary()`: Converte dados em`application/octet-stream`
* `string()`: Converte dados em`text/plain`
* `base64()`: Converte o conteúdo em uma cadeia de caracteres codificada em base64
* `base64toString()`: Converte uma cadeia de caracteres codificada em base64 em`text/plain`
* `base64toBinary()`: Converte uma cadeia de caracteres codificada em base64 em`application/octet-stream`
* `dataUri()`: Converte uma cadeia de caracteres em um URI de dados
* `dataUriToBinary()`: Converte um URI de dados em uma cadeia de caracteres binária
* `dataUriToString()`: Converte um URI de dados em uma cadeia de caracteres

Por exemplo, se você receber uma solicitação HTTP em que `Content-Type` é definido como `application/xml`, tal como este conteúdo:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

É possível converter esse conteúdo usando a expressão `@xml(triggerBody())` com as funções `xml()` e `triggerBody()`, e então usar esse conteúdo posteriormente. Como alternativa, é possível usar a expressão `@xpath(xml(triggerBody()), '/CustomerName')` com as funções `xpath()` e `xml()`. 

## <a name="other-content-types"></a>Outros tipos de conteúdo

Os Aplicativos Lógicos funcionam e são compatíveis com outros tipos de conteúdo, mas podem exigir que você obtenha manualmente o corpo da mensagem, decodificando a variável `$content`.

Por exemplo, suponha que o aplicativo lógico é disparado por uma solicitação com o tipo de conteúdo `application/x-www-url-formencoded`. Para preservar todos os dados, a variável `$content` no corpo da solicitação tem um conteúdo que é codificado como uma cadeia de caracteres base64:

`CustomerName=Frank&Address=123+Avenue`

Como a solicitação não é texto sem formatação, nem JSON, ela é armazenada na ação da seguinte maneira:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Os Aplicativos Lógicos fornecem funções nativas para manipular dados de formulário, por exemplo: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Ou então, é possível acessar manualmente os dados usando uma expressão, como neste exemplo:

`@string(body('formdataAction'))` 

Se quiser que a solicitação de saída também tenha o cabeçalho de tipo de conteúdo `application/x-www-url-formencoded`, você pode adicionar a solicitação ao corpo da ação sem conversões, usando uma expressão como `@body('formdataAction')`. No entanto, esse método só funciona se o corpo for o único parâmetro na entrada `body`. Se você tentar usar a expressão `@body('formdataAction')` em uma solicitação `application/json`, receberá um erro de tempo de execução porque o corpo é enviado codificado.

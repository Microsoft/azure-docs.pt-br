---
title: "Extensões de OpenAPI para conectores personalizados – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Estender a OpenAPI do seu conector personalizado com recursos avançados"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Estender a OpenAPI do seu conector personalizado com recursos avançados

Para criar conectores personalizados para os Aplicativos Lógicos do Azure, Microsoft Flow ou Microsoft PowerApps, você deve fornecer um arquivo de definição de OpenAPI, que é um documento legível para o computador que não é específico de idiomas e que descreve as operações e os parâmetros de sua API. Juntamente com a nova funcionalidade do OpenAPI, você também pode incluir essas extensões do OpenAPI ao criar conectores personalizados para Aplicativos Lógicos e Fluxo:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Aqui estão mais detalhes sobre essas extensões:

<a name="summary"></a>

## <a name="summary"></a>resumo

Especifica o título para a ação (operação). </br>
Aplica-se a: operações </br>
Recomendado: use *caso de frase* para `summary`. </br>
Exemplo: "Quando um evento é adicionado ao calendário" ou "Enviar um email"

!["resumo" para cada operação](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

Especifica o título de uma entidade. </br>
Aplica-se a: parâmetros, esquema de resposta </br>
Recomendado: use *caso de título* para `x-ms-summary`. </br>
Exemplo: "ID do calendário", "Assunto", "Descrição do evento" e assim por diante

!["x-ms-summary" para cada entidade](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

Especifica uma explicação detalhada sobre a funcionalidade da operação ou função e formato de uma entidade. </br>
Aplica-se a: operações, parâmetros, esquema de resposta </br>
Recomendado: use *caso de frase* para `description`. </br>
Exemplo: "Esta operação é acionada quando um novo evento é adicionado ao calendário", "Especificar o assunto do email" e assim por diante

!["descrição" para cada operação ou entidade](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

Especifica a visibilidade voltada para o usuário para uma entidade. </br>
Valores possíveis: `important`, `advanced` e `internal` </br>
Aplica-se a: operações, parâmetros, esquemas

* Operações e parâmetros de `important` são sempre exibidos primeiro para o usuário.
* Operações e os parâmetros de `advanced` estão ocultos em um menu adicional.
* Operações e parâmetros de `internal` são ocultados do usuário.

> [!NOTE] 
> Para parâmetros que sejam `internal` e `required`, você **deve** fornecer valores padrão para esses parâmetros.

Exemplo: O menu **Ver mais** e o menu **Mostrar opções avançadas** estão ocultando operações e parâmetros de `advanced`.

!["x-ms-visibilidade" para mostrar ou ocultar as operações e parâmetros](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

Mostra uma lista preenchida para o usuário para que ele possa selecionar os parâmetros de entrada para uma operação. </br>
Aplica-se a: parâmetros </br>
Como usar: adicione o objeto `x-ms-dynamic-values` à definição do parâmetro. Por exemplo, consulte este [exemplo de OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

!["x-ms-dynamic-values" para a exibir listas](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Propriedades para x-ms-dynamic-values

| Nome | Obrigatório ou opcional | Descrição | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obrigatório | A operação para chamar o preenchimento da lista | 
| **value-path** | Obrigatório | Uma cadeia de caminho no objeto dentro de `value-collection` que faz referência ao valor do parâmetro. </br>Se `value-collection` não for especificado, a resposta é avaliada como uma matriz. | 
| **value-title** | Opcional | Uma cadeia de caminho no objeto dentro de `value-collection` que faz referência à descrição do valor. </br>Se `value-collection` não for especificado, a resposta é avaliada como uma matriz. | 
| **value-collection** | Opcional | Uma cadeia de caracteres de caminho que é avaliada como uma matriz de objetos no conteúdo da resposta | 
| **parâmetros** | Opcional | Um objeto cujas propriedades especificam os parâmetros de entrada necessários para invocar uma operação de valores dinâmicos | 
|||| 

Eis um exemplo que mostra as propriedades em `x-ms-dynamic-values`:

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Exemplo: Todas as extensões de OpenAPI até este ponto

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

Indica que o esquema para a resposta ou o parâmetro atual é dinâmico. Este objeto pode invocar uma operação que é definida pelo valor desse campo, descobrir dinamicamente o esquema e exibir a interface do usuário apropriada para coletar entradas do usuário ou mostrar os campos disponíveis. 

Aplica-se a: parâmetros, resposta

Como usar: adicione o objeto `x-ms-dynamic-schema` a uma definição de corpo de resposta ou parâmetro de solicitação. Para obter um exemplo, consulte este [exemplo de OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

Este exemplo mostra como o formato de entrada é alterado, com base no item que o usuário seleciona na lista suspensa:

!["x-ms-dynamic-schema" para parâmetros dinâmicos](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

E este exemplo mostra como as saídas são alteradas, com base no item que o usuário seleciona na lista suspensa. Nesta versão, o usuário seleciona "Carros":

!["x-ms-dynamic-schema-response" para o item "Carros" selecionado](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

Nesta versão, o usuário seleciona "Comida":

!["x-ms-dynamic-schema-response" para o item "Comida" selecionado](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Propriedades para x-ms-dynamic-schema

| Nome | Obrigatório ou opcional | Descrição | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obrigatório | A operação para chamar o fetch do esquema | 
| **parâmetros** | Obrigatório | Um objeto cujas propriedades especificam os parâmetros de entrada necessários para invocar uma operação de esquema dinâmico | 
| **value-path** |Opcional | Uma cadeia de caminho que se refere à propriedade que tem o esquema. </br>Se não for especificada, presume-se que a resposta contenha o esquema nas propriedades do objeto raiz. | 
|||| 

Este é um exemplo de um parâmetro dinâmico:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Este é um exemplo de uma resposta dinâmica:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Descrever seus conectores e APIs personalizados](../logic-apps/custom-connector-api-postman-collection.md)
* [Aplicativos Lógicos: registrar seu conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: registrar seu conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
---
title: "Código personalizado para Aplicativo Lógico do Azure com o Azure Functions | Microsoft Docs"
description: "Criar e executar código personalizado para Aplicativo Lógico do Azure com o Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Adicionar e executar código personalizado para aplicativos lógicos por meio do Azure Functions

Para executar trechos personalizados do C# ou Node.js em aplicativos lógicos, é possível criar funções personalizadas por meio do Azure Functions. 
O [Azure Functions](../azure-functions/functions-overview.md) oferece uma computação sem servidor no Microsoft Azure e é útil para realizar estas tarefas:

* Formatação avançada ou computação de campos em um aplicativos lógicos
* Execute cálculos em um fluxo de trabalho.
* Estender a funcionalidade do aplicativo lógico com funções com suporte no C# ou no node.js

## <a name="create-custom-functions-for-your-logic-apps"></a>Criar funções personalizadas para aplicativos lógicos

É recomendável que você crie uma função no portal das Azure Functions usando os modelos **Webhook Genérico – Nó** ou **Webhook Genérico – C#**. O resultado cria um modelo populado automaticamente que aceita `application/json` de um aplicativo lógico. As funções que você cria desses modelos são automaticamente detectadas e listadas no Designer de Aplicativos Lógicos em **Azure Functions em minha região.**

No Portal do Azure, no painel **Integrar** de sua função, o modelo deve mostrar que **Modo** está definido como **Webhook** e o **Tipo do Webhook** está definido como **JSON Genérico**. 

As funções do Webhook aceitam uma solicitação e passam-na para o método por meio de uma variável `data` . Você pode acessar as propriedades do conteúdo usando uma notação de ponto como `data.function-name`. Por exemplo, uma função Javascript simples, que converte um valor DateTime em uma cadeia de caracteres de data, terá a seguinte aparência:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Chamar o Azure Functions de aplicativos lógicos

Para listar os contêineres em sua assinatura e selecionar a função que você deseja chamar, no Designer de Aplicativos Lógicos, clique no menu **Ações** e selecione **Azure Functions na minha Região**.

Depois de escolher a função, será solicitado que você especifique um objeto de conteúdo de entrada. Esse objeto é a mensagem que o aplicativo lógico envia para a função e deve ser um objeto JSON. Por exemplo, se você quiser passar a data da **Última Modificação** de um gatilho Salesforce, o conteúdo da função poderá ser como neste exemplo:

![Data da última modificação][1]

## <a name="trigger-logic-apps-from-a-function"></a>Disparar aplicativos lógicos a partir de uma função

Você pode disparar um aplicativo lógico de dentro de uma função. Constule [Aplicativos lógicos como pontos de extremidade escaláveis](logic-apps-http-endpoint.md). Crie um aplicativo lógico que tenha um gatilho manual, em seguida, de dentro de sua função, gere um POST HTTP para a URL do gatilho manual com o conteúdo que você deseja enviar para o aplicativo lógico.

### <a name="create-a-function-from-logic-app-designer"></a>Criar uma função do Designer de Aplicativos Lógicos

Você também pode criar uma função de webhook do node.js de dentro do designer. Primeiro, selecione **Azure Functions em minha região** , em seguida, escolha um contêiner de sua função. Se você ainda não tiver um contêiner, precisará criar um a partir do [portal das Azure Functions](https://functions.azure.com/signin). Em seguida, selecione **Criar Novo**.  

Para gerar um modelo com base nos dados que você deseja calcular, especifique o objeto de contexto que planeja passar para uma função. Esse objeto deve ser um objeto JSON. Por exemplo, se você passar o conteúdo do arquivo por meio de uma ação FTP, o conteúdo do contexto ficará como neste exemplo:

![Conteúdo do contexto][2]

> [!NOTE]
> Como esse objeto não foi convertido em uma cadeia de caracteres, o conteúdo é adicionado diretamente ao conteúdo JSON. No entanto, um erro ocorrerá se o objeto não for um token JSON (ou seja, uma cadeia de caracteres ou um objeto/matriz JSON). Para converter o objeto em uma cadeia de caracteres, adicione aspas conforme mostrado na primeira ilustração neste artigo.
> 

Então, o designer irá gerar um modelo de função que você poderá criar embutido. As variáveis são previamente criadas com base no contexto que você planeja passar para a função.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png

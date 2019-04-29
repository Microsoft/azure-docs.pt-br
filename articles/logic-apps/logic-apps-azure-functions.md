---
title: Adicionar e executar código personalizado em Aplicativos Lógicos do Azure com as Azure Functions | Microsoft Docs
description: Saiba como adicionar e executar snippets de código personalizados nos Aplicativos Lógicos do Azure com as Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bec33a4a8540f9599cf1d479f1f59c4cde39bd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687527"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Adicionar e executar snippets de código personalizados nos Aplicativos Lógicos do Azure com as Azure Functions

Quando você deseja executar somente código suficiente que executa um trabalho específico em seus aplicativos lógicos, você pode criar suas próprias funções com [Azure Functions](../azure-functions/functions-overview.md). Este serviço ajuda você a criar os snippets de código Node.js, C# e F#, para que você não precise compilar um aplicativo completo ou a infraestrutura para executar seu código. O Azure Functions fornece computação sem servidor na nuvem e é útil para executar tarefas, como estes exemplos:

* Estenda o comportamento do seu aplicativo lógico com funções em Node. js ou C#.
* Execute cálculos em seu fluxo de trabalho do aplicativo lógico.
* Aplicar a formatação avançada ou computação de campos em seus aplicativos lógicos.

Você também pode [chamar aplicativos lógicos de dentro de funções do Azure](#call-logic-app).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, você precisa destes itens:

* Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Um aplicativo de função do Azure, que é um contêiner para funções do Azure e sua função do Azure. Se você não tiver um aplicativo de funções, [crie primeiro o aplicativo de função](../azure-functions/functions-create-first-azure-function.md). Em seguida, você pode criar sua função seja [separadamente fora do seu aplicativo lógico](#create-function-external), ou [de dentro de seu aplicativo lógico](#create-function-designer) no Designer do aplicativo lógico.

  Os aplicativos e funções de função novos e existentes têm os mesmos requisitos para trabalhar com aplicativos lógicos:

  * Seu aplicativo de função deve ter a mesma assinatura do Azure que seu aplicativo lógico.

  * A função usa um gatilho HTTP, por exemplo, o modelo de função de **gatilho HTTP** para **JavaScript** ou **C#**. 

    Esse modelo de gatilho HTTP pode aceitar o conteúdo que tenha o tipo `application/json` do seu aplicativo lógico. 
    Quando você adiciona uma função do Azure ao seu aplicativo lógico, o Designer do Aplicativo Lógico mostra funções personalizadas criadas com base neste modelo dentro de sua assinatura do Azure. 

  * Sua função não usa rotas personalizadas, a menos que você tenha definido uma [definição de OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como um [arquivo do Swagger](https://swagger.io/). 
  
  * Se você tiver definido uma definição de OpenAPI para sua função, o Designer dos Aplicativos Lógicos fornece uma experiência mais rica para trabalhar com parâmetros de função. Antes de seu aplicativo lógico pode localizar e acessar funções que têm definições de OpenAPI, [configurar seu aplicativo de função seguindo estas etapas](#function-swagger).

* O aplicativo lógico no qual você deseja adicionar a função, incluindo uma [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Antes de adicionar ações que podem executar funções, seu aplicativo lógico deve começar com um gatilho.

  Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Criar funções de aplicativos lógicos externa

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, crie seu aplicativo de função do Azure, que deve ter a mesma assinatura do Azure do seu aplicativo lógico e, em seguida, crie sua função do Azure.
Se você for novo em realizar criações no Azure Functions, saiba como [criar sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md), mas observe esses requisitos para criar funções que podem ser chamadas de aplicativos lógicos:

* Certifique-se de selecionar o modelo de função **Gatilho HTTP** para **JavaScript** ou **C#**.

  ![Gatilho HTTP - JavaScript ou C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Opcionalmente, se você [gerar uma definição de API](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [arquivo Swagger](https://swagger.io/), para sua função, poderá obter uma experiência mais rica ao trabalhar com parâmetros de função no Logic Apps Designer. Para configurar seu aplicativo de função, para que seu aplicativo lógico possa encontrar e usar funções com descrições de Swagger, siga estas etapas:

  1. Verifique se que seu aplicativo de função está sendo executado ativamente.

  2. No aplicativo de função, configure [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estas etapas:

     1. Na lista **Aplicativos de Funções**, selecione seu aplicativo de função > **Recursos da plataforma** > **CORS**.

        ![Selecione seu aplicativo de função> "Recursos da plataforma"> "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. Em **CORS**, adicione o caractere curinga, `*`  mas remova todas as outras origens da lista e escolha **Salvar**.

        ![Defina "CORS* como o caractere curinga "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Valores de propriedade de acesso dentro de solicitações HTTP

As funções do Webhook podem aceitar solicitações HTTP como entradas e passar essas solicitações para outras funções. Por exemplo, embora o Logic Apps possua [funções que convertem valores de DateTime](../logic-apps/workflow-definition-language-functions-reference.md), essa função JavaScript de amostra básica mostra como você pode acessar uma propriedade dentro de um objeto de solicitação transmitido à função e executar operações nesse valor de propriedade. Para acessar propriedades dentro de objetos, este exemplo usa o [operador ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Aqui está o que acontece dentro dessa função:

1. A função cria uma variável `data` e atribui o objeto `body` dentro do objeto `request` a essa variável. A função usa o operador ponto (.) para fazer referência a `body` dentro do objeto a `request` objeto: 

   ```javascript
   var data = request.body;
   ```

2. A função agora pode acessar a propriedade `date` por meio da variável `data` e converter esse valor de propriedade do tipo DateTime para o tipo DateString chamando a função `ToDateString()`. A função também retorna o resultado através da propriedade `body` na resposta da função: 

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que você criou sua função do Azure, siga as etapas de como [ adicionar funções a aplicativos lógicos ](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicativos lógicos

Antes de poder criar uma função do Azure a partir de dentro de seu aplicativo lógico no Logic App Designer, você deve primeiro ter um aplicativo de função do Azure, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, crie primeiro o aplicativo de função. Veja [Crie sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md). 

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra o aplicativo lógico no Designer do aplicativo lógico. 

2. Para criar e adicionar sua função, siga a etapa que se aplica ao seu cenário:

   * Na última etapa no fluxo de trabalho do aplicativo lógico, escolha **Nova etapa**.

   * Entre as etapas existentes no fluxo de trabalho do aplicativo lógico, mova o mouse sobre a seta, escolha o sinal de adição (+) e, em seguida, selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "funções azure" como seu filtro.
Na lista de ações, selecione esta ação: **Escolher uma função do Azure – Azure Functions** 

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois de abre a lista de ações, selecione esta ação: **Funções do Azure – criar nova função**

   ![Selecione seu aplicativo de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. No editor de definição de função, defina sua função:

   1. Na caixa **Nome da função**, forneça um nome para sua função. 

   2. Na caixa **Código**, adicione seu código à função de modelo, incluindo a resposta e a carga útil que você deseja que sejam retornadas ao seu aplicativo lógico depois que sua função terminar de ser executada. 

      ![Definir sua função](./media/logic-apps-azure-functions/function-definition.png)

      No código do modelo, o *`context` objeto* refere-se à mensagem que seu aplicativo lógico envia por meio do campo **Corpo da solicitação** em uma etapa posterior. 
      Para acessar as propriedades do objeto `context` de dentro de sua função, use a seguinte sintaxe: 

      `context.body.<property-name>`

      Por exemplo, para fazer referência à propriedade `content` dentro do objeto `context`, use a seguinte sintaxe: 

      `context.body.content`

      O código de modelo também inclui uma variável `input`, que armazena o valor do parâmetro `data` para que sua função possa executar operações nesse valor. 
      Dentro de funções de JavaScript, a variável `data` também é um atalho para `context.body`.

      > [!NOTE]
      > A propriedade `body` aqui se aplica ao objeto `context` e não é igual ao token **Corpo** a partir de uma saída da ação, que você também pode passar para sua função. 
 
   3. Quando terminar, escolha **Criar**.

6. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON). 

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **Corpo da Solicitação**, a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens de saídas de etapas anteriores. Este exemplo especifica que o conteúdo do contexto contém uma propriedade chamada `content` que tem o valor do token **De** do gatilho de email:

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Portanto, se este exemplo tiver usado o token **Hora de Recebimento**, você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando marcas de aspas duplas:  

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Para especificar outros detalhes como o método de uso, os cabeçalhos de solicitação ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes para aplicativos lógicos

Para chamar funções existentes do Azure de seus aplicativos lógicos, você pode adicionar funções do Azure como qualquer outra ação no Logic App Designer. 

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra o aplicativo lógico no Designer do aplicativo lógico. 

2. Na etapa em que você deseja adicionar a função, escolha **Nova etapa** > **Adicione uma ação**. 

3. Na caixa de pesquisa, insira "funções azure" como seu filtro.
Na lista de ações, selecione esta ação: **Escolher uma função do Azure – Azure Functions** 

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de funções aparecer, selecione sua função. 

   ![Selecione seu aplicativo de função e a função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que têm definições de API (descrições de Swagger) e que são [configuradas para que seu aplicativo lógico possa encontrar e acessar essas funções](#function-swagger), é possível selecionar **Ações de Swagger**:

   ![Selecione seu aplicativo de função, "Ações de Swagger" "e sua função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON). 

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **Corpo da Solicitação**, a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens de saídas de etapas anteriores. Este exemplo especifica que o conteúdo do contexto contém uma propriedade chamada `content` que tem o valor do token **De** do gatilho de email:

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Portanto, se este exemplo tiver usado o token **Hora de Recebimento**, você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando marcas de aspas duplas: 

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Para especificar outros detalhes como o método de uso, os cabeçalhos de solicitação ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Chamar aplicativos lógicos de funções

Quando você quiser acionar um aplicativo lógico de dentro de uma função do Azure, esse aplicativo lógico deverá ser iniciado com um gatilho que fornece um ponto de extremidade que pode ser chamado. Por exemplo, você pode iniciar o aplicativo lógico com o acionador **HTTP**, **Solicitação**, **Filas do Azure** ou **Grade de Eventos**. Dentro de sua função, envie uma solicitação HTTP POST para a URL do gatilho e inclua o conteúdo que você deseja que esse aplicativo lógico processe. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)

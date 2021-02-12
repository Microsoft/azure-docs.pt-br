---
title: Chamar, disparar ou aninhar aplicativos lógicos usando gatilhos de solicitação
description: Configurar pontos de extremidade HTTPS para chamar, disparar ou aninhar fluxos de trabalho de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981197"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Chamar, disparar ou aninhar aplicativos lógicos usando pontos de extremidade HTTPS em aplicativos lógicos do Azure

Para tornar seu aplicativo lógico chamável por meio de uma URL e ser capaz de receber solicitações de entrada de outros serviços, você pode expor nativamente um ponto de extremidade HTTPS síncrono usando um gatilho baseado em solicitação em seu aplicativo lógico. Com esse recurso, você pode chamar seu aplicativo lógico de outros aplicativos lógicos e criar um padrão de pontos de extremidade que podem ser chamados. Para configurar um ponto de extremidade que possa ser chamado para manipular chamadas de entrada, você pode usar qualquer um desses tipos de gatilho:

* [Solicitação](../connectors/connectors-native-reqres.md)
* [Webhook HTTP](../connectors/connectors-native-webhook.md)
* Gatilhos de conector gerenciado que têm o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber solicitações HTTPS de entrada

Este artigo mostra como criar um ponto de extremidade que possa ser chamado em seu aplicativo lógico usando o gatilho de solicitação e chamar esse ponto de extremidade de outro aplicativo lógico. Todos os princípios se aplicam de forma idêntica aos outros tipos de gatilho que você pode usar para receber solicitações de entrada.


Para obter mais informações sobre segurança, autorização e criptografia para chamadas de entrada para seu aplicativo lógico, como o protocolo [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL), [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml), expondo seu aplicativo lógico com o gerenciamento de API do Azure ou restringindo os endereços IP que originam chamadas de entrada, consulte [acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja usar o gatilho para criar o ponto de extremidade que possa ser chamado. Você pode começar com um aplicativo lógico em branco ou um aplicativo lógico existente, onde você pode substituir o gatilho atual. Este exemplo começa com um aplicativo lógico em branco. Se você for novo em aplicativos lógicos, consulte [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Criar um ponto de extremidade que possa ser chamado

1. Entre no [portal do Azure](https://portal.azure.com). Crie e abra um aplicativo lógico em branco no designer de aplicativo lógico.

1. Na caixa de pesquisa, selecione **interno**. Na caixa de pesquisa, insira `request` como o filtro. Na lista de gatilhos, selecione **quando uma solicitação HTTP é recebida**.

   ![Localizar e selecionar o gatilho de solicitação](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcionalmente, na caixa **esquema JSON do corpo da solicitação** , você pode inserir um esquema JSON que descreve a carga ou os dados que você espera que o gatilho receba.

   O designer usa esse esquema para gerar tokens que representam saídas de gatilho. Você pode fazer referência facilmente a essas saídas em todo o fluxo de trabalho do aplicativo lógico. Saiba mais sobre [tokens gerados a partir de esquemas JSON](#generated-tokens).

   Para este exemplo, digite este esquema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Fornecer o esquema JSON para a ação de solicitação](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou, você pode gerar um esquema JSON fornecendo um conteúdo de exemplo:

   1. No gatilho de **solicitação** , selecione **usar conteúdo de exemplo para gerar o esquema**.

   1. Na caixa **Inserir ou colar um conteúdo JSON de exemplo** , insira seu conteúdo de exemplo, por exemplo:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Quando estiver pronto, selecione **concluído**.

      A caixa **esquema JSON do corpo da solicitação** agora mostra o esquema gerado.

1. Salve seu aplicativo lógico.

   A caixa **URL de postagem http** agora mostra a URL de retorno de chamada gerada que outros serviços podem usar para chamar e disparar seu aplicativo lógico. Essa URL inclui parâmetros de consulta que especificam uma chave de assinatura de acesso compartilhado (SAS), que é usada para autenticação.

   ![URL de retorno de chamada gerada para ponto de extremidade](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Para copiar a URL de retorno de chamada, você tem estas opções:

   * À direita da caixa **URL do http post** , selecione **Copiar URL** (ícone copiar arquivos).

   * Faça essa chamada usando o método esperado pelo gatilho de solicitação. Este exemplo usa o `POST` método:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Copie a URL de retorno de chamada do painel de **visão geral** do seu aplicativo lógico.

     1. No menu do aplicativo lógico, selecione **visão geral**.

     1. Na seção **Resumo** , selecione **Ver Histórico de gatilho**.

        ![Obter URL do ponto de extremidade de portal do Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. Em **URL de retorno de chamada [post]**, copie a URL:

        ![Copiar URL do ponto de extremidade de portal do Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Selecionar o método de solicitação esperado

Por padrão, o gatilho de solicitação espera uma `POST` solicitação. No entanto, você pode especificar um método diferente que o chamador deve usar, mas apenas um único método.

1. No gatilho de solicitação, abra a lista **Adicionar novo parâmetro** e selecione o **método**, que adiciona essa propriedade ao gatilho.

   ![Adicionar a propriedade "Method" para disparar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Na lista **método** , selecione o método que o gatilho deve esperar. Ou, você pode especificar um método personalizado.

   Por exemplo, selecione o método **Get** para que você possa testar a URL do ponto de extremidade mais tarde.

   ![Selecionar o método de solicitação esperado pelo gatilho](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Passar parâmetros por meio da URL do ponto de extremidade

Quando você quiser aceitar valores de parâmetro por meio da URL do ponto de extremidade, terá estas opções:

* [Aceite valores por meio de parâmetros Get](#get-parameters) ou parâmetros de URL.

  Esses valores são passados como pares de nome-valor na URL do ponto de extremidade. Para essa opção, você precisa usar o método GET em seu gatilho de solicitação. Em uma ação subsequente, você pode obter os valores de parâmetro como saídas do gatilho usando a `triggerOutputs()` função em uma expressão.

* [Aceite valores por meio de um caminho relativo](#relative-path) para parâmetros em seu gatilho de solicitação.

  Esses valores são passados por meio de um caminho relativo na URL do ponto de extremidade. Você também precisa selecionar explicitamente [o método](#select-method) esperado pelo gatilho. Em uma ação subsequente, você pode obter os valores de parâmetro como saídas do gatilho referenciando essas saídas diretamente.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Aceitar valores por meio de parâmetros GET

1. No gatilho de solicitação, abra a **lista Adicionar novo parâmetro**, adicione a propriedade **método** ao gatilho e selecione o método **Get** .

   Para obter mais informações, consulte [selecionar método de solicitação esperado](#select-method).

1. No gatilho de solicitação, adicione a ação em que você deseja usar o valor do parâmetro. Para este exemplo, adicione a ação de **resposta** .

   1. No gatilho de solicitação, selecione **nova etapa**  >  **Adicionar uma ação**.
   
   1. Em **Escolher uma ação**, na caixa de pesquisa, insira `response` como o filtro. Na lista ações, selecione a ação **resposta** .

1. Para criar a `triggerOutputs()` expressão que recupera o valor do parâmetro, siga estas etapas:

   1. Clique dentro da propriedade **corpo** da ação de resposta para que a lista conteúdo dinâmico seja exibida e selecione **expressão**.

   1. Na caixa **expressão** , insira esta expressão, substituindo `parameter-name` pelo nome do parâmetro e selecione **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Adicionar a expressão "triggerOutputs ()" para disparar](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      Na propriedade **Body** , a expressão é resolvida para o `triggerOutputs()` token.

      ![Resolvida a expressão "triggerOutputs ()"](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Se você salvar o aplicativo lógico, sair do designer e retornar ao designer, o token mostrará o nome do parâmetro que você especificou, por exemplo:

      ![Expressão resolvida para o nome do parâmetro](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      Na exibição de código, a propriedade **Body** é exibida na definição da ação de resposta da seguinte maneira:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Por exemplo, suponha que você deseja passar um valor para um parâmetro chamado `postalCode` . A propriedade **Body** especifica a cadeia de caracteres, `Postal Code: ` com um espaço à direita, seguido pela expressão correspondente:

      ![Adicione a expressão "triggerOutputs ()" de exemplo para disparar](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Para testar seu ponto de extremidade chamável, copie a URL de retorno de chamada do gatilho de solicitação e cole a URL em outra janela do navegador. Na URL, adicione o nome do parâmetro e o valor seguindo o ponto de interrogação ( `?` ) à URL no formato a seguir e pressione Enter.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   O navegador retorna uma resposta com este texto: `Postal Code: 123456`

   ![Resposta de envio de solicitação para URL de retorno de chamada](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Para colocar o nome e o valor do parâmetro em uma posição diferente dentro da URL, use o e comercial ( `&` ) como um prefixo, por exemplo:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Este exemplo mostra a URL de retorno de chamada com o nome do parâmetro de exemplo e o valor `postalCode=123456` em posições diferentes dentro da URL:

   * 1ª posição: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 2ª posição: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Se você quiser incluir o hash ou símbolo de libra ( **#** ) no URI, use esta versão codificada em vez disso: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Aceitar valores por meio de um caminho relativo

1. No gatilho de solicitação, abra a lista **Adicionar novo parâmetro** e selecione **caminho relativo**, que adiciona essa propriedade ao gatilho.

   ![Adicionar a propriedade "caminho relativo" para disparar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Na propriedade **caminho relativo** , especifique o caminho relativo para o parâmetro no esquema JSON que você deseja que a URL aceite, por exemplo, `/address/{postalCode}` .

   ![Especifique o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. No gatilho de solicitação, adicione a ação em que você deseja usar o valor do parâmetro. Para este exemplo, adicione a ação de **resposta** .

   1. No gatilho de solicitação, selecione **nova etapa**  >  **Adicionar uma ação**.

   1. Em **Escolher uma ação**, na caixa de pesquisa, insira `response` como o filtro. Na lista ações, selecione a ação **resposta** .

1. Na propriedade **Body** da ação de resposta, inclua o token que representa o parâmetro que você especificou no caminho relativo do gatilho.

   Por exemplo, suponha que você deseja que a ação de resposta seja retornada `Postal Code: {postalCode}` .

   1. Na propriedade **corpo** , insira `Postal Code: ` com um espaço à direita. Mantenha o cursor dentro da caixa de edição para que a lista de conteúdo dinâmico permaneça aberta.

   1. Na lista de conteúdo dinâmico, na seção **quando uma solicitação HTTP é recebida** , selecione o token de **PostalCode** .

      ![Adicionar o parâmetro especificado ao corpo da resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      A propriedade **Body** agora inclui o parâmetro selecionado:

      ![Exemplo de corpo de resposta com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Salve seu aplicativo lógico.

   No gatilho de solicitação, a URL de retorno de chamada é atualizada e agora inclui o caminho relativo, por exemplo:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Para testar seu ponto de extremidade chamável, copie a URL de retorno de chamada atualizada do gatilho de solicitação, Cole a URL em outra janela do navegador, substitua `{postalCode}` na URL por `123456` e pressione Enter.

   O navegador retorna uma resposta com este texto: `Postal Code: 123456`

   ![Resposta de envio de solicitação para URL de retorno de chamada](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Se você quiser incluir o hash ou símbolo de libra ( **#** ) no URI, use esta versão codificada em vez disso: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Chamar aplicativo lógico por meio da URL do ponto de extremidade

Depois de criar o ponto de extremidade, você pode disparar o aplicativo lógico enviando uma solicitação HTTPS para a URL completa do ponto de extremidade. Os aplicativos lógicos têm suporte interno para pontos de extremidade de acesso direto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens gerados a partir do esquema

Quando você fornece um esquema JSON no gatilho de solicitação, o designer do aplicativo lógico gera tokens para as propriedades nesse esquema. Assim, você pode usar esses tokens para transmitir dados por meio do fluxo de trabalho do aplicativo lógico.

Por exemplo, se você adicionar mais propriedades, como `"suite"` , ao seu esquema JSON, os tokens para essas propriedades estarão disponíveis para uso nas etapas posteriores para seu aplicativo lógico. Veja a seguir o esquema JSON completo:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Criar aplicativos lógicos aninhados

Você pode aninhar fluxos de trabalho em seu aplicativo lógico adicionando outros aplicativos lógicos que podem receber solicitações. Para incluir esses aplicativos lógicos, siga estas etapas:

1. Na etapa em que você deseja chamar outro aplicativo lógico, selecione **nova etapa**  >  **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `logic apps` como o filtro. Na lista ações, selecione **escolher um fluxo de trabalho de aplicativos lógicos**.

   ![Aninhar aplicativo lógico dentro do aplicativo lógico atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   O designer mostra os aplicativos lógicos qualificados para você selecionar.

1. Selecione o aplicativo lógico a ser chamado do seu aplicativo lógico atual.

   ![Selecionar aplicativo lógico para chamar do aplicativo lógico atual](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Fazer referência ao conteúdo de uma solicitação de entrada

Se o tipo de conteúdo da solicitação de entrada for `application/json` , você poderá fazer referência às propriedades na solicitação de entrada. Caso contrário, esse conteúdo é tratado como uma única unidade binária que você pode passar para outras APIs. Para fazer referência a esse conteúdo dentro do fluxo de trabalho do aplicativo lógico, primeiro você precisa converter esse conteúdo.

Por exemplo, se você estiver passando o conteúdo que tem `application/xml` o tipo, poderá usar a [ `@xpath()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#xpath) para executar uma extração de XPath ou usar a [ `@json()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#json) para converter XML em JSON. Saiba mais sobre como trabalhar com [tipos de conteúdo](../logic-apps/logic-apps-content-type.md)com suporte.

Para obter a saída de uma solicitação de entrada, você pode usar a [ `@triggerOutputs` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Por exemplo, suponha que você tenha uma saída semelhante a este exemplo:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Para acessar especificamente a `body` propriedade, você pode usar a [ `@triggerBody()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) como um atalho.

## <a name="respond-to-requests"></a>Responder às solicitações

Às vezes, você deseja responder a determinadas solicitações que disparam o aplicativo lógico retornando o conteúdo ao chamador. Para construir o código de status, o cabeçalho e o corpo da sua resposta, use a ação de resposta. Essa ação pode aparecer em qualquer lugar no aplicativo lógico, não apenas no fim do fluxo de trabalho. Se seu aplicativo lógico não incluir uma ação de resposta, o ponto de extremidade responderá *imediatamente* com o status de **202 aceito** .

Para que o chamador original obtenha a resposta com êxito, todas as etapas necessárias para a resposta devem ser concluídas dentro do [limite de tempo limite da solicitação](./logic-apps-limits-and-config.md) , a menos que o aplicativo lógico disparado seja chamado como um aplicativo lógico aninhado. Se nenhuma resposta for retornada dentro desse limite, a solicitação de entrada atingirá o tempo limite e receberá a resposta de **tempo limite do cliente 408** .

Para aplicativos lógicos aninhados, o aplicativo lógico pai continua aguardando uma resposta até que todas as etapas sejam concluídas, independentemente da quantidade de tempo necessária.

### <a name="construct-the-response"></a>Construir a resposta

No corpo da resposta, você pode incluir vários cabeçalhos e qualquer tipo de conteúdo. Por exemplo, o cabeçalho dessa resposta especifica que o tipo de conteúdo da resposta é `application/json` e que o corpo contém valores para `town` as `postalCode` Propriedades e, com base no esquema JSON descrito anteriormente neste tópico para o gatilho de solicitação.

![Fornecer conteúdo de resposta para a ação de resposta HTTPS](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade (exibição) | Propriedade (JSON) | Descrição |
|--------------------|-----------------|-------------|
| **Código de status** | `statusCode` | O código de status HTTPS a ser usado na resposta para a solicitação de entrada. Este código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, não há permissão para códigos de status 3xx. |
| **Cabeçalhos** | `headers` | Um ou mais cabeçalhos a serem incluídos na resposta |
| **Corpo** | `body` | Um objeto Body que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo conteúdo binário referenciado de uma etapa anterior |
||||

Para exibir a definição de JSON para a ação de resposta e a definição JSON completa do aplicativo lógico, na barra de ferramentas do designer do aplicativo lógico, selecione **exibição de código**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Perguntas e Respostas

#### <a name="q-what-about-url-security"></a>P: O que dizer sobre a segurança de URL?

**R: o** Azure gera com segurança URLs de retorno de chamada de aplicativo lógico usando [SAS (assinatura de acesso compartilhado)](/rest/api/storageservices/delegate-access-with-shared-access-signature). Essa assinatura passa como um parâmetro de consulta e deve ser validada antes que seu aplicativo lógico possa ser executado. O Azure gera a assinatura usando uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que é executada. Portanto, a menos que alguém tenha acesso à chave secreta do aplicativo lógico, não é possível gerar uma assinatura válida.

> [!IMPORTANT]
> Para sistemas de produção e de maior segurança, aconselhamos fortemente a chamar seu aplicativo lógico diretamente do navegador por esses motivos:
>
> * A chave de acesso compartilhado é exibida na URL.
> * Você não pode gerenciar políticas de conteúdo de segurança devido a domínios compartilhados em clientes de aplicativos lógicos do Azure.

Para obter mais informações sobre segurança, autorização e criptografia para chamadas de entrada para seu aplicativo lógico, como o protocolo [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL), [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml), expondo seu aplicativo lógico com o gerenciamento de API do Azure ou restringindo os endereços IP que originam chamadas de entrada, consulte [acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

#### <a name="q-can-i-configure-callable-endpoints-further"></a>P: posso configurar os pontos de extremidade que podem ser chamados ainda mais?

**R**: Sim, os pontos de extremidade https dão suporte à configuração mais avançada por meio [do gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Esse serviço também oferece a capacidade de gerenciar todas as suas APIs de modo consistente, incluindo aplicativos lógicos, configurar os nomes de domínio personalizados, usar mais métodos de autenticação e mais, por exemplo:

* [Alterar o método de solicitação](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configurar seus domínios de gerenciamento de API no [portal do Azure](https://portal.azure.com/)
* Configurar a política para verificar a autenticação Básica

## <a name="next-steps"></a>Próximas etapas

* [Receber e responder a chamadas HTTPS de entrada usando aplicativos lógicos do Azure](../connectors/connectors-native-reqres.md)
* [Proteger o acesso e os dados no aplicativo lógico do Azure-acesso para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)

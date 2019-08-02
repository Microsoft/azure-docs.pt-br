---
title: Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP-aplicativos lógicos do Azure
description: Configurar pontos de extremidade HTTP para chamar, disparar ou aninhar fluxos de trabalho para Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: f93e90ef442740e4fb17f166023fbe3d5f0bae66
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875946"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-azure-logic-apps"></a>Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos do Azure

Você pode expor nativamente pontos de extremidade HTTP síncronos como gatilhos em aplicativos lógicos para que seja possível disparar ou chamar aplicativos lógicos por meio de uma URL. Também é possível aninhar fluxos de trabalho em aplicativos lógicos usando um padrão de pontos de extremidade resgatáveis.

Para criar pontos de extremidade HTTP, você pode adicionar esses gatilhos para que seus aplicativos lógicos possam receber solicitações de entrada:

* [Solicitação](../connectors/connectors-native-reqres.md)

* [Webhook de Conexão de API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Webhook HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Embora esses exemplos usem o gatilho **Solicitar**, você pode usar qualquer um dos gatilhos HTTP listados e todos os princípios se aplicam de modo idêntico a outros tipos de gatilho.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurar um ponto de extremidade HTTP para o aplicativo lógico

Para criar um ponto de extremidade HTTP, adicione um gatilho que possa receber solicitações de entrada.

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). Vá até o aplicativo lógico e abra o Designer do Aplicativo Lógico.

2. Adicione um gatilho que permita ao aplicativo lógico receber solicitações de entrada. Por exemplo, adicione o gatilho **Solicitação** em seu aplicativo lógico.

3.  Em **Esquema JSON do Corpo da Solicitação**, se desejar, você pode inserir um esquema JSON para o conteúdo que espera receber.

    O designer usa esse esquema para gerar tokens que o aplicativo lógico pode usar para consumir, analisar e transmitir dados do gatilho por meio do fluxo de trabalho. 
    Saiba mais sobre [tokens gerados de esquemas JSON](#generated-tokens).

    Para este exemplo, digite o esquema mostrado no designer:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Adicionar a ação Solicitar][1]

    > [!TIP]
    > 
    > Você pode gerar um esquema para um conteúdo JSON de exemplo usando uma ferramenta como [jsonschema.net](https://jsonschema.net/) ou no gatilho **Solicitar** escolhendo **Use o conteúdo de amostra para gerar o esquema**. 
    > Insira o conteúdo de exemplo e escolha **Concluído**.

    Por exemplo, este conteúdo de exemplo:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    gera este esquema:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Salve seu aplicativo lógico. Em **HTTP POST para esta URL**, agora você deve encontrar uma URL de retorno de chamada gerada, como neste exemplo:

    ![URL de retorno de chamada gerada para ponto de extremidade](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Essa URL contém uma chave de SAS (Assinatura de Acesso Compartilhado) nos parâmetros de consulta usados para autenticação. 
    Você também pode obter a URL de ponto de extremidade HTTP da visão geral do aplicativo lógico no portal do Azure. Em **Histórico de Gatilho**, selecione o gatilho:

    ![Obter a URL de ponto de extremidade HTTP no portal do Azure][2]

    Ou você pode obter a URL fazendo esta chamada:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Alterar o método HTTP para o gatilho

Por padrão, o gatilho **Solicitar** espera uma solicitação HTTP POST, mas você pode usar um método HTTP diferente. 

> [!NOTE]
> Você pode especificar somente um tipo de método.

1. No gatilho **Solicitar**, escolha **Mostrar opções avançadas**.

2. Abra a lista **Método**. Para este exemplo, selecione **GET** para que você possa testar posteriormente sua URL de ponto de extremidade HTTP.

    > [!NOTE]
    > É possível selecionar qualquer outro método HTTP ou especificar um método personalizado para seu próprio aplicativo lógico.

    ![Alterar método HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Aceitar parâmetros por meio da URL de ponto de extremidade HTTP

Quando desejar que a URL de ponto de extremidade HTTP aceite parâmetros, personalize o caminho relativo do gatilho.

1. No gatilho **Solicitar**, escolha **Mostrar opções avançadas**. 

2. Em **Método**, especifique o método HTTP que deseja que sua solicitação use. Para este exemplo, selecione o método **GET**, se ainda não o fez, para que seja possível testar a URL de ponto de extremidade HTTP.

      > [!NOTE]
      > Ao especificar um caminho relativo para o gatilho, você deve especificar explicitamente um método HTTP para o gatilho.

3. Em **Caminho relativo**, especifique o caminho relativo para o parâmetro que sua URL deve aceitar, por exemplo, `customers/{customerID}`.

    ![Especificar o método HTTP e o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relativeurl.png)

4. Para usar o parâmetro, adicione uma ação **Resposta** ao aplicativo lógico. (No gatilho, escolha **Nova etapa** > **Adicionar uma ação** > **Resposta**) 

5. No **Corpo** da resposta, inclua o token para o parâmetro que você especificou no caminho relativo do gatilho.

    Por exemplo, para retornar `Hello {customerID}`, atualize o **Corpo** da resposta com `Hello {customerID token}`. 
    A lista de conteúdo dinâmico deve aparecer e mostrar o token `customerID` para seleção.

    ![Adicionar parâmetro ao corpo da resposta](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    O **Corpo** deve se parecer com este exemplo:

    ![Corpo de resposta com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Salve seu aplicativo lógico. 

    A URL de ponto de extremidade HTTP agora inclui o caminho relativo, por exemplo: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Para testar o ponto de extremidade HTTP, copie e cole a URL atualizada em outra janela do navegador, mas substitua `{customerID}` por `123456` e pressione Enter.

    O navegador agora deve mostrar este texto: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens gerados de esquemas JSON para o aplicativo lógico

Quando você fornece um esquema JSON no gatilho **Solicitar**, o Designer de Aplicativo Lógico gera tokens para propriedades nesse esquema. Assim, você pode usar esses tokens para transmitir dados por meio do fluxo de trabalho do aplicativo lógico.

Para este exemplo, se você adicionar as propriedades `title` e `name` ao esquema JSON, seus tokens estarão disponíveis para uso nas etapas posteriores do fluxo de trabalho. 

Veja a seguir o esquema JSON completo:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Criar fluxos de trabalho aninhados para aplicativos lógicos

Você pode aninhar os fluxos de trabalho no aplicativo lógico adicionando outros aplicativos lógicos que podem receber solicitações. Para incluir esses aplicativos lógicos, adicione a ação **Aplicativos Lógicos do Azure – Escolha um fluxo de trabalho de Aplicativos Lógicos** ao gatilho. Você pode selecionar dentre aplicativos lógicos qualificados.

![Adicionar outro aplicativo lógico](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Chamar ou disparar aplicativos lógicos por meio de pontos de extremidade HTTP

Depois de criar o ponto de extremidade HTTP, é possível disparar o aplicativo lógico por meio de um método `POST` para a URL completa. Os aplicativos lógicos têm suporte interno para pontos de extremidade de acesso direto.

> [!NOTE] 
> Para executar um aplicativo lógico manualmente a qualquer momento, na barra de ferramentas do Designer do Aplicativo Lógico ou Modo de exibição de código do Aplicativo Lógico, escolha **executar**.

## <a name="reference-content-from-an-incoming-request"></a>Fazer referência ao conteúdo de uma solicitação de entrada

Se o tipo do conteúdo for `application/json`, você poderá fazer referência às propriedades da solicitação de entrada. Caso contrário, o conteúdo será tratado como uma única unidade binária que você pode passar para outras APIs. Para fazer referência a esse conteúdo no fluxo de trabalho, você deve converter esse conteúdo. Por exemplo, se transmitir o conteúdo de `application/xml`, você poderá usar `@xpath()` para uma extração de XPath ou `@json()` para converter XML em JSON. Saiba mais sobre [como trabalhar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

Para obter a saída de uma solicitação de entrada, você poderá usar a função `@triggerOutputs()`. A saída pode se parecer com este exemplo:

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

Para acessar a propriedade `body` de forma específica, você pode usar o atalho `@triggerBody()`. 

## <a name="respond-to-requests"></a>Responder às solicitações

Talvez você queira responder a determinadas solicitações que iniciam um aplicativo lógico retornando conteúdo a outro chamador. Para construir o código de status, o cabeçalho e o corpo da resposta, você pode usar a ação **Resposta**. Essa ação pode aparecer em qualquer lugar no aplicativo lógico, não apenas no fim do fluxo de trabalho.

> [!NOTE] 
> Se o aplicativo lógico não incluir uma **Resposta**, o ponto de extremidade HTTP responderá *imediatamente* com um status **202 Aceito**. Além disso, para a solicitação original obter a resposta, todas as etapas exigidas para a resposta devem ser finalizadas dentro do [tempo limite da solicitação](./logic-apps-limits-and-config.md), a menos que você chame o fluxo de trabalho como um aplicativo lógico aninhado. Se não houver resposta dentro desse limite, a solicitação de entrada atingirá o tempo limite e receberá a resposta HTTP **408 Tempo limite de cliente**. Para aplicativos lógicos aninhados, o aplicativo lógico pai continuará a aguardar uma resposta até a conclusão, independentemente de quanto tempo for necessário.

### <a name="construct-the-response"></a>Construir a resposta

Você pode incluir mais de um cabeçalho e qualquer tipo de conteúdo no corpo da resposta. Na resposta de exemplo, o cabeçalho especifica que a resposta tem o tipo de conteúdo `application/json`. E o corpo contém `title` e `name`, com base no esquema JSON atualizado anteriormente para o gatilho **Solicitar**.

![Ação Resposta HTTP][3]

As respostas têm estas propriedades:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| statusCode |Especifica o código de status HTTP para responder à solicitação de entrada. Este código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, não há permissão para códigos de status 3xx. |
| headers |Define qualquer número de cabeçalhos a serem incluídos na resposta. |
| Corpo |Especifica um objeto de corpo que pode ser uma cadeia de caracteres, um objeto JSON ou, até mesmo, o conteúdo binário referenciado em uma etapa anterior. |

Veja a seguir como o esquema JSON se parece agora para a ação **Resposta**:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Para exibir a definição completa de JSON para seu aplicativo lógico, no Designer de Aplicativo Lógico, escolha **Exibição de código**.

## <a name="q--a"></a>Perguntas e respostas

#### <a name="q-what-about-url-security"></a>P: E quanto à segurança de URL?

R: O Azure gera com segurança URLs de retorno de chamada do aplicativo lógico usando uma SAS (assinatura de acesso compartilhado). Essa assinatura é transmitida como um parâmetro de consulta e deve ser validada antes do aplicativo lógico ser acionado. O Azure gera a assinatura usando uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que é executada. Portanto, a menos que alguém tenha acesso à chave secreta do aplicativo lógico, não é possível gerar uma assinatura válida.

   > [!IMPORTANT]
   > Para sistemas seguros e de produção, é altamente recomendável não chamar o aplicativo lógico de chamada diretamente do navegador porque:
   > 
   > * A chave de acesso compartilhado é exibida na URL.
   > * Você não pode gerenciar políticas de conteúdo seguras devido a domínios compartilhados entre clientes de Aplicativos Lógicos.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: Posso configurar pontos de extremidade HTTP ainda mais?

R: Sim, os pontos de extremidade HTTP dão suporte à configuração mais avançada por meio do [**Gerenciamento de API**](../api-management/api-management-key-concepts.md). Esse serviço também oferece a capacidade de gerenciar todas as suas APIs de modo consistente, incluindo aplicativos lógicos, configurar os nomes de domínio personalizados, usar mais métodos de autenticação e mais, por exemplo:

* [Alterar o método de solicitação](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configurar os domínios de Gerenciamento de API no [portal do Azure](https://portal.azure.com/ "portal do Azure")
* Configurar a política para verificar a autenticação Básica

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: O que mudou quando o esquema migrou da versão prévia de 1º de dezembro de 2014?

R: Aqui está um resumo sobre essas alterações:

| Visualização de 1º de dezembro de 2014 | 1º de junho de 2016 |
| --- | --- |
| Clique no aplicativo de API **Ouvinte HTTP** |Clique em **Gatilho manual** (nenhum aplicativo de API é necessário) |
| Configuração “*Envia a resposta automaticamente*” do Ouvinte HTTP |Inclua uma ação **Resposta** ou não na definição do fluxo de trabalho |
| Configure a autenticação Básica ou OAuth |por meio do Gerenciamento de API |
| Configurar o método HTTP |Em **Mostrar opções avançadas**, escolha um método HTTP |
| Configurar o caminho relativo |Em **Mostrar opções avançadas**, adicione um caminho relativo |
| Fazer referência ao corpo de entrada por meio de `@triggerOutputs().body.Content` |Fazer referência por meio de `@triggerOutputs().body` |
| **Enviar resposta HTTP** no Ouvinte HTTP |Clique em **Responder à solicitação HTTP** (nenhum aplicativo de API é necessário) |

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Criar definições de aplicativo lógico](./logic-apps-author-definitions.md)
* [Processar erros e exceções](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png
---
title: Adicionar e chamar funções de aplicativos lógicos do Azure
description: Chamar e executar código personalizado em funções feitas no Azure de tarefas e fluxos de trabalho automatizados em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791872"
---
# <a name="call-functions-from-azure-logic-apps"></a>Chamar funções de aplicativos lógicos do Azure

Quando você deseja executar um código que executa um trabalho específico em seus aplicativos lógicos, pode criar sua própria função com [Azure Functions](../azure-functions/functions-overview.md). Esse serviço ajuda a criar as funções do Node.js, C# e F# para que você não precise criar um aplicativo ou uma infraestrutura completa para executar o código. Você também pode [chamar aplicativos lógicos de funções internas](#call-logic-app). O Azure Functions fornece computação sem servidor na nuvem e é útil para executar tarefas, como estes exemplos:

* Estenda o comportamento do seu aplicativo lógico com funções em Node. js ou C#.
* Execute cálculos em seu fluxo de trabalho do aplicativo lógico.
* Aplicar a formatação avançada ou computação de campos em seus aplicativos lógicos.

Para executar trechos de código sem usar Azure Functions, saiba como [Adicionar e executar código embutido](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A integração entre os Aplicativos Lógicos e o Azure Functions atualmente não funciona com os Slots habilitados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo de funções, que é um contêiner para uma função que é criada no Azure Functions, juntamente com a função que você cria. Se você não tiver um aplicativo de funções, [crie primeiro o aplicativo de função](../azure-functions/functions-get-started.md). Em seguida, você pode criar sua função fora do seu aplicativo lógico no portal do Azure ou [de dentro de seu aplicativo lógico](#create-function-designer) no Designer do Aplicativo Lógico.

* Ao trabalhar com aplicativos lógicos, os mesmos requisitos se aplicam aos aplicativos de funções e às funções, existentes ou novos:

  * Seu aplicativo de funções e aplicativo lógico devem usar a mesma assinatura do Azure.

  * Os novos aplicativos de funções devem usar o .NET ou o JavaScript como a pilha de runtime. Quando você adiciona uma nova função aos aplicativos de funções existentes, pode selecionar C# ou JavaScript.

  * Sua função usa o modelo do **gatilho HTTP**.

    Esse modelo de gatilho HTTP pode aceitar o conteúdo que tenha o tipo `application/json` do seu aplicativo lógico. Quando você adiciona uma função ao seu aplicativo lógico, o designer do aplicativo lógico mostra as funções personalizadas que são criadas a partir desse modelo em sua assinatura do Azure.

  * Sua função não usa rotas personalizadas, a menos que você tenha feito uma [definição de OpenAPI](../azure-functions/functions-openapi-definition.md) (anteriormente conhecida como [arquivo do Swagger](https://swagger.io/)).

  * Se você tem uma definição de OpenAPI para sua função, o Designer dos Aplicativos Lógicos fornece uma experiência melhor para trabalhar com os parâmetros da função. Antes de seu aplicativo lógico pode localizar e acessar funções que têm definições de OpenAPI, [configurar seu aplicativo de função seguindo estas etapas](#function-swagger).

* O aplicativo lógico no qual você deseja adicionar a função, incluindo uma [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico

  Antes de adicionar ações que podem executar funções, seu aplicativo lógico deve começar com um gatilho. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Localizar funções que têm descrições de OpenAPI

Para ter uma experiência melhor quando você trabalha com parâmetros de função no Designer de Aplicativos Lógicos, [gere uma definição de OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [arquivo do Swagger](https://swagger.io/), para sua função. Para configurar seu aplicativo de função, para que seu aplicativo lógico possa encontrar e usar funções com descrições de Swagger, siga estas etapas:

1. Verifique se que seu aplicativo de funções está sendo executado ativamente.

1. No aplicativo de funções, configure o [CORS (Cross-Origin Resource Sharing)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estas etapas:

   1. Na lista **Aplicativos de Funções**, selecione seu aplicativo de funções. No painel à direita, selecione **Recursos da plataforma** > **CORS**.

      ![Selecione seu aplicativo de função> "Recursos da plataforma"> "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Em **CORS**, adicione o caractere curinga asterisco ( **`*`** ), mas remova todas as outras origens na lista e selecione **salvar**.

      ![Defina "CORS* como o caractere curinga "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Valores de propriedade de acesso dentro de solicitações HTTP

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

1. A função agora pode acessar a propriedade `date` por meio da variável `data` e converter esse valor de propriedade do tipo DateTime para o tipo DateString chamando a função `ToDateString()`. A função também retorna o resultado através da propriedade `body` na resposta da função:

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que você criou sua função no Azure, siga as etapas para [adicionar funções a aplicativos lógicos](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicativos lógicos

Você pode criar funções diretamente do fluxo de trabalho do seu aplicativo lógico usando a ação de Azure Functions interna no designer do aplicativo lógico, mas você pode usar esse método somente para funções escritas em JavaScript. Para outros idiomas, você pode criar funções por meio da experiência de Azure Functions no portal do Azure. Para obter mais informações, consulte [Criar sua primeira função no portal do Azure](../azure-functions/functions-get-started.md).

No entanto, antes de poder criar sua função no Azure, você já deve ter um aplicativo de funções, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, crie primeiro o aplicativo de função. Veja [Crie sua primeira função no portal do Azure](../azure-functions/functions-get-started.md).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para criar e adicionar sua função, siga a etapa que se aplica ao seu cenário:

   * Na última etapa no fluxo de trabalho do aplicativo lógico, selecione **Nova etapa**.

   * Entre as etapas existentes no fluxo de trabalho do aplicativo lógico, mova o mouse sobre a seta, escolha o sinal de adição (+) e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "funções azure" como seu filtro. Na lista de ações, selecione a ação **Escolher uma função do Azure**, por exemplo:

   ![Localizar funções no portal do Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de ações for aberta, selecione esta ação: **criar nova função**.

   ![Selecione seu aplicativo de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. No editor de definição de função, defina sua função:

   1. Na caixa **Nome da função**, forneça um nome para sua função.

   1. Na caixa **Código**, adicione seu código ao modelo da função, incluindo a resposta e o conteúdo que deseja retornados para seu aplicativo lógico depois de a função terminar de ser executada. Quando terminar, selecione **Criar**.

   Por exemplo:

   ![Definir sua função](./media/logic-apps-azure-functions/add-code-function-definition.png)

   No código do modelo, o *`context` objeto* refere-se à mensagem que seu aplicativo lógico envia por meio do campo **Corpo da solicitação** em uma etapa posterior. Para acessar as propriedades do objeto `context` de dentro de sua função, use a seguinte sintaxe:

   `context.body.<property-name>`

   Por exemplo, para fazer referência à propriedade `content` dentro do objeto `context`, use a seguinte sintaxe:

   `context.body.content`

   O código de modelo também inclui uma variável `input`, que armazena o valor do parâmetro `data` para que sua função possa executar operações nesse valor. Dentro de funções de JavaScript, a variável `data` também é um atalho para `context.body`.

   > [!NOTE]
   > A propriedade `body` aqui se aplica ao objeto `context` e não é igual ao token **Corpo** a partir de uma saída da ação, que você também pode passar para sua função.

1. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **Corpo da Solicitação**, a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens de saídas de etapas anteriores. Este exemplo especifica que o conteúdo do contexto contém uma propriedade chamada `content` que tem o valor do token **De** a partir do gatilho de email.

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Portanto, se o exemplo usou o token **Hora de Recebimento**, você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando aspas duplas.

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a usar, os cabeçalhos de solicitação, os parâmetros de consulta ou a autenticação, abra a lista **Adicionar novo parâmetro** e selecione as opções desejadas. Para a autenticação, suas opções são diferentes com base na função selecionada. Consulte [habilitar autenticação para funções](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes para aplicativos lógicos

Para chamar funções existentes de seus aplicativos lógicos, você pode adicionar funções como qualquer outra ação no designer de aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Na etapa em que você deseja adicionar a função, selecione **Nova etapa**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira "funções do azure" como o filtro. Na lista de ações, selecione a ação **Escolher uma função do Azure**.

   ![Encontre uma função no Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de funções aparecer, selecione sua função.

   ![Selecione seu aplicativo de funções e função](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para as funções que têm definições de API (descrições de Swagger) e são [configuradas para que seu aplicativo lógico possa encontrar e acessar essas funções](#function-swagger), é possível selecionar **Ações de Swagger**.

   ![Selecione seu aplicativo de funções, "ações do Swagger" e sua função](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Ao clicar no campo **Corpo da Solicitação**, a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens para as saídas das etapas anteriores. Este exemplo especifica que o conteúdo do contexto contém uma propriedade chamada `content` que tem o valor do token **De** a partir do gatilho de email.

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Portanto, se este exemplo tiver usado o token **Hora de Recebimento**, você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando marcas de aspas duplas:

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a usar, os cabeçalhos de solicitação, os parâmetros de consulta ou a autenticação, abra a lista **Adicionar novo parâmetro** e selecione as opções desejadas. Para a autenticação, suas opções são diferentes com base na função selecionada. Consulte [habilitar autenticação em funções](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Chamar aplicativos lógicos de funções

Quando você deseja disparar um aplicativo lógico de dentro de uma função, o aplicativo lógico deve começar com um gatilho que fornece um ponto de extremidade que pode ser chamado. Por exemplo, você pode iniciar o aplicativo lógico com o acionador **HTTP**, **Solicitação**, **Filas do Azure** ou **Grade de Eventos**. Dentro de sua função, envie uma solicitação HTTP POST para a URL do gatilho e inclua o conteúdo que você deseja que esse aplicativo lógico processe. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>Habilitar autenticação para funções

Para acessar facilmente outros recursos protegidos pelo Azure AD (Azure Active Directory) sem precisar entrar e fornecer credenciais ou segredos, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerenciada ou MSI). O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos. Saiba mais sobre os [serviços do Azure que dão suporte a identidades gerenciadas para a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Se você configurar seu aplicativo lógico para usar a identidade atribuída pelo sistema ou uma identidade atribuída manualmente, a função em seu aplicativo lógico também poderá usar essa mesma identidade para autenticação. Para obter mais informações sobre o suporte de autenticação para funções em aplicativos lógicos, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Para configurar e usar a identidade gerenciada com sua função, siga estas etapas:

1. Habilite a identidade gerenciada em seu aplicativo lógico e configure o acesso dessa identidade para o recurso de destino. Consulte [Autenticar o acesso aos recursos do Azure usando identidades gerenciadas nos Aplicativos Lógicos do Azure](../logic-apps/create-managed-service-identity.md).

1. Habilite a autenticação em sua função e aplicativo de funções seguindo estas etapas:

   * [Configurar a autenticação anônima em sua função](#set-authentication-function-app)
   * [Configurar a autenticação do Azure AD em seu aplicativo de funções](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configurar a autenticação anônima em sua função

Para usar a identidade gerenciada do aplicativo lógico em sua função, você deve definir o nível de autenticação da função como anônimo. Caso contrário, seu aplicativo lógico irá gerar um erro "BadRequest".

1. No [portal do Azure](https://portal.azure.com), encontre e selecione seu aplicativo de funções. Essas etapas usam "FabrikamFunctionApp" como o aplicativo de funções de exemplo.

1. No painel do aplicativo de funções, selecione **Recursos da plataforma**. Em **Ferramentas de desenvolvimento**, selecione **Ferramentas avançadas (Kudu)** .

   ![Abrir ferramentas avançadas para Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na barra de título do site Kudu, no menu **Console de Depuração**, selecione **CMD**.

   ![No menu do console de depuração, selecione a opção "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Depois que a próxima página for exibida, na lista de pastas, selecione **site** > **wwwroot** > *sua-função*. Essas etapas usam "FabrikamAzureFunction" como a função de exemplo.

   ![Selecione "site" > "wwwroot" > sua-função](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Abra o arquivo `function.json` para edição.

   ![Clique em Editar para o arquivo "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. No objeto `bindings`, verifique se a propriedade `authLevel` existe. Se existir, defina o valor da propriedade para `anonymous`. Caso contrário, adicione essa propriedade e defina o valor.

   ![Adicione a propriedade "authLevel" e defina para "anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Quando terminar, salve suas configurações e continue na próxima seção.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurar a autenticação do Azure AD em seu aplicativo de funções

Antes de iniciar essa tarefa, localize e reserve esses valores para um uso posterior:

* A ID do objeto gerada para a identidade atribuída pelo sistema que representa seu aplicativo lógico

  * Para gerar essa ID do objeto, [habilite a identidade atribuída pelo sistema do aplicativo lógico](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Caso contrário, para localizar essa ID do objeto, abra seu aplicativo lógico no Designer de Aplicativo Lógico. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade** > **Sistema atribuído**.

* A ID do diretório para seu locatário no Azure AD (Azure Active Directory)

  Para obter a ID do diretório do locatário, você pode executar o comando do PowerShell [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount). No portal do Azure, siga estas etapas:

  1. No [portal do Azure](https://portal.azure.com), encontre e selecione seu aplicativo de funções.

  1. Localize e selecione seu locatário do Azure AD. Essas etapas usam "Fabrikam" como o locatário de exemplo.

  1. No menu do locatário, em **Gerenciar**, selecione **Propriedades**.

  1. Copie a ID do diretório do locatário, por exemplo, e salve essa ID para um uso posterior.

     ![Localizar e copiar a ID do diretório do locatário do Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* A ID do recurso de destino que você deseja acessar

  * Para encontrar essas IDs do recurso, confira os [Serviços do Azure que dão suporte ao Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Essa ID do recurso deve corresponder exatamente ao valor esperado pelo Azure AD, incluindo as barras à direita necessárias.

  Essa ID do recurso também tem o mesmo valor que você usa posteriormente na propriedade **Audience** quando [configura sua ação de função para usar a identidade atribuída pelo sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Agora você está pronto para configurar a autenticação do Azure AD para seu aplicativo de funções.

1. No [portal do Azure](https://portal.azure.com), encontre e selecione seu aplicativo de funções.

1. No painel do aplicativo de funções, selecione **Recursos da plataforma**. Em **Rede**, selecione **Autenticação/Autorização**.

   ![Exibir configurações de autenticação e autorização](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Altere a configuração **Autenticação do Serviço de Aplicativo** para **Ativado**. Na lista **Ação a tomar quando a solicitação não está autenticada**, selecione **Efetuar login com o Azure Active Directory**. Em **Provedores de Autenticação**, selecione **Azure Active Directory**.

   ![Ativar a autenticação com o Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Na página **Configurações do Azure Active Directory**, siga estas etapas:

   1. Defina o **Modo de gerenciamento** para **Avançado**.

   1. Na propriedade **Client ID**, insira a ID do objeto para a identidade atribuída pelo sistema do seu aplicativo lógico.

   1. Na propriedade **Issuer Url**, insira a `https://sts.windows.net/` URL e anexe a ID do diretório do seu locatário do Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Na propriedade **Allowed Token Audiences**, insira a ID do recurso de destino que você deseja acessar.

      Essa ID do recurso tem o mesmo valor que você usa posteriormente na propriedade **Audience** quando [configura sua ação de função para usar a identidade atribuída pelo sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Neste ponto, sua versão é semelhante a este exemplo:

   ![Configurações de autenticação do Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Quando terminar, selecione **OK**.

1. Retorne ao Designer de Aplicativo Lógico e siga as [etapas para autenticar o acesso com a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
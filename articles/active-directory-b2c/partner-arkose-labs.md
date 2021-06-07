---
title: Tutorial para configurar o Azure Active Directory B2C com o arkose Labs
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Active Directory B2C com o arkose Labs para identificar usuários arriscados e fraudulentos
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c2aceedd57bcc7cd88c4e822c7b696e36b28bd8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579783"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial: configurar o arkose Labs com o Azure Active Directory B2C

Neste tutorial de exemplo, saiba como integrar a autenticação B2C do Azure Active Directory (AD) com o [arkose Labs](https://www.arkoselabs.com/). O arkose Labs ajuda as organizações contra ataques de bot, ataques de tomada de conta e aberturas de contas fraudulentas.  

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- Uma conta do [arkose Labs](https://www.arkoselabs.com/book-a-demo/) .

## <a name="scenario-description"></a>Descrição do cenário

A integração do arkose Labs inclui os seguintes componentes:

- **Arkose Labs** -um serviço de fraude e abuso para proteção contra bots e outros abusos automatizados.

- **Fluxo de usuário de inscrição Azure ad B2C** -a experiência de inscrição que usará o serviço arkose Labs. O usará o HTML personalizado e o JavaScript, e os conectores de API para integração com o serviço do arkose Labs.

- **Azure Functions** – ponto de extremidade de API hospedado por você que funciona com o recurso conectores de API. Essa API é responsável por fazer a validação no lado do servidor do token de sessão do arkose Labs.

O diagrama a seguir descreve como o arkose Labs se integra ao Azure AD B2C.

![Imagem mostra o diagrama da arquitetura do arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Etapa  | Descrição |
|---|---|
|1     | Um usuário se inscreve e cria uma conta. Quando o usuário seleciona Enviar, um desafio de imposição do arkose Labs é exibido.         |
|2     |  Depois que o usuário concluir o desafio, Azure AD B2C enviará o status para o arkose Labs para gerar um token. |
|3     |  O arkose Labs gera um token e o envia de volta para o Azure AD B2C.   |
|4     |  Azure AD B2C chama uma API da Web intermediária para passar o formulário de inscrição.      |
|5     |  A API da Web intermediária envia o formulário de inscrição para o arkose Lab para verificação de token.    |
|6     | O arkose Lab processa e envia os resultados de verificação de volta para a API da Web intermediária.|
|7     | A API da Web intermediária envia o resultado de êxito ou falha do desafio para Azure AD B2C. |
|8     | Se o desafio for concluído com êxito, um formulário de inscrição será enviado para Azure AD B2C e Azure AD B2C concluirá a autenticação.|

## <a name="onboard-with-arkose-labs"></a>Integração com o arkose Labs

1. Entre em contato com o [arkose](https://www.arkoselabs.com/book-a-demo/) e crie uma conta.

2. Depois que a conta for criada, navegue até https://dashboard.arkoselabs.com/login  

3. No painel, navegue até configurações de site para localizar sua chave pública e chave privada. Essas informações serão necessárias posteriormente para configurar Azure AD B2C. Os valores de chaves públicas e privadas são chamados de `ARKOSE_PUBLIC_KEY` e `ARKOSE_PRIVATE_KEY` no código de [exemplo](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Parte 1 – criar um atributo personalizado ArkoseSessionToken

Para criar um atributo personalizado, siga estas etapas:  

1. Ir para **portal do Azure**  >  **Azure ad B2C**

2. Selecionar **atributos de usuário**

3. Selecione **Adicionar**

4. Insira **ArkoseSessionToken** como o nome do atributo

5. Escolha **Criar**

Saiba mais sobre [atributos personalizados](./user-flow-custom-attributes.md?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Parte 2-criar um fluxo de usuário

O fluxo do usuário pode ser para **inscrição** e **entrada** ou apenas para **inscrição**. O fluxo de usuário do arkose Labs só será mostrado durante a inscrição.

1. Consulte as [instruções](./tutorial-create-user-flows.md) para criar um fluxo de usuário. Se estiver usando um fluxo de usuário existente, ele deverá ser do tipo de versão **recomendado (visualização de próxima geração)** .

2. Nas configurações de fluxo de usuário, vá para **atributos de usuário** e selecione a Declaração **ArkoseSessionToken** .

![Imagem mostra como selecionar atributos personalizados](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Parte 3-configurar layouts de página, JavaScript e HTML personalizados

Vá para o [script HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)fornecido. O arquivo contém um modelo HTML com `<script>` marcas JavaScript que fará três coisas:

1. Carregue o script do arkose Labs, que renderiza o widget do arkose Labs e faz a validação do arkose Labs do lado do cliente.

2. Oculte o `extension_ArkoseSessionToken` elemento de entrada e o rótulo, correspondentes ao `ArkoseSessionToken` atributo personalizado, da interface do usuário mostrada para ele.

3. Quando um usuário conclui o desafio do arkose Labs, o arkose Labs verifica a resposta do usuário e gera um token. O retorno de chamada `arkoseCallback` no JavaScript personalizado define o valor de `extension_ArkoseSessionToken` para o valor de token gerado. Esse valor será enviado para o ponto de extremidade da API, conforme descrito na próxima seção.

    Consulte [Este artigo](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) para saber mais sobre a validação do lado do cliente do arkose Labs.

Siga as etapas mencionadas para usar o HTML personalizado e JavaScript para seu fluxo de usuário.

1. Modifique [selfAsserted.htmarquivo l](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) para que `<ARKOSE_PUBLIC_KEY>` corresponda ao valor gerado para a validação do lado do cliente e usado para carregar o script do arkose Labs para sua conta.

2. Hospede a página HTML em um ponto de extremidade da Web habilitado para CORS (compartilhamento de recursos entre origens). [Crie uma conta de armazenamento de BLOBs do Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [Configure o CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Se você tiver seu próprio HTML personalizado, copie e cole os `<script>` elementos em sua página HTML.

3. Siga estas etapas para configurar os layouts de página

   a. Na portal do Azure, acesse **Azure ad B2C**

   b. Navegue até **fluxos de usuário** e selecione seu fluxo de usuário

   c. Selecionar **layouts de página**

   d. Selecionar **layout de página de inscrição de conta local**

   e. Alternar **usar conteúdo da página personalizada** para **Sim**

   f. Cole o URI em que seu HTML personalizado reside em **usar conteúdo de página personalizada**

   (por exemplo, Se você estiver usando provedores de identidade social, repita a **etapa e** e **f** para o layout de **página de inscrição de conta social** .

   ![imagem mostrando layouts de página](media/partner-arkose-labs/page-layouts.png)

4. Em seu fluxo de usuário, vá para **Propriedades** e selecione Habilitar o layout de página **ativar JavaScript** (visualização). Consulte este [artigo](./javascript-and-page-layout.md?pivots=b2c-user-flow) para saber mais.

### <a name="part-4---create-and-deploy-your-api"></a>Parte 4-criar e implantar sua API

Instale a [extensão de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

>[!Note]
>As etapas mencionadas nesta seção pressupõem que você esteja usando Visual Studio Code para implantar a função do Azure. Você também pode usar portal do Azure, terminal ou prompt de comando ou qualquer outro editor de código para implantar.

#### <a name="run-the-api-locally"></a>Executar a API localmente

1. Navegue até a extensão do Azure no Visual Studio Code na barra de navegação à esquerda. Selecione a pasta do **projeto local** que representa sua função local do Azure.

2. Pressione **F5** ou use o menu **depurar**  >  **Iniciar Depuração** para iniciar o depurador e anexar ao host Azure functions. Esse comando usa automaticamente a configuração de depuração única que a função do Azure criou.

3. A extensão de função do Azure gerará automaticamente alguns arquivos para desenvolvimento local, instalar dependências e instalar as ferramentas básicas de função se ainda não estiverem presentes. Essas ferramentas ajudam na experiência de depuração.

4. A saída da ferramenta de núcleo de função aparece no painel Visual Studio Code **terminal** . Depois que o host for iniciado, **Alt + clique** na URL local mostrada na saída para abrir o navegador e executar a função. No Azure Functions Explorer, clique com o botão direito do mouse na função para ver a URL da função hospedada localmente.

Para reimplantar a instância local durante o teste, repita as etapas de 1 a 4.

#### <a name="add-environment-variables"></a>Adicionar variáveis de ambiente

Este exemplo protege o ponto de extremidade da API Web usando a [autenticação básica http](https://tools.ietf.org/html/rfc7617).

O nome de usuário e a senha são armazenados como variáveis de ambiente e não como parte do repositório. Consulte [local.settings.jsno](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) arquivo para obter mais informações.

1. Criar um local.settings.jsno arquivo na pasta raiz

2. Copie e cole o código abaixo no arquivo:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Os valores de **BASIC_AUTH_USERNAME** e **BASIC_AUTH_PASSWORD** serão as credenciais usadas para autenticar a chamada à API para sua função do Azure. Escolha os valores desejados.

O `<ARKOSE_PRIVATE_KEY>` é o segredo do lado do servidor que você gerou no serviço do arkose Labs. Ele é usado para chamar a [API de validação do lado do servidor do arkose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) para validar o valor do `ArkoseSessionToken` gerado pelo front-end.

O `<B2C_EXTENSIONS_APP_ID>` é a ID do aplicativo usada pelo Azure ad B2C para armazenar atributos personalizados no diretório. Você pode encontrar essa ID de aplicativo navegando até Registros de aplicativo, procurando por B2C-Extensions-app e copiando a ID do aplicativo (cliente) no painel **visão geral** . Remova os `-` caracteres.

![Imagem mostra a pesquisa por ID do aplicativo](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Implantar o aplicativo na Web

1. Siga as etapas mencionadas [neste guia para](/azure/javascript/tutorial-vscode-serverless-node-04) implantar sua função do Azure na nuvem. Copie a URL da Web do ponto de extremidade de sua função do Azure.

2. Depois de implantado, selecione a opção **carregar configurações** . Ele carregará suas variáveis de ambiente nas [configurações do aplicativo](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) do serviço de aplicativo. Essas configurações de aplicativo também podem ser configuradas ou [gerenciadas por meio do portal do Azure.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Consulte [Este artigo](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) para saber mais sobre o desenvolvimento de Visual Studio Code para Azure functions.

#### <a name="configure-and-enable-the-api-connector"></a>Configurar e habilitar o conector de API

[Crie um conector de API](./add-api-connector.md) e habilite-o para seu fluxo de usuário. A configuração do conector de API deve ser semelhante a:

![Imagem mostra como configurar o conector de API](media/partner-arkose-labs/configure-api-connector.png)

- **URL do ponto de extremidade** -é a URL da função que você copiou anteriormente enquanto implantou o Azure function.

- **Nome de usuário e senha** – o nome de usuário e a senha que você definiu como variáveis de ambiente anteriormente.

Para habilitar o conector de API, nas configurações do **conector de API** para seu fluxo de usuário, selecione o conector de API a ser invocado no **antes de criar a etapa do usuário** . Isso invocará a API quando um usuário selecionar **criar** no fluxo de inscrição. A API fará uma validação no lado do servidor do `ArkoseSessionToken` valor, que foi definido pelo retorno de chamada do widget arkose `arkoseCallback` .

![Imagem mostra habilitar o conector de API](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **fluxos de usuário**.

2. Selecione o fluxo de usuário criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. Aplicativo: selecione o aplicativo registrado (exemplo é JWT)

   b. URL de resposta: selecione a URL de redirecionamento

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. Sair

6. Percorrer o fluxo de entrada  

7. Um quebra-cabeça do arkose Labs será exibido depois que você selecionar **continuar**.

## <a name="additional-resources"></a>Recursos adicionais

- [Códigos de exemplo](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) para Azure ad B2C fluxo de usuário de inscrição

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
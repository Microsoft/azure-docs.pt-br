---
title: Personalizar a interface do usuário com modelos HTML
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário com modelos HTML para seus aplicativos que usam Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e694a5f6144cee65be074d05ce0015d31bfdf65e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104675818"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário com modelos HTML no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A identidade visual e a personalização da interface do usuário que o Azure Active Directory B2C (Azure AD B2C) exibe para seus clientes ajuda a fornecer uma experiência de usuário direta em seu aplicativo. Essas experiências incluem a inscrição, a entrada, a edição de perfil e a redefinição de senha. Este artigo apresenta os métodos de personalização da interface do usuário. 

> [!TIP]
> Se você quiser modificar apenas o logotipo de faixa, a imagem de tela de fundo e a cor da tela de fundo das suas páginas de fluxo de usuário, você poderá experimentar o recurso de [identidade visual da empresa](customize-ui.md) .

## <a name="custom-html-and-css-overview"></a>Visão geral de HTML e CSS personalizados

Azure AD B2C executa o código no navegador do cliente usando o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/). No tempo de execução, o conteúdo é carregado de uma URL que você especifica em seu fluxo de usuário ou política personalizada. Cada página na experiência do usuário carrega seu conteúdo da URL que você especificar para essa página. Depois que o conteúdo é carregado de sua URL, ele é mesclado com um fragmento de HTML inserido por Azure AD B2C e, em seguida, a página é exibida para o cliente.

![Margem de conteúdo da página personalizada](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Conteúdo da página HTML personalizada

Crie uma página HTML com sua própria identidade visual para fornecer o conteúdo da página personalizada. Essa página pode ser uma `*.html` página estática ou uma página dinâmica, como .net, Node.js ou php.

O conteúdo da página personalizada pode conter qualquer elemento HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como iframes. O único elemento necessário é um elemento div com `id` definido como `api` , como este `<div id="api"></div>` dentro de sua página HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalizar as páginas de Azure AD B2C padrão

Em vez de criar o conteúdo da página personalizada do zero, você pode personalizar o conteúdo da página padrão do Azure AD B2C.

A tabela a seguir lista o conteúdo de página padrão fornecido pelo Azure AD B2C. Baixe os arquivos e use-os como um ponto de partida para criar suas próprias páginas personalizadas.

| Página padrão | Descrição | ID de definição de conteúdo<br/>(somente política personalizada) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página Autodeclarada**. Use esse arquivo como um conteúdo de página personalizado para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de entrada de conta local, redefinição de senha e muito mais. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem acessar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página manipula o processo de inscrição e de entrada. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hospedando o conteúdo da página

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, hospede o conteúdo da interface do usuário em qualquer ponto de extremidade HTTPS publicamente disponível que ofereça suporte a CORS. Por exemplo, [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md), [serviços Azure apps](../app-service/index.yml), servidores Web, CDNs, AWS S3 ou sistemas de compartilhamento de arquivos.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para usar o conteúdo da página personalizada

- Use uma URL absoluta quando você incluir recursos externos, como mídia, CSS e arquivos JavaScript em seu arquivo HTML.
- Usando o [layout de página versão](page-layout.md) 1.2.0 e superior, você pode adicionar o `data-preload="true"` atributo em suas marcas HTML para controlar a ordem de carregamento para CSS e JavaScript. Com `data-preload="true"` o, a página é construída antes de ser exibida para o usuário. Esse atributo ajuda a impedir que a página "oscilação", precarregando o arquivo CSS, sem que o HTML com estilo seja mostrado ao usuário. O trecho de código HTML a seguir mostra o uso da `data-preload` marca.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que você comece com o conteúdo da página padrão e se baseie nele.
- Você pode [incluir o JavaScript](javascript-and-page-layout.md) em seu conteúdo personalizado.
- As versões de navegador compatíveis são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Compatibilidade limitada com Internet Explorer 9 e 8
  - Google Chrome 42.0 e superior
  - Mozilla Firefox 38.0 e superior
  - Safari para iOS e macOS, versão 12 e posterior
- Devido a restrições de segurança, Azure AD B2C não dá suporte a `frame` `iframe` `form` elementos HTML, ou.

## <a name="localize-content"></a>Conteúdo de localização

Você localiza seu conteúdo HTML habilitando a [personalização de idioma](language-customization.md) em seu locatário do Azure AD B2C. Habilitar esse recurso permite que Azure AD B2C encaminhe o parâmetro OpenID Connect `ui_locales` para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de diferentes locais com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`.

Por exemplo, o URI da página personalizada pode ser semelhante a:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Você pode carregar a página em francês Obtendo conteúdo de:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Explicação do conteúdo da página personalizada

Aqui está uma visão geral do processo:

1. Prepare um local para hospedar o conteúdo da página personalizada (um ponto de extremidade HTTPS habilitado para CORS, acessível publicamente).
1. Baixe e personalize um arquivo de conteúdo de página padrão, por exemplo `unified.html` .
1. Publique seu conteúdo de página personalizada no ponto de extremidade HTTPS disponível publicamente.
1. Defina o CORS (compartilhamento de recurso entre origem) para seu aplicativo Web.
1. Aponte sua política para o URI de conteúdo da política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. criar seu conteúdo HTML

Crie um conteúdo de página personalizada com o nome da marca do produto no título.

1. Copie o snippet de HTML a seguir. Ele é um HTML5 bem formado com um elemento vazio chamado *\<div id="api"\>\</div\>* localizado dentro das *\<body\>* marcas. Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Colar o trecho copiado em um editor de texto
1. Use CSS para definir o estilo para os elementos de interface do usuário inseridos pelo Azure AD B2C na página. O exemplo a seguir mostra um arquivo CSS simples que também inclui configurações para os elementos HTML injetados da inscrição:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Salve o arquivo como *customize-ui.html*.

> [!NOTE]
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se você usar login.microsoftonline.com. Se você quiser usar elementos de formulário HTML em seu conteúdo HTML personalizado, [use b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. criar uma conta de armazenamento de BLOBs do Azure

Neste artigo, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar seu conteúdo em um servidor Web, mas deve [habilitar CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar seu conteúdo HTML no armazenamento de BLOBs, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu **Hub** , selecione **novo**  >  **armazenamento**  >  **conta de armazenamento**.
1. Selecione uma **assinatura** para sua conta de armazenamento.
1. Crie um **grupo de recursos** ou selecione um existente.
1. Insira um **nome** exclusivo para sua conta de armazenamento.
1. Selecione a **localização geográfica** da sua conta de armazenamento.
1. O **Modelo de implantação** pode permanecer **Gerenciador de Recursos**.
1. O **Desempenho** pode permanecer **Padrão**.
1. Altere **Tipo de Conta** para **Armazenamento de blobs**.
1. A **Replicação** pode permanecer **RA-GRS**.
1. A **Camada de acesso** pode permanecer **Dinâmica**.
1. Selecione **examinar + criar** para criar a conta de armazenamento.
    Depois que a implantação for concluída, a página **conta de armazenamento** será aberta automaticamente.

#### <a name="21-create-a-container"></a>2,1 criar um contêiner

Para criar um contêiner público no armazenamento de BLOBs, execute as seguintes etapas:

1. Em **serviço blob** no menu à esquerda, selecione **BLOBs**.
1. Selecione **+ contêiner**.
1. Para **nome**, insira *raiz*. O nome pode ser um nome de sua escolha, por exemplo, *contoso*, mas usamos *root* neste exemplo para simplificar.
1. Para **nível de acesso público**, selecione **blob** e **OK**.
1. Selecione **raiz** para abrir o novo contêiner.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 carregar seus arquivos de conteúdo de página personalizada

1. Escolha **Carregar**.
1. Selecione o ícone de pasta ao lado de **selecionar um arquivo**.
1. Navegue até e selecione **customize-ui.html**, que você criou anteriormente na seção personalização da interface do usuário da página.
1. Se você quiser carregar para uma subpasta, expanda **avançado** e insira um nome de pasta em **carregar para a pasta**.
1. Escolha **Carregar**.
1. Selecione o blob **customize-ui.html** que você carregou.
1. À direita da caixa de texto **URL** , selecione o ícone **copiar para área de transferência** para copiar a URL para a área de transferência.
1. No navegador da Web, navegue até a URL que você copiou para verificar se o blob que você carregou está acessível. Se ele estiver inacessível, por exemplo, se você encontrar um `ResourceNotFound` erro, verifique se o tipo de acesso do contêiner está definido como **blob**.

### <a name="3-configure-cors"></a>3. configurar CORS

Configure o armazenamento de BLOB para compartilhamento de recursos entre origens executando as seguintes etapas:

1. No menu, selecione **CORS**.
1. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do locatário.
1. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos**, digite um asterisco (*).
1. Para **cabeçalhos expostos**, digite um asterisco (*).
1. Para **Idade máxima de**, insira 200.
1. Clique em **Salvar**.

#### <a name="31-test-cors"></a>CORS de teste 3,1

Valide se você está pronto executando as seguintes etapas:

1. Repita a etapa configurar CORS. Para **origens permitidas**, insira `https://www.test-cors.org`
1. Navegue até [www.Test-CORS.org](https://www.test-cors.org/) 
1. Para a caixa **URL remota** , Cole a URL do seu arquivo HTML. Por exemplo, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selecione **Enviar solicitação**.
    O resultado deve ser `XHR status: 200` . 
    Se você receber um erro, verifique se as Configurações do CORS estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. atualizar o fluxo do usuário

1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Fluxos dos usuários** e, em seguida, selecione o fluxo de usuário *B2C_1_signupsignin1*.
1. Selecione **Layouts da página** e, em seguida, em **Página de inscrição ou entrada unificada**, clique em **Sim** para **Usar o conteúdo da página personalizada**.
1. Em **URI da página personalizada**, insira o URI do arquivo *custom-ui.html* que você gravou anteriormente.
1. Na parte superior da página, selecione **Salvar**.

### <a name="5-test-the-user-flow"></a>5. testar o fluxo do usuário

1. No locatário do Azure AD B2C, selecione **Fluxos do usuário** e selecione o fluxo de usuário *B2C_1_signupsignin1*.
1. Na parte superior da página, clique em **Executar fluxo de usuário**.
1. Clique no botão **Executar fluxo de usuário**.

Você deverá ver uma página semelhante ao exemplo a seguir com os elementos centralizados com base no arquivo CSS que você criou:

![Navegador da Web mostrando a página de inscrição ou de entrada com elementos personalizados da interface do usuário](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. modificar o arquivo de extensões

Para configurar a personalização da interface do usuário, copie o **ContentDefinition** e seus elementos filho do arquivo base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Esse arquivo base é um dos arquivos de política incluídos no pacote de início de política personalizada, que você deve ter obtido no pré-requisito, [introdução às políticas personalizadas](./custom-policy-get-started.md).
1. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions**.
1. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como um filho do elemento **BuildingBlocks** .
1. Pesquise o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML copiado.
1. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Sua política personalizada deve ser semelhante ao seguinte trecho de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salve o arquivo de extensões.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Carregue e teste sua política personalizada atualizada

#### <a name="51-upload-the-custom-policy"></a>5,1 carregar a política personalizada

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **Carregar política personalizada**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testar a política personalizada usando **executar agora**

1. Selecione a política que você carregou e, em seguida, selecione **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurar URI de conteúdo de página personalizada dinâmica

Ao usar Azure AD B2C políticas personalizadas, você pode enviar um parâmetro no caminho da URL ou uma cadeia de caracteres de consulta. Passando o parâmetro para seu ponto de extremidade HTML, é possível alterar dinamicamente o conteúdo da página. Por exemplo, é possível alterar a imagem de tela de fundo na página de inscrição ou de entrada do Azure AD B2C, com base em um parâmetro passado do seu aplicativo Web ou móvel. O parâmetro pode ser qualquer [resolvedor de declaração](claim-resolver-overview.md), como a ID do aplicativo, ID de idioma ou parâmetro de cadeia de caracteres de consulta personalizada, como `campaignId` .

### <a name="sending-query-string-parameters"></a>Enviando parâmetros de cadeia de caracteres de consulta

Para enviar parâmetros de cadeia de caracteres de consulta, na política de terceira parte [confiável](relyingparty.md), adicione um `ContentDefinitionParameters` elemento, conforme mostrado abaixo.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Em sua definição de conteúdo, altere o valor de `LoadUri` para `https://<app_name>.azurewebsites.net/home/unified` . Sua política personalizada `ContentDefinition` deve ser semelhante ao seguinte trecho de código:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando Azure AD B2C carrega a página, ela faz uma chamada para o ponto de extremidade do servidor Web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI de conteúdo de página dinâmica

O conteúdo pode ser extraído de locais diferentes com base nos parâmetros usados. No ponto de extremidade habilitado para CORS, configure uma estrutura de pastas para hospedar conteúdo. Por exemplo, você pode organizar o conteúdo na estrutura a seguir. *Pasta/pasta raiz por idioma/seus arquivos HTML*. Por exemplo, o URI da página personalizada pode ser semelhante a:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envia o código ISO de duas letras para a linguagem, `fr` para francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Modelos de exemplo

Encontre modelos de exemplo para personalização da interface do usuário aqui:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Este projeto contém os seguintes modelos:
- [Azul-marinho](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Cinza-acinzentado](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Clássico](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Recursos de modelo](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Para usar o exemplo:

1. Clone o repositório em seu computador local. Escolha uma pasta de modelo `/AzureBlue` , `/MSA` ou `/classic` .
1. Carregue todos os arquivos na pasta modelo e na `/src` pasta, no armazenamento de BLOBs, conforme descrito nas seções anteriores.
1. Em seguida, abra cada `\*.html` arquivo na pasta modelo. Em seguida, substitua todas as instâncias de `https://login.microsoftonline.com` URLs, pela URL que você carregou na etapa 2. Por exemplo:
    
    De:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Para:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Salve os `\*.html` arquivos e carregue-os no armazenamento de BLOBs.
1. Agora, modifique a política, apontando para o arquivo HTML, conforme mencionado anteriormente.
1. Se você vir fontes, imagens ou CSS ausentes, verifique suas referências na política de extensões e nos \* arquivos. html.

## <a name="use-company-branding-assets-in-custom-html"></a>Usar ativos de identidade visual da empresa em HTML personalizado

Para usar os ativos de [identidade visual da empresa](customize-ui.md#configure-company-branding) em um HTML personalizado, adicione as seguintes marcas fora da `<div id="api">` marca. A origem da imagem é substituída pela imagem de plano de fundo e pelo logotipo de faixa.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Próximas etapas

Saiba como habilitar o [código JavaScript do lado do cliente](javascript-and-page-layout.md).

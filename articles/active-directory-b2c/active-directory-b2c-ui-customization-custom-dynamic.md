---
title: "Azure Active Directory B2C: personalizar a IU (interface do usuário) do Azure AD B2C dinamicamente usando políticas Personalizadas"
description: "Como dar suporte a várias experiências de identidade visual com conteúdo HTML/CSS que mude dinamicamente no tempo de execução"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: configurar a interface do usuário com conteúdo dinâmico usando políticas Personalizadas
As políticas personalizadas do Azure AD B2C permitem que você envie por meio de um parâmetro em uma cadeia de caracteres de consulta. Esse parâmetro é passado para seu ponto de extremidade HTML e pode alterar dinamicamente o conteúdo da página. Por exemplo, você pode alterar a inscrição ou entrada do B2C na imagem da tela de fundo com base em um parâmetro passado do seu aplicativo Web/móvel. 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo se concentra em como personalizar a interface do usuário do Azure AD B2C com **conteúdo dinâmico** usando políticas personalizadas. Para começar com a personalização da interface do usuário de política personalizada, leia o artigo [Personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>
>  O artigo [Configurar a personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md) ensina os conceitos básicos de personalização da interface do usuário do Azure AD B2C com a política personalizada:
> * O recurso de personalização da IU (interface do usuário) da página.
> * Uma ferramenta que ajuda a testar o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo.
> * Os elementos principais da interface do usuário em cada tipo de página.
> * Práticas recomendadas ao exercitar esse recurso.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>Adicionando um link aos modelos HTML5/CSS para o percurso do usuário

Em uma política personalizada, uma definição de conteúdo determina o URI da página HTML5 que é usado para uma determinada etapa da interface do usuário. Por exemplo, as páginas de entrada ou inscrição. A política de base define a aparência padrão apontando para um URI de arquivos HTML5 (refere-se a seu CSS). Na política de extensão, você pode modificar a aparência substituindo o LoadUri para esse arquivo HTML5. Assim, definições de conteúdo contêm URLs para o conteúdo externo que é definido criando arquivos HTML5/CSS conforme apropriado. 

A seção `ContentDefinitions` contém uma série de elementos XML `ContentDefinition`. O atributo de ID do elemento `ContentDefinition` especifica o tipo de páginas relacionadas à definição de conteúdo. Assim, o contexto em que um modelo HTML5/CSS personalizado será usado. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo, reconhecido pelo mecanismo de IEF e o tipo de páginas relacionadas a ele.

| ID de definição de conteúdo | Modelo HTML5 padrão| Descrição | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção de provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções geralmente são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de esquecimento de senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar sua redefinição de senha.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de entrada da conta local**. Esta página contém um formulário de entrada para entradas com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição para inscrições com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que os usuários devem preencher ao se inscreverem usando uma conta existente de um provedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, exceto pelos campos de entrada de senha. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem usar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto pelos campos de entrada de senha. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página gerencia a inscrição e a entrada de usuários. Os usuários podem utilizar provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais.  |

## <a name="serving-dynamic-content"></a>Veiculando conteúdo dinâmico
O artigo anterior, [Configurar personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md), você carregou os arquivos HTML5 para o Armazenamento de Blobs do Azure. Esses arquivos HTML5 são estáticos e renderizam o mesmo conteúdo HTML para cada solicitação. Neste artigo, usamos o aplicativo Web ASP.Net, que pode aceitar parâmetros de cadeia de caracteres de consulta e responder de acordo. Neste passo a passo, você vai:
* Criar um aplicativo Web ASP.NET Core que hospede seu modelo HTML5 
* Adicionar um modelo personalizado HTML5 _unified.cshtml_ 
* Publicar seu aplicativo Web no Aplicativo de Serviço do Azure 
* Definir o CORS para seu aplicativo Web
* Substituir os elementos `LoadUri` para que apontem para o arquivo HTML5

## <a name="step-1-create-an-aspnet-web-app"></a>Etapa 1: Criar um aplicativo Web ASP.NET

1.  No Visual Studio, crie um projeto selecionando **Arquivo > Novo > Projeto**.
2.  Na caixa de diálogo **Novo Projeto**, selecione **Visual C# > Web > Aplicativo Web ASP.NET Core (.NET Core)**.
3.  Nome do aplicativo, por exemplo, Contoso.AADB2C.UI e, em seguida, selecionando **OK**.

    ![Criar um novo projeto do Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  Selecione o modelo **Aplicativo Web**
5.  Verifique se a autenticação está definida como **Sem Autenticação**

    ![Selecione o modelo de API da Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  Clique em **OK** para criar o projeto

## <a name="step-2-create-mvc-view"></a>Etapa 2: Criar exibição do MVC
### <a name="step-21-download-b2c-built-in-html5-template"></a>Etapa 2.1 Baixar B2C criado no modelo do HTML5
O HTML5 personalizado modelado baseia-se em B2C criado no modelo HTML5. Você pode baixar o [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou baixar do [pacote inicial](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Use esse arquivo HTML5 para uma página de inscrição ou entrada unificada.

### <a name="step-22-add-mvc-view"></a>Etapa 2.2 Adicionar exibição do MVC
1. Clique com o botão direito do mouse na pasta Exibições/Inicial e, em seguida, **Adicionar > Novo Item**.
 ![Adicionar novo item do MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. Na caixa de diálogo **Adicionar Novo Item – Contoso.AADB2C.UI**, selecione **Web > ASP > NET**
3. Toque em **Exibir Página do MVC**
4. Na caixa **Nome**, altere o nome para _unified.cshtml_.
5. Clique em **Adicionar**
 ![Adicionar exibição de MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  Se esse arquivo não estiver aberto, clique duas vezes no arquivo para abri-lo. Limpe o conteúdo de sua _unified.cshtml_.
7. Para fins de demonstração, removemos a referência à página de layout. Adicione o seguinte trecho de código a _unified.cshtml_

    ```C#
    @{
        Layout = null;
    }
    ```

8. Abra o arquivo _unified.cshtml_ baixado do AAD B2C criado no modelo do HTML5.
9. Localize o sinal `@` e substitua por `@@`
10. Copie o conteúdo do arquivo e cole-o abaixo da definição de Layout. Seu código deve se parecer com: ![arquivo unified.cshtml depois de adicionar o HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>Etapa 2.3 Alterar a imagem da tela de fundo
10. Localize o elemento `<img>` com a ID `background_background_image` e substitua `src` por _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ ou qualquer outra imagem da tela de fundo que você quiser.
![Alterar a tela de fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>Etapa 2.4 Adicionar sua exibição ao controlador MVC
Abra **Controllers\HomeController.cs** e adicione o método a seguir. 
```C
public IActionResult unified()
{
    return View();
}
```
Esse código especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador. Uma vez que não especificamos explicitamente o nome do arquivo do modelo de exibição, o MVC assumiu como padrão o uso do arquivo de exibição _unified.cshtml_ na pasta/Views/Home.

Depois de adicionar o método _unificado_, seu código deve se parecer com: ![alterar para controlador para renderizar a exibição](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

Depure seu aplicativo Web e certifique-se de que a página _unificada_ esteja acessível. Por exemplo, `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>Etapa 2.5 Publicar no Azure
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.UI** e selecione **Publicar**.

    ![Publicar no Serviço de Aplicativo do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  Verifique se o **Serviço de Aplicativo do Microsoft Azure** está selecionado e clique em **Publicar**.

    ![Criar um novo Serviço de Aplicativo do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    A caixa de diálogo **Criar Serviço de Aplicativo** ajuda a criar todos os recursos do Azure necessários para executar seu aplicativo Web ASP.NET no Azure.

> [!NOTE]
>
>Para obter mais informações, consulte: [Criar um aplicativo Web ASP.NET no Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  Em **Nome do aplicativo Web**, digite um nome exclusivo do aplicativo (os caracteres válidos são `a-z`, `0-9` e `-`). A URL do aplicativo Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome do aplicativo Web. Você pode aceitar o nome gerado automaticamente, que é exclusivo.

    Clique em **Criar** para começar a criar os recursos do Azure.

    ![Fornecer propriedades do Serviço de Aplicativo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  Depois que o assistente for concluído, ele publica o aplicativo Web ASP.NET no Azure e, em seguida, inicia o aplicativo no navegador padrão e copia a URL da página _unificada_. por exemplo, _https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>Etapa 3: Configurar CORS no Serviço de Aplicativo do Azure
1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com/)

2. Clique em **Serviços de Aplicativos**e clique no nome do aplicativo de API.

    ![Selecione o aplicativo de API no portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. Na seção **Configurações**, role para baixo e localize a seção **API** e, em seguida, clique em **CORS**.

    ![Selecione as configurações de CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. Na caixa de texto, insira a URL ou as URLs das quais você deseja que as chamadas JavaScript venham.
Ou insira um asterisco (*) para especificar que todos os domínios de origem são aceitos.

5. Clique em **Salvar**.

    ![Clique em Salvar](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

Depois de clicar em Salvar, o aplicativo de API aceitará chamadas JavaScript das URLs especificadas. 

## <a name="step-4-html5-template-validation"></a>Etapa 4: Validação de modelo do HTML5
Você HTML5 modelado está pronto para uso.  No entanto, ele não está disponível em nenhuma das `ContentDefinition`. Antes de adicionar o `ContentDefinition` à sua política personalizada, você deve:
* Certifique-se de que seu conteúdo esteja em conformidade com HTML5 e acessível
* Verifique se que o servidor de conteúdo está habilitado para CORS.

>[!NOTE]
>
>Para verificar se o site que você está hospedando o conteúdo possui CORS habilitado e testar solicitações CORS, use o site http://test-cors.org/. 

* O conteúdo veiculado é protegido por **HTTPS**.
* Use **URLS absolutas** como https://yourdomain/content para todos os links e conteúdo e imagens CSS.

## <a name="step-5-configure-your-content-definition"></a>Etapa 5: Configurar sua definição de conteúdo
Para configurar o `ContentDefinition`
1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<ContentDefinitions>` e copie todo o conteúdo do nó `<ContentDefinitions>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<BuildingBlocks>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<ContentDefinitions>` copiado como um filho do elemento `<BuildingBlocks>`. 
5.  Localize o nó `<ContentDefinition>` que inclui `Id="api.signuporsignin"` no XML copiado.
6.  Altere o valor de `LoadUri` de _~/tenant/default/unified_ para _https://app_name.azurewebsites.net/home/unified_ Sua política personalizada deve se parecer com: ![sua definição de conteúdo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Etapa 6: Carregar a política para o seu locatário
1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra o **Azure AD B2C**
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Abra **Todas as Políticas**.
4.  Selecione **Carregar Política**.
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Etapa 7: Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >
    >    A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. 
    >    Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.
    >

2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve ser capaz de ver o HTML5 personalizado com a tela de fundo criada anteriormente ![Sua política de inscrição ou entrada](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>Etapa 8: Adicionando conteúdo dinâmico
Nesta seção, alteramos a tela de fundo com base no parâmetro de cadeia de caracteres de consulta chamado _campaignId_. Seu aplicativo de terceira parte confiável (aplicativos Web/móveis) envia o parâmetro para AAD B2C. Sua política lê o parâmetro e envia seu valor para o seu modelo HTML5 


### <a name="step-81-adding-content-definition-parameter"></a>Etapa 8.1: Adicionando parâmetro de definição de conteúdo

Para adicionar o elemento `ContentDefinitionParameters`:
1.  Abra o arquivo SignUpOrSignin da sua política (por exemplo, SignUpOrSignin.xml).
2.  Localizar o nó `<UserJourneyBehaviors>` 
4.  Adicione o seguinte trecho XML dentro do `<UserJourneyBehaviors>` 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>Etapa 8.2 Alterar seu código para aceitar o parâmetro de cadeia de caracteres de consulta e substituir a imagem da tela de fundo de acordo
Nesta seção, modificamos o método _unificada_ do HomeController para aceitar o parâmetro campaignId. Em seguida, o método verifica seu valor e define a variável `ViewData["background"]` adequadamente.

1. Abra **Controllers\HomeController.cs** e altere o método `unified` com o seguinte trecho de código:

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Localize o elemento `<img>` com a ID `background_background_image` e substitua `src` por `@ViewData["background"]`.

    ![Alterar a tela de fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 Carregue as alterações e publique sua política
1. Publique o projeto do Visual Studio para o aplicativo de Serviço do Azure
2. Carregue o arquivo da política de SignUpOrSignin.xml para AAD B2C
3.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**. Você deverá ver a mesma imagem de tela de fundo de antes.
4. Copie a URL da barra de endereços do navegador
5. Adicione o parâmetro de cadeia de caracteres de consulta _campaignId_ ao URI. Por exemplo, adicione `&campaignId=hawaii`, conforme mostrado na imagem a seguir ![Alterar a tela de fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. Pressione **Enter** e sua página apresentará a tela de fundo Havaí ![Alterar a tela de fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. Agora, altere o valor para *Tóquio* e pressione **Enter**. A página apresentará a tela de fundo Tóquio ![Alterar a tela de fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Etapa 9: Alterar o restante do percurso do usuário
Se você navegar para a página de entrada e clicar no link **Inscrever-se agora**, verá a tela de fundo padrão, não aquela que você definiu. Esse comportamento ocorre porque você alterou apenas a página de inscrição ou entrada, no entanto, você precisa alterar também o restante das definições de conteúdo de Autodeclaração. Para fazer isso, percorra as etapas:
* Na etapa nº 2:
    * Baixe o arquivo **selfasserted**.
    * Copie o conteúdo do arquivo.
    * Crie a nova exibição **selfasserted**.
    * Adicione **selfasserted** ao controlador **Inicial**.
* Na etapa nº 4 
    * Na política de extensão, localize o nó `<ContentDefinition>` que inclui `Id="api.selfasserted"`, `Id="api.localaccountsignup"` e `Id="api.localaccountpasswordreset"`
    *  Defina o atributo `LoadUri` para o Uri selfasserted.
* Na etapa nº 8.2, altere o código para aceitar parâmetros de cadeia de caracteres de consulta 
* Carregue a política TrustFrameworkExtensions.xml e garanta que sua validação não falhe.
* Execute o teste de política e clique em **Inscrever-se agora** para ver o resultado

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Baixar os arquivos e código da política completa
* Recomendamos a criação de seu cenário usando seus próprios arquivos de política Personalizada após a conclusão das instruções passo a passo Introdução às políticas personalizadas, em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* Você pode baixar o código completo aqui [Amostra de solução do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)





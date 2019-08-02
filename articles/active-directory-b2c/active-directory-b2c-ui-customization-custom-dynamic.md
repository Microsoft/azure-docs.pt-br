---
title: Personalizar a interface do usuário do Azure AD B2C dinamicamente usando políticas personalizadas | Microsoft Docs
description: Dar suporte a várias experiências de identidade visual com conteúdo HTML5/CSS que muda dinamicamente no tempo de execução.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227214"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: configurar a interface do usuário com conteúdo dinâmico usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usando as políticas personalizadas do Azure AD B2C (Azure Active Directory B2C), é possível enviar um parâmetro em uma cadeia de caracteres de consulta. Passando o parâmetro para seu ponto de extremidade HTML, é possível alterar dinamicamente o conteúdo da página. Por exemplo, é possível alterar a imagem de tela de fundo na página de inscrição ou de entrada do Azure AD B2C, com base em um parâmetro passado do seu aplicativo Web ou móvel.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo se concentra em como personalizar a interface do usuário do Azure AD B2C com *conteúdo dinâmico* usando políticas personalizadas. Para começar, consulte [UI customization in a custom policy](active-directory-b2c-ui-customization-custom.md) (Personalização da interface do usuário em uma política personalizada).

>[!NOTE]
>O artigo do Azure AD B2C, [Configurar a personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md), aborda os conceitos básicos a seguir:
> * O recurso de personalização da IU (interface do usuário) da página.
> * Ferramentas essenciais para testar o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo.
> * Os principais elementos de interface do usuário de cada tipo de página.
> * Práticas recomendadas para aplicar esse recurso.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Adicionar um link aos modelos HTML5/CSS para o percurso do usuário

Em uma política personalizada, uma definição de conteúdo define o URI da página HTML5 usado para uma etapa da interface do usuário especificada (por exemplo, as páginas de inscrição ou de entrada). A política de base define a aparência padrão apontando para um URI de arquivos HTML5 (no CSS). Na política de extensão, é possível modificar a aparência substituindo o LoadUri desse arquivo HTML5. As definições de conteúdo contêm URLs para o conteúdo externo que é definido por meio da criação de arquivos HTML5/CSS conforme apropriado.

A seção `ContentDefinitions` contém uma série de elementos XML `ContentDefinition`. O atributo de ID do elemento `ContentDefinition` especifica o tipo de página relacionada à definição de conteúdo. Ou seja, o elemento define o contexto que um modelo personalizado HTML5/CSS aplicará. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo reconhecido pelo mecanismo de IEF e os tipos de página relacionados a ele.

| ID de definição de conteúdo | Modelo HTML5 padrão| DESCRIÇÃO |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção de provedor de identidade**. Esta página lista provedores de identidade que os usuários podem escolher durante a entrada. Normalmente, as opções são provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de provedor de identidade para inscrição**. Esta página lista provedores de identidade que os usuários podem escolher durante a inscrição. As opções são os provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de esquecimento de senha**. Esta página contém um formulário que os usuários precisam preencher para iniciar uma redefinição de senha.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de entrada da conta local**. Esta página contém um formulário de entrada para entrar com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição da conta local**. Esta página contém um formulário para inscrição de uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta social**. Esta página contém um formulário que os usuários devem preencher ao se inscreverem usando uma conta existente de um provedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, exceto os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem acessar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página manipula o processo de inscrição e de entrada. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais.  |

## <a name="serving-dynamic-content"></a>Veiculando conteúdo dinâmico
No artigo [Configurar personalização da interface do usuário em uma política personalizada](active-directory-b2c-ui-customization-custom.md), você carregará os arquivos HTML5 para o Armazenamento de blobs do Azure. Esses arquivos HTML5 são estáticos e renderizam o mesmo conteúdo HTML para cada solicitação.

Neste artigo, você usará o aplicativo Web ASP.NET, que pode aceitar parâmetros de cadeia de caracteres de consulta e responder de acordo.

Neste passo a passo, você vai:
* Criar um aplicativo Web ASP.NET Core que hospede seu modelo HTML5.
* Adicionar um modelo HTML5 personalizado, _unified.cshtml_.
* Publique seu aplicativo Web no Serviço de Aplicativo do Azure.
* Defina o CORS (compartilhamento de recurso entre origem) para seu aplicativo Web.
* Substitua os elementos `LoadUri` para apontar para o arquivo HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Etapa 1: Criar um aplicativo Web ASP.NET

1. No Visual Studio, crie um projeto selecionando **Arquivo** > **Novo** > **Projeto**.

2. Na janela **Novo Projeto**, selecione **Visual C#**  > **Web** > **Aplicativo Web ASP.NET Core (.NET Core)** .

3. Nomeie o aplicativo (por exemplo, *Contoso.AADB2C.UI*) e, em seguida, selecione **OK**.

    ![Criar um novo projeto do Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selecione o modelo **Aplicativo Web**.

5. Defina a autenticação como **Sem autenticação**.

    ![Selecione o modelo Aplicativo Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selecione **OK** para criar o projeto.

## <a name="step-2-create-mvc-view"></a>Etapa 2: criar exibição do MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Etapa 2.1: baixar o modelo HTML5 interno do B2C
O modelo HTML5 personalizado baseia-se no modelo HTML5 interno do Azure AD B2C. É possível baixar o [arquivo unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou baixar o modelo do [pacote inicial](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Use esse arquivo HTML5 para criar uma página de inscrição ou de entrada unificada.

### <a name="step-22-add-the-mvc-view"></a>Etapa 2.2: adicionar a exibição do MVC
1. Clique com o botão direito do mouse na pasta Exibições/Inicial e, em seguida, **Adicionar** > **Novo Item**.

    ![Adicionar item de menu novo item no Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Na janela **Adicionar Novo Item – Contoso.AADB2C.UI**, selecione **Web > ASP.NET**.

3. Selecione **Página de exibição do MVC**.

4. Na caixa **Nome**, altere o nome para **unified.cshtml**.

5. Selecione **Adicionar**.

    ![Caixa de diálogo Adicionar novo item no Visual Studio com a página exibição do MVC realçada](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Se o arquivo *unified.cshtml* ainda não estiver aberto, clique duas vezes nele para abri-lo e apague o conteúdo do arquivo.

7. Neste passo a passo, removemos a referência à página de layout. Adicione o seguinte snippet de código a _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Abra o arquivo _unified.cshtml_ baixado do modelo HTML5 interno do Azure AD B2C.

9. Substitua o sinal de arroba único (@) por dois sinais de arroba (@@).

10. Copie o conteúdo do arquivo e cole-o abaixo da definição do layout. Seu código deverá ter esta aparência:

    ![arquivo unified.cshtml após adicionar o HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Etapa 2.3: alterar a imagem do plano de fundo

Localize o `<img>` elemento que contém o `ID` valor *background_background_image* e, em seguida, substitua o valor `src` por **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** ou qualquer outra imagem de plano de fundo que você queira utilizar.

![elemento img com valor src personalizado background_background_image](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Etapa 2.4: adicionar sua exibição ao controlador MVC

1. Abra **Controllers\HomeController.cs** e adicione o método a seguir:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Esse código especifica que o método deve usar um arquivo de modelo de *Exibição* para renderizar uma resposta para o navegador. Como não especificamos explicitamente o nome do arquivo de modelo de *Exibição*, o MVC assumiu como padrão o uso do arquivo de Exibição _unified.cshtml_ na pasta */Views/Home*.

    Depois de adicionar o método _unificado_, seu código deverá ter esta aparência:

    ![Altere o controlador para renderizar a exibição](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Depure seu aplicativo Web e certifique-se de que a página _unificada_ esteja acessível (por exemplo, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Etapa 2.5: Publicar no Azure
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.UI** e selecione **Publicar**.

    ![Publicar no Serviço de Aplicativo do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selecione o bloco **Serviço de Aplicativo do Microsoft Azure** e selecione **Publicar**.

    ![Criar um novo Serviço de Aplicativo do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    A janela **Criar Serviço de Aplicativo** é aberta. Nela, é possível começar a criar todos os recursos necessários do Azure para executar o aplicativo Web ASP.NET no Azure.

    > [!NOTE]
    > Para obter mais informações sobre a publicação, consulte [Criar um aplicativo Web ASP.NET no Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Na caixa **Nome do aplicativo Web**, digite um nome exclusivo para o aplicativo (os caracteres válidos são a-z, 0-9 e hífen [-]). A URL do aplicativo Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome do aplicativo Web. Você pode aceitar o nome gerado automaticamente, que é exclusivo.

4. Clique em **Criar** para começar a criar os recursos do Azure.

    ![Fornecer propriedades do Serviço de Aplicativo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Depois que o processo de criação for concluído, o assistente publicará o aplicativo Web ASP.NET no Azure e, em seguida, iniciará o aplicativo no navegador padrão.

5. Copie a URL da página _unificada_ (por exemplo, _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Etapa 3: configurar CORS no Serviço de Aplicativo do Azure
1. No [Portal do Azure](https://portal.azure.com/), selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo de API.

    ![Selecionar o aplicativo de API no Portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Na seção **Configurações**, na seção **API**, selecione **CORS**.

    ![Item de menu CORS realçado no menu serviço de aplicativo no portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Na janela **CORS**, na caixa **Origens permitidas**, siga um destes procedimentos:

    * Insira a URL ou URLs das quais você deseja que as chamadas JavaScript venham. Você precisa usar todas as letras minúsculas nas URLs que você inseriu.
    * Insira um asterisco ( * ) para especificar que todos os domínios de origem são aceitos.

4. Clique em **Salvar**.

    ![Página de configurações de CORS com asterisco realçado em origens permitidas](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Depois de selecionar **Salvar**, o aplicativo de API aceita chamadas JavaScript das URLs especificadas.

## <a name="step-4-html5-template-validation"></a>Etapa 4: validação de modelo do HTML5
Seu modelo HTML5 está pronto para uso. No entanto, ele não está disponível no código `ContentDefinition`. Antes de adicionar `ContentDefinition` à sua política personalizada, certifique-se de que:
* Seu conteúdo esteja em conformidade com HTML5 e seja acessível.
* Seu servidor de conteúdo está habilitado para CORS.

    >[!NOTE]
    >Para verificar se o site em que você está hospedando seu conteúdo habilitou o CORS e pode testar solicitações CORS, acesse o site [test-cors.org](https://test-cors.org/).

* O conteúdo veiculado é protegido em **HTTPS**.
* Você está usando *URLs absolutas*, `https://yourdomain/content`como, para todos os links, conteúdo CSS e imagens.

## <a name="step-5-configure-your-content-definition"></a>Etapa 5: configurar sua definição de conteúdo
Para configurar `ContentDefinition`, faça o seguinte:
1. Abra o arquivo base da política (por exemplo, *TrustFrameworkBase.xml*).

2. Pesquise o elemento `<ContentDefinitions>` e, em seguida, copie todo o conteúdo do nó `<ContentDefinitions>`.

3. Abra o arquivo de extensão (por exemplo, *TrustFrameworkExtensions.xml*) e, em seguida, pesquise o elemento `<BuildingBlocks>`. Se o elemento não existir, adicione-o.

4. Cole todo o conteúdo do nó `<ContentDefinitions>` copiado como um filho do elemento `<BuildingBlocks>`.

5. Pesquise o nó `<ContentDefinition>` que contém `Id="api.signuporsignin"` no XML copiado.

6. Altere o valor de `LoadUri` de _~/tenant/default/unified_ para _https://<nome_do_aplicativo>.azurewebsites.net/home/unified_.
    Sua política personalizada deverá ter a seguinte aparência:

    ![Exemplo de trecho XML com elemento LoadUri realçado](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Etapa 6: Carregar a política ao seu locatário
1. No [Portal do Azure](https://portal.azure.com), mude para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e, em seguida, selecione **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Selecione **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue o arquivo *TrustFrameworkExtensions.xml* e certifique-se de que a validação dele seja aprovada.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Etapa 7: Testar a política personalizada usando a opção Executar Agora
1. Selecione **Configurações do Azure AD B2C** e selecione **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.
    Você deve ser capaz de ver seu HTML5 personalizado com a tela de fundo criada anteriormente.

    ![Sua política de inscrição ou de entrada](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Etapa 8: Adicionar conteúdo dinâmico
Altere a tela de fundo com base no parâmetro da cadeia de caracteres de consulta chamado _campaignId_. Seu aplicativo RP (aplicativos Web e móveis) envia o parâmetro para o Azure AD B2C. Sua política lê o parâmetro e envia seu valor para seu modelo HTML5.

### <a name="step-81-add-a-content-definition-parameter"></a>Etapa 8.1: adicionar um parâmetro de definição de conteúdo

Adicione o elemento `ContentDefinitionParameters` fazendo o seguinte:
1. Abra o arquivo *SignUpOrSignin* da sua política (por exemplo, *SignUpOrSignin.xml*).

2. No nó `<DefaultUserJourney>`, adicione o nó `UserJourneyBehaviors`:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Etapa 8.2: alterar seu código para aceitar um parâmetro de cadeia de consulta e substituir a imagem do plano de fundo
Modifique o método `unified` do HomeController para aceitar o parâmetro campaignId. O método verifica, então, o valor do parâmetro e define a variável `ViewData["background"]` adequadamente.

1. Abra o arquivo *Controllers\HomeController.cs* e, em seguida, altere o método `unified` adicionando o seguinte snippet de código:

    ```csharp
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

2. Localize o elemento `<img>` com a ID `background_background_image` e substitua o valor `src` por `@ViewData["background"]`.

    ![elemento img com valor src realçado ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: carregar as alterações e publicar sua política
1. Publique o projeto do Visual Studio no Serviço de Aplicativo do Azure.

2. Carregue a política *SignUpOrSignin.xml* para o Azure AD B2C.

3. Abra **B2C_1A_signup_signin**, a política RP personalizada carregada e selecione **Executar agora**.
    Você deverá ver a mesma imagem de tela de fundo exibida anteriormente.

4. Copie a URL da barra de endereços do navegador.

5. Adicione o parâmetro da cadeia de caracteres de consulta _campaignId_ ao URI. Por exemplo, adicione `&campaignId=hawaii`, conforme mostrado na imagem a seguir:

    ![URI com o parâmetro de cadeia de caracteres de consulta campaignid realçado](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Selecione **Enter** para exibir a imagem de tela de fundo Havaí.

    ![Página de entrada de inscrição com plano de fundo personalizado de imagem do Havaí](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Altere o valor para *Tóquio* e selecione **Enter**.
    O navegador exibe a tela de fundo Tóquio.

    ![Página de entrada de inscrição com plano de fundo personalizado da imagem de Tokyo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Etapa 9: alterar o restante do percurso do usuário
Se você selecionar o link **Inscrever-se agora** na página de entrada, o navegador exibirá a imagem de tela de fundo padrão, não a imagem que você definiu. Esse comportamento ocorre porque você alterou apenas a página de inscrição ou de entrada. Para alterar o restante das definições de conteúdo autodeclarado:
1. Volte para a “Etapa 2” e faça o seguinte:

    a. Baixe o arquivo *selfasserted*.

    b. Copie o conteúdo do arquivo.

    c. Crie uma nova exibição, *selfasserted*.

    d. Adicione *selfasserted* ao controlador **Inicial**.

2. Volte para a “Etapa 4” e faça o seguinte:

    a. Na política de extensão, localize o nó `<ContentDefinition>` que contém `Id="api.selfasserted"`, `Id="api.localaccountsignup"` e `Id="api.localaccountpasswordreset"`.

    b. Defina o atributo `LoadUri` para seu URI *selfasserted*.

3. Volte para a "Etapa 8.2" e altere seu código para aceitar os parâmetros de cadeia de caracteres de consulta, mas, dessa vez, para a função *selfasserted*.

4. Carregue a política *TrustFrameworkExtensions.xml* e certifique-se de que a validação dela seja aprovada.

5. Execute o teste de política e, em seguida, selecione **Inscrever-se agora** para ver o resultado.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Baixar os arquivos e o código da política completos
* Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) como referência.
* É possível baixar o código completo em [Solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).





---
title: Integrar trocas de declaração de API REST na jornada de usuário do Azure Active Directory B2C | Microsoft Docs
description: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação da entrada do usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed26c4d90738e10f3eb5a9a486cd2734090abd0e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227246"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação da entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Com a Estrutura de Experiência de Identidade subjacente ao Azure AD B2C (Azure Active Directory B2C), é possível integrar-se a uma API RESTful em um percurso do usuário. Neste passo a passo, você aprenderá como o Azure AD B2C interage com serviços RESTful do .NET Framework (API Web).

## <a name="introduction"></a>Introdução
Usando o Azure AD B2C, é possível adicionar sua própria lógica de negócios a um percurso do usuário chamando o seu próprio serviço RESTful. A Estrutura de Experiência de Identidade envia dados para o serviço RESTful em uma coleção *Declarações de entrada* e recebe dados de volta do RESTful em uma coleção *Declarações de saída*. Com a integração de serviço RESTful, você pode:

* **Validar os dados de entrada do usuário**: Essa ação impede que dados malformados sejam mantidos no Azure AD. Se o valor do usuário não for válido, o serviço RESTful retornará uma mensagem de erro que instrui o usuário a fornecer uma entrada. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente.
* **Substituir declarações de entrada**: Por exemplo, se um usuário inserir o nome com todas as letras maiúsculas ou minúsculas, você poderá formatar o nome usando somente a primeira letra maiúscula.
* **Aprimorar os dados do usuário com a integração adicional a aplicativos de linha de negócios corporativos**: Seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. As declarações retornadas podem ser armazenadas na conta do Azure AD do usuário, avaliadas nas próximas *Etapas de Orquestração* ou incluídas no token de acesso.
* **Executar a lógica de negócios personalizada**: Você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e realizar diversas outras ações.

É possível criar a integração com os serviços RESTful das seguintes maneiras:

* **Perfil técnico de validação**: A chamada para o serviço RESTful ocorre dentro do perfil técnico de validação do perfil técnico especificado. O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário possa avançar. Com o perfil técnico de validação, é possível:
   * Enviar declarações de entrada.
   * Validar as declarações de entrada e gerar mensagens de erro personalizadas.
   * Retornar declarações de saída.

* **Troca de declarações**: Esse design é semelhante ao perfil técnico de validação, mas ele ocorre dentro de uma etapa de orquestração. Essa definição é limitada a:
   * Enviar declarações de entrada.
   * Retornar declarações de saída.

## <a name="restful-walkthrough"></a>Passo a passo RESTful
Neste passo a passo, você desenvolverá uma API Web do .NET Framework que valida a entrada do usuário e fornece o número de fidelidade dele. Por exemplo, seu aplicativo pode conceder acesso a *benefícios platinum* com base no número de fidelidade.

Visão geral:
* Desenvolver o serviço RESTful (API Web do .NET Framework).
* Usar o serviço RESTful no percurso do usuário.
* Enviar e ler declarações de entrada no seu código.
* Validar o nome do usuário.
* Retornar um número de fidelidade.
* Adicionar o número de fidelidade a um JWT (JSON Web Token).

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Etapa 1: Criar uma API Web ASP.NET

1. No Visual Studio, crie um projeto selecionando **Arquivo** > **Novo** > **Projeto**.

2. Na janela **Novo Projeto**, selecione **Visual C#**  > **Web** > **Aplicativo Web ASP.NET (.NET Framework)** .

3. Na caixa **Nome**, digite um nome para o aplicativo (por exemplo, *Contoso.AADB2C.API*) e selecione **OK**.

    ![Criando um novo projeto do Visual Studio no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Na janela **Novo aplicativo Web ASP.NET**, selecione um modelo de **API Web** ou **aplicativo de API do Azure**.

    ![Selecionando um modelo de API Web no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Verifique se a autenticação está definida como **Sem Autenticação**.

6. Selecione **OK** para criar o projeto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Etapa 2: Preparar o ponto de extremidade da API REST

### <a name="step-21-add-data-models"></a>Etapa 2.1: Adicionar modelos de dados
Os modelos representam as declarações de entrada e os dados de declarações de saída em seu serviço RESTful. O código lê os dados de entrada desserializando o modelo de declarações de entrada de uma cadeia de caracteres JSON para um objeto C# (seu modelo). A ASP.NET Web API desserializa automaticamente o modelo de declarações de saída para JSON e, em seguida, grava os dados serializados no corpo da mensagem de resposta HTTP.

Crie um modelo que representa as declarações de entrada fazendo o seguinte:

1. Se o Gerenciador de Soluções ainda não estiver aberto, selecione **Exibir** > **Gerenciador de Soluções**.
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos**, selecione **Adicionar** e Classe.

    ![Adicionar item de menu de classe selecionado no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nomeie a classe como `InputClaimsModel` e adicione as seguintes propriedades à classe `InputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Crie um novo modelo, `OutputClaimsModel` e adicione as seguintes propriedades à classe `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Crie mais um modelo, `B2CResponseContent`, que será usado para gerar mensagens de erro de validação de entrada. Adicione as seguintes propriedades à classe `B2CResponseContent`, forneça as referências ausentes e salve o arquivo:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Etapa 2.2: Adicionar um controlador
Na API Web, um _controlador_ é um objeto que manipula as solicitações HTTP. O controlador retorna as declarações de saída ou, se o nome não for válido, gera uma mensagem de erro HTTP de conflito.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Controladores**, selecione **Adicionar** e **Controlador**.

    ![Adicionando um novo controlador no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Na janela **Adicionar Scaffold**, selecione **Controlador da API Web – Vazio** e **Adicionar**.

    ![Selecionando o controlador da API Web 2-vazio no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Na janela **Adicionar Controlador**, nomeie o controlador **IdentityController** e selecione **Adicionar**.

    ![Inserindo o nome do controlador no Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O scaffolding cria um arquivo chamado *IdentityController.cs* na pasta *Controladores*.

4. Se o arquivo *IdentityController.cs* ainda não estiver aberto, clique duas vezes nele e substitua o código no arquivo pelo código a seguir:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Etapa 3: Publicar o projeto no Azure
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.

    ![Publicar no serviço de aplicativo Microsoft Azure com o Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Na janela **Publicar**, escolha **Serviço de Aplicativo do Microsoft Azure** e selecione **Publicar**.

    ![Criar novo Microsoft Azure serviço de aplicativo com o Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    A janela **Criar Serviço de Aplicativo** é aberta. Nela, é possível criar todos os recursos do Azure necessários para executar o aplicativo Web ASP.NET no Azure.

    > [!NOTE]
    >Para obter mais informações sobre como publicar, consulte: [Criar um aplicativo Web ASP.NET no Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Na caixa **Nome do aplicativo Web**, digite um nome exclusivo para o aplicativo (os caracteres válidos são a a z, 0 a 9 e hífen [-]). A URL do aplicativo Web é http://<nome_do_aplicativo>.azurewebsites.NET, em que *nome_do_aplicativo* é o nome do seu aplicativo Web. Você pode aceitar o nome gerado automaticamente, que é exclusivo.

    ![Configurando as propriedades do serviço de aplicativo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Selecione **Criar** para começar a criar os recursos do Azure.
    Depois que o aplicativo Web ASP.NET tiver sido criado, o assistente o publicará no Azure e o abrirá no navegador padrão.

6. Copie a URL do aplicativo Web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Etapa 4: Adicionar a nova declaração `loyaltyNumber` ao esquema do arquivo TrustFrameworkExtensions.xml
A declaração `loyaltyNumber` ainda não está definida no nosso esquema. Adicione uma definição ao interior do elemento `<BuildingBlocks>`, que pode ser encontrado no início do arquivo *TrustFrameworkExtensions.xml*.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Etapa 5: Adicionar um provedor de declarações
Cada provedor de declarações deve ter um ou mais perfis técnicos, os quais determinam os pontos de extremidade e os protocolos necessários para se comunicar com o provedor de declarações.

Um provedor de declarações pode ter vários perfis técnicos por vários motivos. Por exemplo, é possível definir vários perfis técnicos, pois o provedor de declarações dá suporte a vários protocolos, os pontos de extremidade podem ter diferentes capacidades ou as versões podem conter declarações em diferentes níveis de garantia. Pode ser aceitável liberar declarações confidenciais em um percurso do usuário, mas não em outro.

O seguinte snippet de código XML contém um nó de provedor de declarações com dois perfis técnicos:

* **TechnicalProfile Id="REST-API-SignUp"** : Define o serviço RESTful.
  * `Proprietary` é descrito como protocolo para um provedor baseado em RESTful.
  * `InputClaims` define as declarações que serão enviadas do Azure AD B2C ao serviço REST.

    Neste exemplo, o conteúdo da declaração `givenName` é enviado ao serviço REST como `firstName`, o conteúdo da declaração `surname` é enviado ao serviço REST como `lastName` e `email` é enviado no estado em que se encontra. O elemento `OutputClaims` define as declarações que são recuperadas do serviço RESTful para o Azure AD B2C.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"** : Adiciona um perfil técnico de validação a um perfil técnico existente (definido na política de base). Durante o percurso de inscrição, o perfil técnico de validação invoca o perfil técnico anterior. Se o serviço RESTful retornar um erro HTTP 409 (um erro de conflito), a mensagem de erro será exibida para o usuário.

Localize o nó `<ClaimsProviders>` e adicione o seguinte snippet de código XML ao nó `<ClaimsProviders>`:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Etapa 6: Adicionar a declaração `loyaltyNumber` ao seu arquivo de política de terceira parte confiável para que a declaração seja enviada ao aplicativo
Edite o arquivo RP (terceira parte confiável) *SignUpOrSignIn.xml* e modifique o elemento TechnicalProfile Id="PolicyProfile" para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois de adicionar a nova declaração, o código de terceira parte confiável terá esta aparência:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Etapa 7: Carregar a política ao seu locatário

1. No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Abra **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue o arquivo TrustFrameworkExtensions.xml e certifique-se de que a validação dele seja aprovada.

7. Repita a etapa anterior com o arquivo SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Etapa 8: Testar a política personalizada usando a opção Executar Agora
1. Selecione **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    > [!NOTE]
    > A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.

    ![A página de política personalizada do B2C_1A_signup_signin no portal do Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Teste o processo digitando **Teste** na caixa **Nome**.
    O Azure AD B2C exibirá uma mensagem de erro na parte superior da janela.

    ![Testando a validação de entrada de nome fornecida na página de entrada de inscrição](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. Na caixa **Nome**, digite um nome (diferente de "Teste").
    O Azure AD B2C inscreve o usuário e, em seguida, envia um loyaltyNumber para o aplicativo. Observe o número neste JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Baixar os arquivos e o código da política completos
* Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw) como referência.
* É possível baixar o código completo em [Solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Próximas etapas
* [Proteja sua API RESTful com a autenticação básica (nome de usuário e senha)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Proteja sua API RESTful com certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

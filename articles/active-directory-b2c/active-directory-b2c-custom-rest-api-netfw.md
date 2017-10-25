---
title: "Azure Active Directory B2C: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário"
description: "Amostra de como integrar trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário"
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário
A IEF (Estrutura de Experiência de Identidade) subjacente ao Azure AD B2C (Azure Active Directory B2C) permite que você se integre a uma API RESTful em um percurso do usuário. Neste passo a passo, vamos mostrar como o B2C interage com serviços RESTful do .NET Framework (API Web).

## <a name="introduction"></a>Introdução
O Azure AD B2C permite que você adicione sua própria lógica de negócios em um percurso do usuário chamando o seu próprio serviço RESTful. A Estrutura de Experiência de Identidade envia dados ao serviço RESTful na coleção **Declarações de entrada** e recebe dados de volta de RESTful na coleção **Declarações de saída**. Com a integração de serviço RESTful, você pode:

* Valide dados de entrada do usuário, impedindo que dados malformados persistam no Azure AD. Se o valor do usuário não for válido, o seu serviço RESTful retornará a mensagem de erro que instrui o usuário a fornecer uma entrada. Por exemplo, você pode verificar que o endereço de email fornecido pelo usuário existe em seu banco de dados de clientes.
* Substitua declarações de entrada. Por exemplo, se o usuário inserir o nome em minúsculas ou maiúsculas, você poderá formatar o nome e coloque somente a primeira letra em maiúsculas
* Aprimore os dados do usuário com a integração adicional a aplicativos de linha de negócios corporativos. Seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados de clientes e retornar o número de fidelidade do usuário para B2C. As declarações de retorno podem ser armazenadas na conta do AAD do usuário, avaliada nas próximas **Etapas de Orquestração** ou incluídas no token de acesso.
* Executar lógica de negócios personalizada: envie notificações por push, atualize bancos de dados corporativos, execute processo de migração do usuário, gerenciamento de permissões, auditoria e muito mais.

A integração com os serviços RESTful pode ser desenvolvida como uma troca de declarações ou um perfil técnico de Validação:

* **Perfil técnico de validação** A chamada para o serviço RESTful acontece dentro de ValidationTechnicalProfile de um determinado TechnicalProfile. O perfil técnico de validação valida os dados fornecido pelo usuário antes que o percurso do usuário avance. Com a validação do perfil técnico, você pode:
  * Enviar declarações de entrada
  * Validar as declarações de entrada e gerar mensagens de erro personalizadas
  * Enviar declarações de saída de volta

* **Troca de declarações** – semelhante à anterior, mas acontece dentro de uma etapa de orquestração. Essa definição é limitada a:
   * Enviar declarações de entrada
   * Enviar declarações de saída de volta

## <a name="restful-walkthrough"></a>Passo a passo RESTful
Neste passo a passo, você desenvolve um .NET Framework na API Web que valida a entrada do usuário e fornece o número de fidelidade do usuário. Por exemplo, seu aplicativo pode conceder acesso a "benefícios platinum" com base no número de fidelidade.

Visão geral:
*   Desenvolvendo o serviço RESTful (API Web do .NET Framework)
*   Usando o serviço RESTful na jornada do usuário
*   Enviando declarações de entrada de lendo-as em seu código
*   Validando o nome do usuário
*   Enviando de volta o número de fidelidade 
*   Adicionando o número de fidelidade ao token Web JSON (JWT)

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Etapa 1: Criar uma API Web ASP.NET
1.  No Visual Studio, crie um projeto selecionando **Arquivo > Novo > Projeto**.
2.  Na caixa de diálogo **Novo Projeto**, clique em **Visual C# > Web > Aplicativo Web ASP.NET (.NET Framework)**.
3.  Nome do aplicativo, por exemplo, Contoso.AADB2C.API e, em seguida, selecionando **OK**.

    ![Criar um novo projeto do Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  Selecione o modelo **API Web** ou **aplicativo de API do Azure**
5.  Verifique se a autenticação está definida como **Sem Autenticação**

    ![Selecione o modelo de API da Web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  Clique em **OK** para criar o projeto

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Etapa 2: Preparar o ponto de extremidade da API REST

### <a name="step-21-add-data-models"></a>Etapa 2.1 Adicionar modelos de dados
Os modelos representam as declarações de entrada e os dados de declarações de saída em seu serviço RESTful. O código lê os dados de entrada desserializando o modelo de declarações de entrada da cadeia de caracteres JSON para o objeto C# (seu modelo). A API Web ASP.NET desserializa automaticamente o modelo de declarações de saída para JSON e, em seguida, grava os dados serializados no corpo da mensagem de resposta HTTP. Vamos começar criando um modelo que representa as declarações de entrada.

1.  Se o Gerenciador de Soluções não estiver visível, clique no menu **Exibir** e selecione **Gerenciador de Soluções**. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta Modelos. No menu de contexto, selecione **Adicionar** e, em seguida, selecione **Classe**.

    ![Adicionar modelo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  Nomeie a classe `InputClaimsModel` e adicione as seguintes propriedades à classe `InputClaimsModel`

    ```C#
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

3.  Crie um novo modelo `OutputClaimsModel` e adicione as seguintes propriedades à classe `OutputClaimsModel`

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  Criar mais um modelo `B2CResponseContent`. Esse modelo é usado para gerar mensagens de erro de validação de entrada. Adicione as seguintes propriedades à classe `B2CResponseContent`, forneça as referências ausentes e salve o arquivo.

    ```C#
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

### <a name="step-22-add-a-controller"></a>Etapa 2.2 Adicionar um controlador
Na API da Web, um _controlador_ é um objeto que manipula as solicitações HTTP. Adicionamos um controlador que retorna as declarações de saída ou, se o nome não for válido, gera uma mensagem de erro HTTP de conflito.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores. Selecione **Adicionar** e, em seguida, selecione **Controlador**.

    ![Adicionar novo controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador da API Web – Vazio**. Clique em **Adicionar**.

    !![Selecione Controlador da API Web 2 – Vazio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  Na caixa de diálogo **Adicionar Controlador**, nomeie o controlador `IdentityController` e clique em **Adicionar**.

    ![Digite o nome do controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O scaffolding cria um arquivo chamado IdentityController.cs na pasta Controladores.

4.  Se esse arquivo não estiver aberto, clique duas vezes no arquivo para abri-lo. Substitua o conteúdo nesse arquivo pelas linhas de código a seguir:

    ```C#
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
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>Etapa 3: Publicar no Azure
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.

    ![Publicar no Serviço de Aplicativo do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  Verifique se o **Serviço de Aplicativo do Microsoft Azure** está selecionado e clique em **Publicar**.

    ![Criar um novo Serviço de Aplicativo do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Isso abre a caixa de diálogo **Criar Serviço de Aplicativo**, o que ajuda a criar todos os recursos do Azure necessários para executar seu aplicativo Web ASP.NET no Azure.

> [!NOTE]
>Para obter mais informações, consulte: [Criar um aplicativo Web ASP.NET no Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  Em **Nome do aplicativo Web**, digite um nome exclusivo do aplicativo (os caracteres válidos são `a-z`, `0-9` e `-`). A URL do aplicativo Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome do aplicativo Web. Você pode aceitar o nome gerado automaticamente, que é exclusivo.

    Clique em **Criar** para começar a criar os recursos do Azure.

    ![Fornecer propriedades do Serviço de Aplicativo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  Depois que o assistente for concluído, ele publicará o aplicativo Web ASP.NET no Azure e, em seguida, iniciará o aplicativo no navegador padrão; copie a URL.

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Etapa 4: Adicionar a nova declaração `loyaltyNumber` ao esquema do seu arquivo TrustFrameworkExtensions.xml
A declaração `loyaltyNumber` não está definida em nenhum outro lugar no nosso esquema. Portanto, adicione uma definição dentro do elemento `<BuildingBlocks>`. Você encontra esse elemento no início do arquivo TrustFrameworkExtensions.xml.

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

## <a name="step-5-adding-claims-provider"></a>Etapa 5: Adicionar o provedor de declarações 
Cada provedor de declarações deve ter um ou mais perfis técnicos que determine os pontos de extremidade e os protocolos necessários para se comunicar com esse provedor de declarações. Um provedor de declarações pode ter vários perfis técnicos por vários motivos. Por exemplo, vários perfis técnicos podem ser definidos, pois o provedor de declarações considerado dá suporte a vários protocolos, vários pontos de extremidade com diferentes capacidades ou libera diferentes declarações em diferentes níveis de garantia. Pode ser aceitável liberar declarações confidenciais em um percurso do usuário, mas não em outro. 

O trecho XML a seguir contém o nó `ClaimsProvider` com dois `TechnicalProfile`:
* `<TechnicalProfile Id="REST-API-SignUp">` define o serviço RESTful. O `Proprietary` é descrito como protocolo para um provedor baseado em RESTful. O elemento `InputClaims` define as declarações que serão enviadas do B2C ao serviço REST. Neste exemplo, o conteúdo da declaração `givenName` envia ao serviço REST como `firstName`, o conteúdo da declaração `surename` envia ao serviço REST como `lastName`, o `email` envia como está. O elemento `OutputClaims` define as declarações que recuperam do serviço RESTful para B2C.

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` adiciona o perfil técnico de validação ao perfil técnico existente (definido na política de base). Durante o percurso de inscrição, o perfil técnico de validação invoca o perfil técnico acima. Se o serviço RESTful retornar o erro HTTP 409 (conflito), a mensagem de erro será presentada ao usuário. 

Localize o nó `<ClaimsProviders>` e adicione o seguinte trecho XML no nó `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Etapa 6: Adicionar a declaração `loyaltyNumber` ao seu arquivo de política de terceira parte confiável para que a declaração seja enviada ao seu aplicativo
Edite o arquivo RP (terceira parte confiável) SignUpOrSignIn.xml e modifique o elemento `<TechnicalProfile Id="PolicyProfile">` para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois de adicionar a nova declaração, o `RelyingParty` terá esta aparência:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Etapa 7: Carregar a política para o seu locatário
1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra o **Azure AD B2C**
2.  Selecione **Estrutura de Experiência de Identidade**
3.  Abra **Todas as Políticas** 
4.  Selecione **Carregar Política**
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação
7.  Repita a última etapa e carregue o SignUpOrSignIn.xml

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Etapa 8: Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

> [!NOTE]
>
>A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.


2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  Tente digitar "Teste" no campo **Nome**; o B2C exibe a mensagem de erro na parte superior da página

    ![Testar sua política](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Tente digitar um nome (diferente de "teste") no campo **Nome**. O B2C inscreve o usuário e, em seguida, envia loyaltyNumber ao seu aplicativo. Observe o número neste JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Baixar os arquivos e código da política completa
* Recomendamos a criação de seu cenário usando seus próprios arquivos de política Personalizada após a conclusão das instruções passo a passo Introdução às políticas personalizadas, em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* Você pode baixar o código completo aqui [Amostra de solução do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)
    
## <a name="next-steps"></a>Próximas etapas
1.  [Proteja sua API RESTful com a autenticação básica (nome de usuário e senha)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
2.  [Proteja sua API RESTful com certificado de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
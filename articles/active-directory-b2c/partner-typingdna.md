---
title: TypingDNA com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o TypingDNA para ajudar com a verificação de identidade e a verificação de ortografia com base no padrão de digitação do usuário, fornece soluções de verificação de ID que impõem a autenticação multifator e ajuda a cumprir os requisitos de SCA para a diretiva 2 de serviços de pagamento (PSD2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97108341"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Tutorial para configurar o TypingDNA com o Azure Active Directory B2C

Neste tutorial, saiba como integrar um aplicativo de pagamento online de exemplo no Azure Active Directory B2C com o aplicativo TypingDNA. Usando o aplicativo TypingDNA, os clientes do Azure AD B2C podem cumprir os requisitos de transação de PSD2 ( [diretiva de serviços de pagamento 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) ) por meio do Dynamics de pressionamento de teclas e autenticação de cliente forte. Saiba mais sobre o TypingDNA [aqui](https://www.typingdna.com/).

 Azure AD B2C usa as tecnologias do TypingDNA para capturar as características de digitação dos usuários e fazer com que elas sejam registradas e analisadas quanto à familiaridade em cada autenticação. Isso adiciona uma camada de proteção relacionada ao risco de uma autenticação e avalia os níveis de risco. Azure AD B2C pode invocar outros mecanismos para fornecer maior confiança de que o usuário é quem alega ser invocando a MFA do Azure AD, forçando a verificação de email ou qualquer outra lógica personalizada para seu cenário.

>[!NOTE]
> Esta política de exemplo se baseia no pacote de início do [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) .

## <a name="scenario-description"></a>Descrição do cenário

![Captura de tela do diagrama de arquitetura do TypingDNA](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Inscrição

1. Azure AD B2C páginas usam a biblioteca JavaScript do TypingDNA para registrar o padrão de digitação do usuário. Por exemplo, o nome de usuário e a senha são registrados na inscrição para o registro inicial e, em seguida, em cada entrada para verificação.

2. Quando o usuário enviar a página, a biblioteca TypingDNA calculará a característica de digitação do usuário. Depois disso, insira as informações em um campo de texto oculto que Azure AD B2C tenha renderizado. Esse campo é ocultado com CSS.  

    O [exemplo contém arquivos HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) com as modificações de JavaScript e CSS e é referenciado pelas `api.selfasserted.tdnasignin` `api.selfasserted.tdnasignup` definições de conteúdo e. Consulte [hospedando o conteúdo da página](./customize-ui-with-html.md#hosting-the-page-content) para hospedar seus arquivos HTML.

3. Azure AD B2C agora tem o padrão de digitação dentro do recipiente de declarações quando o usuário envia suas credenciais. Ele deve chamar uma API (sua) para passar esses dados para o ponto de extremidade da API REST do TypingDNA. Essa API está incluída no [exemplo (typingDNA-API-interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. Em seguida, a API de camada intermediária passa os dados de padrão de digitação para a API REST do amTypingDNA. Na inscrição, o ponto de [extremidade do usuário de verificação](https://api.typingdna.com/index.html#api-API_Services-GetUser) é chamado para confirmar se o usuário não existe e, em seguida, o ponto de extremidade de [salvar padrão](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) é chamado para salvar o primeiro padrão de digitação do usuário.

> [!NOTE]
> Todas as chamadas para o ponto de extremidade da API REST do TypingDNA enviam um UserId. Deve ser um valor com hash. Azure AD B2C usa a `HashObjectIdWithEmail` transformação declarações para aplicar o hash ao email com um salt e um segredo aleatórios.  

As chamadas à API REST são modeladas com o `validationTechnicalProfiles` em `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Conexão

Em uma entrada subsequente, o padrão de digitação do usuário é calculado da mesma maneira que na inscrição usando o [HTML personalizado](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Depois que o perfil de digitação estiver dentro do conjunto de declarações Azure AD B2C, Azure AD B2C chamará sua API para chamar o ponto de extremidade da API REST do TypingDNA. O ponto de extremidade do [usuário de verificação](https://api.typingdna.com/index.html#api-API_Services-GetUser) é chamado para confirmar se o usuário existe. Em seguida, [Verifique se](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) o ponto de extremidade do padrão é chamado para retornar o `net_score` . Essa `net_score` é uma indicação de quão perto o padrão de digitação era o original na inscrição.

Esse padrão de digitação é modelado com `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Se o usuário obtiver um padrão de digitação que tenha um alto `net_score` , você poderá salvá-lo usando o ponto de extremidade do [padrão TypingDNA salvar digitação](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) .  

Sua API deverá retornar uma declaração  `saveTypingPattern` se você quiser que o ponto de extremidade de padrão de digitação TypingDNA Save seja chamado por Azure ad B2C (por meio de sua API).

O exemplo no repositório contém uma API (TypingDNA-API-interface) que é configurada com as propriedades a seguir.

- Modo de treinamento – se o usuário tiver menos de dois padrões salvos, sempre solicite a MFA.

- Se o usuário tiver 2-5 padrões salvos e o `net_score` for inferior a 50, solicite o MFA.

- Se o usuário tiver 5 padrões salvos e o `net_score` for inferior a 65, solicite o MFA.

Esses limites devem ser ajustados em seu caso de uso.

- Depois que a API tiver avaliado o `net_score` , ele deverá retornar uma declaração booliana para B2C `promptMFA` .

- A `promptMFA` declaração é usada em uma pré-condição para executar a MFA do Azure ad condicionalmente.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Integração com o TypingDNA

1. Inscreva-se no TypingDNA [aqui](https://www.typingdna.com/)
2. Faça logon no painel do TypingDNA e obtenha a **chave de API** e o **segredo da API**. Isso será necessário na configuração da interface de API mais tarde

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrar o TypingDNA ao Azure AD B2C

1. Hospede o [TypingDNA-API-interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) no seu provedor de Hospedagem de sua escolha
2. Substitua todas as instâncias de `apiKey` e `apiSecret` na solução [TypingDNA-API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) pelas credenciais do seu painel do TypingDNA
3. Hospede os arquivos HTML no seu provedor de escolha seguindo os requisitos de CORS [aqui](./customize-ui-with-html.md#3-configure-cors)
4. Substitua os elementos LoadURI para as `api.selfasserted.tdnasignup` `api.selfasserted.tdnasignin` definições de conteúdo e no `TrustFrameworkExtensions.xml` arquivo para o URI de seus arquivos HTML hospedados, respectivamente.
5. Crie uma chave de política do B2C na estrutura de experiência de identidade na folha do Azure AD no **portal do Azure**. Use a `Generate` opção e nomeie essa chave `tdnaHashedId` .
6. Substituir os Tenantid nos arquivos de política
7. Substitua as URLs em todos os perfis técnicos da API REST do TypingDNA (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) pelo ponto de extremidade para sua [API TypingDNA-API-interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Carregar [arquivos de política](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) para seu locatário.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário B2C e escolha Identity Experience Framework.
2. Selecione o fluxo de **usuário** criado anteriormente.
3. Selecione **executar** fluxo de usuário

    a. **Aplicativo** – selecione o aplicativo registrado (exemplo é JWT)

    b. **URL de resposta** -selecione a URL de redirecionamento

    c. Selecione **Executar fluxo de usuário**.
  
4. Percorrer o fluxo de inscrição e criar uma conta
5. Sair
6. Percorrer o fluxo de entrada
7. O resultado de JWT resultante mostrará os resultados de TypingDNA

## <a name="live-version"></a>Versão do Live

• A MFA foi desabilitada nesta versão de teste, mas você pode ver o resultado se a MFA teria sido solicitada pela Declaração `promptMFA` após a autenticação.

• Inscreva-se [aqui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) e entre [aqui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no AAD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no AAD B2C](./custom-policy-get-started.md?tabs=applications)
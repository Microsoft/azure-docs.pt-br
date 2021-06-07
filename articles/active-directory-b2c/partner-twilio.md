---
title: Twilio verificar aplicativo com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar um aplicativo de pagamento online de exemplo no Azure AD B2C com a API de verificação twilio. Cumpra os requisitos de transação PSD2 (Diretiva de serviços de pagamento 2) por meio de vinculação dinâmica e autenticação de cliente forte.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994036"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrando o aplicativo twilio Verify ao Azure Active Directory B2C

Neste tutorial, saiba como integrar um aplicativo de pagamento online de exemplo no Azure Active Directory B2C (Azure AD B2C) com a API de verificação twilio. Usando o aplicativo twilio Verify, os clientes do Azure AD B2C podem cumprir os requisitos de transação PSD2 (Diretiva de serviços de pagamento 2) por meio de vinculação dinâmica e autenticação de cliente forte.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.
* Uma [conta de avaliação](https://www.twilio.com/try-twilio) em twilio.

## <a name="scenario-description"></a>Descrição do cenário

Os seguintes componentes compõem a solução twilio:

- [Aplicativo Web de demonstração](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)do .net PSD2, que fornece a capacidade de entrar ou se inscrever e executar uma transação de alto risco fictícia.
- Azure AD B2C [política de entrada e inscrição](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)combinada.
- Azure AD B2C política integrada com a API de verificação do twilio usando `id_token_hint` .
- Aplicativo Web .NET, que hospeda um `.well-known` ponto de extremidade OpenIdConnect para permitir a validação de um `id_token_hint` .


    ![fluxo de twilio](media/partner-twilio/twilio-flow.png)

| Etapa | Descrição |
|------|------|
| 1     | O usuário inicia a entrada ou a inscrição no aplicativo de demonstração do PSD2. O usuário é autenticado por meio da política de entrada e inscrição Azure AD B2C combinada. Um token é retornado para o aplicativo. Na inscrição, o número de telefone do usuário é verificado usando o SMS/telefone e é registrado em sua conta de Azure AD B2C.     |
| 2     | O usuário inicia uma transação de alto risco, como uma transferência de $50. O token de acesso atual do usuário é avaliado para a política para determinar se o usuário já foi autenticado por meio de uma Step-Up política personalizada.     |
| 3     | O aplicativo registra o valor de transação e o favorecido, $50 e John Doe, e gera um token assinado. Esse token é chamado de `id_token_hint` e contém a Declaração `amount:$500, payee:john doe` . O `id_token_hint` é enviado junto com a solicitação para a Azure ad B2C política personalizada, que é integrada com twilio.     |
| 4     | Azure AD B2C verifica a assinatura do id_token_hint verificando o `/.well-known` ponto de extremidade do OpenID Connect de aplicativos. Depois de verificar, ele extrai as declarações desse token, notadamente, `amount` e `payee` . O usuário verá uma página para verificar seu número de telefone celular via mensagem SMS.     |
| 5     | O usuário solicita a verificação de seu número de telefone via mensagem de SMS e Azure AD B2C faz uma solicitação de API REST para o ponto de extremidade de API de verificação do twilio. Ele também envia a transação `amount` e `payee` como parte do processo PSD2 para gerar a OTP (senha de uso único). Twilio envia uma mensagem SMS para o número de telefone registrado do usuário.     |
| 6     |  O usuário insere a OTP recebida em sua mensagem SMS e a envia para Azure AD B2C. Azure AD B2C faz uma solicitação de API com essa OTP para a API de verificação do twilio para verificar se a OTP está correta. Por fim, um token é emitido para o aplicativo, com uma nova PolicyId, o que significa que o usuário concluiu sua autenticação.    |
|      |      |

## <a name="onboard-with-twilio"></a>Integração com o twilio

1. Obtenha uma [conta de avaliação](https://www.twilio.com/try-twilio) em twilio.

2. Compre um número de telefone em twilio, conforme descrito neste [artigo](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

3. Navegue para [verificar API](https://www.twilio.com/console/verify/services) no console do twilio e siga [as instruções](https://www.twilio.com/docs/verify/verifying-transactions-psd2) para criar um serviço e habilitar a opção PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Configurar o aplicativo de demonstração do PSD2

1. Abra a solução B2C-WebAPI-DotNet e substitua os seguintes valores pelos seus próprios valores específicos de locatário no web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. O [aplicativo Web](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) também hospeda o gerador de token de ID e o ponto de extremidade de metadados.
   - Crie seu certificado de assinatura conforme descrito nesta [Descrição de exemplo](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Atualize as seguintes linhas com base em seu certificado no web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Carregue o aplicativo de demonstração para o provedor de Hospedagem de sua escolha. As diretrizes para o serviço de Azure App são fornecidas nesta [Descrição de exemplo](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service), incluindo instruções para carregar seu certificado.

4. Atualize seu registro de aplicativo Azure AD B2C adicionando uma URL de resposta equivalente à URL na qual o aplicativo está hospedado.

5. Abra os [arquivos de política](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) e substitua todas as instâncias de  `contoso` pelo nome do locatário.

6. Localize o perfil técnico do twilio REST API **personalizado-SMS-registrar**. Atualize o  `ServiceURL`   com seu twilio AccountId e o número de para o número de telefone comprado.

7. Localize os perfis técnicos da API REST do twilio, **TwilioRestAPI-Verify-etapa 1**   e **TwilioRestAPI-Verify-etapa 2** e atualize o  `ServiceURL`   com seu twilio AccountId.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

Adicione os arquivos de política a Azure AD B2C:

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue até **Azure ad B2C**  >  chaves de política do **Identity Experience Framework**  >  .

5. Adicione uma nova chave com o nome **B2cRestTwilioClientId**. Selecione **manual** e forneça o valor de AccountId twilio.

6. Adicione uma nova chave com o nome **B2cRestTwilioClientSecret**. Selecione **manual** e forneça o valor do token de autenticação twilio.

7. Carregue todos os arquivos de política para seu locatário.

8. Personalize a cadeia de caracteres na transformação GenerateOTPMessageEnrol Claims para seu texto SMS de inscrição.

## <a name="test-the-solution"></a>Testar a solução

* Navegue até seu aplicativo e teste a entrada, Inscreva-se e envie ações do Money.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- Consulte o GitHub para obter [exemplos de código de integração do twilio](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [Políticas personalizadas no AAD B2C](custom-policy-overview.md)

- [Introdução às políticas personalizadas no AAD B2C](custom-policy-get-started.md?tabs=applications)

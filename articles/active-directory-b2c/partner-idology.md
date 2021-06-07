---
title: Integração do IDology com o Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar um aplicativo de pagamento online de exemplo no Azure AD B2C com o IDology. O IDology é uma verificação de identidade e um provedor de prova de ortografia com várias soluções.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928674"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Tutorial para configurar o IDology com o Azure Active Directory B2C 

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure AD B2C com o [IDology](https://www.idology.com/solutions/). O IDology é uma verificação de identidade e um provedor de prova de ortografia com várias soluções. Neste exemplo, abordaremos a solução de Esperaid por IDology.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração do IDology inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização responsável por verificar as credenciais do usuário. Ele também é conhecido como provedor de identidade.
- IDology – o serviço IDology usa a entrada fornecida pelo usuário e verifica a identidade do usuário.
- API REST personalizada – essa API implementa a integração entre o Azure AD e o serviço IDology.

O diagrama de arquitetura a seguir mostra a implementação.

![Diagrama de arquitetura do IDology](media/partner-idology/idology-architecture-diagram.png)

| Etapa | Descrição |
|------|------|
|1     | Um usuário chega na página de entrada. |
|2     | O usuário seleciona a opção de inscrição para criar uma nova conta e inserir informações na página. Azure AD B2C coleta os atributos de usuário. |
|3     | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário. |
|4     | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API IDOlogy pode consumir. Em seguida, ele envia as informações para IDology. |
|5     | O IDology consome as informações e a processa e, em seguida, retorna o resultado para a API da camada intermediária. |
|6     | A API de camada intermediária processa as informações e envia as informações relevantes de volta para Azure AD B2C. |
|7     | Azure AD B2C recebe informações de volta da API de camada intermediária. Se ele mostrar uma resposta de **falha** , uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de **êxito** , o usuário será autenticado e gravado no diretório. |
|      |      |

> [!NOTE]
> Azure AD B2C também pode solicitar que o cliente execute a autenticação de Step-up, mas esse cenário está fora do escopo deste tutorial.

## <a name="onboard-with-idology"></a>Integração com o IDology

1. O IDology fornece uma variedade de soluções, que podem ser encontradas [aqui](https://www.idology.com/solutions/). Para este exemplo, usamos o esperado.

2. Para criar uma conta do IDology, entre em contato com [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. Depois que uma conta for criada, você receberá as informações necessárias para a configuração da API. As seções a seguir descrevem o processo.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Parte 1-implantar a API

Implante o [código de API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) fornecido em um serviço do Azure. O código pode ser publicado no Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).

Você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

### <a name="part-2---configure-the-api"></a>Parte 2-configurar a API 

As configurações do aplicativo podem ser [configuradas no serviço de aplicativo no Azure](../app-service/configure-common.md#configure-app-settings). Com esse método, as configurações podem ser configuradas com segurança sem verificá-las em um repositório. Você precisará fornecer as seguintes configurações para a API REST:

| Configurações do aplicativo | Fonte | Observações |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Configuração da conta do IDology |     |
|IdologySettings:ApiPassword | Configuração da conta do IDology |     |
|WebApiSettings:ApiUsername |Definir um nome de usuário para a API| Usado na configuração do ExtId |
|WebApiSettings:ApiPassword | Definir uma senha para a API | Usado na configuração do ExtId

### <a name="part-3---create-api-policy-keys"></a>Parte 3-criar chaves de política de API

Siga este [documento](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) para criar duas chaves de política: uma para o nome de usuário da API e outra para a senha da API que você definiu acima.

A política de exemplo usa estes nomes de chave:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4-configurar a política de Azure AD B2C

1. Siga este [documento](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de início do LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurar a política para o locatário Azure ad B2C. Siga as instruções até concluir a seção **testar a política personalizada** .

2. Baixe as duas políticas de exemplo [aqui](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy).

3. Atualize as duas políticas de exemplo:

   1. Abra ambas as políticas:

      1. Na seção `Idology-ExpectId-API` , atualize o `ServiceUrl` item de metadados com o local da API implantada acima.

      1. Substitua `yourtenant` pelo nome de seu locatário do Azure AD B2C.
      Por exemplo, se o nome do seu locatário de Azure AD B2C for  `contosotenant` , substitua todas as instâncias de  `yourtenant.onmicrosoft.com`   por `contosotenant.onmicrosoft.com` .

   1. Abra o arquivo TrustFrameworkExtensions.xml:

      1. Localize o elemento  `<TechnicalProfile Id="login-NonInteractive">` . Substitua ambas as instâncias de  `IdentityExperienceFrameworkAppId`   pela ID do aplicativo IdentityExperienceFramework que você criou anteriormente.

      1. Substitua ambas as instâncias de  `ProxyIdentityExperienceFrameworkAppId`   pela ID do aplicativo ProxyIdentityExperienceFramework que você criou anteriormente.

4. Substitua o SignInorSignUp.xml e TrustFrameworkExtensions.xml carregados anteriormente para Azure AD B2C na etapa 1 com as duas políticas de exemplo atualizadas.

> [!NOTE]
> Como prática recomendada, recomendamos que os clientes adicionem notificação de autorização na página coleção de atributos. Notifique os usuários que as informações serão enviadas a serviços de terceiros para verificação de identidade.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em **políticas**, selecione **fluxos de usuário**.

2. Selecione o fluxo de **usuário** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   1. **Aplicativo** – selecione o aplicativo registrado (exemplo é JWT).

   1. **URL de resposta** -selecione a **URL de redirecionamento**.

   1. Selecione **Executar fluxo de usuário**.

4. Percorra o fluxo de inscrição e crie uma conta.

5. Saia.

6. Percorra o fluxo de entrada.

7. O quebra-cabeça IDology será exibido depois que você inserir **continuar**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](custom-policy-get-started.md?tabs=applications)

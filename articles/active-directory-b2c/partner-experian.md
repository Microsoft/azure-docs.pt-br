---
title: Tutorial para configurar o Azure Active Directory B2C com o Experian
titleSuffix: Azure AD B2C
description: Saiba como integrar Azure AD B2C autenticação com Experian para verificação de identificação e prova de ortografia com base em atributos de usuário para evitar fraudes.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928691"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Tutorial para configurar o Experian com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure AD B2C com o [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). O Experian fornece uma variedade de soluções, que podem ser encontradas [aqui](https://www.experian.com/).

Neste exemplo, a identidade digital integrada do Experian e a plataforma de risco de fraude **CrossCore** são usadas. CrossCore é um serviço de verificação de ID usado para verificar a identificação do usuário. Ele faz a análise de risco com base em várias informações fornecidas pelo usuário durante o fluxo de inscrição. CrossCore é usado para determinar se o usuário deve ter permissão para continuar a fazer logon ou não. Os seguintes atributos podem ser usados na análise de risco do CrossCore:

- Email
- Endereço IP
- Nome
- Middle Name
- Sobrenome
- Endereço
- City
- Estado/Província
- Código postal
- País/Região
- Número do telefone

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração do Experian inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização, responsável por verificar as credenciais do usuário, também conhecido como provedor de identidade

- Experian – o serviço Experian usa as entradas fornecidas pelo usuário e verifica a identidade do usuário

- API REST personalizada – essa API implementa a integração entre Azure AD B2C e o serviço Experian.

O diagrama de arquitetura a seguir mostra a implementação.

![captura de tela para Experian-arquitetura-diagrama](media/partner-experian/experian-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. O usuário seleciona inscrever-se para criar uma nova conta e insere informações na página. Azure AD B2C coleta os atributos de usuário.
| 2. | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário.
| 3. | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API Experian pode consumir. Em seguida, envia-o para Experian.
| 4. | O Experian consome as informações e processa-a para validar a identificação do usuário com base na análise de riscos. Em seguida, ele retorna o resultado para a API da camada intermediária.
| 5. | A API de camada intermediária processa as informações e envia informações relevantes de volta no formato JSON correto para Azure AD B2C.
| 6. | Azure AD B2C recebe informações de volta da API de camada intermediária. Se ele mostrar uma resposta de falha, uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de êxito, o usuário será autenticado e gravado no diretório.

## <a name="onboard-with-experian"></a>Integração com o Experian

1. Para criar uma conta do Experian, entre em contato com [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. Depois que uma conta for criada, você receberá as informações necessárias para a configuração da API. As seções a seguir descrevem o processo.

## <a name="configure-azure-ad-b2c-with-experian"></a>Configurar Azure AD B2C com Experian

### <a name="part-1---deploy-the-api"></a>Parte 1-implantar a API

Implante o [código de API](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) fornecido em um serviço do Azure. O código pode ser publicado no Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2-implantar o certificado do cliente

A chamada à API Experian é protegida por um certificado de cliente. Esse certificado de cliente será fornecido pelo Experian. Seguindo as instruções mencionadas neste [documento](../app-service/environment/certificates.md#private-client-certificate), o certificado deve ser carregado para o serviço de Azure app. A política de exemplo usa estas etapas de chaves no processo:

- Carregar o certificado

- Defina a `WEBSITE_LOAD_ROOT_CERTIFICATES` chave com a impressão digital do certificado.

### <a name="part-3---configure-the-api"></a>Parte 3-configurar a API

As configurações do aplicativo podem ser [configuradas no serviço de aplicativo no Azure](../app-service/configure-common.md#configure-app-settings). Com esse método, as configurações podem ser configuradas com segurança sem verificá-las em um repositório. Você precisará fornecer as seguintes configurações para a API REST:

| Configurações do aplicativo | Fonte | Observações |
| :-------- | :------------| :-----------|
|CrossCoreConfig: Tenantid | Configuração da conta do Experian |     |
|CrossCoreConfig:OrgCode | Configuração da conta do Experian |     |
|CrossCore:ApiEndpoint |Configuração da conta do Experian|  |
|CrossCore:ClientReference | Configuração da conta do Experian | |
| CrossCore:ModelCode |Configuração da conta do Experian|
| CrossCore:OrgCode | Configuração da conta do Experian |
| CrossCore:SignatureKey  | Configuração da conta do Experian |
| CrossCore: Tenantid  | Configuração da conta do Experian |
| CrossCore: CertificateThumbprint | Certificado Experian |
| BasicAuth:ApiUsername | Definir um nome de usuário para a API | Usado na configuração do ExtId |
| BasicAuth:ApiPassword | Definir uma senha para a API | Usado na configuração do ExtId

### <a name="part-4---create-api-policy-keys"></a>Parte 4-criar chaves de política de API

Consulte este [documento](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) e crie duas chaves de política – uma para o nome de usuário da API e outra para a senha da API que você definiu acima para autenticação básica http.

>[!NOTE]
>Você precisará das chaves para configurar as políticas posteriormente.

### <a name="part-5---replace-the-configuration-values"></a>Parte 5-substituir os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)fornecidas, localize os seguintes espaços reservados e substitua pelos valores correspondentes da sua instância

|                      Espaço reservado                       |                                   Substituir pelo valor                                 |                   Exemplo                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Seu nome curto do locatário                                                           | "seulocatario" de yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Azure AD B2C nome da sua política TrustFrameworkBase                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID do aplicativo IdentityExperienceFramework configurada em seu locatário de Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID do aplicativo ProxyIdentityExperienceFramework configurada em seu locatário de Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID de aplicativo do aplicativo de armazenamento do seu locatário                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID de objeto do aplicativo de armazenamento do locatário                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_api_username_key_name}                           | Nome da chave de nome de usuário que você criou [aqui](#part-4---create-api-policy-keys)             | B2C \_ 1Uma \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Nome da chave de senha que você criou [aqui](#part-4---create-api-policy-keys)             | B2C \_ 1Uma \_ RestApiPassword                     |
| {your_app_service_URL}                                 | URL do serviço de aplicativo que você configurou                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6-configurar a política de Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para obter instruções sobre como configurar seu locatário Azure ad B2C e configurar políticas.

>[!NOTE]
>Esta política de exemplo se baseia no [pacote de início de contas locais](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Como prática recomendada, recomendamos que os clientes adicionem notificação de autorização na página coleção de atributos. Notifique os usuários que as informações serão enviadas a serviços de terceiros para verificação de identidade.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **fluxos de usuário**.

2. Selecione o fluxo de **usuário** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. Fazer logoff

6. Percorrer o fluxo de entrada  

7. O quebra-cabeça CrossCore será exibido depois que você entrar em **continuar**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

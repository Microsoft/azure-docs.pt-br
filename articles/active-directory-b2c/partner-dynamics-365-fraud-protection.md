---
title: Tutorial para configurar Azure Active Directory B2C com a proteção contra fraudes do Microsoft Dynamics 365
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Active Directory B2C com a proteção contra fraudes do Microsoft Dynamics 365 para identificar contas arriscadas e fraudulentas
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9483b0a8829c042a7cf8d516c6007cbbf14a97ac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639745"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Tutorial: configurar a proteção contra fraudes do Microsoft Dynamics 365 com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o DFP ( [proteção contra fraude do Microsoft Dynamics 365](https://docs.microsoft.com/dynamics365/fraud-protection/overview) ) com o Azure Active Directory (AD) B2C.

O Microsoft DFP fornece aos clientes a capacidade de avaliar se o risco de tentativas de criar novas contas e tentativas de logon no ecossistema do cliente são fraudulentos. A avaliação do Microsoft DFP pode ser usada pelo cliente para bloquear ou desafiar tentativas suspeitas de criar novas contas falsas ou de comprometer as contas existentes. A proteção de conta inclui a inteligência artificial capacitada para impressão digital de dispositivos, APIs para avaliação de riscos em tempo real, experiência de regras e listas para otimizar a estratégia de risco conforme as necessidades de negócios de cada cliente e um scorecard para monitorar a eficácia e as tendências da proteção contra fraudes no ecossistema do cliente.

Neste exemplo, integraremos os recursos de proteção de conta do Microsoft DFP com um fluxo de usuário Azure AD B2C. O serviço será impressão digital externamente todas as tentativas de entrada ou inscrição e observará qualquer comportamento suspeito passado ou presente. Azure AD B2C invoca um ponto de extremidade de decisão do Microsoft DFP, que retorna um resultado com base em todos os comportamentos passados e presentes do usuário identificado e também as regras personalizadas especificadas no serviço Microsoft DFP. Azure AD B2C toma uma decisão de aprovação com base nesse resultado e passa o mesmo de volta para o Microsoft DFP.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md). O locatário está vinculado à sua assinatura do Azure.

- Obtenha uma [assinatura](https://dynamics.microsoft.com/pricing/#Sales)do Microsoft DFP. Você também pode configurar uma [versão de cliente de avaliação](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) .

## <a name="scenario-description"></a>Descrição do cenário

A integração do Microsoft DFP inclui os seguintes componentes:

- **Azure ad B2C locatário**: autentica o usuário e atua como um cliente do Microsoft DFP. Hospeda um script de impressão digital coletando dados de diagnóstico e de identificação de cada usuário que executa uma política de destino. Bloqueia ou desafia mais tarde as tentativas de entrada ou inscrição se o Microsoft DFP encontrá-las suspeitas.

- **Serviço de aplicativo personalizado**: um aplicativo Web que atende a duas finalidades.

  - O serve páginas HTML para serem usadas como interface do usuário da estrutura de experiência de identidade. Responsável por inserir o script de impressão digital do Microsoft Dynamics 365.

  - Um controlador de API com pontos de extremidade RESTful que conecta o Microsoft DFP ao Azure AD B2C. O processamento de dados do identificador, a estrutura e adere aos requisitos de segurança de ambos.

- **Serviço de impressão digital do Microsoft DFP**: script inserido dinamicamente, que registra a telemetria do dispositivo e os detalhes do usuário autodeclarado para criar uma impressão digital exclusivamente identificável para que o usuário seja usado posteriormente no processo de tomada de decisão.

- **Pontos de extremidade da API do Microsoft DFP**: fornece o resultado da decisão e aceita um status final que reflete a operação executada pelo aplicativo cliente. Azure AD B2C não se comunicam com os pontos de extremidade diretamente devido a diversos requisitos de segurança e carga de API, em vez disso, o usa o serviço de aplicativo como intermediário.

O diagrama de arquitetura a seguir mostra a implementação.

![Imagem mostra o diagrama da arquitetura de proteção contra fraudes da Microsoft dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. Os usuários selecionam inscrever-se para criar uma nova conta e inserir informações na página. Azure AD B2C coleta atributos de usuário.
| 2. | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário.
| 3. | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API do Microsoft DFP pode consumir. Depois de enviá-lo para a API do Microsoft DFP.
| 4. | Depois que a API do Microsoft DFP consumir as informações e processá-las, ela retornará o resultado para a API da camada intermediária.
| 5. | A API de camada intermediária processa as informações e envia informações relevantes de volta para Azure AD B2C.
| 6. | Azure AD B2C recebe informações de volta da API da camada intermediária. Se ele mostrar uma resposta de falha, uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de êxito, o usuário será autenticado e gravado no diretório.

## <a name="set-up-the-solution"></a>Configurar a solução

1. [Crie um aplicativo do Facebook](./identity-provider-facebook.md#create-a-facebook-application) configurado para permitir que a Federação Azure ad B2C.
2. [Adicione o segredo do Facebook](./custom-policy-get-started.md#create-the-facebook-key) criado como uma chave de política da estrutura de experiência de identidade.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configurar seu aplicativo no Microsoft DFP

[Configure seu locatário do Azure ad](/dynamics365/fraud-protection/integrate-real-time-api) para usar o Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Implantar no aplicativo Web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementar impressão digital do Microsoft DFP Service

A [impressão digital do dispositivo Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) é um requisito para a proteção de conta do Microsoft DFP.

>[!NOTE]
>Além de Azure AD B2C páginas de interface do usuário, o cliente também pode implementar o serviço de impressão digital no código do aplicativo para a criação de perfil de dispositivo mais abrangente. O serviço de impressão digital no código do aplicativo não está incluído neste exemplo.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Implantar o código de API Azure AD B2C

Implante o [código de API fornecido](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) em um serviço do Azure. O código pode ser [publicado no Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Configurar CORS, adicionar **origem permitida**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Posteriormente, você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

Consulte a [documentação do serviço de aplicativo](../app-service/app-service-web-tutorial-rest-api.md) para saber mais.

### <a name="add-context-dependent-configuration-settings"></a>Adicionar definições de configuração dependentes de contexto

Defina as configurações do aplicativo no [serviço de aplicativo no Azure](../app-service/configure-common.md#configure-app-settings). Isso permite que as configurações sejam configuradas com segurança sem verificá-las em um repositório. A API REST precisa das seguintes configurações fornecidas:

| Configurações do aplicativo | Fonte | Observações |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Configuração do Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Sua ID do cliente de impressão digital do dispositivo Microsoft |     |
| FraudProtectionSettings:ApiBaseUrl |  Sua URL base do portal DFP da Microsoft   | Remova "-int" para chamar a API de produção|
|  TokenProviderConfig: recurso  | Sua URL base- https://api.dfp.dynamics-int.com     | Remova "-int" para chamar a API de produção|
|   TokenProviderConfig: ClientId       |Sua ID do aplicativo cliente do Azure AD de comerciante de proteção contra fraude      |       |
| TokenProviderConfig: autoridade | https://login.microsoftonline.com/<directory_ID> | Sua autoridade de locatário do Azure AD de comerciante de proteção contra fraudes |
| TokenProviderConfig: CertificateThumbprint * | A impressão digital do certificado a ser usada para autenticar em seu aplicativo cliente do Azure AD de comerciante |
| TokenProviderConfig: ClientSecret * | O segredo para seu aplicativo cliente do Azure AD de comerciante | Recomendado para usar um Gerenciador de segredos |

* Somente defina 1 dos dois parâmetros marcados dependendo de se você autenticar com um certificado ou um segredo, como uma senha.

## <a name="azure-ad-b2c-configuration"></a>Configuração de Azure AD B2C

### <a name="replace-the-configuration-values"></a>Substituir os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)fornecidas, localize os espaços reservados a seguir e substitua-os pelos valores correspondentes da sua instância.

| Espaço reservado | Substitua por | Observações |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Seu nome curto do locatário |  "seulocatario" de yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID do locatário do seu locatário de Azure AD B2C |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID do aplicativo IdentityExperienceFramework configurada em seu locatário de Azure AD B2C    |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID do aplicativo ProxyIdentityExperienceFramework configurada em seu locatário de Azure AD B2C      |   01234567-89ab-cdef-0123-456789ABCDEF     |
|  {your_tenant_extensions_appid}   |  ID de aplicativo do aplicativo de armazenamento do seu locatário   |  01234567-89ab-cdef-0123-456789ABCDEF  |
|   {your_tenant_extensions_app_objectid}  | ID de objeto do aplicativo de armazenamento do locatário    | 01234567-89ab-cdef-0123-456789ABCDEF   |
|   {your_app_insights_instrumentation_key}  |   Chave de instrumentação de sua instância do App insights *  |   01234567-89ab-cdef-0123-456789ABCDEF |
|  {your_ui_base_url}   | Ponto de extremidade em seu serviço de aplicativo de onde seus arquivos de interface do usuário são atendidos    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | URL do seu serviço de aplicativo    |  https://yourapp.azurewebsites.net  |
|   {your-Facebook-app-ID}  |  ID do aplicativo do Facebook que você configurou para federação com Azure AD B2C   | 000000000000000   |
|  {seu-Facebook-app-Secret}   |  Nome da chave de política na qual você salvou o segredo do aplicativo do Facebook   | B2C_1A_FacebookAppSecret   |

* O app insights pode estar em um locatário diferente. Esta etapa é opcional. Remova os TechnicalProfiles e OrechestrationSteps correspondentes se não forem necessários.

### <a name="call-microsoft-dfp-label-api"></a>Chamar a API de rótulo do Microsoft DFP

Os clientes precisam [implementar a API de rótulo](/dynamics365/fraud-protection/integrate-ap-api). Consulte [API do Microsoft DFP](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) para saber mais.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

O valor de userID precisa ser o mesmo que o valor de configuração de Azure AD B2C correspondente (ObjectID).

>[!NOTE]
>Adicionar notificação de consentimento à página de coleção de atributos. Notifique que a telemetria dos usuários e as informações de identidade do usuário serão registradas para fins de proteção de conta.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurar a política de Azure AD B2C

1. Vá para a [política de Azure ad B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) na pasta políticas.

2. Siga este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de início do LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configure a política para o locatário Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para relacionar-se ao seu locatário específico.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **estrutura de experiência de identidade**.

2. Selecione o **SignUpSignIn** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. O serviço Microsoft DFP será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

>[!NOTE]
>Atualize as regras diretamente no portal do Microsoft DFP se estiver usando [o mecanismo de regras do Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Exemplos do Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

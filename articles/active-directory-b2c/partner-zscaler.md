---
title: Tutorial – configurar o Azure Active Directory B2C com o Zscaler
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe427150b15c6bccb97172ae751235d388c95c7b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675019"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Tutorial: configurar o acesso privado do Zscaler com o Azure Active Directory B2C

Neste tutorial, você aprenderá a integrar a autenticação de Azure Active Directory B2C (Azure AD B2C) com o [ZPA (Zscaler Private Access)](https://www.zscaler.com/products/zscaler-private-access). O ZPA fornece acesso seguro e baseado em políticas a aplicativos e ativos privados sem o custo, os aborrecimentos ou os riscos de segurança de uma VPN (rede virtual privada). A oferta de acesso híbrido seguro do Zscaler permite uma superfície de ataque zero para aplicativos voltados para o consumidor quando ele é combinado com Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

- Uma assinatura do Azure. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).  
- [Um locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.  
- [Uma assinatura do ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Descrição do cenário

A integração do ZPA inclui os seguintes componentes:

- **Azure ad B2C**: o IDP (provedor de identidade) que é responsável por verificar as credenciais do usuário. Também é responsável por inscrever um novo usuário.  
- **ZPA**: o serviço responsável por proteger o aplicativo Web impondo o [acesso de confiança zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **O aplicativo Web**: hospeda o serviço que o usuário está tentando acessar.

O diagrama a seguir mostra como o ZPA se integra ao Azure AD B2C.

![Diagrama da arquitetura Zscaler, mostrando como o ZPA se integra ao Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

A sequência é descrita na tabela a seguir:

|Etapa | Descrição |
| :-----:| :-----------|
| 1 | Um usuário chega em um portal do usuário do ZPA ou um aplicativo de acesso ao navegador do ZPA.
| 2 | O ZPA requer informações de contexto do usuário antes de poder decidir se deseja permitir que o usuário acesse o aplicativo Web. Para autenticar o usuário, o ZPA executa um redirecionamento SAML para a página de logon Azure AD B2C.  
| 3 | O usuário chega na página de logon do Azure AB B2C. Novos usuários se inscrevem para criar uma conta e os usuários existentes fazem logon com suas credenciais existentes. Azure AD B2C valida a identidade do usuário.
| 4 | Após a autenticação bem-sucedida, Azure AD B2C redireciona o usuário de volta para ZPA junto com a Asserção SAML. ZPA verifica a Asserção SAML e define o contexto do usuário.
| 5 | ZPA avalia as políticas de acesso para o usuário. Se o usuário tiver permissão para acessar o aplicativo Web, a conexão poderá ser transmitida.

## <a name="onboard-to-zpa"></a>Integração ao ZPA

Este tutorial pressupõe que você já tenha uma configuração de ZPA funcional. Se você estiver começando a usar o ZPA, consulte o [Guia de configuração passo a passo para o ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integrar o ZPA ao Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Etapa 1: configurar Azure AD B2C como um IdP em ZPA

Para configurar Azure AD B2C como um [IDP em ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), faça o seguinte:

1. Faça logon no [portal de administração do ZPA](https://admin.private.zscaler.com).

1. Vá para **Administração**  >  **configuração do IDP**.

1. Selecione **Adicionar configuração IDP**.

   O painel **Adicionar configuração IDP** é aberto.

   ![Captura de tela da guia "informações do IdP" no painel "adicionar configuração IdP".](media/partner-zscaler/add-idp-configuration.png)

1. Selecione a guia **informações do IDP** e, em seguida, faça o seguinte:

   a. Na caixa **nome** , digite **Azure ad B2C**.  
   b. Em **logon único**, selecione **usuário**.  
   c. Na lista suspensa **domínios** , selecione os domínios de autenticação que você deseja associar a este IDP.

1. Selecione **Avançar**.

1. Selecione a guia **metadados do SP** e, em seguida, faça o seguinte:

   a. Em **URL do provedor de serviços**, copie ou anote o valor para uso posterior.  
   b. Em **ID da entidade do provedor de serviço**, copie ou anote o valor para uso posterior.

   ![Captura de tela da guia "metadados do SP" no painel "adicionar configuração do IdP".](media/partner-zscaler/sp-metadata.png)

1. Selecione **Pausar**.

Depois de configurar Azure AD B2C, o restante da configuração do IdP será retomada.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Etapa 2: configurar políticas personalizadas no Azure AD B2C

>[!Note]
>Esta etapa será necessária somente se você ainda não tiver configurado políticas personalizadas. Se você já tiver uma ou mais políticas personalizadas, poderá ignorar esta etapa.

Para configurar políticas personalizadas em seu locatário do Azure AD B2C, consulte Introdução [às políticas personalizadas no Azure Active Directory B2C](./custom-policy-get-started.md).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Etapa 3: registrar o ZPA como um aplicativo SAML no Azure AD B2C

Para configurar um aplicativo SAML no Azure AD B2C, consulte [registrar um aplicativo SAML no Azure ad B2C](./connect-with-saml-service-providers.md). 

Na etapa ["3,2 carregar e testar seus metadados de política"](./connect-with-saml-service-providers.md#32-upload-and-test-your-policy-metadata), copie ou anote a URL de metadados SAML do IDP usada pelo Azure ad B2C. Você precisará dela mais tarde.

Siga as instruções na etapa ["4,2 atualizar o manifesto do aplicativo"](./connect-with-saml-service-providers.md#42-update-the-app-manifest). Na etapa 4,2, atualize as propriedades do manifesto do aplicativo da seguinte maneira:

- Para **identifierUris**: Use a ID de entidade do provedor de serviços que você copiou ou anotou anteriormente em "Step 1.6. b".  
- Para **samlMetadataUrl**: ignore essa propriedade, porque ZPA não hospeda uma URL de metadados do SAML.  
- Para **replyUrlsWithType**: Use a URL do provedor de serviços que você copiou ou anotou anteriormente em "Step 1.6. a".  
- Para **logoutUrl**: ignore esta propriedade, porque ZPA não dá suporte a uma URL de logout.

O restante das etapas não é relevante para este tutorial.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Etapa 4: extrair os metadados de SAML do IdP do Azure AD B2C

Em seguida, você precisa obter uma URL de metadados do SAML no seguinte formato:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Observe que `<tenant-name>` é o nome do seu locatário de Azure ad B2C e `<policy-name>` é o nome da política SAML personalizada que você criou na etapa anterior.

Por exemplo, a URL pode ser `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata`.

Abra um navegador da Web e vá para a URL de metadados do SAML. Clique com o botão direito do mouse em qualquer lugar da página, selecione **salvar como** e salve o arquivo em seu computador para uso na próxima etapa.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Etapa 5: concluir a configuração do IdP em ZPA

Conclua a [configuração do IDP no portal de administração do ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) que você configurou parcialmente anteriormente em "Step 1: Configure Azure ad B2C como um IDP no ZPA".

1. No [portal de administração do ZPA](https://admin.private.zscaler.com), vá para **Administração**  >  **configuração do IDP**.

1. Selecione o IdP que você configurou na "etapa 1" e selecione **retomar**.

1. No painel **Adicionar configuração IDP** , selecione a guia **criar IDP** e, em seguida, faça o seguinte:

   a. Em **arquivo de metadados IDP**, carregue o arquivo de metadados que você salvou anteriormente em "etapa 4: extrair os metadados SAML do IdP do Azure ad B2C".  
   b. Verifique se o **status** da configuração IDP está **habilitado**.  
   c. Selecione **Salvar**.

   ![Captura de tela da guia "criar IdP" no painel "adicionar configuração IdP".](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testar a solução

Vá para um portal do usuário do ZPA ou um aplicativo de acesso ao navegador e teste o processo de inscrição ou entrada. O teste deve resultar em uma autenticação SAML bem-sucedida.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, examine os seguintes artigos:

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md)
- [Registrar um aplicativo SAML no Azure AD B2C](./connect-with-saml-service-providers.md)
- [Guia de configuração passo a passo para ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Configurar um IdP para logon único](https://help.zscaler.com/zpa/configuring-idp-single-sign)
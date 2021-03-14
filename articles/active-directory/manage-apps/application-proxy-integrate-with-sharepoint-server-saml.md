---
title: Publicar em = SharePoint local com o proxy de aplicativo-Azure AD
description: Aborda as noções básicas sobre como integrar um servidor do SharePoint local com o Azure Proxy de Aplicativo do AD para SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cadf5b7d92e26e561e570f824295e69ca421e16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644503"
---
# <a name="integrate-with-sharepoint-saml"></a>Integrar ao SharePoint (SAML)

Este guia passo a passo explica como proteger o acesso ao [Azure Active Directory do SharePoint local integrado (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) usando o proxy de aplicativo do AD do Azure, onde os usuários em sua organização (Azure AD, B2B) se conectam ao SharePoint pela Internet.

> [!NOTE] 
> Se você for novo no Azure Proxy de Aplicativo do AD e quiser saber mais, consulte [acesso remoto a aplicativos locais por meio do azure proxy de aplicativo do AD](./application-proxy.md).

Há três vantagens principais dessa configuração:

- O Azure Proxy de Aplicativo do AD garante que o tráfego autenticado possa acessar sua rede interna e o servidor do SharePoint.
- Os usuários podem acessar os sites do SharePoint normalmente sem usar a VPN.
- Você pode controlar a atribuição de acesso por usuário no nível de Proxy de Aplicativo do AD do Azure e pode aumentar a segurança com recursos do Azure AD como acesso condicional e autenticação multifator (MFA).

Esse processo requer dois aplicativos empresariais. Uma é uma instância local do SharePoint que você publica da galeria na sua lista de aplicativos SaaS gerenciados. O segundo é um aplicativo local (aplicativo inexistente na Galeria) que você usará para publicar o primeiro aplicativo da Galeria de empresas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essa configuração, você precisa dos seguintes recursos:
 - Um farm do SharePoint 2013 ou mais recente. O farm do SharePoint deve ser [integrado ao Azure ad](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Um locatário do Azure AD com um plano que inclui o proxy de aplicativo. Saiba mais sobre os [preços e planos do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).
 - Um [domínio personalizado e verificado](../fundamentals/add-custom-domain.md) no locatário do Azure AD. O domínio verificado deve corresponder ao sufixo de URL do SharePoint.
 - Um certificado SSL é necessário. Consulte os detalhes em [publicação de domínio personalizado](./application-proxy-configure-custom-domain.md).
 - Os usuários locais Active Directory devem ser sincronizados com o Azure AD Connect e devem ser configurados para [entrar no Azure](../hybrid/plan-connect-user-signin.md). 
 - Para usuários convidados somente em nuvem e B2B, você precisa [conceder acesso a uma conta de convidado para o SharePoint local no portal do Azure](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Um conector de proxy de aplicativo instalado e em execução em um computador dentro do domínio corporativo.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Etapa 1: integrar o SharePoint local ao Azure AD 

1. Configure o aplicativo local do SharePoint. Para obter mais informações, consulte [tutorial: integração de logon único do Azure Active Directory com o SharePoint local](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Valide a configuração antes de passar para a próxima etapa. Para validar, tente acessar o SharePoint local da rede interna e confirmar que ele está acessível internamente. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Etapa 2: publicar o aplicativo local do SharePoint com o proxy de aplicativo

Nesta etapa, você cria um aplicativo em seu locatário do Azure AD que usa o proxy de aplicativo. Você define a URL externa e especifica a URL interna, que são usadas posteriormente no SharePoint.

> [!NOTE] 
> As URLs internas e externas devem corresponder à **URL de logon** na configuração de aplicativo baseada em SAML na etapa 1.

   ![Captura de tela que mostra o valor da URL de logon.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Crie um novo aplicativo de Proxy de Aplicativo do AD do Azure com o domínio personalizado. Para obter instruções detalhadas, consulte [domínios personalizados no Azure proxy de aplicativo do AD](./application-proxy-configure-custom-domain.md).

    - URL interna: https://portal.contoso.com/
    - URL externa: https://portal.contoso.com/
    - Pré-autenticação: Azure Active Directory
    - Converter URLs em cabeçalhos: não
    - Traduzir URLs no corpo do aplicativo: não

        ![Captura de tela que mostra as opções usadas para criar o aplicativo.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Atribua os [mesmos grupos](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) que você atribuiu ao aplicativo da galeria do SharePoint local.

3. Por fim, vá para a seção **Propriedades** e defina **visível para usuários?** como **não**. Essa opção garante que apenas o ícone do primeiro aplicativo seja exibido no portal meus aplicativos ( https://myapplications.microsoft.com) .

   ![Captura de tela que mostra onde definir o visível para os usuários? Option.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Etapa 3: testar seu aplicativo

Usando um navegador de um computador em uma rede externa, navegue até a URL ( https://portal.contoso.com/) que você configurou durante a etapa de publicação. Certifique-se de que você pode entrar com a conta de teste que você configurou.
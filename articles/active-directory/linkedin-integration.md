---
title: "Configurar a integração do LinkedIn no Azure AD | Microsoft Docs"
description: "Explica como habilitar ou desabilitar a integração do LinkedIn para aplicativos da Microsoft no Azure Active Directory."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Habilitando a integração do LinkedIn no Azure Active Directory
Habilitar a integração do LinkedIn permite aos usuários acessar dados LinkedIn públicos e, se desejarem, também a rede LinkedIn pessoal deles de dentro de aplicativos da Microsoft. Cada usuário pode, de modo independente, escolher conectar a própria conta de trabalho à própria conta do LinkedIn.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>Integração do LinkedIn da perspectiva de seus usuários finais
Quando os usuários finais na sua organização conectam as próprias contas do LinkedIn às próprias contas de trabalho, eles são capazes de identificar melhor as pessoas com quem trabalham dentro e fora da organização. Conectar as duas contas permite que as conexões e dados de perfil do LinkedIn do usuário sejam usados em aplicativos da Microsoft da sua organização, mas eles sempre têm a opção de evitar isso removendo a permissão para o LinkedIn compartilhar esses dados. A integração também usa informações de perfil disponíveis publicamente, sendo que os usuários podem optar por compartilhar suas informações de rede e de perfil públicas com aplicativos da Microsoft por meio das configurações de privacidade do LinkedIn.

>[!NOTE]
> Habilitar a integração do LinkedIn no Azure AD permite que aplicativos e serviços acessem algumas das informações de seus usuários finais. Leia a [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement/) para saber mais sobre as considerações de privacidade, ao habilitar a integração do LinkedIn no Azure AD. 

## <a name="enable-linkedin-integration"></a>Habilitar a integração do LinkedIn
A integração do LinkedIn para empresas é habilitada por padrão no Azure AD. Assim, quando esse recurso é disponibilizado para seu locatário, todos os usuários em sua organização podem ver perfis e recursos do LinkedIn. Habilitar a integração do LinkedIn permite que os usuários em sua organização usem os recursos do LinkedIn dentro de serviços Microsoft, tais como exibir perfis ao receber um email no Outlook. Desabilitar esse recurso impede o acesso a recursos do LinkedIn e interrompe conexões de conta de usuário e compartilhamento de dados entre o LinkedIn e sua empresa por meio de serviços da Microsoft.

> [!IMPORTANT]
> Este recurso não está disponível para locatários do governo, soberanos e go-local. Além disso, atualizações de serviço do Azure AD, tais como funcionalidades de integração do LinkedIn, não são implantadas em todos os locatários do Azure simultaneamente. Você não pode habilitar a integração do LinkedIn com o Azure AD até que essa funcionalidade tenha sido implantada em seu locatário do Azure.

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do diretório.
2. Selecione **Usuários e grupos**.
3. Na folha **Usuários e grupos**, selecione **Configurações de usuário**.
4. Em **Integração do LinkedIn**, selecione Sim ou Não para habilitar ou desabilitar a integração do LinkedIn.
   ![Habilitando a integração do LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Saiba mais 
* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Próximas etapas
Você pode usar o link a seguir para habilitar ou desabilitar a integração do LinkedIn com o Azure AD do Portal do Azure:

[Configurar a integração do LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 
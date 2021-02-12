---
title: Integrar aplicativos locais com o Cloud App Security-Azure AD
description: Configure um aplicativo local no Azure Active Directory para trabalhar com MCAS (Microsoft Cloud App Security). Use o Controle de Aplicativos de Acesso Condicional MCAS para monitorar e controlar sessões em tempo real com base em políticas de acesso condicional. Você pode aplicar essas políticas a aplicativos locais que usam o Proxy de Aplicativo no Azure AD (Azure Active Directory).
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139f347a3d1bce7a8f841cd1c39cd250b9a2182d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259348"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configurar o monitoramento de acesso de aplicativo em tempo real com Microsoft Cloud App Security e Azure Active Directory
Configure um aplicativo local no Azure AD (Azure Active Directory) para usar o MCAS (Microsoft Cloud App Security) para monitoramento em tempo real. O MCAS usa Controle de Aplicativos de Acesso Condicional para monitorar e controlar sessões em tempo real com base em políticas de acesso condicional. Você pode aplicar essas políticas a aplicativos locais que usam o Proxy de Aplicativo no Azure AD (Azure Active Directory).

A seguir, são apresentados alguns exemplos dos tipos de políticas que podem ser criadas com MCAS:

- Bloqueie ou proteja o download de documentos confidenciais em dispositivos não gerenciados.
- Monitore quando usuários de alto risco conectam-se aos aplicativos e, em seguida, registram suas ações na sessão. Com essas informações, é possível analisar o comportamento do usuário para determinar como aplicar políticas de sessão.
- Use certificados do cliente ou conformidade de dispositivos para bloquear o acesso a aplicativos específicos de dispositivos não gerenciados.
- Restrinja sessões de usuários de redes não corporativas. É possível conceder acesso restrito a usuários que acessam um aplicativo de fora da rede corporativa. Por exemplo, esse acesso restrito pode impedir o usuário de baixar documentos confidenciais.

Para obter mais informações, consulte [Proteger aplicativos com Controle de Aplicativos de Acesso Condicional do Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Requisitos

Licença:

- Licença EMS E5 ou 
- Azure Active Directory Premium P1 e MCAS Standalone.

Aplicativo local:

- O aplicativo local deve usar KCD (Delegação Restrita de Kerberos)

Configurar Proxy de Aplicativo:

- Configure o Azure AD para usar o Proxy de Aplicativo, inclusive preparando o ambiente e instalando o conector do Proxy de Aplicativo. Para um tutorial, consulte [Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Adicionar aplicativo local ao Azure AD

Adicione um aplicativo local ao Azure AD. Para um início rápido, consulte [Adicionar um aplicativo local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Ao adicionar o aplicativo, certifique-se de definir as duas configurações a seguir na folha **Adicionar seu aplicativo local**:

- **Pré-autenticação**: insira **Azure Active Directory**.
- **Traduzir URLs no corpo do aplicativo**: escolha **Sim**.

Essas duas configurações são necessárias para o aplicativo funcionar com o MCAS.

## <a name="test-the-on-premises-application"></a>Teste o aplicativo local

Após adicionar o aplicativo ao Azure AD, use as etapas em [Testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application) para adicionar um usuário para teste e testar o logon. 

## <a name="deploy-conditional-access-app-control"></a>Implantar o Controle de Aplicativos de Acesso Condicional

Para configurar o aplicativo com o Controle de Aplicativos de Acesso Condicional, siga as instruções em [Implantar o Controle de Aplicativos de Acesso Condicional para aplicativos Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Testar o Controle de Aplicativos de Acesso Condicional

Para testar a implantação de aplicativos Azure AD com o Controle de Aplicativos de Acesso Condicional, siga as instruções em [Testar a implantação para aplicativos Azure AD](/cloud-app-security/proxy-deployment-aad).






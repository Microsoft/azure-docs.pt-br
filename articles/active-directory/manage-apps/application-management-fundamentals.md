---
title: 'Gerenciamento de aplicativos: práticas recomendadas e recomendações | Microsoft Docs'
description: Conheça as práticas recomendadas e recomendações para o gerenciamento de aplicativos no Azure Active Directory. Saiba como usar o provisionamento automático e a publicação de aplicativos locais com o proxy de aplicativo.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23c688d9b2e118ef29303d435bb83ef02ad36105
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259127"
---
# <a name="application-management-best-practices"></a>Melhores práticas de gerenciamento de aplicativos

Este artigo contém recomendações e práticas recomendadas para o gerenciamento de aplicativos no Azure Active Directory (AD do Azure), usando o provisionamento automático e Publicando aplicativos locais com o proxy de aplicativo.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Recomendações de logon único e de aplicativo de nuvem
| Recomendação | Comentários |
| --- | --- |
| Verificar a Galeria de aplicativos do Azure AD para aplicativos  | O Azure AD tem uma galeria que contém milhares de aplicativos previamente integrados que são habilitados com o SSO (logon único) corporativo. Para obter diretrizes de instalação específicas do aplicativo, consulte a [lista de tutoriais do aplicativo SaaS](../saas-apps/tutorial-list.md).  | 
| Usar SSO baseado em SAML federado  | Quando um aplicativo dá suporte a ele, use SSO Federado baseado em SAML com o Azure AD em vez de SSO baseado em senha e ADFS.  | 
| Usar SHA-256 para assinatura de certificado  | O Azure AD usa o algoritmo SHA-256 por padrão para assinar a resposta SAML. Use SHA-256, a menos que o aplicativo exija SHA-1 (consulte [Opções de assinatura de certificado](certificate-signing-options.md) e problema de [conexão do aplicativo](application-sign-in-problem-application-error.md)).  | 
| Exigir atribuição de usuário  | Por padrão, os usuários podem acessar seus aplicativos empresariais sem serem atribuídos a eles. No entanto, se o aplicativo expor funções ou se você quiser que o aplicativo apareça nos meus aplicativos de um usuário, exija a atribuição de usuário.  | 
| Implantar meus aplicativos para seus usuários | [Meus aplicativos](end-user-experiences.md) em `https://myapps.microsoft.com` é um portal baseado na Web que fornece aos usuários um único ponto de entrada para seus aplicativos baseados em nuvem atribuídos. Como recursos adicionais como gerenciamento de grupo e redefinição de senha de autoatendimento são adicionados, os usuários podem encontrá-los em meus aplicativos. Consulte [planejar a implantação de meus aplicativos](my-apps-deployment-plan.md).
| Usar atribuição de grupo  | Se estiver incluído em sua assinatura, atribua grupos a um aplicativo para que você possa delegar o gerenciamento de acesso contínuo ao proprietário do grupo.  | 
| Estabelecer um processo para gerenciar certificados | O tempo de vida máximo de um certificado de autenticação é de três anos. Para evitar ou minimizar a interrupção devido a um certificado expirando, use funções e listas de distribuição de email para garantir que as notificações de alteração relacionadas ao certificado sejam monitoradas de forma detalhada. |

## <a name="provisioning-recommendations"></a>Recomendações de provisionamento
| Recomendação | Comentários |
| --- | --- |
| Use os tutoriais para configurar o provisionamento com aplicativos de nuvem | Consulte a [lista de tutoriais do aplicativo SaaS](../saas-apps/tutorial-list.md) para obter diretrizes passo a passo sobre como configurar o provisionamento para o aplicativo da galeria que você deseja adicionar. |
| Usar logs de provisionamento (versão prévia) para monitorar o status | Os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) fornecem detalhes sobre todas as ações executadas pelo serviço de provisionamento, incluindo o status para usuários individuais. |
| Atribuir um grupo de distribuição ao email de notificação de provisionamento | Para aumentar a visibilidade dos alertas críticos enviados pelo serviço de provisionamento, atribua um grupo de distribuição à configuração emails de notificação. |


## <a name="application-proxy-recommendations"></a>Recomendações de proxy de aplicativo
| Recomendação | Comentários |
| --- | --- |
| Usar o proxy de aplicativo para acesso remoto a recursos internos | O proxy de aplicativo é recomendado para conceder aos usuários remotos acesso a recursos internos, substituindo a necessidade de uma VPN ou proxy reverso. Ele não se destina a acessar recursos de dentro da rede corporativa porque poderia adicionar latência.
| Usar domínios personalizados | Configure domínios personalizados para seus aplicativos (consulte [configurar domínios personalizados](application-proxy-configure-custom-domain.md)) para que as URLs para usuários e entre aplicativos funcionem dentro ou fora da sua rede. Você também poderá controlar sua identidade visual e personalizar suas URLs.  Ao usar nomes de domínio personalizados, planeje adquirir um certificado público de uma autoridade de certificação confiável que não seja da Microsoft. O proxy de Aplicativo Azure dá suporte a certificados padrão, ([curinga](application-proxy-wildcard.md)) ou baseados em San. (Consulte [planejamento de proxy de aplicativo](application-proxy-deployment-plan.md).) |
| Sincronizar usuários antes de implantar o proxy de aplicativo | Antes de implantar o proxy de aplicativo, sincronize as identidades de usuário de um diretório local ou crie-as diretamente no Azure AD. Sincronização de Identidades permite que o Azure AD autentique previamente os usuários antes de conceder a eles acesso aos aplicativos publicados do proxy de aplicativo. Ele também fornece as informações de identificador de usuário necessárias para executar o logon único (SSO). (Consulte [planejamento de proxy de aplicativo](application-proxy-deployment-plan.md).) |
| Siga nossas dicas para alta disponibilidade e balanceamento de carga | Para saber como o tráfego flui entre usuários, conectores de proxy de aplicativo e servidores de aplicativos de back-end e para obter dicas para otimizar o desempenho e o balanceamento de carga, consulte [alta disponibilidade e balanceamento de carga dos seus aplicativos e conectores de proxy de aplicativo](application-proxy-high-availability-load-balancing.md). |
| Usar vários conectores | Use dois ou mais conectores de proxy de aplicativo para maior resiliência, disponibilidade e escala (consulte [conectores de proxy de aplicativo](application-proxy-connectors.md)). Crie grupos de conectores e verifique se cada grupo de conectores tem pelo menos dois conectores (três conectores são ideais). |
| Localize servidores de conector perto de servidores de aplicativos e verifique se eles estão no mesmo domínio | Para otimizar o desempenho, localize fisicamente o servidor do conector próximo aos servidores de aplicativos (consulte [considerações de topologia de rede](application-proxy-network-topology.md)). Além disso, o servidor do conector e os servidores de aplicativos Web devem pertencer ao mesmo domínio Active Directory ou devem abranger Domínios confiantes. Essa configuração é necessária para SSO com IWA (autenticação integrada do Windows) e KCD (delegação restrita de Kerberos). Se os servidores estiverem em domínios diferentes, você precisará usar a delegação baseada em recursos para o SSO (consulte [KCD para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Habilitar atualizações automáticas para conectores | Habilite atualizações automáticas para seus conectores para os recursos e correções de bug mais recentes. A Microsoft fornece suporte direto para a versão mais recente do conector e uma versão anterior. (Consulte [histórico de versão](application-proxy-release-version-history.md)de versão do proxy de aplicativo.) |
| Ignorar o proxy local | Para uma manutenção mais fácil, configure o conector para ignorar o proxy local para que ele se conecte diretamente aos serviços do Azure. (Consulte [conectores de proxy de aplicativo e servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Usar o Azure Proxy de Aplicativo do AD sobre o proxy de aplicativo Web | Use o Azure Proxy de Aplicativo do AD para a maioria dos cenários locais. O proxy de aplicativo Web só é preferencial em cenários que exigem um servidor proxy para AD FS e onde você não pode usar domínios personalizados no Azure Active Directory. (Consulte [migração de proxy de aplicativo](application-proxy-migration.md).) |
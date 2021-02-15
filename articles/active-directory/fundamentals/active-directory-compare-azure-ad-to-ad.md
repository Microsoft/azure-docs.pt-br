---
title: Comparar o Active Directory ao Azure Active Directory
description: Este documento compara Active Directory Domain Services (ADDS) com Azure Active Directory (AD). Ele descreve os principais conceitos em ambas as soluções de identidade e explica como ele é diferente ou semelhante.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371912"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Comparar o Active Directory ao Azure Active Directory

Azure Active Directory é a próxima evolução das soluções de gerenciamento de identidade e acesso para a nuvem. A Microsoft introduziu Active Directory Domain Services no Windows 2000 para oferecer às organizações a capacidade de gerenciar vários componentes e sistemas de infraestrutura local usando uma única identidade por usuário.

O Azure AD leva essa abordagem para o próximo nível, fornecendo às organizações uma solução de IDaaS (identidade como serviço) para todos os seus aplicativos na nuvem e no local.

A maioria dos administradores de ti está familiarizada com os conceitos de Active Directory Domain Services. A tabela a seguir descreve as diferenças e semelhanças entre Active Directory conceitos e Azure Active Directory.

|Conceito|AD (Active Directory)|Azure Active Directory |
|:-|:-|:-|
|**Usuários**|||
|Provisionamento: usuários | As organizações criam usuários internos manualmente ou usam um sistema de provisionamento interno ou automatizado, como o Microsoft Identity Manager, para integrar-se a um sistema de RH.|As organizações do AD existentes usam [Azure ad Connect](../hybrid/how-to-connect-sync-whatis.md) para sincronizar identidades para a nuvem.</br> O Azure AD adiciona suporte para criar automaticamente usuários de [sistemas de RH de nuvem](../saas-apps/workday-tutorial.md). </br>O Azure AD pode provisionar identidades em aplicativos SaaS [habilitados para scim](../app-provisioning/use-scim-to-provision-users-and-groups.md) para fornecer automaticamente aos aplicativos os detalhes necessários para permitir o acesso aos usuários. |
|Provisionamento: identidades externas| As organizações criam usuários externos manualmente como usuários regulares em uma floresta de AD externa dedicada, resultando em sobrecarga de administração para gerenciar o ciclo de vida de identidades externas (usuários convidados)| O Azure AD fornece uma classe especial de identidade para dar suporte a identidades externas. O [Azure ad B2B](/azure/active-directory/b2b/) gerenciará o link para a identidade do usuário externo para certificar-se de que eles são válidos. |
| Gerenciamento e grupos de direitos| Os administradores fazem os usuários membros de grupos. Os proprietários de aplicativos e recursos dão acesso a grupos a aplicativos ou recursos.| Os [grupos](./active-directory-groups-create-azure-portal.md) também estão disponíveis no Azure AD e os administradores também podem usar grupos para conceder permissões a recursos. No Azure AD, os administradores podem atribuir a associação a grupos manualmente ou usar uma consulta para incluir dinamicamente usuários em um grupo. </br> Os administradores podem usar o [Gerenciamento de direitos](../governance/entitlement-management-overview.md) no Azure ad para fornecer aos usuários acesso a uma coleção de aplicativos e recursos usando fluxos de trabalho e, se necessário, critérios baseados em tempo. |
| Gerenciamento de administração|As organizações usarão uma combinação de domínios, unidades organizacionais e grupos no AD para delegar direitos administrativos para gerenciar o diretório e os recursos que ele controla.| O Azure AD fornece [funções internas](./active-directory-users-assign-role-azure-portal.md) com seu sistema de controle de acesso baseado em função (RBAC do Azure AD) do Azure AD, com suporte limitado para a [criação de funções personalizadas](../roles/custom-overview.md) para delegar acesso privilegiado ao sistema de identidade, aos aplicativos e recursos que ele controla.</br>O gerenciamento de funções pode ser aprimorado com o [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) para fornecer acesso baseado em tempo hábil, restrito ou de fluxo de trabalho a funções privilegiadas. |
| Gerenciamento de credenciais| As credenciais no Active Directory baseiam-se em senhas, autenticação de certificado e autenticação de cartão inteligente. As senhas são gerenciadas usando políticas de senha baseadas no comprimento, na expiração e na complexidade da senha.|O Azure AD usa a [proteção de senha](../authentication/concept-password-ban-bad.md) inteligente para nuvem e local. A proteção inclui o bloqueio inteligente, além de bloquear frases e substituições de senha comuns e personalizadas. </br>O Azure AD aumenta significativamente [a segurança por meio da autenticação multifator](../authentication/concept-mfa-howitworks.md) e de tecnologias com [senha](../authentication/concept-authentication-passwordless.md) , como o FIDO2. </br>O Azure AD reduz os custos de suporte fornecendo aos usuários um sistema de [redefinição de senha de autoatendimento](../authentication/concept-sspr-howitworks.md) . |
| **Aplicativos**|||
| Aplicativos de infraestrutura|Active Directory forma a base para muitos componentes locais de infraestrutura, por exemplo, DNS, DHCP, IPSec, WiFi, NPS e acesso VPN|Em um novo mundo de nuvem, o Azure AD é o novo plano de controle para acessar aplicativos em comparação com controles de rede. Quando os usuários se autenticam[, o acesso condicional (CA)](../conditional-access/overview.md)controla quais usuários terão acesso a quais aplicativos sob condições necessárias.|
| Aplicativos tradicionais e herdados| A maioria dos aplicativos locais usa LDAP, autenticação Windows-Integrated (NTLM e Kerberos) ou autenticação baseada em cabeçalho para controlar o acesso aos usuários.| O Azure AD pode fornecer acesso a esses tipos de aplicativos locais usando agentes de [proxy de aplicativo do Azure ad](../manage-apps/application-proxy.md) em execução no local. Usando esse método, o Azure AD pode autenticar Active Directory usuários locais usando o Kerberos enquanto você migra ou precisa coexistir com aplicativos herdados. |
| Aplicativos SaaS|Active Directory não dá suporte a aplicativos SaaS nativamente e requer o sistema de Federação, como AD FS.|Aplicativos SaaS com suporte a OAuth2, SAML e WS- \* Authentication podem ser integrados para usar o Azure ad para autenticação. |
| Aplicativos LOB (linha de negócios) com autenticação moderna|As organizações podem usar AD FS com Active Directory para dar suporte a aplicativos LOB que exigem autenticação moderna.| Os aplicativos LOB que exigem autenticação moderna podem ser configurados para usar o Azure AD para autenticação. |
| Serviços de camada intermediária/daemon|Os serviços em execução em ambientes locais normalmente usam contas de serviço do AD ou contas de serviço gerenciado de grupo (gMSA) para serem executados. Esses aplicativos herdarão as permissões da conta de serviço.| O Azure AD fornece [identidades gerenciadas](../managed-identities-azure-resources/index.yml) para executar outras cargas de trabalho na nuvem. O ciclo de vida dessas identidades é gerenciado pelo Azure AD e está vinculado ao provedor de recursos não pode ser usado para outras finalidades para obter acesso ao Backdoor.|
| **Dispositivos**|||
| Móvel|Active Directory não oferece suporte nativo a dispositivos móveis sem soluções de terceiros.| A solução de gerenciamento de dispositivo móvel da Microsoft, Microsoft Intune, é integrada ao Azure AD. Microsoft Intune fornece informações de estado do dispositivo ao sistema de identidade para avaliar durante a autenticação. |
| Áreas de trabalho do Windows|Active Directory fornece a capacidade de ingressar no domínio em dispositivos Windows para gerenciá-los usando Política de Grupo, System Center Configuration Manager ou outras soluções de terceiros.|Os dispositivos Windows podem ser [ingressados no Azure ad](../devices/index.yml). O acesso condicional pode verificar se um dispositivo é ingressado no Azure AD como parte do processo de autenticação. Os dispositivos Windows também podem ser gerenciados com [Microsoft Intune](/intune/what-is-intune). Nesse caso, o acesso condicional, considerará se um dispositivo está em conformidade (por exemplo, patches de segurança atualizados e assinaturas de vírus) antes de permitir o acesso aos aplicativos.|
| Servidores Windows| O Active Directory fornece recursos de gerenciamento robustos para servidores Windows locais usando Política de Grupo ou outras soluções de gerenciamento.| As máquinas virtuais do Windows Server no Azure podem ser gerenciadas com [Azure AD Domain Services](../../active-directory-domain-services/index.yml). [Identidades gerenciadas](../managed-identities-azure-resources/index.yml) podem ser usadas quando as VMs precisam acessar os recursos ou o diretório do sistema de identidade.|
| Cargas de trabalho do Linux/Unix|O Active Directory não oferece suporte nativo a não-Windows sem soluções de terceiros, embora os computadores Linux possam ser configurados para autenticar com Active Directory como um realm do Kerberos.|As VMs Linux/Unix podem usar [identidades gerenciadas](../managed-identities-azure-resources/index.yml) para acessar o sistema de identidade ou os recursos. Algumas organizações, migram essas cargas de trabalho para tecnologias de contêiner de nuvem, que também podem usar identidades gerenciadas.|

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Active Directory?](./active-directory-whatis.md)
- [Comparar o Active Directory Domain Services autogerenciado, o Azure Active Directory e o Azure Active Directory Domain Services gerenciado](../../active-directory-domain-services/compare-identity-solutions.md)
- [Perguntas frequentes sobre o Azure Active Directory](./active-directory-faq.md)
- [O que há de novo no Azure Active Directory?](./whats-new.md)

---
title: Comparar o Active Directory ao Azure Active Directory
description: Este documento compara o ADDS (Active Directory Domain Services) ao Azure AD (Active Directory). Ele descreve os principais conceitos das soluções de identidade e explica suas diferenças e semelhanças.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371912"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Comparar o Active Directory ao Azure Active Directory

O Azure Active Directory é a próxima evolução das soluções de gerenciamento de identidade e acesso para a nuvem. A Microsoft introduziu o Active Directory Domain Services no Windows 2000 para permitir que as organizações gerenciem vários sistemas e componentes de infraestrutura localmente usando uma única identidade por usuário.

O Microsoft Azure AD melhora ainda mais o nível dessa abordagem, fornecendo às organizações uma solução de IDaaS (identidade como serviço) para todos os seus aplicativos locais e na nuvem.

A maioria dos administradores de TI está familiarizada com os conceitos do Active Directory Domain Services. A tabela a seguir descreve as diferenças e semelhanças entre o Active Directory conceitos e o Azure Active Directory.

|Conceito|AD (Active Directory)|Azure Active Directory |
|:-|:-|:-|
|**Usuários**|||
|Provisionar: usuários | As organizações criam usuários internos manualmente ou usam um sistema de provisionamento interno ou automatizado, como o Microsoft Identity Manager, para integrarem-se a um sistema de RH.|As organizações do AD existentes usam o [Microsoft Azure Active Directory Connect](../hybrid/how-to-connect-sync-whatis.md) para sincronizar identidades para a nuvem.</br> O Microsoft Azure AD adiciona suporte para criar automaticamente usuários de [sistemas de RH na nuvem](../saas-apps/workday-tutorial.md). </br>O Azure AD pode provisionar identidades em aplicativos SaaS [habilitados para SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para fornecer automaticamente aos aplicativos os detalhes necessários para permitir o acesso aos usuários. |
|Provisionamento: identidades externas| As organizações criam usuários externos manualmente como usuários regulares em uma floresta de AD externa dedicada, resultando em sobrecarga de administração para gerenciar o ciclo de vida de identidades externas (usuários convidados)| O Microsoft Azure AD fornece uma classe especial de identidade para dar suporte a identidades externas. O [Azure Microsoft Azure AD B2B](/azure/active-directory/b2b/) gerenciará o link para a identidade do usuário externo para verificar se eles são válidos. |
| Gerenciamento de direitos e grupos| Os administradores fazem os usuários membros de grupos. Os proprietários de aplicativos e recursos permite o acesso de grupos a aplicativos ou recursos.| Os [grupos](./active-directory-groups-create-azure-portal.md) também estão disponíveis no Azure AD e os administradores também podem usar grupos para conceder permissões a recursos. No Azure AD, os administradores podem atribuir a associação a grupos manualmente ou usar uma consulta para incluir usuários em um grupo de forma dinâmica. </br> Os administradores podem usar o [Gerenciamento de direitos](../governance/entitlement-management-overview.md) no Azure AD para permitir aos usuários acessar a uma coleção de aplicativos e recursos usando fluxos de trabalho e, se necessário, critérios baseados em tempo. |
| Gerenciamento de administração|As organizações usarão uma combinação de domínios, unidades organizacionais e grupos no AD para delegar direitos administrativos para gerenciar o diretório e os recursos que ele controla.| O Azure AD fornece [funções internas](./active-directory-users-assign-role-azure-portal.md) com o sistema de RBAC (controle de acesso baseado em função) do Azure AD, com suporte limitado para a [criação de funções personalizadas](../roles/custom-overview.md) para delegar acesso privilegiado ao sistema de identidade, aos aplicativos e recursos que ele controla.</br>O gerenciamento de funções pode ser aprimorado com o [ PIM (Privileged Identity Management)](../privileged-identity-management/pim-configure.md) para fornecer acesso just-in-time, com restrição de tempo ou baseado no fluxo de trabalho, em funções privilegiadas. |
| Gerenciamento de credenciais| As credenciais no Active Directory baseiam-se em senhas, autenticação de certificado e autenticação de cartão inteligente. As senhas são gerenciadas usando políticas de senha baseadas no comprimento, na expiração e na complexidade da senha.|O Azure AD usa a [proteção de senha](../authentication/concept-password-ban-bad.md) inteligente local e na nuvem. A proteção inclui o bloqueio inteligente, além de bloquear frases e substituições de senha comuns e personalizadas. </br>O Azure AD aumenta significativamente [a segurança por meio da autenticação multifator](../authentication/concept-mfa-howitworks.md) e de tecnologias com [sem senha](../authentication/concept-authentication-passwordless.md), como o FIDO2. </br>O Azure AD reduz os custos de suporte fornecendo aos usuários um sistema de [autoatendimento para redefinição de senha](../authentication/concept-sspr-howitworks.md). |
| **Aplicativos**|||
| Dispositivos de Infraestrutura|O Active Directory é a base para muitos componentes locais de infraestrutura, por exemplo, DNS, DHCP, IPSec, WiFi, NPS e acesso VPN|Em um novo mundo na nuvem, o Azure AD é o novo painel de controle para acessar aplicativos comparado aos controles de rede. Quando os usuários autenticam o[ CA (Acesso condicional)](../conditional-access/overview.md), ele controla quais usuários terão acesso a certos aplicativos sob as condições necessárias.|
| Aplicativos tradicionais e herdados| A maioria dos aplicativos locais usa LDAP, autenticação NTLM e Kerberos (Windows-Integrated) ou autenticação baseada em cabeçalho para controlar o acesso aos usuários.| O Azure AD pode fornecer acesso a esses tipos de aplicativos locais usando agentes de [proxy de aplicativo do Azure AD](../manage-apps/application-proxy.md) em execução no local. Usando esse método, o Azure AD pode autenticar usuários locais do Active Directory usando o Kerberos enquanto você migra ou precisa coexistir com aplicativos herdados. |
| Aplicativos SaaS|O Active Directory não dá suporte a aplicativos SaaS de forma nativa e requer o sistema de federação, como o AD FS.|Os aplicativos SaaS com suporte a autenticação OAuth2, SAML e WS-\* podem ser integrados para usar o Azure AD para autenticação. |
| Aplicativos LOB (linha de negócios) com autenticação moderna|As organizações podem usar o AD FS com o Active Directory para dar suporte a aplicativos LOB que exigem autenticação moderna.| Os aplicativos LOB que necessitam de autenticação moderna podem ser configurados para usar o Azure AD para autenticação. |
| Serviços de camada intermediária/Daemon|Os serviços em execução em ambientes locais normalmente usam contas de serviço do AD ou de contas gMSA (Serviço Gerenciado de Grupo) para serem executados. Esses aplicativos herdarão as permissões da conta de serviço.| O Azure AD fornece [identidades gerenciadas](../managed-identities-azure-resources/index.yml) para executar outras cargas de trabalho na nuvem. O ciclo de vida dessas identidades é gerenciado pelo Azure AD e está vinculado ao provedor de recursos não pode ser usado para outras finalidades para obter acesso ao backdoor.|
| **Dispositivos**|||
| Dispositivos móveis|Active Directory não oferece suporte de forma nativa a dispositivos móveis sem soluções de terceiros.| A solução de gerenciamento de dispositivo móvel da Microsoft, o Microsoft Intune, é integrado ao Azure AD. O Microsoft Intune fornece informações do estado do dispositivo ao sistema de identidade para avaliar durante a autenticação. |
| Áreas de trabalho do Windows|O Active Directory fornece a capacidade de ingressar no domínio em dispositivos Windows para gerenciá-los usando a Política de Grupo, o System Center Configuration Manager ou outras soluções de terceiros.|Os dispositivos Windows podem ser [ingressados no Azure AD](../devices/index.yml). O acesso condicional pode verificar se um dispositivo é ingressado no Azure AD como parte do processo de autenticação. Os dispositivos Windows também podem ser gerenciados com o [Microsoft Intune](/intune/what-is-intune). Nesse caso, o acesso condicional, considerará se um dispositivo está em conformidade (por exemplo, se tem patches de segurança e assinaturas de vírus atualizados) antes de permitir o acesso aos aplicativos.|
| Servidores Windows| O Active Directory fornece funcionalidades de gerenciamento robustas para servidores Windows locais usando a Política de Grupo ou outras soluções de gerenciamento.| As máquinas virtuais do Windows Server no Azure podem ser gerenciadas com [Azure AD Domain Services](../../active-directory-domain-services/index.yml). As [Identidades gerenciadas](../managed-identities-azure-resources/index.yml) podem ser usadas quando as VMs precisam acessar os recursos ou o diretório do sistema de identidade.|
| Cargas de trabalho do Linux/Unix|O Active Directory não oferece suporte de forma nativa fora do Windows sem soluções de terceiros, embora os computadores Linux possam ser configurados para autenticar com Active Directory como um realm do Kerberos.|As VMs Linux/Unix podem usar [identidades gerenciadas](../managed-identities-azure-resources/index.yml) para acessar o sistema de identidade ou os recursos. Algumas organizações, migram essas cargas de trabalho para tecnologias de contêiner de nuvem, que também podem usar identidades gerenciadas.|

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Active Directory?](./active-directory-whatis.md)
- [Comparar o Active Directory Domain Services autogerenciado, o Azure Active Directory e o Azure Active Directory Domain Services gerenciado](../../active-directory-domain-services/compare-identity-solutions.md)
- [Perguntas frequentes sobre o Azure Active Directory](./active-directory-faq.md)
- [Quais são as novidades no Azure Active Directory?](./whats-new.md)

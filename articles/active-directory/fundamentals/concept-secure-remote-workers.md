---
title: Responder rapidamente a identidades seguras com Azure Active Directory
description: Responder rapidamente às ameaças com identidades baseadas em nuvem do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836675"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Responder rapidamente a identidades seguras com o Azure AD

Pode parecer assustador tentar proteger seus funcionários no mundo atual, especialmente quando você precisa responder rapidamente e fornecer acesso a vários serviços rapidamente. Este artigo destina-se a fornecer uma lista concisa de todas as ações a serem tomadas, ajudando você a identificar e priorizar qual ordem para implantar os recursos do Azure AD com base no tipo de licença que você possui. O Azure AD oferece muitos recursos e fornece muitas camadas de segurança para suas identidades, a navegação de qual recurso é relevante às vezes pode ser impressionante. Muitas organizações já estão na nuvem ou mudando rapidamente para a nuvem, este documento destina-se a permitir que você implante serviços rapidamente, com a proteção de suas identidades como a principal consideração. 

Cada tabela fornece uma recomendação de segurança consistente, protegendo as identidades de administrador e de usuário dos principais ataques de segurança (violação de repetição, phishing e irrigação de senha), minimizando o impacto do usuário e melhorando a experiência do usuário. 

As diretrizes também permitirão que os administradores configurem o acesso a aplicativos SaaS e locais de maneira segura e protegida e sejam aplicáveis a identidades de nuvem ou híbridas (sincronizadas) e se aplicam a usuários que trabalham remotamente ou no escritório.

Esta lista de verificação ajudará você a implantar rapidamente ações recomendadas críticas para proteger sua organização imediatamente explicando como:

- Fortaleça suas credenciais.
- Reduza a área de superfície de ataque.
- Automatize a resposta a ameaças.
- Use a inteligência de nuvem.
- Habilite o autoatendimento do usuário final.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que sua nuvem somente ou identidades híbridas já tenham sido estabelecidas no Azure AD. Para obter ajuda com a escolha do tipo de identidade, consulte o artigo [escolher o método de autenticação certo para sua solução de identidade híbrida Azure Active Directory](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Resumo

Há muitos aspectos em uma infraestrutura de identidade segura, mas essa lista de verificação se concentra em uma infraestrutura de identidade seguro e segura, permitindo que os usuários trabalhem remotamente. Proteger sua identidade é apenas parte da sua história de segurança, proteger dados, aplicativos e dispositivos também deve ser considerado.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Diretrizes para clientes Azure AD Gratuito, Office 365 ou Microsoft 365.

Há várias recomendações que Azure AD Gratuito, o Office 365 ou os clientes de aplicativos Microsoft 365 devem seguir para proteger suas identidades de usuário, a seguinte tabela destina-se a destacar as principais ações para as seguintes assinaturas de licença:

- Office 365 (Office 365 E1, E3, e5, F1, a1, a3, a5)
- Microsoft 365 (Business Basic, aplicativos para empresas, Standard Business, Business Premium, a1)
- Azure AD Gratuito (incluído no Azure, Dynamics 365, Intune e Power Platform)

| Ação recomendada | Detalhe |
| --- | --- |
| [Habilitar padrões de segurança](concept-fundamentals-security-defaults.md) | Proteger todas as identidades de usuário e aplicativos habilitando a MFA e bloqueando a autenticação herdada |
| [Habilitar a sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) (se estiver usando identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo o bloqueio inteligente, o bloqueio de IP e a capacidade de descobrir credenciais vazadas.) |
| [Habilitar o bloqueio inteligente do ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege seus usuários de experimentarem o bloqueio de conta de extranet de atividades mal-intencionadas. |
| [Habilitar o bloqueio inteligente de Azure Active Directory](../authentication/howto-password-smart-lockout.md) (se estiver usando identidades gerenciadas) | O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. |
| [Desabilitar consentimento do usuário final para aplicativos](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira segura de conceder acesso a aplicativos que exigem aprovação de administrador para que os usuários finais não exponham dados corporativos. A Microsoft recomenda desabilitar todas as operações futuras de consentimento do usuário para ajudar a reduzir a área de superfície e atenuar esse risco. |
| [Integrar aplicativos SaaS com suporte da galeria ao Azure AD e habilitar o logon único](../manage-apps/add-application-portal.md) | O Azure Active Directory tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que a sua organização usa provavelmente estão na galeria, que pode ser acessada pelo portal do Azure. Fornecer acesso a aplicativos SaaS corporativos remotamente e com segurança com experiência de usuário aprimorada (SSO) |
| [Automatizar o provisionamento e desprovisionamento de usuários de aplicativos SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades de usuário e funções nos aplicativos de nuvem (SaaS) aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam, aumentando a segurança da sua organização. |
| [Habilitar acesso híbrido seguro: proteger aplicativos herdados com redes e controladores de entrega de aplicativos existentes](../manage-apps/secure-hybrid-access.md) (se aplicável) | Publique e proteja seus aplicativos de autenticação herdados locais e na nuvem conectando-os ao Azure AD com seu controlador de entrega de aplicativos ou rede existente. |
| [Habilitar a redefinição de senha de autoatendimento](../authentication/tutorial-enable-sspr.md) (aplicável somente a contas em nuvem) | Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não pode entrar no dispositivo ou em um aplicativo. |
| [Usar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos administradores acesso apenas às áreas de que precisam. Nem todos os administradores precisam ser administradores globais. |
| [Habilitar diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os usuários alterem as respectivas senhas em um intervalo definido, desabilite os requisitos de complexidade e os usuários estarão mais propensos a lembrar de suas senhas e mantê-las em segurança. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Diretrizes para clientes do Azure AD Premium plano 1.

A tabela a seguir destina-se a destacar as principais ações para as seguintes assinaturas de licença:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Ação recomendada | Detalhe |
| --- | --- |
| [Habilitar a experiência de registro combinado para MFA do Azure AD e SSPR para simplificar a experiência de registro do usuário](../authentication/howto-registration-mfa-sspr-combined.md) | Permita que os usuários se registrem de uma experiência comum para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento. |
| [Definir configurações de MFA para sua organização](../authentication/howto-mfa-getstarted.md) | Verifique se as contas estão protegidas de serem comprometidas com a autenticação multifator |
| [Habilitar a redefinição de senha por autoatendimento](../authentication/tutorial-enable-sspr.md) | Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não pode entrar no dispositivo ou em um aplicativo |
| [Implementar o Write-back de senha](../authentication/tutorial-enable-sspr-writeback.md) (se estiver usando identidades híbridas) | Permita que as alterações de senha na nuvem sejam gravadas em um ambiente local do Windows Server Active Directory. |
| Criar e habilitar políticas de acesso condicional | [MFA para administradores para proteger contas que recebem direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloquear protocolos de autenticação herdados devido ao maior risco associado aos protocolos de autenticação herdados.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA para todos os usuários e aplicativos para criar uma política de MFA balanceada para seu ambiente, protegendo seus usuários e aplicativos.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Exigir MFA para gerenciamento do Azure para proteger seus recursos privilegiados exigindo a autenticação multifator para qualquer usuário que acesse recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Habilitar a sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) (se estiver usando identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo o bloqueio inteligente, o bloqueio de IP e a capacidade de descobrir credenciais vazadas.) |
| [Habilitar o bloqueio inteligente do ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege seus usuários de experimentarem o bloqueio de conta de extranet de atividades mal-intencionadas. |
| [Habilitar o bloqueio inteligente de Azure Active Directory](../authentication/howto-password-smart-lockout.md) (se estiver usando identidades gerenciadas) | O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. |
| [Desabilitar consentimento do usuário final para aplicativos](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira segura de conceder acesso a aplicativos que exigem aprovação de administrador para que os usuários finais não exponham dados corporativos. A Microsoft recomenda desabilitar todas as operações futuras de consentimento do usuário para ajudar a reduzir a área de superfície e atenuar esse risco. |
| [Habilitar o acesso remoto a aplicativos herdados locais com o proxy de aplicativo](../manage-apps/application-proxy-add-on-premises-application.md) | Habilite o Azure Proxy de Aplicativo do AD e integre com aplicativos herdados para que os usuários acessem aplicativos locais com segurança entrando com sua conta do Azure AD. |
| [Habilitar acesso híbrido seguro: proteger aplicativos herdados com redes e controladores de entrega de aplicativo existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja seus aplicativos de autenticação herdados locais e na nuvem conectando-os ao Azure AD com seu controlador de entrega de aplicativos ou rede existente. |
| [Integrar aplicativos SaaS com suporte da galeria ao Azure AD e habilitar o logon único](../manage-apps/add-application-portal.md) | O Azure Active Directory tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que a sua organização usa provavelmente estão na galeria, que pode ser acessada pelo portal do Azure. Forneça acesso a aplicativos SaaS corporativos remotamente e com segurança com o SSO (experiência do usuário) aprimorado. |
| [Automatizar o provisionamento e desprovisionamento de usuários de aplicativos SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades de usuário e funções nos aplicativos de nuvem (SaaS) aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam, aumentando a segurança da sua organização. |
| [Habilitar acesso condicional – baseado em dispositivo](../conditional-access/require-managed-devices.md) | Melhore as experiências de segurança e de usuário com acesso condicional baseado em dispositivo. Essa etapa garante que os usuários só possam acessar de dispositivos que atendam aos seus padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. Os dispositivos gerenciados podem ser dispositivos ingressados no Azure AD híbrido ou em conformidade com o Intune. |
| [Habilitar proteção por senha](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os usuários do uso de senhas fracas e fáceis de adivinhar. |
| [Designar mais de um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanentes somente de nuvem para uso se houver uma emergência. Essas contas não são usadas diariamente e devem ter senhas longas e complexas. As contas de vidro garantem que você possa acessar o serviço em uma emergência. |
| [Usar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos administradores acesso apenas às áreas de que precisam. Nem todos os administradores precisam ser administradores globais. |
| [Habilitar diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os usuários alterem as respectivas senhas em um intervalo definido, desabilite os requisitos de complexidade e os usuários estarão mais propensos a lembrar de suas senhas e mantê-las em segurança. |
| [Criar um plano para acesso do usuário convidado](../external-identities/what-is-b2b.md) | Colabore com usuários convidados, permitindo que eles entrem em seus aplicativos e serviços com suas próprias identidades corporativas, de estudante ou sociais. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Diretrizes para clientes do Azure AD Premium plano 2.

A tabela a seguir destina-se a destacar as principais ações para as seguintes assinaturas de licença:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 e5, a5)

| Ação recomendada | Detalhe |
| --- | --- |
| [Habilitar a experiência de registro combinado para MFA do Azure AD e SSPR para simplificar a experiência de registro do usuário](../authentication/howto-registration-mfa-sspr-combined.md) | Permita que os usuários se registrem de uma experiência comum para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento. |
| [Definir configurações de MFA para sua organização](../authentication/howto-mfa-getstarted.md) | Verifique se as contas estão protegidas de serem comprometidas com a autenticação multifator |
| [Habilitar a redefinição de senha por autoatendimento](../authentication/tutorial-enable-sspr.md) | Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não pode entrar no dispositivo ou em um aplicativo |
| [Implementar o Write-back de senha](../authentication/tutorial-enable-sspr-writeback.md) (se estiver usando identidades híbridas) | Permita que as alterações de senha na nuvem sejam gravadas em um ambiente local do Windows Server Active Directory. |
| [Habilitar políticas de proteção de identidade para impor o registro de MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Gerencie a distribuição da MFA (autenticação multifator) do Azure AD. |
| [Habilitar o usuário de proteção de identidade e as políticas de risco de entrada](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Habilite as políticas de usuário e de entrada do Identity Protection. A política de entrada recomendada é direcionar entradas de risco médio e exigir MFA. Para as políticas de usuário, ele deve ter como destino usuários de alto risco que exigem a ação de alteração de senha. |
| Criar e habilitar políticas de acesso condicional | [MFA para administradores para proteger contas que recebem direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloquear protocolos de autenticação herdados devido ao maior risco associado aos protocolos de autenticação herdados.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Exigir MFA para gerenciamento do Azure para proteger seus recursos privilegiados exigindo a autenticação multifator para qualquer usuário que acesse recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Habilitar a sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) (se estiver usando identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo o bloqueio inteligente, o bloqueio de IP e a capacidade de descobrir credenciais vazadas.) |
| [Habilitar o bloqueio inteligente do ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege seus usuários de experimentarem o bloqueio de conta de extranet de atividades mal-intencionadas. |
| [Habilitar o bloqueio inteligente de Azure Active Directory](../authentication/howto-password-smart-lockout.md) (se estiver usando identidades gerenciadas) | O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. |
| [Desabilitar consentimento do usuário final para aplicativos](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira segura de conceder acesso a aplicativos que exigem aprovação de administrador para que os usuários finais não exponham dados corporativos. A Microsoft recomenda desabilitar todas as operações futuras de consentimento do usuário para ajudar a reduzir a área de superfície e atenuar esse risco. |
| [Habilitar o acesso remoto a aplicativos herdados locais com o proxy de aplicativo](../manage-apps/application-proxy-add-on-premises-application.md) | Habilite o Azure Proxy de Aplicativo do AD e integre com aplicativos herdados para que os usuários acessem aplicativos locais com segurança entrando com sua conta do Azure AD. |
| [Habilitar acesso híbrido seguro: proteger aplicativos herdados com redes e controladores de entrega de aplicativo existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja seus aplicativos de autenticação herdados locais e na nuvem conectando-os ao Azure AD com seu controlador de entrega de aplicativos ou rede existente. |
| [Integrar aplicativos SaaS com suporte da galeria ao Azure AD e habilitar o logon único](../manage-apps/add-application-portal.md) | O Azure Active Directory tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que a sua organização usa provavelmente estão na galeria, que pode ser acessada pelo portal do Azure. Forneça acesso a aplicativos SaaS corporativos remotamente e com segurança com o SSO (experiência do usuário) aprimorado. |
| [Automatizar o provisionamento e desprovisionamento de usuários de aplicativos SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades de usuário e funções nos aplicativos de nuvem (SaaS) aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam, aumentando a segurança da sua organização. |
| [Habilitar acesso condicional – baseado em dispositivo](../conditional-access/require-managed-devices.md) | Melhore as experiências de segurança e de usuário com acesso condicional baseado em dispositivo. Essa etapa garante que os usuários só possam acessar de dispositivos que atendam aos seus padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. Os dispositivos gerenciados podem ser dispositivos ingressados no Azure AD híbrido ou em conformidade com o Intune. |
| [Habilitar proteção por senha](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os usuários do uso de senhas fracas e fáceis de adivinhar. |
| [Designar mais de um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanentes somente de nuvem para uso se houver uma emergência. Essas contas não são usadas diariamente e devem ter senhas longas e complexas. As contas de vidro garantem que você possa acessar o serviço em uma emergência. |
| [Usar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos administradores acesso apenas às áreas de que precisam. Nem todos os administradores precisam ser administradores globais. |
| [Habilitar diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os usuários alterem as respectivas senhas em um intervalo definido, desabilite os requisitos de complexidade e os usuários estarão mais propensos a lembrar de suas senhas e mantê-las em segurança. |
| [Criar um plano para acesso do usuário convidado](../external-identities/what-is-b2b.md) | Colabore com usuários convidados, permitindo que eles entrem em seus aplicativos e serviços com suas próprias identidades corporativas, de estudante ou sociais. |
| [Habilitar Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Permite que você gerencie, controle e monitore o acesso a recursos importantes em sua organização, garantindo que os administradores tenham acesso apenas quando necessário e com aprovação |

## <a name="next-steps"></a>Próximas etapas

- Para obter diretrizes de implantação detalhadas para recursos individuais do Azure AD, examine os [planos de implantação do projeto do Azure ad](active-directory-deployment-plans.md).

- Para obter uma lista de verificação de implantação do Azure AD de ponta a ponta, consulte o artigo [Azure Active Directory guia de implantação de recursos](active-directory-deployment-checklist-p2.md)
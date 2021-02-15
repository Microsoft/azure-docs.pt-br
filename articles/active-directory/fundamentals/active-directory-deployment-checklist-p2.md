---
title: Lista de verificação da implantação do Azure Active Directory
description: Lista de verificação da implantação de recursos do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836913"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guia de implantação de recurso do Azure Active Directory

Pode parecer desanimador implantar o Azure AD (Azure Active Directory) para sua organização e mantê-lo seguro. Este artigo identifica tarefas comuns que os clientes consideram úteis concluir em fases, ao longo de 30, 60, 90 dias ou mais, para melhorar a postura de segurança. Mesmo as organizações que já implantaram o Azure Active Directory podem usar este guia para garantir o máximo aproveitamento do investimento já feito.

Uma infraestrutura de identidade bem planejada e executada abre o caminho para o acesso seguro às suas cargas de trabalho de produtividade e dados apenas para usuários e dispositivos conhecidos.

Além disso, os clientes podem verificar a respectiva [classificação de segurança da identidade](identity-secure-score.md) para ver o quão alinhados eles estão com as práticas recomendadas da Microsoft. Verifique a sua classificação de segurança antes e depois de implementar essas recomendações para ver o seu desempenho em comparação com outros integrantes do setor e outras organizações do mesmo tamanho.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das recomendações neste guia podem ser implementadas com Azure AD Gratuito ou sem licença. Quando as licenças são necessárias, declaramos qual licença é necessária, no mínimo, para realizar a tarefa.

Informações adicionais sobre licenciamento podem ser encontradas nas seguintes páginas:

* [Licenciamento do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Preços de identidades externas do Azure AD](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: criar uma base de segurança

Nessa fase, os administradores habilitam recursos de segurança básicos para criar um alicerce mais seguro e fácil de usar no Azure Active Directory antes de importar ou criar contas de usuário normais. Essa fase inicial garante que você esteja em um estado mais seguro desde o início e que os usuários finais tenham que ser introduzidos a novos conceitos apenas uma vez.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Designar mais de um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanentes somente de nuvem para uso se houver uma emergência. Essas contas não são usadas diariamente e devem ter senhas longas e complexas. | AD do Azure Gratuito |
| [Usar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos administradores acesso apenas às áreas de que precisam. Nem todos os administradores precisam ser administradores globais. | AD do Azure Gratuito |
| [Habilitar o Privileged Identity Management para rastreamento do uso da função administrativa](../privileged-identity-management/pim-getting-started.md) | Habilite o Privileged Identity Management para começar a rastrear o uso da função administrativa. | Azure AD Premium P2 |
| [Distribuir a redefinição de senha de autoatendimento](../authentication/howto-sspr-deployment.md) | Reduza as chamadas à assistência técnica para redefinir senhas permitindo que a equipe redefina as suas próprias senhas com políticas que você define como controlador de administradores. | |
| [Criar uma lista de senhas banidas personalizada específica da organização](../authentication/tutorial-configure-custom-password-protection.md) | Impeça os usuários de criarem senhas que contenham palavras ou frases comuns da organização ou da área. | |
| [Habilitar a integração local à proteção de senha do Azure Active Directory](../authentication/concept-password-ban-bad-on-premises.md) | Estenda a lista de senhas banidas para o seu diretório local, de modo a garantir que as senhas locais definidas também estejam em conformidade com a lista de senhas específicas banidas do locatário e globais. | Azure AD Premium P1 |
| [Habilitar diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os usuários alterem as respectivas senhas em um intervalo definido, desabilite os requisitos de complexidade e os usuários estarão mais propensos a lembrar de suas senhas e mantê-las em segurança. | AD do Azure Gratuito |
| [Desabilitar redefinições de senha periódicas para contas de usuário baseadas em nuvem](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | As redefinições de senha periódicas incentivam os usuários a incrementar as senhas existentes. Siga as orientações do documento de diretrizes de senha da Microsoft e espelhe a política local para os usuários apenas de nuvem. | AD do Azure Gratuito |
| [Personalizar o bloqueio inteligente do Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Impeça que os bloqueios de usuários baseados na nuvem sejam replicados para os usuários locais do Active Directory | |
| [Habilitar o bloqueio inteligente da Extranet para o AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | O bloqueio de Extranet do AD FS protege contra ataques de detecção de senha de força bruta, ao mesmo tempo que permite aos usuários com AD FS válido continuar usando as respectivas contas. | |
| [Bloquear a autenticação herdada no Azure AD com acesso condicional](../conditional-access/block-legacy-authentication.md) | Bloquear protocolos de autenticação herdados como POP, SMTP, IMAP e MAPI que não podem impor a autenticação multifator, tornando-os um ponto de entrada preferencial para adversários. | Azure AD Premium P1 |
| [Implantar a autenticação multifator do Azure AD usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) | Exigir que os usuários executem a verificação em duas etapas ao acessar aplicativos confidenciais usando políticas de acesso condicional. | Azure AD Premium P1 |
| [Ativar a proteção de identidade do Active Directory do Azure](../identity-protection/overview-identity-protection.md) | Habilite o rastreamento de conexões arriscadas e credenciais comprometidas para usuários da sua organização. | Azure AD Premium P2 |
| [Usar as detecções de risco para disparar a autenticação multifator e as alterações de senha](../authentication/tutorial-risk-based-sspr-mfa.md) | Habilite a automação que pode disparar eventos como autenticação multifator, redefinição de senha e bloqueio de conexões com base no risco. | Azure AD Premium P2 |
| [Habilitar registro combinado para redefinição de senha de autoatendimento e autenticação multifator do Azure AD](../authentication/concept-registration-mfa-sspr-combined.md) | Permita que os usuários se registrem de uma experiência comum para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: importar usuários, habilitar a sincronização e gerenciar dispositivos

A seguir, fazemos inclusões na base criada na fase 1 importando usuários e habilitando a sincronização, planejando o acesso de convidado e preparando o suporte a recursos adicionais.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Instalar Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Prepare-se para sincronizar usuários do diretório local existente com a nuvem. | AD do Azure Gratuito |
| [Implementar a sincronização de Hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) | Sincronize os hashes de senha para permitir a replicação das alterações de senha, a detecção e a correção de senhas incorretas, bem como relatos de credenciais vazadas. | AD do Azure Gratuito |
| [Implementar o write-back de senha](../authentication/tutorial-enable-sspr-writeback.md) | Permita que as alterações de senha na nuvem sejam gravadas em um ambiente local do Windows Server Active Directory. | Azure AD Premium P1 |
| [Implementar o Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Habilite o monitoramento das principais estatísticas de integridade para servidores do Azure AD Connect, servidores do AD FS e controladores de domínio. | Azure AD Premium P1 |
| [Atribuir licenças a usuários por meio da associação a grupos no Azure Active Directory](../enterprise-users/licensing-groups-assign.md) | Economize tempo e esforço criando grupos de licenciamento que habilitam ou desabilitam recursos por grupo, em vez de configurar cada usuário. | |
| [Criar um plano para acesso do usuário convidado](../external-identities/what-is-b2b.md) | Colabore com usuários convidados permitindo que eles se conectem a seus aplicativos e serviços com as próprias contas corporativas, de estudante ou identidades sociais. | [Preços de identidades externas do Azure AD](../external-identities/external-identities-pricing.md) |
| [Decida a estratégia de gerenciamento de dispositivos](../devices/overview.md) | Decida o que é permitido pela organização em relação aos dispositivos. Registro versus ingresso, traga o seu próprio dispositivo versus fornecido pela empresa. | |
| [Implantar o Windows Hello para Empresas na sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Preparar a autenticação com senha usando o Windows Hello | |
| [Implantar métodos de autenticação com senha para seus usuários](../authentication/concept-authentication-passwordless.md) | Forneça aos seus usuários métodos de autenticação convenientes sem senha | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: gerenciar aplicativos

Conforme continuamos o trabalho iniciado nas fases anteriores, identificamos aplicativos candidatos à migração e integração ao Azure Active Directory, e concluímos a configuração desses aplicativos.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| Identificar os aplicativos | Identifique os aplicativos em uso na sua organização: locais, aplicativos SaaS na nuvem e outros aplicativos de linhas de negócios. Determine se esses aplicativos podem e devem ser gerenciados com o Azure Active Directory. | Nenhuma licença necessária |
| [Integrar aplicativos SaaS compatíveis à galeria](../manage-apps/add-application-portal.md) | O Azure Active Directory tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que a sua organização usa provavelmente estão na galeria, que pode ser acessada pelo portal do Azure. | AD do Azure Gratuito |
| [Usar o Proxy de Aplicativo para integrar aplicativos locais](../manage-apps/application-proxy-add-on-premises-application.md) | O Proxy de Aplicativo permite que os usuários acessem aplicativos locais entrando com a respectiva conta do Azure Active Directory. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: auditar identidades com privilégios, concluir uma revisão de acesso e gerenciar o ciclo de vida do usuário

Na Fase 4 os administradores impõem princípios de privilégio mínimo para a administração, executando suas primeiras revisões de acesso e habilitando a automação de tarefas comuns do ciclo de vida do usuário.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Impor o uso do Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Remova funções administrativas das contas normais diárias de usuário. Qualifique os usuários administrativos para usarem a respectiva função após a verificação bem-sucedida de uma autenticação multifator, fornecendo uma justificativa de negócios ou solicitando aprovação dos aprovadores designados. | Azure AD Premium P2 |
| [Concluir uma revisão de acesso para funções de diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Trabalhe com as equipes de segurança e liderança para criar uma política de revisão de acesso a fim de examinar o acesso administrativo com base nas políticas da sua organização. | Azure AD Premium P2 |
| [Implementar políticas de associação de grupo dinâmica](../enterprise-users/groups-dynamic-membership.md) | Use os grupos dinâmicos para atribuir automaticamente os usuários aos grupos com base em seus atributos de RH (ou outra fonte de atributos), como departamento, cargo, região e outros atributos. |  |
| [Implementar o provisionamento de aplicativo baseado em grupo](../manage-apps/what-is-access-management.md) | Use o provisionamento de gerenciamento de acesso baseado em grupo para provisionar automaticamente os usuários para aplicativos SaaS. |  |
| [Automatizar o provisionamento e desprovisionamento do usuário](../app-provisioning/user-provisioning.md) | Remova etapas manuais do ciclo de vida da conta do funcionário para impedir acesso não autorizado. Sincronize identidades da sua fonte de verdade (HR System) para o Azure AD. |  |

## <a name="next-steps"></a>Próximas etapas

[Detalhes de preços e licenciamento do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Políticas de acesso a dispositivos e identidades recomendadas comuns](/microsoft-365/enterprise/identity-access-policies)
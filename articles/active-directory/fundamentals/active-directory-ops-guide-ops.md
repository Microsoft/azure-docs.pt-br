---
title: Referência geral do guia de operações do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve executar para proteger as operações gerais
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836828"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referência geral do guia de operações do Azure Active Directory

Esta seção do [Guia de referência de operações do Azure ad](active-directory-ops-guide-intro.md) descreve as verificações e as ações que você deve seguir para otimizar as operações gerais do Azure Active Directory (AD do Azure).

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas operacionais à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas principais

O gerenciamento de Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de distribuição. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Aprimoramentos de unidade na pontuação de segurança de identidade | Equipe de operações do batalha |
| Manter servidores Azure AD Connect | Equipe de operações IAM |
| Executar e fazer triagem regularmente dos relatórios do IdFix | Equipe de operações IAM |
| Alertas do Azure AD Connect Health de triagem para sincronização e AD FS | Equipe de operações IAM |
| Se não estiver usando Azure AD Connect Health, o cliente terá o processo equivalente e as ferramentas para monitorar a infraestrutura personalizada | Equipe de operações IAM |
| Se não estiver usando AD FS, o cliente terá o processo equivalente e as ferramentas para monitorar a infraestrutura personalizada | Equipe de operações IAM |
| Monitorar logs híbridos: conectores de proxy de Aplicativo Azure AD | Equipe de operações IAM |
| Monitorar logs híbridos: agentes de autenticação de passagem | Equipe de operações IAM |
| Monitorar logs híbridos: serviço de write-back de senha | Equipe de operações IAM |
| Monitorar logs híbridos: gateway de proteção de senha local | Equipe de operações IAM |
| Monitorar logs híbridos: extensão NPS do Azure AD MFA (se aplicável) | Equipe de operações IAM |

Ao examinar sua lista, você pode achar necessário atribuir um proprietário para tarefas que não têm um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhadas com as recomendações acima.

#### <a name="owners-recommended-reading"></a>Leitura recomendada de proprietários

- [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governança no Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Gerenciamento híbrido

### <a name="recent-versions-of-on-premises-components"></a>Versões recentes de componentes locais

Ter as versões mais atualizadas dos componentes locais fornece ao cliente todas as atualizações de segurança mais recentes, melhorias de desempenho, bem como funcionalidades que podem ajudar a simplificar ainda mais o ambiente. A maioria dos componentes tem uma configuração de atualização automática, que automatizará o processo de atualização.

Esses componentes incluem:

- Azure AD Connect
- Conectores do Proxy de Aplicativo do Azure AD
- Agentes de autenticação de passagem do Azure AD
- Agentes de Azure AD Connect Health

A menos que um tenha sido estabelecido, você deve definir um processo para atualizar esses componentes e contar com o recurso de atualização automática sempre que possível. Se você encontrar componentes que tenham seis ou mais meses atrás, você deverá atualizar o mais rápido possível.

#### <a name="hybrid-management-recommended-reading"></a>Leitura recomendada de gerenciamento híbrido

- [Azure AD Connect: Atualização automática](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Entender os conectores de Proxy de Aplicativo do AD do Azure | Atualizações automáticas](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Linha de base de alerta Azure AD Connect Health

As organizações devem implantar [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) para monitoramento e emissão de relatórios de Azure AD Connect e AD FS. Azure AD Connect e AD FS são componentes críticos que podem interromper o gerenciamento e a autenticação do ciclo de vida e, portanto, levam a interrupções. Azure AD Connect Health ajuda a monitorar e obter informações sobre sua infraestrutura de identidade local, garantindo assim a confiabilidade do seu ambiente.

![Arquitetura integridade de Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ao monitorar a integridade do seu ambiente, você deve resolver imediatamente todos os alertas de severidade alta, seguidos de alertas de severidade mais baixos.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health leitura recomendada

- [Instalação do Agente do Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Logs de agentes locais

Alguns serviços de gerenciamento de identidade e acesso exigem agentes locais para habilitar cenários híbridos. Os exemplos incluem redefinição de senha, PTA (autenticação de passagem), Azure Proxy de Aplicativo do AD e extensão NPS do Azure AD MFA. É fundamental que a linha de base da equipe de operações e monitore a integridade desses componentes, arquivando e analisando os logs do agente de componentes usando soluções como o System Center Operations Manager ou o SIEM. É igualmente importante que sua equipe de operações do batalha ou suporte técnico entenda como solucionar problemas de padrões de erros.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Leitura recomendada de logs de agentes locais

- [Solucionar problemas de Proxy de Aplicativo](../manage-apps/application-proxy-troubleshoot.md)
- [Solução de problemas de redefinição de senha por autoatendimento – Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: solucionar problemas de autenticação de passagem](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Solucionar problemas de códigos de erro para a extensão NPS do Azure AD MFA](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Gerenciamento de agentes locais

A adoção de práticas recomendadas pode ajudar na operação ideal de agentes locais. Considere as seguintes práticas recomendadas:

- Vários conectores de proxy de aplicativo do Azure AD por grupo de conectores são recomendados para fornecer balanceamento de carga e alta disponibilidade contínuos, evitando pontos únicos de falha ao acessar os aplicativos de proxy. Se você tiver atualmente apenas um conector em um grupo de conectores que lide com aplicativos em produção, você deve implantar pelo menos dois conectores para redundância.
- Criar e usar um grupo de conectores de proxy de aplicativo para fins de depuração pode ser útil para cenários de solução de problemas e ao integrar novos aplicativos locais. Também recomendamos a instalação de ferramentas de rede, como o analisador de mensagens e o Fiddler nos computadores do conector.
- Vários agentes de autenticação de passagem são recomendados para fornecer balanceamento de carga e alta disponibilidade contínuos, evitando o ponto único de falha durante o fluxo de autenticação. Certifique-se de implantar pelo menos dois agentes de autenticação de passagem para redundância.

#### <a name="on-premises-agents-management-recommended-reading"></a>Leitura recomendada do gerenciamento de agentes locais

- [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](../manage-apps/application-proxy-connectors.md)
- [Autenticação de passagem do Azure AD – início rápido](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gerenciamento em escala

### <a name="identity-secure-score"></a>Pontuação da segurança de identidade

A [Pontuação](./identity-secure-score.md) de segurança de identidade fornece uma medida quantificável da postura de segurança da sua organização. É importante revisar constantemente e endereçar as descobertas relatadas e se esforçar para ter a pontuação mais alta possível. A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

![Classificação de segurança](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se sua organização atualmente não tem nenhum programa em vigor para monitorar as alterações na pontuação de segurança de identidade, é recomendável implementar um plano e atribuir proprietários para monitorar e impulsionar ações de aperfeiçoamento. As organizações devem corrigir as ações de aperfeiçoamento com um impacto de pontuação maior que 30 assim que possível.

### <a name="notifications"></a>Notificações

A Microsoft envia comunicações por email aos administradores para notificar várias alterações no serviço, as atualizações de configuração necessárias e os erros que exigem a intervenção do administrador. É importante que os clientes definam os endereços de email de notificação para que as notificações sejam enviadas aos membros apropriados da equipe que podem reconhecer e agir sobre todas as notificações. Recomendamos que você adicione vários destinatários ao [centro de mensagens](/office365/admin/manage/message-center) e solicite que as notificações (incluindo notificações de Azure ad Connect Health) sejam enviadas para uma lista de distribuição ou caixa de correio compartilhada. Se você tiver apenas uma conta de administrador global com um endereço de email, certifique-se de configurar pelo menos duas contas compatíveis com email.

Há dois endereços "de" usados pelo AD do Azure: <o365mc@email2.microsoft.com> , que envia notificações do centro de mensagens; e <azure-noreply@microsoft.com> , que envia notificações relacionadas a:

- [Revisões de acesso do Azure AD](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Gerenciamento de identidades com privilégios do AD do Azure](../privileged-identity-management/pim-email-notifications.md)
- [Notificação de certificado expirando do Aplicativo Enterprise](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Notificações do serviço de provisionamento do Aplicativo Enterprise

Consulte a tabela a seguir para saber o tipo de notificações que são enviadas e onde verificá-las:

| Origem da notificação | O que é enviado | Onde verificar |
|:-|:-|:-|
| Contato técnico | Erros de sincronização | Folha portal do Azure-Propriedades |
| Centro de mensagens | Avisos sobre incidentes e degradação dos serviços de identidade e serviços de back-end Microsoft 365 | Portal do Office |
| Resumo semanal da proteção de identidade | Resumo da proteção de identidade | Folha Azure AD Identity Protection |
| Azure AD Connect Health | Notificações de alerta | Folha portal do Azure Azure AD Connect Health |
| Notificações de aplicativos empresariais | Notificações quando os certificados estão prestes a expirar e erros de provisionamento | Folha de aplicativos portal do Azure-Enterprise (cada aplicativo tem sua própria configuração de endereço de email) |

#### <a name="notifications-recommended-reading"></a>Leitura recomendada para notificações

- [Altere o endereço de sua organização, o contato técnico e muito mais](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Área de superfície operacional

### <a name="ad-fs-lockdown"></a>Bloqueio de AD FS

As organizações, que configuram aplicativos para que se autentiquem diretamente no Azure AD se beneficiam do [bloqueio inteligente do Azure AD](../authentication/concept-sspr-howitworks.md). Se você usar AD FS no Windows Server 2012 R2, implemente AD FS [proteção contra bloqueio de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se você usar AD FS no Windows Server 2016 ou posterior, implemente o [bloqueio inteligente da extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). No mínimo, recomendamos que você habilite o bloqueio de extranet para conter o risco de ataques de força bruta contra Active Directory locais. No entanto, se você tiver AD FS no Windows 2016 ou superior, habilite também o bloqueio inteligente de extranet que ajudará a reduzir os ataques de [irrigação de senha](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Se AD FS for usado somente para a Federação do Azure AD, há alguns pontos de extremidade que podem ser desativados para minimizar a área da superfície de ataque. Por exemplo, se AD FS for usado somente para o Azure AD, você deverá desabilitar WS-Trust pontos de extremidade diferentes dos pontos de extremidade habilitados para **usernamemixed** e **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Acesso a computadores com componentes de identidade local

As organizações devem bloquear o acesso aos computadores com componentes híbridos locais da mesma maneira que o seu domínio local. Por exemplo, um operador de backup ou administrador do Hyper-V não deve ser capaz de fazer logon no servidor de Azure AD Connect para alterar as regras.

O modelo de camada administrativa Active Directory foi projetado para proteger sistemas de identidade usando um conjunto de zonas de buffer entre controle total do ambiente (camada 0) e os ativos de estação de trabalho de alto risco que os invasores freqüentemente comprometem. ![Diagrama que mostra as três camadas do modelo de Camadas](./media/active-directory-ops-guide/active-directory-ops-img18.png)

O [modelo de camada](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) é composto de três níveis e inclui apenas contas administrativas, não contas de usuário padrão.

- **Camada 0** -Controle direto de identidades corporativas no ambiente. A Camada 0 inclui contas, grupos e outros recursos que têm controle administrativo direto ou indireto sobre as florestas, domínios ou controladores de domínio do Active Directory, e todos os ativos que eles contêm. A sensibilidade da segurança de todos os ativos de Camada 0 é equivalente, pois todos estão controlando uns aos outros.
- **Camada 1** -Controle de servidores e aplicativos corporativos. Entre os ativos de Camada 1 estão sistemas operacionais de servidor, serviços de nuvem e aplicativos corporativos. Contas de administrador de Camada 1 têm controle administrativo sobre uma quantidade considerável de conteúdo corporativo hospedada nesses ativos. Uma função de exemplo comum é a de administradores de servidor, que proporciona a esses sistemas operacionais a capacidade de afetar todos os serviços corporativos.
- **Camada 2**: controle de dispositivos e estações de trabalho do usuário. Contas de administrador de Camada 2 têm controle administrativo sobre uma quantidade considerável de conteúdo corporativo hospedada em estações de trabalho e dispositivos do usuário. Entre os exemplos estão Suporte técnico e administradores de suporte ao computador, pois podem afetar a integridade de quase todos os dados do usuário.

Bloqueie o acesso a componentes de identidade locais, como Azure AD Connect, AD FS e serviços SQL da mesma maneira como você faz para controladores de domínio.

## <a name="summary"></a>Resumo

Há sete aspectos para uma infraestrutura de identidade segura. Essa lista o ajudará a encontrar as ações que você deve executar para otimizar as operações do Azure Active Directory (AD do Azure).

- Atribuir proprietários a tarefas principais.
- Automatize o processo de atualização para componentes híbridos locais.
- Implante Azure AD Connect Health para monitoramento e relatório de Azure AD Connect e AD FS.
- Monitore a integridade de componentes híbridos locais arquivando e analisando os logs do agente de componentes usando System Center Operations Manager ou uma solução SIEM.
- Implemente melhorias de segurança medindo sua postura de segurança com a pontuação segura de identidade.
- Bloquear AD FS.
- Bloquear o acesso a computadores com componentes de identidade local.

## <a name="next-steps"></a>Próximas etapas

Consulte os [planos de implantação do Azure ad](active-directory-deployment-plans.md) para obter detalhes de implementação sobre qualquer recurso que você não tenha implantado.
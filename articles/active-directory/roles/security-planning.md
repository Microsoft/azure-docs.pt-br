---
title: Práticas de acesso seguro para administradores no AD do Azure | Microsoft Docs
description: Verifique se as contas de administrador e acesso administrativo da sua organização estão seguras. Para arquitetos de sistema e profissionais de TI que configurarem o Azure AD, Azure e serviços Online da Microsoft.
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594297"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteger o acesso privilegiado para implantações de nuvem híbrida no Azure AD

A segurança dos ativos de negócios da organização moderna depende da integridade das contas com privilégios que administram seus sistemas de TI. Os invasores virtuais usam ataques de roubo de credenciais para direcionar contas de administrador e outro acesso privilegiado para tentar obter acesso a dados confidenciais.

Para serviços de nuvem, prevenção e resposta são as responsabilidades conjuntas do provedor de serviços de nuvem e do cliente. Para obter mais informações sobre as ameaças mais recentes aos pontos de extremidade e à nuvem, consulte o [Relatório de Inteligência de Segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Este artigo pode ajudá-lo a desenvolver um roteiro para fechar as lacunas entre os planos atuais e as diretrizes descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com os padrões e conformidade normativa. Saiba mais sobre como a equipe de resposta a incidentes globais da Microsoft atenua os efeitos de ataques contra serviços de nuvem e como a segurança é criada em produtos comerciais da Microsoft e serviços de nuvem no [Microsoft Trust Center - Segurança](https://www.microsoft.com/trustcenter/security)e destinos de conformidade da Microsoft em [Microsoft Trust Center - Conformidade](https://www.microsoft.com/trustcenter/compliance).

Tradicionalmente, a segurança organizacional se concentrou nos pontos de entrada e saída de uma rede como o perímetro de segurança. No entanto, os aplicativos SaaS e dispositivos pessoais na Internet tornaram essa abordagem menos eficaz. No Azure AD, substituímos o perímetro de segurança de rede pela autenticação na camada de identidade da sua organização, com usuários atribuídos a funções administrativas privilegiadas no controle. Seu acesso deve ser protegido, independentemente de o ambiente estar no local, na nuvem ou em um híbrido.

Proteger acesso privilegiado requer alterações a:

* Processos, práticas administrativas e gerenciamento de conhecimento
* Componentes técnicos, como proteção de host, proteções de conta e gerenciamento de identidade

Proteja seu acesso privilegiado de uma maneira que seja gerenciado e relatado nos serviços da Microsoft que você deseja. Se você tiver contas administrativas locais, confira as diretrizes para acesso privilegiado no local e híbrido no Active Directory em [Proteção de acesso privilegiado](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> As diretrizes neste artigo referem-se principalmente a recursos do Azure Active Directory que são incluídos em planos de Azure Active Directory Premium P1 e P2. O Azure Active Directory Premium P2 está incluído no conjunto de EMS E5 e Microsoft 365 E5. Este guia pressupõe que sua organização já tem as licenças do Azure AD Premium P2 adquiridas para os usuários. Se você não tiver essas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, em todo este artigo, o termo administrador global significa a mesma coisa que "administrador da empresa" ou "administrador de locatários".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro

A Microsoft recomenda que você crie e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Você sempre pode ajustar seu roteiro para acomodar seus recursos existentes e os requisitos específicos dentro da sua organização. Cada estágio de nossos planos deverá aumentar o custo e a dificuldade de adversários de atacar o acesso privilegiado ao seu local, nuvem e ativos híbridos. A Microsoft recomenda os quatro estágios de roteiro a seguir. Agende primeiro as implementações mais eficazes e rápidas. Este artigo pode ser seu guia, com base nas experiências da Microsoft com a implementação de incidente e resposta de ataque cibernético. As linhas do tempo deste roteiro são aproximadas.

![Etapas do roteiro com linhas do tempo](./media/security-planning/roadmap-timeline.png)

* Estágio 1 (24 a 48 horas): Itens críticos, recomendamos que você faça imediatamente

* Estágio 2 (2 a 4 semanas): Reduzir as técnicas de ataque usados com mais frequência

* Estágio 3 (1 a 3 meses): Criar visibilidade e obter controle total da atividade do administrador

* Estágio 4 (seis meses ou mais): Continuar criando defesas para proteger ainda mais sua plataforma de segurança

Essa estrutura de roteiro foi projetada para maximizar o uso de tecnologias da Microsoft que você já tiver implantado. Considere a vincular qualquer ferramenta de segurança de outros fornecedores que você já tenha implantado ou esteja considerando a implantação.

## <a name="stage-1-critical-items-to-do-right-now"></a>Etapa 1: Itens críticos para fazer no momento

![Etapa 1 Itens críticos para fazer primeiro](./media/security-planning/stage-one.png)

A etapa 1 do roteiro do destina-se as tarefas críticas que são rápidas e fáceis de implementar. É recomendável que você faça alguns desses itens imediatamente dentro de 24 a 48 horas primeiro para garantir um nível básico de proteção ao acesso privilegiado. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ative o Azure AD Privileged Identity Management

Recomendamos que você ative o Azure AD PIM (Privileged Identity Management) em seu ambiente de produção do Azure AD. Depois de ativar o PIM, você receberá mensagens de email de notificação para alterações de função de acesso privilegiado. Notificações fornecem aviso antecipado quando usuários adicionais são adicionados às funções altamente privilegiadas.

O Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS E5. Para ajudá-lo a proteger o acesso a aplicativos e recursos locais e na nuvem, inscreva-se na [avaliação gratuita de 90 dias do Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). O Azure AD Privileged Identity Management e o Azure AD Identity Protection monitoram a atividade usando relatório, auditoria e alertas do Azure AD.

Depois de ativar o Azure AD Privileged Identity Management:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que seja um administrador global da sua organização de produção do Azure AD.

2. Para selecionar a organização do Azure AD em que você deseja usar o Privileged Identity Management, selecione seu nome de usuário no canto superior direito do portal do Azure.

3. No menu do portal do Azure, selecione **Todos os serviços** e filtre a lista para **Azure AD Privileged Identity Management**.

4. Abra o Privileged Identity Management a partir de **Todos os serviços** liste e fixá-o ao seu painel.

Garanta que a primeira pessoa a usar o PIM em sua organização seja atribuída com as funções de **Administrador de segurança** e **Administrador de funções com privilégios**. Somente os administradores com privilégios de função podem gerenciar atribuições de função de usuários do diretório do Azure AD. O assistente de segurança do PIM orienta você durante a experiência inicial de detecção e atribuição. Você pode sair do assistente sem fazer alterações adicionais no momento.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifique e categorize as contas que estão em funções altamente privilegiadas

Depois de ativar Azure AD Privileged Identity Management, visualize os usuários que estão nas seguintes funções do Azure AD:

* Administrador global
* Administrador de função com privilégios
* Administradores do Exchange
* Administrador do SharePoint

Se você não tiver o Azure AD Privileged Identity Management em sua organização, poderá usar a [API do PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember). Comece com a função de administrador global, pois um administrador global tem as mesmas permissões em todos os serviços de nuvem para os quais sua organização assinou. Essas permissões são concedidas independentemente de onde foram atribuídas: no Centro de Administração do Microsoft 365, no portal do Azure ou pelo módulo do Azure AD para Microsoft PowerShell.

Remova todas as contas que não são mais necessárias nessas funções. Em seguida, categorize as contas restantes atribuídas às funções administrativas:

* Atribuídas a usuários administrativos, mas também podem ser usadas para fins não administrativos (por exemplo, email pessoal)
* Atribuídas a usuários administrativos e usadas apenas para fins administrativos
* Compartilhada por vários usuários
* Para cenários de acesso de emergência em situação crítica
* Para scripts automatizados
* Para usuários externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Defina pelo menos duas contas de acesso de emergência

É possível que um usuário seja bloqueado acidentalmente de sua função. Por exemplo, se um provedor de identidade local federado não estiver disponível, os usuários não poderão entrar ou ativar uma conta de administrador existente. Você pode se preparar para a falta de acesso acidental armazenando duas ou mais contas de acesso de emergência.

As contas de acesso de emergência ajudam a restringir o acesso privilegiado em uma organização do Azure AD. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou urgência em que as contas administrativas normais não podem ser usadas. Certifique-se de controlar e reduzir o uso da conta de emergência somente pelo tempo necessário.

Avalie as contas atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente em nuvem usando o domínio \*.onmicrosoft.com (para acesso de emergência), crie-as. Para obter mais informações, consulte [Gerenciamento de contas administrativas de acesso de emergência no Azure AD](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ativar autenticação multifator e registrar todas as outras contas de administrador de usuário único, não federadas e altamente privilegiadas

Exigir a MFA (autenticação multifator) do Azure AD na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: administrador global, administrador de função com privilégios, administrador do Exchange e administrador do SharePoint. Use o guia para habilitar [Multi-factor Authentication (MFA) para suas contas de administrador](../authentication/howto-mfa-userstates.md) e certifique-se de que todos os usuários se registraram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas na etapa 2 e a etapa 3 do guia [proteger o acesso a dados e serviços no Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Etapa 2: Atenuar ataques usados com frequência

![Estágio 2 Atenuar ataques usados com frequência](./media/security-planning/stage-two.png)

O estágio 2 do roteiro se concentra na redução das técnicas de ataque e roubo de credenciais usadas com mais frequência e pode ser implementado em aproximadamente 2 a 4 semanas. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

O aumento de "bring your own device" e políticas de trabalho de casa e o crescimento da conectividade sem fio torna importante monitorar quem está se conectando à sua rede. Uma auditoria de segurança pode revelar dispositivos, aplicativos e programas em sua rede para os quais sua organização não dá suporte e que representam alto risco. Para obter mais informações, veja [visão geral de monitoramento e gerenciamento de segurança do Azure](../../security/fundamentals/management-monitoring-overview.md). Certifique-se de incluir todas as tarefas a seguir em seu processo de inventário.

* Identifique os usuários que têm os serviços e funções administrativas, onde eles podem gerenciar.
* Use o Azure AD PIM para descobrir quais usuários em sua organização têm acesso de administrador ao Azure AD.
* Além das funções definidas no Azure AD, o Microsoft 365 vem com um conjunto de funções de administrador que você pode atribuir a usuários em sua organização. Cada função de administrador é mapeada para funções comerciais comuns e concede permissão às pessoas em sua organização para realizar tarefas específicas no [Centro de Administração do Microsoft 365](https://admin.microsoft.com). Use o centro de administração Microsoft 365 para descobrir quais usuários em sua organização têm acesso de administrador a Microsoft 365, incluindo funções não gerenciadas no Azure AD. Para obter mais informações, consulte [sobre Microsoft 365 funções de administrador](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [práticas de segurança para o Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Execute o inventário em serviços de que sua organização depende, como Azure, Intune ou Dynamics 365.
* Verifique se as contas são usadas para fins de administração:

  * Tenha endereços de email de trabalho anexados a elas
  * Ter se registrado para a autenticação multifator do Azure AD ou usar MFA local
* Pergunte aos usuários sua justificativa de negócios para acesso administrativo.
* Remova o acesso de administrador para os indivíduos e serviços que não precisam dele.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas de trabalho ou escolares

Se os administradores globais iniciais reutilizarem suas credenciais de conta Microsoft existentes quando começarem a usar o Azure AD, substitua as contas da Microsoft por contas individuais baseadas em nuvem ou sincronizadas.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garantir contas de usuário separadas e encaminhamento de email para contas de administrador global

As contas de email pessoais são regularmente analisadas por invasores cibernéticos, um risco que torna os endereços de email pessoais inaceitáveis para contas de administrador global. Para ajudar a separar os riscos de internet de privilégios administrativos, crie contas dedicadas para cada usuário com privilégios administrativos.

* Certifique-se de criar contas separadas para os usuários realizarem tarefas de administrador global.
* Certifique-se de que seus administradores globais não abram emails acidentalmente ou executem programas com suas contas de administrador.
* Certifique-se de que essas contas têm seu email encaminhado para uma caixa de correio do trabalho.
* Contas de administrador global (e outros grupos privilegiados) devem ser contas somente em nuvem sem ligações para Active Directory locais.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as senhas de contas administrativas foram alteradas recentemente

Certifique-se de que todos os usuários entraram nas contas administrativas e alteraram as senhas pelo menos uma vez nos últimos 90 dias. Além disso, verifique se todas as contas compartilhadas tiveram as senhas alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ativar a sincronização de hash de senha

O Azure AD Connect sincroniza um hash do hash da senha do usuário de um Active Directory local para uma organização do Azure AD baseada em nuvem. Você poderá usar a sincronização de hash de senha como um backup se usar a federação com os AD FS (Serviços de Federação do Active Directory). Esse backup poderá ser útil se seus servidores do Active Directory ou AD FS locais estiverem temporariamente indisponíveis.

A sincronização de hash de senha permite que os usuários se conectem ao serviço com a mesma senha usada para entrar em sua instância local do Active Directory. A sincronização de hash de senha permite que a Proteção de Identidade detecte credenciais comprometidas comparando hashes de senha com senhas conhecidamente comprometidas. Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Exigir autenticação multifator para usuários em funções privilegiadas e usuários expostos

O Azure AD recomenda que você exija a MFA (autenticação multifator) para todos os usuários. Considere os usuários que teriam um impacto significativo se sua conta estivesse comprometida (por exemplo, gerentes financeiros). A MFA reduz o risco de um ataque devido a uma senha comprometida.

Ativar:

* [A MFA usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) para todos os usuários em sua organização.

Se você usar o Windows Hello for Business, o requisito de MFA pode ser atendido usando o logon do Windows Hello. Para obter mais informações, consulte [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Configurar o Identity Protection

Azure AD Identity Protection é uma ferramenta de monitoramento e relatório baseada em algoritmos que detecta possíveis vulnerabilidades que afetam as identidades da sua organização. Você pode configurar as respostas automatizadas a essas atividades suspeitas detectadas e tomar as devidas providências para resolvê-los. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Obter sua pontuação de segurança Microsoft 365 (se estiver usando Microsoft 365)

A pontuação segura examina suas configurações e atividades para os serviços de Microsoft 365 que você está usando e os compara a uma linha de base estabelecida pela Microsoft. Você receberá uma pontuação com base em quão alinhado com as práticas de segurança. Qualquer pessoa que tenha as permissões de administrador para uma assinatura Microsoft 365 Business Standard ou Enterprise pode acessar a pontuação segura em [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Examine as diretrizes de segurança e conformidade do Microsoft 365 (se estiver usando Microsoft 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de um cliente do Office 365 para configurar o Office 365 e habilitar outros recursos do EMS. Em seguida, examine as etapas 3-6 de como [proteger o acesso a dados e serviços no Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e no guia de como [monitorar a segurança e a conformidade no Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Configurar o monitoramento de atividades Microsoft 365 (se estiver usando Microsoft 365)

Monitore sua organização para usuários que estão usando Microsoft 365 para identificar a equipe que tem uma conta de administrador, mas pode não precisar de acesso Microsoft 365 porque elas não se conectam a esses portais. Para obter mais informações, confira [Relatórios de atividade no Centro de administração do Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de plano de resposta de incidente/emergência

O estabelecimento de uma capacidade de resposta a incidentes bem-sucedida requer planejamento considerável e recursos. Você deve monitorar continuamente os ataques cibernéticos e estabelecer prioridades para o tratamento de incidentes. Colete, analise e relate dados de incidentes para criar relações e estabelecer comunicação com outros grupos internos e proprietários do plano. Para obter mais informações, consulte [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteja as contas as contas administrativas locais, se ainda não tiver feito isso

Se a sua organização do Azure Active Directory estiver sincronizada ao Active Directory local, siga as orientações em [Roteiro de acesso privilegiado à segurança](/windows-server/identity/securing-privileged-access/securing-privileged-access): Este estágio inclui:

* Criar contas de administrador separadas para usuários que precisam realizar tarefas administrativas locais
* Implantar estações de trabalho com acesso privilegiado para administradores do Active Directory
* Criar senhas de administrador local exclusivas para estações de trabalho e servidores

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de assinaturas

Use o portal da Enterprise e o portal do Azure para identificar as assinaturas em sua organização que hospedam aplicativos de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover as contas da Microsoft de funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook não devem ser usadas como contas de administrador para assinaturas da sua organização. Remova o status administrativo de todas as contas da Microsoft e substitua por contas corporativas ou de estudante do Azure AD (por exemplo, chris@contoso.com). Para fins de administração, dependa de contas que são autenticadas no Azure AD e não em outros serviços.

#### <a name="monitor-azure-activity"></a>Monitorar a atividade do Azure

O Log de Atividades do Azure fornece um histórico de eventos no nível da assinatura no Azure. Oferece informações sobre quem criou, atualizou ou excluiu quais recursos e quando fez isso. Para obter mais informações, consulte [Auditar e receber notificações sobre ações importantes em sua assinatura do Azure](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="configure-conditional-access-policies"></a>Configurar as políticas de acesso condicional

Prepare as políticas de acesso condicional para o local e os aplicativos hospedados em nuvem. Se você tiver os dispositivos vinculados no local de trabalho dos usuários, obtenha mais informações em [Configurar acesso condicional local usando o registro do dispositivo do Azure Active Directory](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Etapa 3: Assuma o controle da atividade do administrador

![Estágio 3: assuma o controle da atividade do administrador](./media/security-planning/stage-three.png)

O Estágio 3 amplia as atenuações do Estágio 2 e deve ser implementado em aproximadamente um a três meses. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Concluir uma análise de acesso de usuários em funções de administrador

Mais usuários corporativos estão ganhando acesso privilegiado por meio de serviços de nuvem, o que pode levar a acesso não gerenciado. Atualmente, os usuários podem se tornar administradores globais para Microsoft 365, administradores de assinatura do Azure ou ter acesso de administrador a VMs ou por meio de aplicativos SaaS.

Sua organização deve fazer com que todos os funcionários tratem transações de negócios comuns como usuários sem privilégios e conceder direitos de administrador somente quando necessário. Conclua as revisões de acesso para identificar e confirmar os usuários que estão qualificados para ativar os privilégios de administrador.

É recomendável que você:

1. Determine quais usuários são administradores do Azure AD, habilite sob demanda, acesso de administração just-in-time e controles de segurança baseada em função.
2. Converter os usuários que têm sem justificativa clara para acesso de administrador com privilégios para uma função diferente (se não houver função qualificada, remova-o).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuar a distribuição de autenticação mais forte para todos os usuários

Exigir que usuários altamente expostos tenham autenticação moderna e forte, como o Azure AD MFA ou o Windows Hello. Exemplos de usuários altamente expostos incluem:

* Diretores
* Gerentes de alto nível
* Pessoal de TI e segurança crítica

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Use estações de trabalho dedicadas para a administração do Azure AD

Os invasores podem direcionar para contas com privilégios para interromper a integridade e autenticidade dos dados. Geralmente, eles usam código mal-intencionado que altera a lógica do programa ou espiona o administrador inserindo uma credencial. As Estações de Trabalho com Acesso Privilegiado (PAWs) fornecem um sistema operacional dedicado para as tarefas confidenciais protegidas contra ataques da Internet e vetores de ameaça. Separar essas contas e tarefas confidenciais de dispositivos e estações de trabalho de uso diário proporciona forte proteção contra:

* Ataques de phishing
* Vulnerabilidades do aplicativo e do sistema operacional
* Ataques de usurpação de identidade
* Ataques de roubo de credenciais, como registro de pressionamento de teclas, Pass-the-Hash e Pass-the-Ticket

Com a implantação de estações de trabalho de acesso privilegiado, você pode reduzir o risco de os administradores inserirem credenciais em um ambiente de área de trabalho que não foi protegido. Para saber mais, confira [Privileged Identity Management](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Analise as recomendações do Instituto Nacional de padrões e tecnologia para lidar com incidentes

O National Institute of Standards and Technology (NIST) fornece diretrizes para tratamento de incidentes, especialmente para analisar dados relacionados a incidentes e determinar a resposta apropriada para cada incidente. Para obter mais informações, consulte [(NIST) o Computer Security Incident Handling Guide (SP 61 800, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Privileged Identity Management (PIM) para JIT a funções administrativas adicionais

Para o Azure Active Directory, use o recurso [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). Ativação de tempo limitado de funções privilegiadas funciona, permitindo que você:

* Ativar os privilégios de administrador para executar uma tarefa específica
* Imponha o MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora de banda
* Permita que os usuários mantenham os privilégios de acesso por um período de tempo pré-configurado
* Permita que os administradores de segurança:

  * Descubra todas as identidades com privilégios
  * Visualize relatórios de auditoria
  * Crie revisões de acesso para identificar cada usuário que é elegível para ativar privilégios de administrador

Se você já estiver usando Azure AD Privileged Identity Management, ajuste os períodos de tempo para os privilégios associados à hora conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine a exposição a protocolos com senha (se estiver usando o Exchange Online)

Recomendamos a identificação de todos os usuários potenciais que poderão ser catastróficos para a organização se suas credenciais forem comprometidas. Para esses usuários, coloque em vigor requisitos de autenticação fortes e use o acesso condicional do Azure AD para impedir a entrada em seus emails usando o nome de usuário e a senha. Você pode bloquear a [autenticação herdada usando acesso condicional](../conditional-access/block-legacy-authentication.md) e pode [bloquear a autenticação básica](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) por meio do Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Concluir uma avaliação de análise de funções para Microsoft 365 funções (se estiver usando Microsoft 365)

Avalie se todos os usuários administradores estão nas funções corretas (exclua e reatribua de acordo com essa avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Examinar a abordagem de gerenciamento de incidentes de segurança usada em Microsoft 365 e comparar com sua própria organização

Você pode baixar esse relatório do [Gerenciamento de incidentes de segurança no Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger as contas administrativas privilegiadas locais

Se o seu Azure Active Directory estiver conectado ao Active Directory local, siga as orientações em [Roteiro de Acesso Privilegiado à Segurança](/windows-server/identity/securing-privileged-access/securing-privileged-access): Estágio 2. Neste estágio, você pode:

* Implantar estações de trabalho com acesso privilegiado para todos os administradores
* Exigir MFA
* Usar apenas o administrador suficiente para a manutenção do controlador de domínio, reduzindo a superfície de ataque de domínios
* Implantar a avaliação avançada de ameaças para detecção de ataque

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitoramento integrado

A [Central de Segurança do Azure Security](../../security-center/security-center-introduction.md):

* Fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure
* Ajuda a detectar ameaças que não seriam observadas de outra forma
* Funciona com uma ampla variedade de soluções de segurança

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Faça o inventário de contas privilegiadas em Máquinas Virtuais hospedadas

Normalmente, você não precisa dar aos usuários permissões irrestritas para todas as suas assinaturas ou recursos do Azure. Use as funções de administrador do Azure AD para conceder somente o acesso para os usuários que precisam realizar seus trabalhos. Você pode usar as funções do Azure AD para permitir que um administrador gerencie apenas máquinas virtuais em uma assinatura, enquanto outro pode gerenciar banco de dados SQL dentro da mesma assinatura. Para obter mais informações, consulte [o que é o controle de acesso baseado em função do Azure](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Use o Privileged Identity Management com as funções de administrador do Azure Ad para gerenciar, controlar e monitorar o acesso aos recursos do Azure. Use as proteções de PIM reduzindo o tempo de exposição de privilégios e aumentando sua visibilidade sobre o uso por meio de alertas e relatórios. Para obter mais informações, consulte [o que é Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usar integrações do log do Azure para enviar logs relevantes do Azure para seus sistemas SIEM

A integração de log do Azure permite que você integre logs brutos de seus recursos do Azure para os sistemas de SIEM (informações de segurança e gerenciamento de eventos) existentes da sua organização. [A integração de logs do Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) coleta eventos do Windows de logs de Visualizador de Eventos do Windows e recursos do Azure de:

* Logs de atividades do Azure
* Alertas da Central de Segurança do Azure
* Logs de recursos do Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento do usuário para aplicativos conectados

O Azure AD permite automatizar a criação e a manutenção de identidades do usuário em aplicativos de nuvem, como Dropbox, Salesforce e ServiceNow. Para saber mais, confira [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar proteção de informações

O Microsoft Cloud App Security permite que você investigue arquivos e defina políticas com base em rótulos de classificação da Proteção de Informações do Azure permitindo maior visibilidade e controle de seus dados na nuvem. Verifique e classifique arquivos na nuvem e aplique rótulos de proteção de informações do Azure. Para obter mais informações, consulte [integração da Proteção de Informações do Microsoft Azure](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurar acesso condicional

Configure o acesso condicional com base em grupo, localização e sensibilidade de aplicativo para [aplicativos SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicativos conectados ao Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorar a atividade de aplicativos de nuvem conectados

Recomendamos o uso do [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) para garantir que o acesso do usuário também esteja protegido em aplicativos conectados. Esse recurso protege o acesso corporativo a aplicativos de nuvem, além de proteger suas contas de administrador, permitindo:

* Aumentar a visibilidade e controle para aplicativos em nuvem
* Criar políticas de acesso, atividades e compartilhamento de dados
* Identificar automaticamente as atividades arriscadas, comportamentos anormais e ameaças
* Impedir o vazamento de dados
* Minimizar o risco e prevenção de ameaças automatizado e aplicação de políticas

O agente SIEM Cloud App Security integra Cloud App Security com seu servidor SIEM para habilitar o monitoramento centralizado de alertas e atividades de Microsoft 365. Ele é executado no servidor e recebe alertas e atividades de segurança do aplicativo de nuvem e transmite-as no servidor SIEM. Para obter mais informações, consulte [Integração SIEM](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Estágio 4: Continuar criando defesas

![Estágio 4: adotar uma postura de segurança ativa](./media/security-planning/stage-four.png)

O estágio 4 do roteiro deve ser implementado em cerca de seis meses. Conclua seu roteiro para reforçar as proteções de acesso privilegiado de possíveis ataques que são conhecidos hoje. Para as ameaças de segurança do amanhã, é recomendável ver a segurança como um processo contínuo para aumentar os custos e reduzir a taxa de sucesso dos adversários que estão direcionando para o seu ambiente.

Proteger o acesso privilegiado é importante para estabelecer garantias de segurança para seus ativos de negócios. No entanto, ele deve fazer parte de um programa de segurança completo que fornece garantias de segurança contínuas. Este programa deve incluir elementos como:

* Política
* Operações
* Segurança das informações
* Servidores
* Aplicativos
* PCs
* Dispositivos
* Malha da nuvem

Recomendamos as seguintes práticas quando você estiver gerenciando contas de acesso privilegiado:

* Certifique-se de que os administradores estão fazendo seus negócios diários, como usuários sem privilégios
* Conceda acesso privilegiado apenas quando necessário e remova-o posteriormente (just-in-time)
* Mantenha registros de atividades de auditoria relacionadas a contas privilegiadas

Para obter mais informações sobre a criação de um roteiro de segurança completa, consulte [recursos de arquitetura de TI de nuvem da Microsoft](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Para que os serviços da Microsoft ajudem você a implementar qualquer parte do seu roteiro, entre em contato com seu representante da Microsoft ou confira [Construir defesas cibernéticas essenciais para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Essa etapa final do roteiro Secured Privileged Access inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-ad"></a>Analise as funções de administrador no Azure AD

Determine se as funções de administrador integradas atuais do Azure AD ainda estão atualizadas e garanta que os usuários estão apenas com as funções de que precisam. Com o Azure AD, você pode atribuir administradores separados para realizar diferentes funções. Para saber mais informações, consulte [Atribuindo funções de administrador no Azure Active Directory](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Analise os usuários que têm administraçõa dos dispositivos unidos do Azure AD

Para obter mais informações, consulte [Como configurar dispositivos híbridos unidos do Azure Active Directory](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Examinar os membros de [funções internas de administrador de Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Ignore esta etapa se você não estiver usando Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Valiar o plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que você valide regularmente seu que está funcionando conforme o esperado:

* Passe pelo seu roteiro existente para ver o que foi perdido
* Com base na análise post mortem, revise práticas existentes ou defina novas práticas
* Certifique-se de que seu plano de resposta a incidentes está atualizado e que as práticas são distribuídas por toda a organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure 

Determine se você precisa [transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Vigilância": o que fazer em caso de emergência

![Contas para acesso à interrupção de emergência](./media/security-planning/emergency.jpeg)

1. Notifique os principais gerentes e executivos de segurança com informações sobre o incidente.

2. Analise o guia estratégico de ataque.

3. Acesse sua combinação de nome de usuário e senha da conta de "vigilância"” para entrar no Azure AD.

4. Obtenha ajuda da Microsoft ao [abrir uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Examine os [relatórios de entrada do Azure AD](../reports-monitoring/overview-reports.md). Pode haver um espaço de tempo entre um evento ocorrendo e quando ele é incluído no relatório.

6. Para ambientes híbridos, se sua infraestrutura local federada e seu servidor de AD FS não estiverem disponíveis, você poderá alternar temporariamente da autenticação federada para usar a sincronização de hash de senha. Essa opção reverte a Federação de domínio de volta para a autenticação gerenciada até que o servidor de AD FS se torne disponível.

7. Monitorar o email quanto a contas com privilégios.

8. Certifique-se de salvar os backups de logs relevantes para investigação forense e investigação jurídica.

Para obter mais informações sobre como o Microsoft Office 365 trata os incidentes de segurança, consulte [gerenciamento de incidentes de segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Perguntas frequentes: Respostas para proteger o acesso privilegiado  

**P:** O que devo fazer se ainda não tiver implementado nenhum componente de acesso seguro?

**Resposta:** Defina pelo menos duas contas de interrupção, atribua o MFA às suas contas de administrador privilegiado e separe as contas de usuário das contas de administrador global.

**P:** Após uma violação, qual é o problema superior que precisa ser abordado primeiro?

**Resposta:** Certifique-se de que você está exigindo a autenticação mais forte para indivíduos altamente expostos.

**P:** O que acontecerá se nossos administradores com privilégios forem desativados?

**Resposta:** Crie uma conta de administrador global que sempre seja mantida atualizada.

**P:** O que acontece se houver apenas um administrador global e eles não puderem ser acessados?

**Resposta:** Use uma de suas contas de vigilância para obter acesso privilegiado imediato.

**P:** Como posso proteger administradores dentro da minha organização?

**Resposta:** Os administradores sempre fazem seus negócios diários como usuários "sem privilégios" padrão.

**P:** Quais são as práticas recomendadas para a criação de contas de administrador no Azure AD?

**Resposta:** Reserva com privilégios de acesso para as tarefas administrativas específicas.

**P:** Quais ferramentas existem para reduzir o acesso de administrador persistentes?

**Resposta:** Funções de administrador Privileged Identity Management (PIM) e Azure AD.

**P:** Qual é a posição da Microsoft sobre a sincronização de contas de administrador no Azure AD?

**Resposta:** As contas de administrador do Nível 0 são usadas somente para contas do AD local. Essas contas normalmente não são sincronizadas com o Azure AD na nuvem. As contas de administrador do Nível 0 incluem contas, grupos e outros ativos que têm controle administrativo direto ou indireto de floresta, domínios, controladores de domínio e ativos do Active Directory local.

**P:** Como podemos impedir que os administradores atribuam acesso aleatório de administrador no portal?

**Resposta:** Use contas sem privilégios para todos os usuários e a maioria dos administradores. Inicie desenvolvimento de um volume da organização para determinar quais contas de administrador devem ser privilegiadas. E monitore usuários administrativos recém-criados.

## <a name="next-steps"></a>Próximas etapas

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) - Produtos e serviços de nuvem de recursos de segurança da Microsoft

* [Central de confiabilidade da Microsoft-conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – conjunto abrangente de ofertas de conformidade da Microsoft para serviços de nuvem

* [Orientação sobre como realizar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – gerenciar requisitos de conformidade e segurança para serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros Microsoft Online Services

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) - O Microsoft Intune oferece recursos de gerenciamento de dispositivo móvel, gerenciamento de aplicativo móvel e gerenciamento de PC na nuvem.

* [Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – O Dynamics 365 é a solução baseada em nuvem da Microsoft que unifica o gerenciamento de relacionamento com clientes (CRM) e recursos de planejamento de recursos empresariais (ERP).

---
title: Práticas recomendadas de segurança de identidade e acesso do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas para gerenciamento de identidade e controle de acesso usando recursos internos do Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f872c61ad0597d2307cd244668fdfc258f7a45cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611245"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Práticas recomendadas de Gerenciamento de Identidade do Azure e segurança de controle de acesso

Muitos consideram a identidade como a nova camada de limite para segurança, assumindo a função antes exercida pela perspectiva centrada em rede tradicional. Essa evolução do eixo primário para a atenção e investimentos em relação à segurança decorre do fato de que os perímetros de rede estão se tornando cada vez mais porosos e a defesa do perímetro não pode ser tão eficiente quanto era antes da explosão de dispositivos [BYOD](https://aka.ms/byodcg) e aplicativos de nuvem.

Neste artigo, abordaremos uma coleção de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidades do Azure. Essas práticas recomendadas derivam da nossa experiência com o [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo sobre práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure se baseia em um consenso e nos recursos e conjuntos de recursos da plataforma Azure existentes quando este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure discutidas neste artigo incluem:

* Tratar identidade como o perímetro de segurança primário
* Centralizar o gerenciamento de identidade
* Habilitar logon único
* Ativar acesso condicional
* Habilitar o gerenciamento de senhas
* Impor a verificação de autenticação multifator para usuários
* Use o controle de acesso baseado em função
* Exposição menor de contas privilegiadas
* Controlar os locais onde os recursos estão localizados

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar identidade como o perímetro de segurança primário

Muitos consideram que a identidade seja primário perímetro de segurança. Essa é uma mudança de enfoque tradicional de segurança de rede. Perímetros de rede cada vez mais porosos e a defesa do perímetro não pode ser tão eficiente quanto era antes da explosão da [BYOD](https://aka.ms/byodcg) dispositivos e aplicativos de nuvem.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidades e acesso. O Azure AD é um multilocatário baseado em nuvem, identidade e diretório de serviço de gerenciamento da Microsoft. Combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução.

As seções a seguir listam as práticas recomendadas de segurança de acesso e identidade usando o Microsoft Azure Active Directory.

## <a name="centralize-identity-management"></a>Centralizar o gerenciamento de identidade

Em um cenário de [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?), recomendamos que você integre seus locais e diretórios na nuvem. A integração permite que sua equipe de TI gerencie contas de um único local, independentemente de onde uma conta é criada. Integração também ajuda os usuários a serem mais produtivos fornecendo uma identidade comum para acessar os recursos de nuvem e locais.


**Melhor prática**: Integrar seus diretórios locais ao Azure AD.  
**Detalhe**: Use o [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) para sincronizar seu diretório local com seu diretório na nuvem.

**Melhor prática**: Ativar a sincronização de hash de senha.  
**Detalhe**: A sincronização de hash de senha é um recurso usado para sincronizar hashes de hashes da senha do usuário de uma instância do Active Directory local para uma instância do Microsoft Azure AD baseada em nuvem.

Mesmo se você decidir usar a federação com os serviços de Federação do Active Directory (AD FS) ou outros provedores de identidade, você pode definir opcionalmente a sincronização de hash de senha como um backup no caso de seus servidores locais falhem ou fiquem temporariamente indisponíveis. Isso permite que os usuários se conectem ao serviço usando a mesma senha que eles usam para entrar em sua instância local do Active Directory. Além disso, permite a proteção de identidade para detectar credenciais comprometidas, comparando os hashes de senha com senhas conhecidas como compormetidas, se um usuário tiver utilizado o mesmo endereço de email e senha em outros serviços não estiverem conectados ao Microsoft Azure Active Directory.

Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

Organizações que não integram suas identidades locais com sua identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e violações de segurança.

## <a name="enable-single-sign-on"></a>Habilitar logon único

Em um mundo de dispositivos móveis e nuvem em primeiro lugar, você deseja habilitar o logon único (SSO) para dispositivos, aplicativos e serviços de qualquer lugar para que os usuários possam ser produtivos sempre. Quando você tiver várias soluções de identidade para gerenciar, isso se torna um problema administrativo não apenas para TI, mas também para os usuários que precisem se lembrar de várias senhas.

Ao usar a mesma solução de identidade para todos os aplicativos e recursos, você pode obter o SSO. E seus usuários podem usar o mesmo conjunto de credenciais para entrar e acessar os recursos que eles precisam, se os recursos estiverem localizados no local ou na nuvem.

**Melhor prática**: Habilitar SSO.  
**Detalhe**: O Azure AD [estende o Active Directory local](../active-directory/connect/active-directory-aadconnect.md) para a nuvem. Os usuários podem usar sua conta escolar ou corporativa primária para seus dispositivos ingressados no domínio, os recursos da empresa e todos os aplicativos de SaaS que eles precisam para realizar seu trabalho e web. Os usuários não precisam se lembrar de vários conjuntos de nomes de usuário e senhas e o acesso do aplicativo pode ser automaticamente provisionado (ou desprovisionado) com base em suas associações de grupo da organização e seu status como funcionário. E você pode controlar o acesso para aplicativos da galeria ou para seus próprios aplicativos locais que você já desenvolveu e publicado por meio de [com o Proxy de aplicativo Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Use o SSO para permitir que os usuários acessem seus [aplicativos de SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base em sua conta corporativa ou de estudante no Microsoft Azure Active Directory. Isso é aplicável não apenas a aplicativos de SaaS da Microsoft, mas também a outros aplicativos, como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). É possível configurar seu aplicativo para usar o Microsoft Azure Active Directory como uma [identidade baseada em um servidor SAML](../active-directory/fundamentals-identity.md). Como um controle de segurança do Azure AD emite um token que permite aos usuários entrar no aplicativo, a menos que eles receberam acesso por meio do Microsoft Azure Active Directory. Você pode conceder acesso diretamente ou por meio de um grupo do qual fazem parte.

As organizações que não criam uma identidade comum para estabelecer SSO para seus usuários e aplicativos estão mais expostas a cenários em que os usuários têm várias senhas. Nesses cenários aumentam a probabilidade de reutilização de senhas ou usar senhas fracas.

## <a name="turn-on-conditional-access"></a>Ativar acesso condicional

Os usuários podem acessar os recursos da organização usando uma grande variedade de dispositivos e aplicativos de qualquer lugar. Como um administrador de TI, você deseja certificar-se de que esses dispositivos atendam aos padrões de segurança e conformidade. Por causa disso, concentrar-se apenas em quem pode acessar um recurso que não é mais suficiente.

Para dominar o equilíbrio entre segurança e produtividade, você também precisa considerar como um recurso é acessado em uma decisão de controle de acesso. Com acesso condicional do Azure AD, você pode atender a esse requisito. Com o acesso condicional, você pode tomar decisões de controle de acesso automatizado para o acesso a aplicativos de nuvem com base em condições.

**Melhor prática**: Gerenciar e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configure o [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md) do Azure AD com base em um grupo, localização e sensibilidade de aplicativo para aplicativos SaaS e aplicativos conectados ao Azure AD.

## <a name="enable-password-management"></a>Habilitar o gerenciamento de senhas

Se você tiver vários locatários ou deseja permitir que os usuários [redefinam suas próprias senhas](../active-directory/active-directory-passwords-update-your-own-password.md), é importante que você use as políticas de segurança apropriadas para evitar abusos.

**Melhor prática**: Configurar SSPR (redefinição de senha self-service) para seus usuários.  
**Detalhe**: Use o recurso de [redefinição de senha self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) do Azure AD.

**Melhor prática**: Monitorar como ou se o SSPR realmente está sendo usado.  
**Detalhe**: Monitore os usuários que estão se registrando com o uso do [relatório de atividade de registro de redefinição de senha](../active-directory/active-directory-passwords-get-insights.md) do Azure AD. O recurso de relatório do Microsoft Azure AD fornece ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor a verificação de autenticação multifator para usuários

É recomendável que você exija a verificação em duas etapas para todos os seus usuários. Isso inclui os administradores e outros em sua organização que pode ter um impacto significativo se sua conta for comprometida (por exemplo, gerentes financeiros).

Há várias opções para exigir a verificação em duas etapas. A melhor opção para você depende de suas metas, a edição do Microsoft Azure Active Directory que você está executando e o programa de licenciamento. Consulte [Como exigir verificação em duas etapas para um usuário](../active-directory/authentication/howto-mfa-userstates.md) para determinar a melhor opção para você. Consulte a[Microsoft Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) e [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas de preço para obter mais informações sobre as licenças de preço e preços.

Estas são as opções e benefícios para habilitar a verificação em duas etapas:

**Opção 1**: [Habilitar a Autenticação Multifator alterando o estado do usuário](../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Esse é o método tradicional para exigir a verificação em duas etapas. Funciona com ambos [Autenticação Multifator do Azure na nuvem e o Servidor de Autenticação Multifator do Microsoft Azure](../active-directory/authentication/concept-mfa-whichversion.md). O uso desse método exigirá que os usuários realizem a verificação em duas etapas sempre que entrarem e substituírem as políticas de acesso condicional.

**Opção 2**: [Habilitar a Autenticação Multifator com a política de acesso condicional](../active-directory/authentication/howto-mfa-getstarted.md).
**Benefício**: Essa opção permite que você solicite a verificação em duas etapas sob condições específicas usando o [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md). As condições específicas podem ser entrada do usuário de locais diferentes, não confiável de dispositivos ou aplicativos que você considere arriscadas. Definir condições específicas em que você exige verificação em duas etapas permite que você evite solicitação constante para seus usuários, que podem ser uma experiência de usuário desagradáveis.

Essa é a maneira mais flexível para habilitar a verificação em duas etapas para seus usuários. Habilitar uma política de acesso condicional funciona apenas para autenticação multifator do Azure na nuvem e é um recurso premium do Microsoft Azure Active Directory. Você pode encontrar mais informações sobre esse método em [implantar autenticação de multifator do Azure baseado em nuvem](../active-directory/authentication/howto-mfa-getstarted.md).

**Opção 3**: Habilitar a Autenticação Multifator com políticas de acesso condicional ao avaliar o usuário e o risco de entrada do [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Benefício**: Essa opção permite que você:

- Detecte possíveis vulnerabilidades que afetam as identidades da organização.
- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização.
- Investigar incidentes suspeitos e tomar as devidas providências para resolvê-los.

Este método usa a avaliação de risco do Azure AD Identity Protection para determinar se a verificação em duas etapas é necessária com base no usuário e risco de entrada para todos os aplicativos de nuvem. Este método requer o licenciamento do Azure Active Directory P2. Você pode encontrar mais informações sobre esse método no [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> A opção 1, que habilita a Autenticação Multifator do Microsoft Azure alterando o estado do usuário, substitui as políticas de acesso condicionais. Como opções 2 e 3 usam políticas de acesso condicional, você não pode usar a opção 1 com eles.

As organizações que não adicionam camadas adicionais de proteção de identidade, como verificação em duas etapas, são mais suscetíveis a ataques de roubo de credencial. Um ataque de roubo de credencial pode levar ao comprometimento de dados.

## <a name="use-role-based-access-control-rbac"></a>Usar o RBAC (controle de acesso baseado em função)

Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. Você pode usar [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

É possível usar funçõs do [RBAC interno](../role-based-access-control/built-in-roles.md) no Azure para atribuir privilégios aos usuários. As organizações que não impõem o controle de acesso de dados usando recursos como o RBAC podem estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento dos dados, permitindo o acesso de usuário a determinados tipos de dados (por exemplo, alto impacto nos negócios) que não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposição menor de contas privilegiadas

Proteger com privilégios o acesso é a primeira etapa crítica para proteger os ativos de negócios. Minimizando o número de pessoas que têm acesso a informações seguras ou recursos reduz a chance de um usuário mal-intencionado obter acesso, ou um usuário autorizado afetar acidentalmente um recurso confidencial.

As contas privilegiadas são aquelas que administram e gerenciam sistemas de TI. Os invasores virtuais visam essas contas para obter acesso aos sistemas e aos dados de uma organização. Para proteger o acesso privilegiado, isole as contas e os sistemas do risco de exposição a um usuário mal-intencionado.

É recomendável que você desenvolva e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Para obter informações sobre como criar um roteiro detalhado para proteger as identidades e acesso que são gerenciados ou relatado no Microsoft Azure AD Microsoft Azure, Office 365 e outros serviços de nuvem, examine [acesso privilegiado de proteção para implantações de nuvem e híbridos no Microsoft Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

A seguir, um resumo das práticas recomendadas encontradas na [proteção de acesso privilegiado para implantações de nuvem e híbridos no Microsoft Azure Active Directory](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Melhor prática**: Gerenciar, controlar e monitorar o acesso a contas privilegiadas.   
**Detalhe**: Ative o [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Depois de ativar o Privileged Identity Management, você receberá mensagens de notificação por email para alterações da função de acesso privilegiado. Essas notificações fornecem aviso antecipado quando usuários adicionais são adicionados às funções altamente privilegiadas no seu diretório.

**Melhor prática**: Identificar e categorizar as contas que estão em funções altamente privilegiadas.   
**Detalhe**: Depois de ativar o Azure AD Privileged Identity Management, exiba os usuários que estão no administrador global, administrador de função com privilégios e outras funções altamente privilegiadas. Remova as contas que não são mais necessárias nessas funções e categorize as contas que são atribuídas a funções de administrador:

- Atribuídas individualmente a usuários administrativos e podem ser usadas para fins não administrativos (por exemplo, email pessoal)
- Individualmente atribuídas para usuários administrativos e designadas para fins administrativos apenas
- Compartilhada por vários usuários
- Para cenários de acesso de emergência
- Para scripts automatizados
- Para usuários externos

**Melhor prática**: Implementar acesso JIT ("Just-In-Time") para reduzir ainda mais o tempo de exposição de privilégios e aumentar sua visibilidade sobre o uso de contas privilegiadas.   
**Detalhe**: O Gerenciamento de identidades com privilégios do AD do Azure:

- Limitar os usuários a assumir seus privilégios JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Melhor prática**: Definir pelo menos duas contas de acesso de emergência.   
**Detalhe**: Contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. Contas de acesso de emergência são limitadas a cenários em que as contas administrativas normais não podem ser usadas. As organizações devem limitar o uso da conta de emergência para somente a quantidade necessária de tempo.

Avalie as contas que são atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente na nuvem usando o `*.onmicrosoft.com` domínio (usado para acesso de emergência), crie-os. Para obter mais informações, consulte Gerenciamento de contas administrativas de acesso de emergência no Microsoft Azure Active Directory.

**Melhor prática**: Ativar a Autenticação Multifator e registrar todas as outras contas de administrador de usuário único, não federadas e altamente privilegiadas.  
**Detalhe**: Exija a Autenticação Multifator do Azure na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais funções de administrador do Azure AD: administrador global, administrador de funções com privilégios, administrador do Exchange Online e administrador do SharePoint Online. Use o guia para habilitar [a Autenticação Multifator do Microsoft Azure para suas contas de administrador](../active-directory/authentication/howto-mfa-userstates.md) e certifique-se de que todos esses usuários têm [registrado](https://aka.ms/mfasetup).

**Melhor prática**: Realizar etapas para diminuir as técnicas de ataque usadas com mais frequência.  
**Detalhe**: [Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas corporativas ou de estudante](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Garanta contas de usuário separadas e emails de encaminhamento para as contas de administrador global](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Certifique-se de que as senhas de contas administrativas foram alteradas recentemente](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ativar a sincronização de hash de senha](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Exigir Autenticação Multifator do Microsoft Azure para usuários em todas as funções privilegiadas, bem como os usuários expostos](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obter o Office 365 Secure Score (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Verifique as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configure o Office 365 Activity Monitoring (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Estabelecer os proprietários de plano de resposta de incidente/emergência](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Continuar a proteger as contas administrativas privilegiadas locais](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Se você não proteger o acesso privilegiado, talvez você têm muitos usuários em funções altamente privilegiadas e são mais vulneráveis a ataques. Atores mal-intencionados, incluindo os invasores virtuais, contas de administrador de destino com frequência e outros elementos de acesso privilegiado para obter acesso a dados confidenciais e sistemas usando o roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Controlar os locais onde os recursos estão localizados

É muito importante habilitar os operadores de nuvem a executar tarefas, mas impedi-los de romper convenções que são necessárias para gerenciar recursos de sua organização. As organizações que desejam controlar os locais em que os recursos são criados devem codificar esses locais.

Você pode usar [o Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar políticas de segurança cujas definições descrevem as ações ou os recursos que são especificamente negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são o mesmo que o RBAC. Na verdade, usam o RBAC para autorizar usuários a criar esses recursos.
>
>

As organizações que não estão controlando como os recursos são criados são mais suscetíveis a usuários que podem usar o serviço de maneira imprópria, criando mais recursos do que o necessário. Proteger o processo de criação de recursos é uma etapa importante para proteger um cenário de multilocatário.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorar ativamente as atividades suspeitas

Uma sistema de monitoramento de identidade Active Directory pode detectar o comportamento suspeito e disparar um alerta para uma investigação mais aprofundada rapidamente. A tabela a seguir lista os dois recursos do Azure Active Directory que podem ajudar as organizações a monitorar suas identidades:

**Melhor prática**: Ter um método para identificar:

- Tenta entrar em [sem rastreamento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Força bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataques contra uma conta específica.
- Tentativas de entrada de vários locais.
- Entradas de [dispositivos infectados](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Entradas de endereços IP suspeitos.

**Detalhe**: Use os [relatórios de anomalia](../active-directory/active-directory-view-access-usage-reports.md) do Azure AD Premium. Ter processos e procedimentos em vigor para que os administradores de TI executem esses relatórios diariamente ou sob demanda (geralmente em um cenário de resposta a incidentes).

**Melhor prática**: Ter um sistema de monitoramento ativo que notifica você a respeito dos riscos e pode ajustar o nível de risco (alto, médio ou baixo) a seus requisitos de negócios.   
**Detalhe**: Use o [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), que sinaliza os riscos atuais em seu próprio painel e envia notificações com resumos diárias por email. Para ajudar a proteger as identidades da organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado for atingido.

As organizações que não monitoram ativamente os seus sistemas de identidade estão em risco de ter as credenciais de usuário comprometidas. Sem o conhecimento que atividades suspeitas estão ocorrendo através dessas credenciais, as organizações não podem reduzir esse tipo de ameaça.

## <a name="next-step"></a>Próxima etapa

Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

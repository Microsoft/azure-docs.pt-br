---
title: Práticas recomendadas de segurança de identidade e acesso do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas para gerenciamento de identidade e controle de acesso usando recursos internos do Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: f69fe97c33a17ade39f67078d5b035dac4d0bfaf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034159"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Práticas recomendadas de Gerenciamento de Identidade do Azure e segurança de controle de acesso

Neste artigo, abordaremos uma coleção de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidades do Azure. Essas práticas recomendadas derivam da nossa experiência com o [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo sobre práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure se baseia em um consenso e nos recursos e conjuntos de recursos da plataforma Azure existentes quando este artigo foi escrito.

Este artigo tem como objetivo fornecer um roteiro geral para uma postura de segurança mais robusta após a implantação guiada por nossa lista de verificação “[5 etapas para proteger sua infraestrutura de identidade](steps-secure-identity.md)” que apresenta um passo a passo de alguns dos nossos principais recursos e serviços.

As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure discutidas neste artigo incluem:

* Tratar identidade como o perímetro de segurança primário
* Centralizar o gerenciamento de identidade
* Gerenciar locatários conectados
* Habilitar logon único
* Ativar acesso condicional
* Planejar melhorias de segurança de rotina
* Habilitar o gerenciamento de senhas
* Impor a verificação de autenticação multifator para usuários
* Use o controle de acesso baseado em função
* Exposição menor de contas privilegiadas
* Controlar os locais onde os recursos estão localizados
* Usar o Azure Active Directory para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar identidade como o perímetro de segurança primário

Muitos consideram que a identidade seja primário perímetro de segurança. Essa é uma mudança de enfoque tradicional de segurança de rede. Perímetros de rede cada vez mais porosos e a defesa do perímetro não pode ser tão eficiente quanto era antes da explosão da [BYOD](/mem/intune/fundamentals/byod-technology-decisions) dispositivos e aplicativos de nuvem.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidades e acesso. O Azure AD é um multilocatário baseado em nuvem, identidade e diretório de serviço de gerenciamento da Microsoft. Combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução.

As seções a seguir listam as práticas recomendadas de segurança de acesso e identidade usando o Microsoft Azure Active Directory.

**Melhor prática**: Centralizar controles de segurança e detecções em relação a identidades de usuário e de serviço.
**Detalhe**: Usar o Azure Active Directory para colocar controles e identidades.

## <a name="centralize-identity-management"></a>Centralizar o gerenciamento de identidade

Em um cenário de [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?), recomendamos que você integre seus locais e diretórios na nuvem. A integração permite que sua equipe de TI gerencie contas de um local, independentemente de onde a conta é criada. A integração também ajuda os usuários a serem mais produtivos fornecendo uma identidade comum para acessar os recursos de nuvem e locais.

**Melhor prática**: Estabeleça uma única instância do Azure Active Directory. A consistência e uma única fonte autoritativa aumentarão a clareza e reduzirão os riscos de segurança de erros humanos e da complexidade da configuração.
**Detalhe**: Designe um único diretório do Azure Active Directory como a fonte de autoridade para contas corporativas e organizacionais.

**Melhor prática**: Integrar seus diretórios locais ao Azure AD.  
**Detalhe**: Use o [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) para sincronizar seu diretório local com seu diretório na nuvem.

> [!Note]
> Há [fatores que afetam o desempenho do Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). O Azure AD Connect precisa ter capacidade suficiente para evitar que os sistemas com baixo desempenho prejudiquem a segurança e a produtividade. Organizações grandes ou complexas, que provisionam mais de 100.000 objetos, devem seguir as [recomendações](../../active-directory/hybrid/whatis-hybrid-identity.md) para otimizar sua implementação de Azure AD Connect.

**Melhor prática**: Não sincronize contas para o Azure Active Directory que têm privilégios altos na sua instância de Active Directory existente.
**Detalhe**: Não altere a configuração padrão do [Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) que filtra essas contas. Essa configuração reduz o risco de os adversários dinamizarem da nuvem para ativos locais (o que poderia criar um grande incidente).

**Melhor prática**: Ativar a sincronização de hash de senha.  
**Detalhe**: A sincronização de hash de senha é um recurso usado para sincronizar hashes de senhas de usuário de uma instância do Active Directory local para uma instância do Azure Active Directory local. Essa sincronização ajuda a proteger contra credenciais vazadas reproduzidas de ataques anteriores.

Mesmo se você decidir usar a federação com os serviços de Federação do Active Directory (AD FS) ou outros provedores de identidade, você pode definir opcionalmente a sincronização de hash de senha como um backup no caso de seus servidores locais falhem ou fiquem temporariamente indisponíveis. Essa sincronização permite que os usuários se conectem ao serviço usando a mesma senha que eles usam para entrar em sua instância local do Active Directory. Além disso, permite a proteção de identidade para detectar credenciais comprometidas, comparando os hashes de senha sincronizadas com senhas conhecidas como comprometidas, se um usuário tiver utilizado o mesmo endereço de email e senha em outros serviços não estão conectados ao Azure Active Directory.

Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Melhor prática**: Para o desenvolvimento de novos aplicativos, use o Azure Active Directory para autenticação.
**Detalhe**: Use os recursos corretos para dar suporte à autenticação:

  - Azure Active Directory para funcionários
  - [Azure AD B2B](../../active-directory/external-identities/index.yml) para convidados e parceiros externos
  - [Azure AD B2C](../../active-directory-b2c/index.yml) para controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos

Organizações que não integram suas identidades locais com sua identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e violações de segurança.

> [!Note]
> Você precisa escolher em quais diretórios as contas críticas residirão e se a estação de trabalho de administração usada será gerenciada por novos serviços de nuvem ou processos existentes. Usar processos de gerenciamento e provisionamento de identidade pode reduzir alguns riscos, mas também criar o risco de um invasor comprometer uma conta local e dinamizar para a nuvem. Você pode precisar de uma estratégia diferente para funções diferentes (por exemplo, administradores de TI x administradores de unidade de negócios). Você tem duas opções. A primeira opção é criar contas do Azure Active Directory que não estejam sincronizadas com sua instância de Active Directory local. Ingresse sua estação de trabalho de administrador no Azure Active Directory, que você pode gerenciar e aplicar patches com Microsoft Intune. A segunda opção é usar contas de administrador existentes ao sincronizar com sua instância de Active Directory local. Use estações de trabalho existentes no seu domínio do Active Directory para gerenciamento e segurança.

## <a name="manage-connected-tenants"></a>Gerenciar locatários conectados
Sua organização de segurança precisa de visibilidade para avaliar o risco e determinar se as políticas da sua organização e os requisitos regulatórios estão sendo cumpridos. Você deve garantir que sua organização de segurança possa ver todas as assinaturas conectadas ao seu ambiente de produção e rede (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) ou [VPN site a site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Um [administrador global](../../active-directory/roles/permissions-reference.md#global-administrator) no Azure AD pode elevar seu acesso à função de [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) e ver todas as assinaturas e grupos gerenciados conectados ao seu ambiente.

Consulte [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md) para garantir que você e seu grupo de segurança possam exibir todas as assinaturas ou grupos de gerenciamento conectados ao seu ambiente. Você deve remover esse acesso elevado depois de avaliar os riscos.

## <a name="enable-single-sign-on"></a>Habilitar logon único

Em um mundo de dispositivos móveis e nuvem em primeiro lugar, você deseja habilitar o logon único (SSO) para dispositivos, aplicativos e serviços de qualquer lugar para que os usuários possam ser produtivos sempre. Quando você tiver várias soluções de identidade para gerenciar, isso se torna um problema administrativo não apenas para TI, mas também para os usuários que precisem se lembrar de várias senhas.

Ao usar a mesma solução de identidade para todos os aplicativos e recursos, você pode obter o SSO. E seus usuários podem usar o mesmo conjunto de credenciais para entrar e acessar os recursos que eles precisam, se os recursos estiverem localizados no local ou na nuvem.

**Melhor prática**: Habilitar SSO.  
**Detalhe**: O Azure AD [estende o Active Directory local](../../active-directory/hybrid/whatis-hybrid-identity.md) para a nuvem. Os usuários podem usar sua conta escolar ou corporativa primária para seus dispositivos ingressados no domínio, os recursos da empresa e todos os aplicativos de SaaS que eles precisam para realizar seu trabalho e web. Os usuários não precisam se lembrar de vários conjuntos de nomes de usuário e senhas e o acesso do aplicativo pode ser automaticamente provisionado (ou desprovisionado) com base em suas associações de grupo da organização e seu status como funcionário. E você pode controlar o acesso para aplicativos da galeria ou para seus próprios aplicativos locais que você já desenvolveu e publicado por meio de [com o Proxy de aplicativo Azure AD](../../active-directory/manage-apps/application-proxy.md).

Use o SSO para permitir que os usuários acessem seus [aplicativos de SaaS](../../active-directory/manage-apps/what-is-single-sign-on.md) com base em sua conta corporativa ou de estudante no Microsoft Azure Active Directory. Isso é aplicável não apenas a aplicativos de SaaS da Microsoft, mas também a outros aplicativos, como [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) e [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md). É possível configurar seu aplicativo para usar o Microsoft Azure Active Directory como uma [identidade baseada em um servidor SAML](../../active-directory/fundamentals/active-directory-whatis.md). Como um controle de segurança do Azure AD emite um token que permite aos usuários entrar no aplicativo, a menos que eles receberam acesso por meio do Microsoft Azure Active Directory. Você pode conceder acesso diretamente ou por meio de um grupo do qual fazem parte.

As organizações que não criam uma identidade comum para estabelecer SSO para seus usuários e aplicativos estão mais expostas a cenários em que os usuários têm várias senhas. Nesses cenários aumentam a probabilidade de reutilização de senhas ou usar senhas fracas.

## <a name="turn-on-conditional-access"></a>Ativar acesso condicional

Os usuários podem acessar os recursos da organização usando uma grande variedade de dispositivos e aplicativos de qualquer lugar. Como administrador de TI, você deseja que esses dispositivos atendam aos padrões de segurança e conformidade. Por causa disso, concentrar-se apenas em quem pode acessar um recurso que não é mais suficiente.

Para dominar o equilíbrio entre segurança e produtividade, você também precisa considerar como um recurso é acessado em uma decisão sobre o controle de acesso. Com acesso condicional do Azure Active Directory, você pode atender a esse requisito. Com o acesso condicional, você pode tomar decisões de controle de acesso automatizado com base em condições de acesso a aplicativos de nuvem.

**Melhor prática**: Gerenciar e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configure as [políticas de acesso condicional](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) comuns do Azure Active Directory com base em um grupo, localização e sensibilidade de aplicativo para aplicativos SaaS e aplicativos conectados ao Azure Active Directory.

**Melhor prática**: Bloquear protocolos de autenticação herdados.
**Detalhe**: Todos os dias, invasores exploram os pontos fracos nos protocolos mais antigos, especialmente para ataques de irrigação de senha. Configure o acesso condicional para [bloquear protocolos herdados](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Planejar melhorias de segurança de rotina

A segurança está sempre em evolução e é importante integrar uma maneira de mostrar regularmente o crescimento e descobrir novas maneiras de proteger seu ambiente à sua estrutura de gerenciamento de internet e de nuvem.

A classificação de segurança de identidade é um conjunto de controles de segurança recomendados que a Microsoft publica e que oferece uma pontuação numérica para medir objetivamente sua postura de segurança e ajudar a planejar melhores de segurança futuras. Você também pode comparar sua pontuação com a de outros setores, bem como suas próprias tendências ao longo do tempo.

**Melhor prática**: Planejar revisões de segurança de rotina e aprimoramentos com base nas melhores práticas do seu setor.
**Detalhe**: Use o recurso de classificação de segurança de identidade para classificar as melhorias ao longo do tempo.

## <a name="enable-password-management"></a>Habilitar o gerenciamento de senhas

Se você tiver vários locatários ou deseja permitir que os usuários [redefinam suas próprias senhas](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), é importante que você use as políticas de segurança apropriadas para evitar abusos.

**Melhor prática**: Configurar SSPR (redefinição de senha self-service) para seus usuários.  
**Detalhe**: Use o recurso de [redefinição de senha self-service](../../active-directory-b2c/user-flow-self-service-password-reset.md) do Azure AD.

**Melhor prática**: Monitorar como ou se o SSPR realmente está sendo usado.  
**Detalhe**: Monitore os usuários que estão se registrando com o uso do [relatório de atividade de registro de redefinição de senha](../../active-directory/authentication/howto-sspr-reporting.md) do Azure AD. O recurso de relatório do Microsoft Azure AD fornece ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

**Melhor prática**: Estender as políticas de senha baseadas em nuvem para sua infraestrutura local.
**Detalhe**: Aprimore as políticas de senha na sua organização executando as mesmas verificações usadas em alterações de senha baseada em nuvem para as alterações de senha local. Instale a [proteção por senha do Azure Active Directory](../../active-directory/authentication/concept-password-ban-bad.md) para agentes do Active Directory do Windows Server local para estender as listas de senhas proibidas à infraestrutura existente. Os usuários e administradores que alteram, definem ou redefinem senhas locais precisam obedecer à mesma política de senha que os usuários somente na nuvem.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor a verificação de autenticação multifator para usuários

É recomendável que você exija a verificação em duas etapas para todos os seus usuários. Isso inclui os administradores e outros em sua organização que pode ter um impacto significativo se sua conta for comprometida (por exemplo, gerentes financeiros).

Há várias opções para exigir a verificação em duas etapas. A melhor opção para você depende de suas metas, a edição do Microsoft Azure Active Directory que você está executando e o programa de licenciamento. Consulte [Como exigir verificação em duas etapas para um usuário](../../active-directory/authentication/howto-mfa-userstates.md) para determinar a melhor opção para você. Consulte as páginas de preços da [autenticação multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) do Azure [ad](https://azure.microsoft.com/pricing/details/active-directory/) e do Azure ad para obter mais informações sobre licenças e preços.

Estas são as opções e benefícios para habilitar a verificação em duas etapas:

**Opção 1**: Habilite a MFA para todos os usuários e métodos de logon com os **Benefícios** dos padrões de segurança do Azure Active Directory: Essa opção permite aplicar a MFA de forma fácil e rápida para todos os usuários no seu ambiente com uma política rigorosa para:

* Desafiar contas administrativas e mecanismos de logon administrativo
* Exigir desafio de MFA por meio do Microsoft Authenticator para todos os usuários
* Restringir protocolos de autenticação herdados.

Esse método está disponível a todos os níveis de licenciamento, mas não pode ser misturado com políticas de acesso condicional existentes. Você pode encontrar mais informações nos [padrões de segurança do Azure ad](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

**Opção 2**: [Habilitar a Autenticação Multifator alterando o estado do usuário](../../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Esse é o método tradicional para exigir a verificação em duas etapas. Ele funciona com [a autenticação multifator do Azure ad na nuvem e servidor de autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md). O uso desse método exigirá que os usuários realizem a verificação em duas etapas sempre que entrarem e substituírem as políticas de acesso condicional.

Para determinar onde a autenticação multifator precisa ser habilitada, consulte [qual versão do Azure ad MFA é adequada para minha organização?](../../active-directory/authentication/concept-mfa-howitworks.md).

**Opção 3**: [Habilitar a Autenticação Multifator com a política de acesso condicional](../../active-directory/authentication/howto-mfa-getstarted.md).
**Benefício**: Essa opção permite que você solicite a verificação em duas etapas sob condições específicas usando o [acesso condicional](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). As condições específicas podem ser entrada do usuário de locais diferentes, não confiável de dispositivos ou aplicativos que você considere arriscadas. Definir condições específicas em que você exige verificação em duas etapas permite que você evite solicitação constante para seus usuários, que podem ser uma experiência de usuário desagradáveis.

Essa é a maneira mais flexível para habilitar a verificação em duas etapas para seus usuários. A habilitação de uma política de acesso condicional funciona apenas para a autenticação multifator do Azure AD na nuvem e é um recurso Premium do Azure AD. Você pode encontrar mais informações sobre esse método em [implantar a autenticação multifator do Azure ad baseada em nuvem](../../active-directory/authentication/howto-mfa-getstarted.md).

**Opção 4**: Habilite a Autenticação Multifator com políticas de acesso condicional avaliando [políticas de acesso condicional com base em risco](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Benefício**: Essa opção permite que você:

* Detecte possíveis vulnerabilidades que afetam as identidades da organização.
* Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização.
* Investigar incidentes suspeitos e tomar as devidas providências para resolvê-los.

Este método usa a avaliação de risco do Azure AD Identity Protection para determinar se a verificação em duas etapas é necessária com base no usuário e risco de entrada para todos os aplicativos de nuvem. Este método requer o licenciamento do Azure Active Directory P2. Você pode encontrar mais informações sobre esse método no [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> A opção 2, permitindo a autenticação multifator alterando o estado do usuário, substitui as políticas de acesso condicional. Como as opções 3 e 4 usam políticas de acesso condicional, você não pode usar a opção 2 com elas.

As organizações que não adicionam camadas adicionais de proteção de identidade, como verificação em duas etapas, são mais suscetíveis a ataques de roubo de credencial. Um ataque de roubo de credencial pode levar ao comprometimento de dados.

## <a name="use-role-based-access-control"></a>Use o controle de acesso baseado em função

O gerenciamento de acesso para recursos de nuvem é essencial para qualquer organização que usa a nuvem. O Azure [RBAC (controle de acesso baseado em função) do Azure](../../role-based-access-control/overview.md) ajuda você a gerenciar quem tem acesso aos recursos do Azure, o que eles podem fazer com esses recursos e em quais áreas eles têm acesso.

A designação de funções individuais ou de grupos responsáveis por funções específicas no Azure ajuda a evitar confusão que podem levar a erros humanos e de automação que criam riscos de segurança. Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados.

A equipe de segurança precisa ver os recursos do Azure para avaliar e corrigir riscos. Se a equipe de segurança tiver responsabilidades operacionais, ela precisará de permissões adicionais para realizar o trabalho.

Você pode usar o [Azure RBAC](../../role-based-access-control/overview.md) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

**Melhor prática**: Separar tarefas dentro da equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, permita apenas determinadas ações para um escopo específico.
**Detalhe**: use [funções internas do Azure](../../role-based-access-control/built-in-roles.md) no Azure para atribuir privilégios a usuários.

> [!Note]
> Permissões específicas criam complexidade e confusão desnecessárias, acumuladas em uma configuração "herdada" que é difícil de corrigir, devido ao medo de quebrar algo. Evite permissões específicas para cada recurso. Em vez disso, use grupos de gerenciamento para permissões de toda a empresa e grupos de recursos para permissões nas assinaturas. Evite permissões específicas para cada usuário. Em vez disso, atribua acesso a grupos do Azure Active Directory.

**Melhor prática**: Conceda às equipes de segurança acesso às responsabilidades do Azure para ver os recursos do Azure para que eles possam avaliar e corrigir riscos.
**Detalhe**: conceder às equipes de segurança a função de [leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader) do RBAC do Azure. Você pode usar o grupo de gerenciamento raiz ou o grupo de gerenciamento de segmentos, dependendo do escopo das responsabilidades:

* **Grupo de gerenciamento raiz** para equipes responsáveis por todos os recursos da empresa
* **Grupo de gerenciamento de segmento** para equipes com escopo limitado (normalmente devido a regulamentações ou outros limites organizacionais)

**Melhor prática**: Conceda permissões apropriadas às equipes de segurança que têm responsabilidades operacionais diretas.
**Detalhe**: examine as funções internas do Azure para a atribuição de função apropriada. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md). Assim como as funções internas, é possível atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, no grupo de recursos e nos escopos de recurso.

**Práticas recomendadas**: Conceda acesso à Central de Segurança do Azure a funções de segurança que precisam dela. A Central de Segurança permite que as equipes de segurança identifiquem e corrijam riscos rapidamente.
**Detalhe**: Adicione equipes de segurança com essas necessidades à função [administrador de segurança](../../role-based-access-control/built-in-roles.md#security-admin) RBAC do Azure para que possam exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações e ignorar alertas e recomendações. Você pode fazer isso usando o grupo de gerenciamento raiz ou o grupo de gerenciamento de segmentos, dependendo do escopo das responsabilidades.

As organizações que não impõem o controle de acesso a dados usando recursos como o RBAC do Azure podem estar concedendo mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento dos dados, pois permite o acesso de usuário a tipos de dados (por exemplo, alto impacto nos negócios) que eles não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposição menor de contas privilegiadas

Proteger com privilégios o acesso é a primeira etapa crítica para proteger os ativos de negócios. Minimizando o número de pessoas que têm acesso a informações seguras ou recursos reduz a chance de um usuário mal-intencionado obter acesso, ou um usuário autorizado afetar acidentalmente um recurso confidencial.

As contas privilegiadas são aquelas que administram e gerenciam sistemas de TI. Os invasores virtuais visam essas contas para obter acesso aos sistemas e aos dados de uma organização. Para proteger o acesso privilegiado, isole as contas e os sistemas do risco de exposição a um usuário mal-intencionado.

É recomendável que você desenvolva e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Para obter informações sobre como criar um roteiro detalhado para proteger identidades e acesso que são gerenciados ou relatados no Azure AD, Microsoft Azure, Microsoft 365 e outros serviços de nuvem, examine [protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure ad](../../active-directory/roles/security-planning.md).

A seguir, um resumo das práticas recomendadas encontradas na [proteção de acesso privilegiado para implantações de nuvem e híbridos no Microsoft Azure Active Directory](../../active-directory/roles/security-planning.md):

**Melhor prática**: Gerenciar, controlar e monitorar o acesso a contas privilegiadas.   
**Detalhe**: Ative o [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md). Depois de ativar o Privileged Identity Management, você receberá mensagens de notificação por email para alterações da função de acesso privilegiado. Essas notificações fornecem aviso antecipado quando usuários adicionais são adicionados às funções altamente privilegiadas no seu diretório.

**Melhor prática**: Verifique se todas as contas de administrador críticas são contas gerenciadas do Azure Active Directory.
**Detalhe**: Remova todas as contas de consumidor das funções de administrador críticas (ex.: contas da Microsoft, como hotmail.com, live.com e outlook.com).

**Melhor prática**: Verifique se todas as funções de administrador críticas têm uma conta separada para tarefas administrativas para evitar phishing e outros ataques que comprometam os privilégios administrativos.
**Detalhe**: Crie uma conta de administrador separada com privilégios necessários para executar tarefas administrativas. Bloqueie o uso dessas contas administrativas para ferramentas de produtividade diárias como Microsoft 365 email ou navegação na Web arbitrária.

**Melhor prática**: Identificar e categorizar as contas que estão em funções altamente privilegiadas.   
**Detalhe**: Depois de ativar o Azure AD Privileged Identity Management, exiba os usuários que estão no administrador global, administrador de função com privilégios e outras funções altamente privilegiadas. Remova as contas que não são mais necessárias nessas funções e categorize as contas que são atribuídas a funções de administrador:

* Atribuídas individualmente a usuários administrativos e podem ser usadas para fins não administrativos (por exemplo, email pessoal)
* Individualmente atribuídas para usuários administrativos e designadas para fins administrativos apenas
* Compartilhada por vários usuários
* Para cenários de acesso de emergência
* Para scripts automatizados
* Para usuários externos

**Melhor prática**: Implementar acesso JIT ("Just-In-Time") para reduzir ainda mais o tempo de exposição de privilégios e aumentar sua visibilidade sobre o uso de contas privilegiadas.   
**Detalhe**: O Gerenciamento de identidades com privilégios do AD do Azure:

* Limitar os usuários a assumir seus privilégios JIT.
* Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Melhor prática**: Definir pelo menos duas contas de acesso de emergência.   
**Detalhe**: Contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. Contas de acesso de emergência são limitadas a cenários em que as contas administrativas normais não podem ser usadas. As organizações devem limitar o uso da conta de emergência para somente a quantidade necessária de tempo.

Avalie as contas que são atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente na nuvem usando o `*.onmicrosoft.com` domínio (usado para acesso de emergência), crie-os. Para obter mais informações, consulte [Gerenciamento de contas administrativas de acesso de emergência no Azure AD](../../active-directory/roles/security-emergency-access.md).

**Melhor prática**: Ter um processo de “interrupção de emergência” em caso de necessidade.
**Detalhe**: Siga as etapas em [Proteger o acesso privilegiado para implantações de nuvem e híbridos no Azure Active Directory](../../active-directory/roles/security-planning.md).

**Melhor prática**: Exigir que todas as contas de administrador críticas sejam sem senha (preferencial) ou exijam a Autenticação Multifator.
**Detalhe**: Use o [aplicativo Microsoft Authenticator](../../active-directory/authentication/howto-authentication-passwordless-phone.md) para entrar em qualquer conta do Azure Active Directory sem usar uma senha. Como o [Windows Hello para Empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário que esteja associada a um dispositivo e use autenticação biométrica ou um PIN.

Exigir autenticação multifator do Azure AD na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: administrador global, administrador de função com privilégios, administrador do Exchange Online e administrador do SharePoint Online. Habilite a [ Autenticação Multifator do Microsoft Azure para suas contas de administrador](../../active-directory/authentication/howto-mfa-userstates.md) e verifique se os usuários de conta de administrador se registraram.

**Melhor prática**: Para contas de administrador críticas, tenha uma estação de trabalho de administração em que as tarefas de produção não são permitidas (ex.: navegação e email). Isso protegerá as contas de administrador contra vetores de ataque que usam navegação e email e reduzirá significativamente o risco de um incidente grave.
**Detalhe**: Use uma estação de trabalho de administração. Escolha um nível de segurança de estação de trabalho:

- Dispositivos de produtividade altamente seguros fornecem segurança avançada para navegação e outras tarefas de produtividade.
- [Estações de trabalho com acesso privilegiado (PAWs)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) fornecem um sistema operacional dedicado protegido contra ataques da Internet e vetores de ameaça para tarefas confidenciais.

**Melhor prática**: Desprovisione contas de administrador quando os funcionários deixarem sua organização.
**Detalhe**: Tenha um processo em vigor que desabilite ou exclua contas de administrador quando os funcionários deixarem sua organização.

**Melhor prática**: Teste regularmente as contas de administrador usando as técnicas de ataque atuais.
**Detalhe**: Use Microsoft 365 o simulador de ataque ou uma oferta de terceiros para executar cenários de ataque realistas em sua organização. Isso pode ajudá-lo a encontrar usuários vulneráveis antes que ocorra um ataque real.

**Melhor prática**: Realizar etapas para diminuir as técnicas de ataque usadas com mais frequência.  
**Detalhe**: [Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas corporativas ou de estudante](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Garanta contas de usuário separadas e emails de encaminhamento para as contas de administrador global](../../active-directory/roles/security-planning.md)  

[Certifique-se de que as senhas de contas administrativas foram alteradas recentemente](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ativar a sincronização de hash de senha](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Exigir Autenticação Multifator do Microsoft Azure para usuários em todas as funções privilegiadas, bem como os usuários expostos](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Obter sua pontuação de segurança Microsoft 365 (se estiver usando Microsoft 365)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Examinar as diretrizes de segurança de Microsoft 365 (se estiver usando Microsoft 365)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Configurar o monitoramento de atividades Microsoft 365 (se estiver usando Microsoft 365)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Estabelecer os proprietários de plano de resposta de incidente/emergência](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Continuar a proteger as contas administrativas privilegiadas locais](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Se você não proteger o acesso privilegiado, talvez você têm muitos usuários em funções altamente privilegiadas e são mais vulneráveis a ataques. Atores mal-intencionados, incluindo os invasores virtuais, contas de administrador de destino com frequência e outros elementos de acesso privilegiado para obter acesso a dados confidenciais e sistemas usando o roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Controlar os locais onde os recursos estão localizados

É muito importante habilitar os operadores de nuvem a executar tarefas, mas impedi-los de romper convenções que são necessárias para gerenciar recursos de sua organização. As organizações que desejam controlar os locais em que os recursos são criados devem codificar esses locais.

Você pode usar [o Azure Resource Manager](../../azure-resource-manager/management/overview.md) para criar políticas de segurança cujas definições descrevem as ações ou os recursos que são especificamente negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são as mesmas que as do Azure RBAC. Na verdade, eles usam o RBAC do Azure para autorizar os usuários a criarem esses recursos.
>
>

As organizações que não estão controlando como os recursos são criados são mais suscetíveis a usuários que podem usar o serviço de maneira imprópria, criando mais recursos do que o necessário. Proteger o processo de criação de recursos é uma etapa importante para proteger um cenário de multilocatário.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorar ativamente as atividades suspeitas

Uma sistema de monitoramento de identidade Active Directory pode detectar o comportamento suspeito e disparar um alerta para uma investigação mais aprofundada rapidamente. A tabela a seguir lista os dois recursos do Azure Active Directory que podem ajudar as organizações a monitorar suas identidades:

**Melhor prática**: Ter um método para identificar:

- Tenta entrar em [sem rastreamento](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- [Força bruta](../../active-directory/reports-monitoring/howto-find-activity-reports.md) ataques contra uma conta específica.
- Tentativas de entrada de vários locais.
- Entradas de [dispositivos infectados](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Entradas de endereços IP suspeitos.

**Detalhe**: Use os [relatórios de anomalia](../../active-directory/reports-monitoring/overview-reports.md) do Azure AD Premium. Ter processos e procedimentos em vigor para que os administradores de TI executem esses relatórios diariamente ou sob demanda (geralmente em um cenário de resposta a incidentes).

**Melhor prática**: Ter um sistema de monitoramento ativo que notifica você a respeito dos riscos e pode ajustar o nível de risco (alto, médio ou baixo) a seus requisitos de negócios.   
**Detalhe**: Use o [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), que sinaliza os riscos atuais em seu próprio painel e envia notificações com resumos diárias por email. Para ajudar a proteger as identidades da organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado for atingido.

As organizações que não monitoram ativamente os seus sistemas de identidade estão em risco de ter as credenciais de usuário comprometidas. Sem o conhecimento que atividades suspeitas estão ocorrendo através dessas credenciais, as organizações não podem reduzir esse tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Usar o Azure Active Directory para autenticação de armazenamento
O [Armazenamento do Microsoft Azure](../../storage/common/storage-auth-aad.md) dá suporte a autenticação e autorização com o Azure Active Directory para armazenamento de blob e de filas. Com a autenticação do Azure Active Directory, você pode usar o controle de acesso baseado em função do Azure para conceder permissões específicas a usuários, grupos e aplicativos para o escopo de um contêiner de blob ou fila individual.

Recomendamos usar o [Azure Active Directory para autenticar o acesso ao armazenamento](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Próxima etapa

Veja [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.
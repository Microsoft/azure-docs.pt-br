---
title: O que é o Active Directory do Azure? - Active Directory do Azure | Microsoft Docs
description: Visão geral e informações conceituais sobre o Azure Active Directory, incluindo terminologia, quais licenças estão disponíveis e uma lista de recursos associados com links para mais informações.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 05/08/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fafa7bd95801be46025727b2261fc95bc539988
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440541"
---
# <a name="what-is-azure-active-directory"></a>O que é o Active Directory do Azure?

O Azure Active Directory (Azure AD) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft, que ajuda seus funcionários a entrar e acessar recursos em:

- Recursos externos, como o Microsoft Office 365, o portal do Azure e milhares de outros aplicativos SaaS.

- Recursos internos, como aplicativos em sua rede corporativa e intranet, juntamente com outros aplicativos de nuvem desenvolvidos por sua organização.

Você pode usar os vários cartazes da [Série Microsoft Cloud for Enterprise Architects](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) para entender melhor os principais serviços de identidade no Azure, no Azure AD e no Office 365.

## <a name="who-uses-azure-ad"></a>Quem usa o Azure AD?

O Azure AD destina-se a:

- **Administradores de TI.** Como administrador de TI, você pode usar o Azure AD para controlar o acesso aos seus aplicativos e recursos de aplicativos com base em suas necessidades comerciais. Por exemplo, você pode usar o Azure AD para exigir a autenticação multifator ao acessar recursos importantes da organização. Além disso, você pode usar o Azure AD para automatizar o provisionamento de usuários entre o Windows Server AD existente e seus aplicativos de nuvem, incluindo o Office 365. Por fim, o Azure AD fornece ferramentas avançadas para ajudar a proteger automaticamente as credenciais e identidades de usuário e para atender aos seus requisitos de controle de acesso. Para começar, inscreva-se em uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Desenvolvedores de aplicativos.** Como desenvolvedor de aplicativos, o Azure AD fornece uma abordagem baseada em padrões para adicionar SSO (logon único) ao seu aplicativo, permitindo que ele funcione com as credenciais pré-existentes de um usuário. O Azure AD também fornece APIs que podem ajudar você a criar experiências de aplicativo personalizadas usando os dados organizacionais existentes. Para começar, inscreva-se em uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Para saber mais, confira também o [Azure Active Directory para desenvolvedores](../develop/index.yml).

- **Assinantes do Microsoft 365, do Office 365, do Azure ou do Dynamics CRM Online.** Como assinante, você já está usando o Azure AD. Cada locatário do Microsoft 365, do Office 365, do Azure e do Dynamics CRM Online é automaticamente um locatário do Azure AD. Comece imediatamente a gerenciar o acesso aos seus aplicativos de nuvem integrados.

## <a name="what-are-the-azure-ad-licenses"></a>O que são as licenças do Azure AD?

Serviços comerciais do Microsoft Online, como o Office 365 ou o Microsoft Azure, exigem o Azure AD para entrada e para ajudar com a proteção da identidade. Se você assina algum serviço empresarial do Microsoft Online, você obtém automaticamente o Azure AD com acesso a todos os recursos gratuitos.

Para aprimorar a implementação do Azure AD, também é possível adicionar recursos pagos atualizando para as licenças do Azure Active Directory Basic, Premium P1 ou Premium P2. As licenças pagas do Azure AD se baseiam no diretório gratuito existente, oferecendo autoatendimento, monitoramento avançado, relatórios de segurança e acesso seguro para seus usuários móveis.

>[!Note]
>Para ver as opções de preço dessas licenças, confira [Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
>
>O Azure Active Directory Premium P1, Premium P2 e Azure Active Directory Basic não têm suporte atualmente na China. Para obter mais informações sobre os preços do Azure AD, contate o [Fórum do Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Gratuito.** Fornece gerenciamento de usuários e de grupos, sincronização de diretório local, relatórios básicos, autoatendimento para alteração de senha e logon único no Azure, no Office 365 e em muitos aplicativos SaaS populares.

- **Azure Active Directory Basic.** Além dos recursos gratuitos, o Básico também fornece acesso a aplicativos centrados na nuvem, gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos de nuvem, e Proxy de Aplicativo do Azure AD, que permite que você publique aplicativos Web locais usando o Azure AD.

- **Azure Active Directory Premium P1.** Além dos recursos dos planos Básico e Gratuito, o P1 também permite que os usuários híbridos acessem recursos locais e na nuvem. Ele também dá suporte à administração avançada, como grupos dinâmicos, gerenciamento de grupo de autoatendimento, Microsoft Identity Manager (um conjunto de gerenciamento de acesso e identidade local) e recursos de write-back de nuvem, que permitem a redefinição de senha por autoatendimento para os usuários locais.

- **Azure Active Directory Premium P2.** Além dos recursos nos planos Gratuito, Básico e P1, o P2 também oferece o [Azure Active Directory Identity Protection](../identity-protection/enable.md), a fim de fornecer acesso condicional baseado em risco para seus aplicativos e dados críticos da empresa, e o [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md), para ajudar a descobrir, restringir e monitorar os administradores e o acesso deles a recursos e fornecer acesso just-in-time quando for necessário.

- **Licenças de recurso de "Pagamento conforme o uso".** Você também pode obter licenças de outros recursos, como o Azure Active Directory B2C (Entre empresa e consumidor). O B2C pode ajudar você a fornecer soluções de gerenciamento de acesso e identidade para seus aplicativos voltados ao cliente. Para saber mais, confira a [Documentação sobre o Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre como associar uma assinatura do Azure com o Azure AD, confira [Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) e, para obter mais informações sobre como atribuir licenças aos usuários, confira [Como atribuir ou remover licenças do Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologia

Para entender melhor o Azure AD e sua documentação, recomendamos ler os termos a seguir.

|Termo ou conceito|DESCRIÇÃO|
|---------------|-----------|
|Identidade| Um item que possa ser autenticado. Uma identidade pode ser um usuário com um nome de usuário e senha. Identidades também incluem aplicativos ou outros servidores que podem exigir a autenticação por meio de certificados ou chaves secretas.|
|Conta| Uma identidade que tenha dados associados a ela. Você não pode ter uma conta sem uma identidade.|
|Conta do AD do Azure| Uma identidade criada no Azure AD ou por outro serviço de nuvem da Microsoft, como o Office 365. As identidades são armazenadas no Azure AD e podem ser acessadas pelas assinaturas do serviço de nuvem da organização. Às vezes, essa conta é chamada de conta corporativa ou de estudante.|
|Assinatura do Azure| Usada para pagar pelos serviços de nuvem do Azure. Você pode ter várias assinaturas, e elas estarão vinculadas a um cartão de crédito.|
|Locatário do Azure| Uma instância dedicada e confiável do Azure AD criada automaticamente quando sua organização se inscreve em uma assinatura do serviço de nuvem da Microsoft, como do Microsoft Azure, do Microsoft Intune ou do Office 365. Um locatário do Azure representa uma única organização.|
|Locatário único| Locatários do Azure que acessem outros serviços em um ambiente dedicado são considerados locatários únicos.|
|Multilocatário| Locatários do Azure que acessam outros serviços em um ambiente compartilhado em várias organizações são considerados multilocatários.|
|Diretório do AD do Azure|Cada locatário do Azure tem um diretório do Azure AD dedicado e confiável. O diretório do Azure AD inclui usuários, grupos e aplicativos do locatário e é usado para executar as funções de identidade e gerenciamento de acesso aos recursos de locatário.|
|Domínio personalizado|Todo diretório novo do Azure AD vem com um nome de domínio inicial, nomededomínio.onmicrosoft.com. Além do nome inicial, você também pode adicionar à lista os nomes de domínio de sua organização, que incluem os nomes que você usa para fazer negócios e que seus usuários usam para acessar recursos da organização. Adicionar nomes de domínio personalizados ajuda você a criar nomes de usuário com os quais seus usuários estejam familiarizados, como alain@contoso.com.|
|Administrador de conta|Essa função clássica de administrador de assinatura é, conceitualmente, o proprietário para cobrança de uma assinatura. Essa função tem acesso ao [Centro de Contas do Azure](https://account.azure.com/Subscriptions) e permite que você gerencie todas as assinaturas em uma conta. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções RBAC (controle de acesso baseado em função) do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador de serviços|Essa função clássica de administrador de assinatura permite que você gerencie todos os recursos do Azure, incluindo o acesso. Essa função tem o acesso equivalente ao de um usuário que recebe a função de Proprietário no escopo da assinatura. Para saber mais, confira [Funções clássicas de administrador da assinatura, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietário|Essa função ajuda você a gerenciar todos os recursos do Azure, incluindo o acesso. Essa função está inserida em um sistema de autorização mais recente, chamado de RBAC (controle de acesso baseado em função), que fornece gerenciamento de acesso refinado aos recursos do Azure. Para saber mais, confira [Funções clássicas de administrador da assinatura, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador Global do Azure AD|Essa função de administrador é automaticamente atribuída a quem criou o locatário do Azure AD. Os Administradores globais podem executar todas as funções administrativas do Azure AD e de todos serviços federados ao Azure AD, como o Exchange Online, o SharePoint Online e o Skype for Business Online. Você pode ter vários administradores globais, mas somente os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos usuários.<br><br>**Observação**<br>Essa função de administrador é chamada de Administrador Global no portal do Azure, mas tem o nome de **Administrador da empresa** na API do Microsoft Graph, na API do Graph do Azure AD e no PowerShell do Azure AD.<br><br>Para saber mais sobre as várias funções de administrador, confira [Permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta da Microsoft (também chamada de MSA)|Contas pessoais que fornecem acesso aos produtos da Microsoft e serviços de nuvem voltados ao consumidor, como o Outlook, o OneDrive, o Xbox LIVE ou o Office 365. Sua conta da Microsoft é criada e armazenada no sistema de contas de identidade de consumidor da Microsoft executado pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Quais recursos funcionam no Azure AD?

Depois de escolher sua licença do Azure AD, você obterá acesso a alguns ou a todos os seguintes recursos para sua organização:

|Categoria|DESCRIÇÃO|
|-------|-----------|
|Gerenciamento de aplicativos|Gerencie seus aplicativos de nuvem e locais usando o Proxy de Aplicativo, o logon único, o portal Meus aplicativos (também conhecido como Painel de acesso) e aplicativos SaaS (software como serviço). Para saber mais, confira [Como fornecer acesso remoto seguro a aplicativos locais](../manage-apps/application-proxy.md) e a [Documentação sobre gerenciamento de aplicativo](../manage-apps/index.yml).|
|Authentication|Gerencie a redefinição de senha por autoatendimento do Azure Active Directory, a Autenticação Multifator, a lista personalizada de senhas banidas e o bloqueio inteligente. Para saber mais, confira a [Documentação sobre a autenticação do Azure AD](../authentication/index.yml).|
|B2B (Entre empresas)|Gerencie usuários convidados e parceiros externos enquanto mantém o controle sobre seus próprios dados corporativos. Para saber mais, confira a [Documentação sobre o Azure Active Directory B2B](../b2b/index.yml).|
|B2C (Entre empresa e consumidor)|Personalize e controle como os usuários se inscrevem, entram e gerenciam seus perfis ao usar os aplicativos. Para saber mais, confira a [Documentação sobre o Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerencie o acesso a seus aplicativos de nuvem. Para saber mais, confira a [Documentação sobre Acesso condicional do Azure AD](../conditional-access/index.yml).|
|Azure Active Directory para desenvolvedores|Crie aplicativos que aceitam todas as identidades da Microsoft, obtenha tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para saber mais, confira a [Plataforma de identidade da Microsoft (Azure Active Directory para desenvolvedores)](../develop/index.yml).|
|Gerenciamento de dispositivo|Gerencie como os dispositivos de nuvem ou locais acessam seus dados corporativos. Para saber mais, confira a [Documentação sobre gerenciamento de dispositivo do Azure AD](../devices/index.yml).|
|Serviços do domínio|Adicione máquinas virtuais do Azure a um domínio sem usar controladores de domínio. Para saber mais, confira a [Documentação sobre o Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Usuários corporativos|Gerencie a atribuição de licenças, o acesso a aplicativos e configure representantes usando grupos e funções de administrador. Para saber mais, confira a [Documentação sobre gerenciamento de usuário do Azure Active Directory](../users-groups-roles/index.yml).|
|Identidade híbrida|Use o Azure Active Directory Connect e o Connect Health para fornecer uma identidade de usuário única para autenticação e autorização de todos os recursos, independentemente da localização (nuvem ou local). Para saber mais, confira a [Documentação sobre Identidade híbrida](../hybrid/index.yml).|
|Governança de identidade|Gerencie a identidade da organização por meio de controles de acesso de funcionário, parceiro de negócios, fornecedor, serviço e aplicativo. Você também pode executar revisões de acesso. Para saber mais, confira a [Documentação sobre governança de identidade do Azure AD](../governance/identity-governance-overview.md) e [Revisões de acesso do Azure AD](../governance/access-reviews-overview.md).|
|Identity Protection|Detecte possíveis vulnerabilidades que afetam as identidades da organização, configure políticas para responder a ações suspeitas e tome as devidas providências para resolvê-las. Para saber mais, veja [Azure AD Identity Protection](../identity-protection/index.yml).|
|Identidades gerenciadas dos recursos do Azure|Fornece as serviços do Azure uma identidade gerenciada automaticamente no Azure AD que pode autenticar qualquer serviço de autenticação com suporte do Azure AD, incluindo o Key Vault. Para saber mais, confira [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).|
|PIM (Privileged Identity Management)|Gerencie, controle e monitore o acesso em sua organização. Esse recurso inclui o acesso aos recursos no Azure AD e no Azure e outros Serviços do Microsoft Online, como o Office 365 ou o Intune. Para obter mais informações, confira [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitoramento|Obtenha insights sobre padrões de uso e segurança em seu ambiente. Para saber mais, confira [Relatórios e monitoramento do Azure Active Directory](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Próximas etapas

- [Inscrever-se no Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associar uma assinatura do Azure ao seu Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Acessar o Azure Active Directory e criar um novo locatário](active-directory-access-create-new-tenant.md)

- [Lista de verificação de implantação de recursos do Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)

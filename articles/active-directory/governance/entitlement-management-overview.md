---
title: O que é gerenciamento de direitos? – Azure AD
description: Obtenha uma visão geral sobre o gerenciamento de direitos do Azure Active Directory e saiba como você pode usá-lo para gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e externos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 11/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7efaaf9cbfdb13c80ce653c99d05da451c2b8968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547308"
---
# <a name="what-is-azure-ad-entitlement-management"></a>O que é o gerenciamento de direitos do Azure AD?

O gerenciamento de direitos do Azure Active Directory (Azure AD) é um recurso de [governança de identidade](identity-governance-overview.md) que permite que as organizações gerenciem o ciclo de vida de identidade e acesso em escala, automatizando fluxos de trabalho de solicitação de acesso, atribuições de acesso, revisões e expiração.

Os funcionários em organizações precisam acessar vários grupos, aplicativos e sites para executar seu trabalho. O gerenciamento desse acesso é desafiador, conforme os requisitos mudam: novos aplicativos são adicionados ou os usuários precisam de direitos de acesso adicionais.  Esse cenário fica mais complicado quando você colabora com organizações externas. Talvez você não saiba quem na outra organização precisa de acesso aos recursos da sua organização, e eles não saberão quais aplicativos, grupos ou sites sua organização está usando.

O gerenciamento de direitos do Azure AD pode ajudá-lo a gerenciar com mais eficiência o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e também para usuários de fora da sua organização que precisam de acesso a esses recursos.

## <a name="why-use-entitlement-management"></a>Por que usar o gerenciamento de direitos?

Ao gerenciar o acesso de funcionários a recursos, as organizações empresariais geralmente enfrentam desafios como:

- Os usuários podem não saber qual acesso eles devem ter e, mesmo se souberem, poderão ter dificuldade para localizar os indivíduos certos para aprovar esse acesso
- Depois que os usuários localizarem e receberem acesso a um recurso, eles poderão manter o acesso por mais tempo que o necessário para os fins empresariais

Esses problemas são ainda maiores para usuários que precisam de acesso de outra organização, como usuários externos que são de organizações da cadeia de fornecedores ou de outros parceiros empresariais. Por exemplo:

- Ninguém consegue conhecer todos os indivíduos específicos nos diretórios de outras organizações para poder convidá-los
- Mesmo que pudessem convidar esses usuários, ninguém nessa organização poderia se lembrar de gerenciar todo o acesso dos usuários de maneira consistente

O gerenciamento de direitos do Azure AD pode ajudar a resolver esses desafios.  Para saber mais sobre como os clientes têm usado o gerenciamento de direitos do Azure AD, leia o [estudo de caso da Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e o [estudo de caso da Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure).  Este vídeo fornece uma visão geral do gerenciamento de direitos e seu valor:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com o gerenciamento de direitos?

Aqui estão alguns recursos de gerenciamento de direitos:

- Delegar a não administradores a capacidade de criar pacotes de acesso. Esses pacotes de acesso contêm recursos que os usuários podem solicitar e os gerenciadores de pacotes de acesso delegados podem definir políticas com regras de quais usuários podem solicitar, quem deve aprovar o acesso e quando o acesso se expira.
- Selecionar as organizações conectadas cujos usuários podem solicitar acesso.  Quando um usuário que ainda não está em seu diretório solicita acesso e é aprovado, ele é automaticamente convidado para seu diretório e recebe acesso.  Quando o acesso expirar, se o usuário não tiver nenhuma outra atribuição de pacote de acesso, a conta B2B do seu diretório poderá ser automaticamente removida.

>[!NOTE]
>Se você estiver pronto para experimentar o Gerenciamento de direitos, poderá começar com nosso [tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md).

Você também pode ler os [cenários comuns](entitlement-management-scenarios.md) ou assistir a vídeos, incluindo

- [Como implantar o gerenciamento de direitos do Azure AD em sua organização](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Como monitorar e dimensionar o uso do gerenciamento de direitos do Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Como delegar no gerenciamento de direitos](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>O que são pacotes do acesso e quais recursos posso gerenciar com eles?

O gerenciamento de direitos introduz o conceito de *pacote de acesso* ao Azure AD. Um pacote de acesso é um pacote de todos os recursos com o acesso de que um usuário precisa para trabalhar em um projeto ou executar sua tarefa. Os pacotes de acesso são usados para controlar o acesso de seus funcionários internos e também dos usuários de fora da sua organização.

 Estes são os tipos de recursos para os quais você pode gerenciar o acesso de usuários com o gerenciamento de direitos:

- Associação de grupos de segurança do Azure AD
- Associação dos Grupos do Microsoft 365 e Teams
- Atribuição a aplicativos empresariais do Azure AD, incluindo aplicativos SaaS e aplicativos personalizados compatíveis com federação/logon único e/ou provisionamento
- Associação de sites do SharePoint Online

Você também pode controlar o acesso a outros recursos que dependem de grupos de segurança do Azure AD ou grupos do Microsoft 365.  Por exemplo:

- Você pode conceder aos usuários licenças para o Microsoft 365 usando um grupo de segurança do Azure AD em um pacote de acesso e configurando um [licenciamento baseado em grupo](../enterprise-users/licensing-groups-assign.md) para esse grupo.
- Você pode conceder aos usuários acesso para gerenciar recursos do Azure usando um grupo de segurança do Azure AD em um pacote de acesso e criando uma [atribuição de função do Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo.
- Você pode conceder aos usuários acesso para gerenciar funções do Azure AD usando grupos atribuíveis às funções do Azure AD em um pacote de acesso e [atribuindo uma função do Azure AD a esse grupo](../roles/groups-assign-role.md).

## <a name="how-do-i-control-who-gets-access"></a>Como faço para controlar quem obtém acesso?

Com um pacote de acesso, um administrador ou um gerenciador de pacotes de acesso delegado pode listar os recursos (grupos, aplicativos e sites) e as funções que os usuários precisam para esses recursos.

Os pacotes de acesso também incluem uma ou mais *políticas*. Uma política define as regras ou grades de proteção para atribuição ao pacote de acesso. Cada política pode ser usada para garantir que apenas os usuários apropriados sejam capazes de solicitar acesso, que há aprovadores para essa solicitação e que o acesso a esses recursos é limitado por tempo e expirará se não for renovado.

![Pacote de acesso e políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Em cada política, um administrador ou um gerenciador de pacotes de acesso define

- Os usuários já existentes (normalmente funcionários ou participantes já convidados) ou as organizações parceiras de usuários externos, que são qualificadas para solicitar acesso
- O processo de aprovação e os usuários que podem aprovar ou negar o acesso
- A duração da atribuição de acesso de um usuário, uma vez aprovada, antes que a atribuição expire

O diagrama a seguir mostra um exemplo dos diferentes elementos no gerenciamento de direitos. Ele mostra um catálogo com dois pacotes de acesso de exemplo.

- O **pacote de acesso 1** inclui um grupo como um recurso. O acesso é definido com uma política que permite que um conjunto de usuários no diretório solicite acesso.
- O **pacote de acesso 2** inclui um grupo, um aplicativo e um site do SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de usuários no diretório solicite acesso. A segunda política permite que os usuários em um diretório externo solicitem acesso.

![Visão geral do gerenciamento de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usar pacotes de acesso?

Os pacotes de acesso não substituem outros mecanismos para atribuição de acesso.  Eles são mais adequados em situações como:

- Os funcionários precisam de acesso limitado por tempo para uma determinada tarefa.  Por exemplo, você pode usar o licenciamento baseado em grupo e um grupo dinâmico para verificar se todos os funcionários têm uma caixa de correio do Exchange Online. Depois, use pacotes de acesso para situações em que os funcionários precisam de acesso adicional, como para ler recursos departamentais de outro departamento.
- O acesso precisa da aprovação do gerente de um funcionário ou de outros indivíduos designados.
- Os departamentos desejam gerenciar as próprias políticas de acesso para seus recursos sem envolvimento de TI.  
- Duas ou mais organizações estão colaborando em um projeto e, como resultado, vários usuários de uma organização precisarão ser incluídos por meio do B2B do Azure AD para acessar os recursos de outra organização.

## <a name="how-do-i-delegate-access"></a>Como faço para delegar acesso?

 Pacotes de acesso são definidos em contêineres chamados *catálogos*.  Você pode ter um catálogo para todos os seus pacotes de acesso ou pode designar indivíduos para criar e serem proprietários dos próprios catálogos. Um administrador pode adicionar recursos a qualquer catálogo, mas alguém que não seja administrador só pode adicionar a um catálogo os recursos de que é proprietário. Um proprietário de catálogo pode adicionar outros usuários como coproprietários de catálogo ou como gerenciadores de pacotes de acesso.  Esses cenários são descritos mais detalhadamente no artigo [delegação e funções no gerenciamento de direitos do Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Resumo da terminologia

Para entender melhor o gerenciamento de direitos e sua documentação, veja a lista de termos a seguir.

| Termo | Descrição |
| --- | --- |
| pacote de acesso | Um pacote de recursos de que uma equipe ou um projeto precisa e é regido por políticas. Um pacote de acesso sempre está contido em um catálogo. Você criaria um pacote de acesso para um cenário no qual os usuários precisassem solicitar acesso.  |
| solicitação de acesso | Uma solicitação para acessar os recursos em um pacote de acesso. Normalmente, uma solicitação passa por um fluxo de trabalho de aprovação.  Se aprovada, o usuário solicitante receberá uma atribuição de pacote de acesso. |
| atribuição | Uma atribuição de um pacote de acesso a um usuário garante que o usuário tenha todas as funções de recurso desse pacote de acesso.  As atribuições de pacote de acesso normalmente têm um limite de tempo para expirarem. |
| catálogo | Um contêiner de recursos relacionados e pacotes de acesso.  Os catálogos são usados para delegação, de modo que não administradores possam criar pacotes de acesso próprios. Os proprietários do catálogo podem adicionar recursos que eles têm a um catálogo. |
| criador de catálogos | Uma coleção de usuários que estão autorizados a criar catálogos.  Quando um usuário não administrador que está autorizado a ser um criador de catálogos cria um catálogo, ele se torna automaticamente o proprietário desse catálogo. |
| organização conectada | Um diretório ou domínio externo do Azure AD com o qual você tem uma relação. Os usuários de uma organização conectada podem ser especificados em uma política como tendo permissão para solicitar acesso. |
| policy | Um conjunto de regras que define o ciclo de vida do acesso, como a forma que os usuários obtêm acesso, quem pode aprovar e por quanto tempo os usuários têm acesso por meio de uma atribuição. Uma política está vinculada a um pacote de acesso. Por exemplo, um pacote de acesso pode ter duas políticas: uma para os funcionários solicitarem acesso e uma segunda para usuários externos solicitarem acesso. |
| recurso | Um ativo, como um grupo do Office, um grupo de segurança, um aplicativo ou um site do SharePoint Online, com uma função à qual um usuário pode ter permissões concedidas. |
| diretório de recursos | Um diretório que tem um ou mais recursos para serem compartilhados. |
| função de recurso | Uma coleção de permissões associadas a um recurso e definidas por ele. Um grupo tem duas funções: membro e proprietário. Os sites do SharePoint normalmente têm 3 funções, mas podem ter funções personalizadas adicionais. Os aplicativos podem ter funções personalizadas. |


## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como o Azure Alemanha e o Azure China 21Vianet, não estão disponíveis para uso no momento.

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você precisa ter?

Seu diretório deve ter pelo menos a quantidade de licenças Azure AD Premium P2 que você tem:

- Usuários membros que **podem** solicitar um pacote de acesso.
- Usuários membros que <u>solicitam</u> um pacote de acesso.
- Usuários membros que <u>aprovam solicitações</u> para um pacote de acesso.
- Usuários membros que <u>examinam as atribuições</u> de um pacote de acesso. 
- Usuários membros que têm uma <u>atribuição direta</u> a um pacote de acesso.

Para os usuários convidados, as necessidades de licenciamento dependerão do [modelo de licenciamento](../external-identities/external-identities-pricing.md) utilizado. No entanto, as atividades dos usuários convidados abaixo são consideradas como uso do Azure AD Premium P2:
- Usuários convidados que <u>solicitam</u> um pacote de acesso. 
- Usuários convidados que <u>aprovam solicitações</u> para um pacote de acesso.
- Usuários convidados que <u>examinam as atribuições</u> de um pacote de acesso.
- Usuários convidados que têm uma <u>atribuição direta</u> a um pacote de acesso. 

As licenças do Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Nenhuma licença é necessária para usuários com a função Administrador global que configura os catálogos iniciais, os pacotes de acesso e as políticas e delega tarefas administrativas a outros usuários.
- Nenhuma licença é necessária para os usuários a quem foram delegadas tarefas administrativas, como criador de catálogos, proprietário do catálogo e gerenciador de pacotes de acesso.
- Nenhuma licença é necessária para convidados que **podem** solicitar pacotes de acesso, mas **não** solicitam um pacote de acesso.

Para obter mais informações sobre licenças, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns exemplos de cenários de licença para ajudá-lo a determinar o número de licenças que você precisa ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um Administrador global no Banco Woodgrove cria catálogos iniciais e delega tarefas administrativas a seis outros usuários. Uma das políticas especifica que **Todos os funcionários** (2 mil funcionários) podem solicitar um conjunto específico de pacotes de acesso. 150 funcionários solicitam os pacotes de acesso. | 2 mil funcionários que **podem** solicitar os pacotes de acesso | 2\.000 |
| Um Administrador global no Banco Woodgrove cria catálogos iniciais e delega tarefas administrativas a seis outros usuários. Uma das políticas especifica que **Todos os funcionários** (2 mil funcionários) podem solicitar um conjunto específico de pacotes de acesso. Outra política especifica que alguns usuários que são **Usuários do parceiro Contoso** (convidados) podem solicitar os mesmos pacotes de acesso, sujeitos à aprovação. A Contoso tem 30 mil usuários. 150 funcionários solicitam os pacotes de acesso e 10.500 usuários da Contoso solicitam acesso. | 2 mil funcionários + 500 usuários convidados da Contoso que excedem a taxa de 1:5 (10.500 - (2.000 * 5)) | 2\.500 |

## <a name="next-steps"></a>Próximas etapas

- Se tiver interesse em usar o portal do Azure para gerenciar o acesso aos recursos, confira [Tutorial: Gerenciar acesso a recursos – portal do Azure](entitlement-management-access-package-first.md).
- Se tiver interesse em usar o Microsoft Graph para gerenciar o acesso aos recursos, confira [Tutorial: gerenciar o acesso a recursos – Microsoft Graph](/graph/tutorial-access-package-api?toc=/azure/active-directory/governance/toc.json&bc=/azure/active-directory/governance/breadcrumb/toc.json)
- [Cenários comuns](entitlement-management-scenarios.md)

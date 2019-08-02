---
title: O que é o gerenciamento de direitos do AD do Azure? (Visualização)-Azure Active Directory
description: Obtenha uma visão geral do gerenciamento de direitos Azure Active Directory e como você pode usá-lo para gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e externos.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 896cc495d6f369c034171833a9b90aa53e6477b6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234649"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>O que é o gerenciamento de direitos do AD do Azure? (Visualização)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os funcionários em organizações precisam acessar vários grupos, aplicativos e sites para executar seu trabalho. O gerenciamento desse acesso é desafiador. Na maioria dos casos, não há nenhuma lista organizada de todos os recursos de que um usuário precisa para um projeto. O gerente de projeto tem uma boa compreensão dos recursos necessários, dos indivíduos envolvidos e de quanto tempo o projeto durará. No entanto, o gerente de projeto normalmente não tem permissões para aprovar ou conceder acesso a outras pessoas. Esse cenário fica mais complicado quando você tenta trabalhar com indivíduos ou empresas externas.

O gerenciamento de direitos do Azure Active Directory (AD do Azure) pode ajudá-lo a gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e também usuários fora da sua organização.

## <a name="why-use-entitlement-management"></a>Por que usar o gerenciamento de direitos?

As organizações empresariais geralmente enfrentam desafios ao gerenciar o acesso a recursos como:

- Os usuários podem não saber qual acesso eles devem ter
- Os usuários podem ter dificuldade para localizar os indivíduos certos ou os recursos corretos
- Depois que os usuários localizarem e receberem acesso a um recurso, eles poderão manter o acesso mais longo do que o necessário para fins comerciais

Esses problemas são compostos por usuários que precisam de acesso de outro diretório, como usuários externos que são de organizações de cadeia de fornecedores ou outros parceiros comerciais. Por exemplo:

- As organizações podem não conhecer todos os indivíduos específicos de outros diretórios para poderem convidá-los
- Mesmo que as organizações possam convidar esses usuários, as organizações podem não se lembrar de gerenciar todo o acesso do usuário de forma consistente

O gerenciamento de direitos do Azure AD pode ajudar a resolver esses desafios.

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com o gerenciamento de direitos?

Aqui estão alguns recursos de gerenciamento de direitos:

- Criar pacotes de recursos relacionados que os usuários podem solicitar
- Definir regras para como solicitar recursos e quando o acesso expirar
- Regem o ciclo de vida do acesso para usuários internos e externos
- Delegar gerenciamento de recursos
- Designar aprovadores para aprovar solicitações
- Criar relatórios para acompanhar o histórico

Para obter uma visão geral de governança de identidade e gerenciamento de direitos, Assista ao vídeo a seguir da conferência do Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Quais recursos posso gerenciar?

Estes são os tipos de recursos para os quais você pode gerenciar o acesso com o gerenciamento de direitos:

- Grupos de segurança do Azure AD
- Grupos do Office 365
- Aplicativos empresariais do Azure AD, incluindo aplicativo SaaS e aplicativos integrados personalizados que dão suporte à Federação ou provisionamento
- Sites e conjuntos de sites do SharePoint Online

Você também pode controlar o acesso a outros recursos que dependem de grupos de segurança do Azure AD ou grupos do Office 365.  Por exemplo:

- Você pode conceder aos usuários licenças para o Microsoft Office 365 usando um grupo de segurança do Azure AD em um pacote do Access e Configurando o [Licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Você pode conceder aos usuários acesso para gerenciar recursos do Azure usando um grupo de segurança do Azure AD em um pacote do Access e criando uma [atribuição de função do Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="what-are-access-packages-and-policies"></a>O que são pacotes e políticas de acesso?

O gerenciamento de direitos apresenta o conceito de um *pacote de acesso*. Um pacote do Access é um grupo de todos os recursos que um usuário precisa para trabalhar em um projeto ou executar seu trabalho. Os recursos incluem acesso a grupos, aplicativos ou sites. Os pacotes do Access são usados para controlar o acesso de seus funcionários internos e também usuários fora da sua organização. Pacotes de acesso são definidos em contêineres chamados *catálogos*.

Pacotes de acesso também incluem uma ou mais *políticas*. Uma política define as regras ou guardrails para acessar um pacote de acesso. A habilitação de uma política impõe que somente os usuários certos tenham acesso concedido, aos recursos certos e à quantidade de tempo certa.

![Acessar o pacote e as políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Com um pacote de acesso e suas políticas, o Gerenciador de pacotes de acesso define:

- Recursos
- Funções que os usuários precisam para os recursos
- Usuários internos e externos que estão qualificados para solicitar acesso
- Processo de aprovação e os usuários que podem aprovar ou negar o acesso
- Duração do acesso do usuário

O diagrama a seguir mostra um exemplo dos diferentes elementos no gerenciamento de direitos. Ele mostra dois pacotes de acesso de exemplo.

- O **pacote do Access 1** inclui um único grupo como um recurso. O acesso é definido com uma política que permite que um conjunto de usuários no diretório solicite acesso.
- O **pacote de acesso 2** inclui um grupo, um aplicativo e um site do SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de usuários no diretório solicite acesso. A segunda política permite que os usuários em um diretório externo solicitem acesso.

![Visão geral do gerenciamento de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Usuários externos

Ao usar a experiência de convite [B2B (entre empresas) do Azure ad](../b2b/what-is-b2b.md) , você já deve conhecer os endereços de email dos usuários convidados externos que deseja colocar em seu diretório de recursos e trabalhar com o. Isso funciona muito bem quando você está trabalhando em um projeto de menor ou curto prazo e já conhece todos os participantes, mas isso é mais difícil de gerenciar se você tiver muitos usuários com os quais deseja trabalhar ou se os participantes mudarem ao longo do tempo.  Por exemplo, você pode estar trabalhando com outra organização e ter um ponto de contato com essa organização, mas com o passar do tempo, usuários adicionais dessa organização também precisarão de acesso.

Com o gerenciamento de direitos, você pode definir uma política que permite aos usuários de organizações que você especifica, que também estão usando o Azure AD, para poder solicitar um pacote de acesso. Você pode especificar se a aprovação é necessária e uma data de validade para o acesso. Se a aprovação for necessária, você também poderá designar como um Aprovador um ou mais usuários da organização externa que você convidou anteriormente, já que é provável que eles saibam quais usuários externos de sua organização precisam de acesso. Depois de configurar o pacote do Access, você pode enviar um link para o pacote do Access para sua pessoa de contato na organização externa. Esse contato pode compartilhar com outros usuários na organização externa e pode usar esse link para solicitar o pacote de acesso.  Os usuários dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando uma solicitação é aprovada, o gerenciamento de direitos provisionará o usuário com o acesso necessário, que pode incluir o convite do usuário se ele ainda não estiver em seu diretório. O Azure AD criará automaticamente uma conta B2B para eles.  Observe que um administrador pode ter limitado anteriormente quais organizações têm permissão para colaboração, definindo uma [lista de permissão ou negação B2B](../b2b/allow-deny-list.md) para permitir ou bloquear convites para outras organizações.  Se o usuário não for permitido pela lista de permissões ou bloqueios, eles não serão convidados.

Como você não deseja que o acesso do usuário externo seja feito por último, especifique uma data de validade na política, como 180 dias. Após 180 dias, se o acesso não for renovado, o gerenciamento de direitos removerá todo o acesso associado a esse pacote de acesso.  Se o usuário que foi convidado por meio do gerenciamento de direitos não tiver outras atribuições de pacote de acesso, quando perderem sua última atribuição, sua conta B2B será impedida de entrar por 30 dias e, subsequentemente, removida.  Isso impede a proliferação de contas desnecessárias.  

## <a name="terminology"></a>Terminologia

Para entender melhor o gerenciamento de direitos e sua documentação, você deve examinar os termos a seguir.

| Termo ou conceito | DESCRIÇÃO |
| --- | --- |
| gerenciamento de direitos | Um serviço que atribui, revoga e administra pacotes de acesso. |
| pacote de acesso | Uma coleção de permissões e políticas para recursos que os usuários podem solicitar. Um pacote do Access sempre está contido em um catálogo. |
| solicitação de acesso | Uma solicitação para acessar um pacote de acesso. Normalmente, uma solicitação passa por um fluxo de trabalho. |
| policy | Um conjunto de regras que define o ciclo de vida de acesso, como como os usuários obtêm acesso, quem pode aprovar e por quanto tempo os usuários têm acesso. As políticas de exemplo incluem acesso de funcionário e acesso externo. |
| catalog | Um contêiner de recursos relacionados e pacotes de acesso. |
| Catálogo geral | Um catálogo interno que está sempre disponível. Para adicionar recursos ao catálogo geral, o requer determinadas permissões. |
| resource | Um ativo ou serviço (como um grupo, um aplicativo ou um site) ao qual um usuário pode receber permissões. |
| Tipo de recurso | O tipo de recurso, que inclui grupos, aplicativos e sites do SharePoint Online. |
| função de recurso | Uma coleção de permissões associadas a um recurso. |
| diretório de recursos | Um diretório que tem um ou mais recursos a serem compartilhados. |
| usuários atribuídos | Uma atribuição de um pacote do Access a um usuário ou grupo. |
| enable | O processo de disponibilizar um pacote de acesso para os usuários solicitarem. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como Azure governamental, Azure Alemanha e Azure China 21Vianet, atualmente não estão disponíveis para uso nesta versão prévia.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)

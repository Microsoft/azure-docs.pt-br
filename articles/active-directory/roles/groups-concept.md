---
title: Usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory | Microsoft Docs
description: Visualize funções personalizadas do Azure AD para delegar o gerenciamento de identidades. Gerencie as atribuições de função do Azure no portal do Azure, PowerShell ou API do Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a204db8453ec8126e1aa9033e10f739a6f8a0d43
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095130"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)

O Azure Active Directory (AD do Azure) está introduzindo uma visualização pública na qual você pode atribuir um grupo de nuvem a funções internas do Azure AD. Com esse recurso, você pode usar grupos para conceder acesso de administrador no Azure AD com esforço mínimo de seus administradores de função globais e com privilégios.

Considere este exemplo: a contoso contratou pessoas entre geografias para gerenciar e redefinir senhas para funcionários em sua organização do Azure AD. Em vez de solicitar que um administrador de função com privilégios ou administrador global atribua a função de administrador de assistência técnica a cada pessoa individualmente, ele pode criar um grupo de Contoso_Helpdesk_Administrators e atribuí-lo à função. Quando as pessoas ingressam no grupo, elas recebem a função indiretamente. O fluxo de trabalho de governança existente pode cuidar do processo de aprovação e da auditoria da associação do grupo para garantir que apenas usuários legítimos sejam membros do grupo e, portanto, sejam atribuídos à função de administrador de assistência técnica.

## <a name="how-this-feature-works"></a>Como esse recurso funciona

Crie um novo Microsoft 365 ou grupo de segurança com a propriedade ' isAssignableToRole ' definida como ' true '. Você também pode habilitar essa propriedade ao criar um grupo no portal do Azure ativando **as funções do Azure AD que podem ser atribuídas ao grupo**. De qualquer forma, você pode atribuir o grupo a uma ou mais funções do Azure AD da mesma maneira que atribui funções aos usuários. Um máximo de 250 grupos de atribuição de função pode ser criado em uma única organização do AD do Azure (locatário).

Se você não quiser que os membros do grupo tenham acesso à função, você poderá usar Azure AD Privileged Identity Management. Atribua um grupo como um membro qualificado de uma função do Azure AD. Em seguida, cada membro do grupo é elegível para que sua atribuição seja ativada para a função à qual o grupo está atribuído. Em seguida, eles podem ativar sua atribuição de função por uma duração fixa.

> [!Note]
> Você deve estar na versão atualizada do Privileged Identity Management para poder atribuir um grupo à função do Azure AD por meio do PIM. Você pode estar em uma versão mais antiga do PIM porque sua organização do Azure AD aproveita a API de Privileged Identity Management. Entre em contato com o alias pim_preview@microsoft.com para mover sua organização e atualizar sua API. Saiba mais em [funções e recursos do Azure AD no PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Por que impõem a criação de um grupo especial para atribuí-lo a uma função

Se um grupo for atribuído a uma função, qualquer administrador de ti que possa gerenciar a associação de grupo também poderá gerenciar indiretamente a associação da função. Por exemplo, suponha que um grupo Contoso_User_Administrators seja atribuído à função de administrador da conta de usuário. Um administrador do Exchange que pode modificar a associação de grupo pode se adicionar ao grupo de Contoso_User_Administrators e, dessa forma, se torna um administrador de conta de usuário. Como você pode ver, um administrador pode elevar seus privilégios de uma forma que você não pretendia.

O Azure AD permite que você proteja um grupo atribuído a uma função usando uma nova propriedade chamada isAssignableToRole para grupos. Somente os grupos de nuvem que tinham a propriedade isAssignableToRole definida como ' true ' no momento da criação podem ser atribuídos a uma função. Essa propriedade é imutável; Depois que um grupo é criado com essa propriedade definida como ' true ', ele não pode ser alterado. Não é possível definir a propriedade em um grupo existente.
Projetamos como os grupos são atribuídos às funções para evitar que o tipo de possível violação ocorra:

- Somente administradores globais e administradores de função com privilégios podem criar um grupo de função atribuível (com a propriedade "isAssignableToRole" habilitada).
- Não pode ser um grupo dinâmico do Azure AD; ou seja, ele deve ter um tipo de associação "atribuído". A população automatizada de grupos dinâmicos pode levar a uma conta indesejada adicionada ao grupo e, portanto, atribuída à função.
- Por padrão, somente administradores globais e administradores de função com privilégios podem gerenciar a associação de um grupo de função atribuível, mas você pode delegar o gerenciamento de grupos de função atribuível adicionando proprietários de grupo.
- Para evitar a elevação de privilégio, as credenciais de membros e proprietários de um grupo de função atribuível podem ser alteradas somente por um administrador de autenticação privilegiada ou um administrador global.
- Sem aninhamento. Um grupo não pode ser adicionado como membro de um grupo de função atribuível.

## <a name="limitations"></a>Limitações

Os cenários a seguir não têm suporte no momento:  

- Atribuir grupos locais a funções do Azure AD (interno ou personalizado)

## <a name="known-issues"></a>Problemas conhecidos

- O recurso **habilitar a distribuição preparada para entrada de usuário gerenciado** não dá suporte à atribuição por meio de grupo.
- *Somente clientes licenciados do Azure ad P2*: não atribua um grupo como ativo a uma função por meio do Azure AD e do PRIVILEGED Identity Management (PIM). Especificamente, não atribua uma função a um grupo de função atribuível quando ela estiver sendo criada *e* atribua uma função ao grupo usando o PIM posteriormente. Isso resultará em problemas em que os usuários não podem ver suas atribuições de função ativas no PIM, bem como a incapacidade de remover essa atribuição de PIM. As atribuições qualificadas não são afetadas neste cenário. Se você tentar fazer essa atribuição, poderá ver um comportamento inesperado, como:
  - A hora de término da atribuição de função pode ser exibida incorretamente.
  - No portal do PIM, **minhas funções** podem mostrar apenas uma atribuição de função, independentemente de quantos métodos são concedidos pela atribuição (por meio de um ou mais grupos e diretamente).
- *Somente clientes licenciados do Azure ad P2* Mesmo após a exclusão do grupo, ele ainda mostra um membro qualificado da função na interface do usuário do PIM. Funcionalmente, não há problema; é apenas um problema de cache na portal do Azure.  
- Use o novo [centro de administração do Exchange](https://admin.exchange.microsoft.com/) para atribuições de função por meio da Associação de grupo. O antigo centro de administração do Exchange ainda não dá suporte a esse recurso. Os cmdlets do PowerShell do Exchange funcionarão conforme o esperado.
- O portal da proteção de informações do Azure (o portal clássico) não reconhece a associação de função via grupo ainda. Você pode [migrar para a plataforma de rotulação de sensibilidade unificada](/azure/information-protection/configure-policy-migrate-labels) e, em seguida, usar o centro de conformidade do Office 365 Security & para usar atribuições de grupo para gerenciar funções.

Estamos corrigindo esses problemas.

## <a name="required-license-plan"></a>Plano de licença necessária

O uso desse recurso exige que você tenha uma licença do Azure AD Premium P1 disponível em sua organização do Azure AD. Para usar também Privileged Identity Management para a ativação de função just-in-time, é necessário ter uma licença do Azure AD Premium P2 disponível. Para encontrar a licença certa para seus requisitos, consulte comparando [recursos disponíveis em geral dos planos gratuito e Premium](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Próximas etapas

- [Criar um grupo ao qual funções podem ser atribuídas](groups-create-eligible.md)
- [Atribuir uma função a um grupo de função atribuível](groups-assign-role.md)

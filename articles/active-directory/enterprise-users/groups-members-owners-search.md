---
title: Pesquisar e filtrar grupos de membros e proprietários (visualização)-Azure Active Directory | Microsoft Docs
description: Pesquisar e filtrar grupos de membros e proprietários no portal do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547365"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Pesquisar grupos e Membros (versão prévia) no Azure Active Directory

Este artigo mostra como Pesquisar Membros e proprietários de um grupo e como usar filtros de pesquisa como parte da versão prévia de aperfeiçoamento dos grupos no portal do Azure Active Directory (AD do Azure). Há vários aprimoramentos nas experiências de grupos para ajudá-lo a gerenciar seus grupos, incluindo membros e proprietários, com rapidez e facilidade. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações nesta visualização incluem:

- Novos grupos de recursos de pesquisa, como a pesquisa de Subcadeia em nomes de grupo
- Novas opções de filtragem e classificação em listas de membros e proprietários
- Novos recursos de pesquisa para listas de membros e proprietários
- Contagens de grupos mais precisas para grupos grandes

## <a name="enabling-and-managing-the-preview"></a>Habilitando e gerenciando a visualização

Tornamos fácil ingressar na versão prévia:

  1. Entre no [portal do AD do Azure](https://portal.azure.com)e selecione **grupos**.
  2. Na página grupos – todos os grupos, selecione a faixa na parte superior da página para ingressar na versão prévia.

Você também pode conferir os recursos e melhorias mais recentes selecionando **informações de visualização** na página **todos os grupos** . Depois de ingressar na versão prévia, você poderá ver a marca de visualização em todas as páginas de grupos que têm melhorias e que fazem parte da versão prévia. Nem todas as páginas de grupos foram atualizadas como parte desta visualização.

Se você tiver problemas, poderá voltar à experiência herdada selecionando a faixa na parte superior da página **todos os grupos** . Agradecemos seus comentários para que possamos melhorar nossa experiência.

## <a name="group-search-and-sorting"></a>Pesquisa e classificação de grupo

A pesquisa da lista de grupos foi aprimorada, de modo que, quando você pode inserir uma cadeia de caracteres de pesquisa, a pesquisa executa automaticamente uma `startswith` pesquisa de e subcadeia de caracteres na lista de nomes de grupos. A pesquisa de subcadeia de caracteres é executada somente em palavras inteiras e não inclui caracteres especiais. A pesquisa de subcadeia de caracteres diferencia maiúsculas de minúsculas.

![novas pesquisas de subcadeias na página todos os grupos](./media/groups-members-owners-search/groups-search-preview.png)

Por exemplo, uma pesquisa por "política" agora retornará "política de MDM – oeste" e "grupo de políticas". Um grupo chamado "New_policy" não será retornado.

- Você também pode executar a mesma pesquisa em listas de associação de grupo.
- Agora você pode classificar a lista de grupos por nome usando as setas à direita do título de coluna de nome para classificar a lista em ordem crescente ou decrescente.

## <a name="group-member-search-and-filtering"></a>Pesquisa e filtragem de membros do grupo

### <a name="search-group-member-and-owner-lists"></a>Pesquisar membros do grupo e listas de proprietários

Agora você pode pesquisar os membros de um grupo específico por nome e executar a mesma pesquisa na lista de proprietários do grupo também. Na nova experiência, se você inserir uma cadeia de caracteres na caixa de pesquisa, uma pesquisa StartsWith será executada automaticamente. Por exemplo, uma pesquisa por "Scott" retornará Scott Wilkinson.

![Nova subcadeia de pesquisa nas listas membros do grupo e proprietários](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtrar Membros e lista de proprietários

Além da pesquisa, agora você pode filtrar as listas membro e proprietário por tipo de usuário. Essas são as informações encontradas na coluna tipo de usuário da lista. Portanto, você pode filtrar a lista por membros e convidados para determinar se há algum convidado no grupo.

### <a name="view-and-manage-membership"></a>Exibir e gerenciar associação

Além de exibir os membros diretos de um grupo específico, agora você pode exibir a lista de todos os membros do grupo dentro da página Membros. A lista de membros inclui todos os membros exclusivos do grupo, incluindo quaisquer membros transitivos.

Você também pode pesquisar e filtrar a lista de membros diretos e a lista todos os membros individualmente. A filtragem da lista todos os membros não afeta os filtros que são aplicados na lista de membros diretos.

## <a name="improved-group-member-counts"></a>Contagens de membros do grupo aprimoradas

Melhoramos a página de **visão geral** do grupo para fornecer contagens de membros do grupo para grupos de todos os tamanhos. Você pode ver as contagens de membros mesmo para grupos com mais de 1.000 membros. Agora você pode ver o número total de membros diretos para um grupo e a contagem total de associações (todos os membros exclusivos do grupo, incluindo membros transitivos) na página **visão geral** .

![Maior precisão nas contagens de associação de grupo](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem informações adicionais sobre como trabalhar com grupos no Azure AD.

- [Exibir grupos e membros](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerenciar associação ao grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-create-rule.md)
- [Editar as configurações de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar acesso a recursos usando grupos](../fundamentals/active-directory-manage-groups.md)
- [Gerenciar acesso a aplicativos SaaS usando grupos](groups-saasapps.md)
- [Gerenciar grupos usando comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Adicionar uma assinatura do Azure ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)

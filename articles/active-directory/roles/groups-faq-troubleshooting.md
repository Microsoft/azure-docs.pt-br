---
title: Perguntas frequentes sobre solução de problemas de funções atribuídas a grupos de nuvem-Azure Active Directory | Microsoft Docs
description: Aprenda algumas perguntas comuns e dicas de solução de problemas para atribuir funções a grupos no Azure Active Directory.
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
ms.openlocfilehash: 0ff906654dc91c7b1980292efd2a737503e684e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742906"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Solução de problemas de funções atribuídas a grupos de nuvem

Aqui estão algumas perguntas comuns e dicas de solução de problemas para atribuir funções a grupos no Azure Active Directory (AD do Azure).

**P:** Sou administrador de grupos, mas não consigo ver que as **funções do Azure ad podem ser atribuídas à opção de grupo** .

**R:** Somente administradores de função com privilégios ou administradores globais podem criar um grupo qualificado para atribuição de função. Somente os usuários nessas funções veem esse controle.

**P:** Quem pode modificar a associação de grupos atribuídos às funções do Azure AD?

**R:** Por padrão, somente administrador de função com privilégios e administrador global gerenciam a associação de um grupo de função atribuível, mas você pode delegar o gerenciamento de grupos de função atribuível adicionando proprietários de grupo.

**P**: sou administrador de assistência técnica em minha organização, mas não consigo atualizar a senha de um usuário que é um leitor de diretório. Por que isso acontece?

**R**: o usuário pode ter chegado ao leitor de diretório por meio de um grupo de função atribuível. Todos os membros e proprietários de um grupo de funções atribuíveis são protegidos. Somente usuários no administrador de autenticação privilegiada ou funções de administrador global podem redefinir credenciais para um usuário protegido.

**P:** Não é possível atualizar a senha de um usuário. Eles não têm nenhuma função com privilégios mais altos atribuída. Por que isso está acontecendo?

**R:** O usuário pode ser um proprietário de um grupo de função atribuível. Protegemos os proprietários de grupos de função atribuíveis para evitar a elevação de privilégio. Um exemplo pode ser se um grupo Contoso_Security_Admins for atribuído à função de administrador de segurança, onde Bob é o proprietário do grupo e Alice é administrador de senha na organização. Se essa proteção não estivesse presente, Alice poderia redefinir as credenciais de Bob e assumir sua identidade. Depois disso, Alice poderia adicionar a si mesmo ou qualquer pessoa ao grupo Contoso_Security_Admins grupo para se tornar um administrador de segurança na organização. Para descobrir se um usuário é um proprietário de grupo, obtenha a lista de objetos de propriedade desse usuário e veja se algum dos grupos tem isAssignableToRole definido como true. Em caso afirmativo, esse usuário será protegido e o comportamento será por design. Consulte estas documentações para obter objetos de propriedade:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [Listar ownedObjects](/graph/api/user-list-ownedobjects?tabs=http)

**P:** Posso criar uma revisão de acesso em grupos que podem ser atribuídos às funções do Azure AD (especificamente, grupos com a propriedade isAssignableToRole definida como true)?  

**R:** Sim, você pode. Se você estiver na versão mais recente da revisão de acesso, os revisores serão direcionados para o meu acesso por padrão e somente os administradores globais poderão criar revisões de acesso em grupos de função atribuíveis. No entanto, se você estiver na versão mais antiga da revisão de acesso, os revisores serão direcionados para o painel de acesso por padrão, e os administradores globais e o administrador de usuários poderão criar revisões de acesso em grupos de função atribuíveis. A nova experiência será distribuída para todos os clientes em 28 de julho de 2020, mas se você quiser atualizar mais cedo, faça uma solicitação para as [revisões de acesso do Azure ad – experiência de revisor atualizado em minha inscrição de acesso](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**P:** Posso criar um pacote de acesso e colocar grupos que possam ser atribuídos às funções do Azure AD nele?

**R:** Sim, você pode. O administrador global e o administrador de usuários têm a capacidade de colocar qualquer grupo em um pacote de acesso. Nada muda para o administrador global, mas há uma ligeira alteração nas permissões da função de administrador de usuários. Para colocar um grupo de função atribuível em um pacote do Access, você deve ser um administrador do usuário e também o proprietário do grupo de funções de atribuição. Veja a tabela completa que mostra quem pode criar o pacote de acesso no gerenciamento de licenças corporativas:

Função do diretório do Azure Active Directory | Função de gerenciamento de direitos | Pode adicionar grupo de segurança\* | Pode adicionar Microsoft 365 grupo\* | Pode adicionar aplicativo | Pode adicionar site do SharePoint Online
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Administrador global | N/D | ✔️ | ✔️ | ✔️  | ✔️
Administrador de usuários  | N/D  | ✔️  | ✔️  | ✔️
Administrador do Intune | Proprietário do catálogo | ✔️  | ✔️  | &nbsp;  | &nbsp;
Administradores do Exchange  | Proprietário do catálogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrador de serviços de equipes | Proprietário do catálogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrador do SharePoint | Proprietário do catálogo | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Administrador de aplicativos | Proprietário do catálogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Administrador de aplicativos de nuvem | Proprietário do catálogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Usuário | Proprietário do catálogo | Somente se o proprietário do grupo | Somente se o proprietário do grupo | Somente se o proprietário do aplicativo  | &nbsp;

\*O grupo não pode ser atribuído à função; ou seja, isAssignableToRole = false. Se um grupo for de função atribuível, a pessoa que criar o pacote de acesso também deverá ser proprietária do grupo de funções de atribuição.

**P:** Não é possível localizar a opção "remover atribuição" em "funções atribuídas". Como fazer excluir a atribuição de função a um usuário?

**R:** Essa resposta é aplicável somente a organizações P1 Azure AD Premium.

1. Entre no [portal do Azure](https://portal.azure.com) e abra **Azure Active Directory**.
1. Selecione usuários e abra um perfil de usuário.
1. Selecione **funções atribuídas**.
1. Selecione o ícone de engrenagem. Um painel é aberto e pode fornecer essas informações. Há um botão "remover" ao lado das atribuições diretas. Para remover a atribuição de função indireta, remova o usuário do grupo ao qual a função foi atribuída.

**P:** Como fazer ver todos os grupos que podem ser atribuídas por função?

**R:** Siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e abra **Azure Active Directory**.
1. Selecione **grupos**  >  **todos os grupos**.
1. Selecione **adicionar filtros**.
1. Filtrar para **função atribuível**.

**P:** Como fazer saber qual função está atribuída a uma entidade de segurança direta e indiretamente?

**R:** Siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e abra **Azure Active Directory**.
1. Selecione usuários e abra um perfil de usuário.
1. Selecione **funções atribuídas** e, em seguida:

    - Em Azure AD Premium as organizações licenciadas P1: selecione o ícone de engrenagem. Um painel é aberto e pode fornecer essas informações.
    - Em organizações licenciadas Azure AD Premium P2: você encontrará informações de licença diretas e herdadas na coluna **Associação** .

**P:** Por que impõem a criação de um novo grupo de nuvem para atribuí-lo à função?  

**R:** Se você atribuir um grupo existente a uma função, o proprietário do grupo existente poderá adicionar outros membros a esse grupo sem que os novos membros perceberem que terão a função. Como os grupos de função atribuíveis são poderosos, estamos colocando muitas restrições para protegê-los. Você não quer alterações no grupo que seriam surpreendentes para a pessoa que está gerenciando o grupo.

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Criar um grupo ao qual funções podem ser atribuídas](groups-create-eligible.md)

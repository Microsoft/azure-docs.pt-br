---
title: Alterar planos de licença para usuários e grupos-Azure AD | Microsoft Docs
description: Como migrar usuários dentro de um grupo para diferentes planos de serviço usando o licenciamento de grupo no Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546532"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Alterar as atribuições de licença para um usuário ou grupo no Azure Active Directory

Este artigo descreve como mover usuários e grupos entre os planos de licença de serviço no Azure Active Directory (AD do Azure). A abordagem do objetivo do Azure AD é garantir que não haja perda de serviço ou dados durante a alteração da licença. Os usuários devem alternar entre os serviços diretamente. As etapas de atribuição do plano de licença neste artigo descrevem a alteração de um usuário ou grupo no Office 365 E1 para o Office 365 E3, mas as etapas se aplicam a todos os planos de licença. Quando você atualiza as atribuições de licença para um usuário ou grupo, as remoções de atribuição de licença e as novas atribuições são feitas simultaneamente para que os usuários não percam o acesso aos seus serviços durante as alterações de licença ou consulte conflitos de licença entre planos.

## <a name="before-you-begin"></a>Antes de começar

Antes de atualizar as atribuições de licença, é importante verificar se certas suposições são verdadeiras para todos os usuários ou grupos a serem atualizados. Se as suposições não forem verdadeiras para todos os usuários em um grupo, a migração poderá falhar para alguns. Como resultado, alguns dos usuários poderão perder o acesso a serviços ou dados. Verifique se:

- Os usuários têm o plano de licença atual (nesse caso, o Office 365 E1) que é atribuído a um grupo e herdado pelo usuário e não é atribuído diretamente.

- Você tem licenças suficientes disponíveis para o plano de licença que está atribuindo. Se você não tiver licenças suficientes, talvez alguns usuários não sejam atribuídos ao novo plano de licença. É possível verificar o número de licenças disponíveis.

- Os usuários não têm outras licenças de serviço atribuídas que podem entrar em conflito com a licença desejada ou impedir a remoção da licença atual. Por exemplo, uma licença de um serviço como o Workplace Analytics ou o Project online que tem uma dependência de outros serviços.

- Se você gerenciar grupos locais e sincronizá-los no Azure AD via Azure AD Connect, você adicionará ou removerá usuários usando seu sistema local. Pode levar algum tempo para que as alterações sejam sincronizadas com o Azure AD para serem selecionadas por licenciamento de grupo.

- Se você estiver usando associações de grupo dinâmico do Azure AD, adicione ou remova usuários alterando seus atributos, mas o processo de atualização para atribuições de licença permanece o mesmo.

## <a name="change-user-license-assignments"></a>Alterar atribuições de licença de usuário

Na página **Atualizar atribuições de licença** , se você vir que algumas caixas de seleção estão indisponíveis, indicará serviços que não podem ser alterados porque são herdados de uma licença de grupo.

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de licenças na sua organização do Azure AD.
1. Selecione **Azure Active Directory**  >  **usuários** e, em seguida, abra a página de **perfil** de um usuário.
1. Selecione **Licenças**.
1. Selecione **atribuições** para editar a atribuição de licença para o usuário ou grupo. A página **atribuições** é onde você pode resolver conflitos de atribuição de licença.
1. Marque a caixa de seleção do Office 365 E3 e verifique se, no mínimo, todos os serviços E1 atribuídos ao usuário estão selecionados.
1. Desmarque a caixa de seleção do Office 365 E1.

    ![página de atribuições de licença para um usuário mostrando o Office 365 E1 limpo e o Office 365 E3 selecionado](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecione **Salvar**.

O Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para fornecer continuidade de serviço.

## <a name="change-group-license-assignments"></a>Alterar atribuições de licença de grupo

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de licenças na sua organização do Azure AD.
1. Selecione **Azure Active Directory**  >  **grupos** de Azure Active Directory e, em seguida, abra a página **visão geral** de um grupo.
1. Selecione **Licenças**.
1. Selecione o comando **atribuições** para editar a atribuição de licença para o usuário ou grupo.
1. Marque a caixa de seleção do Office 365 E3. Para manter a continuidade do serviço, certifique-se de selecionar todos os serviços E1 que já estão atribuídos ao usuário.
1. Desmarque a caixa de seleção do Office 365 E1.

    ![Selecione o comando atribuições em uma página licenças de usuário ou grupo](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecione **Salvar**.

Para fornecer continuidade de serviço, o Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para todos os usuários no grupo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários de gerenciamento de licenças por meio de grupos nos seguintes artigos:

- [Atribuição de licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar usuários individuais licenciados para o licenciamento de grupo no Azure Active Directory](licensing-groups-migrate-users.md)
- [Cenários adicionais de licenciamento de grupo de Azure Active Directory](licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento de grupo no Azure Active Directory](licensing-ps-examples.md)

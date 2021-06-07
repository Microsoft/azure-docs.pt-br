---
title: Início rápido da política de nomenclatura de grupo – Azure Active Directory | Microsoft Docs
description: Explica como adicionar novos usuários no Azure Active Directory ou excluir usuários existentes dele
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11eec374d9f465dc68fc0d17fafc9bd3aa93b27e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056922"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início Rápido: política de nomenclatura para grupos no Azure Active Directory

Neste início rápido, você vai configurar a política de nomenclatura na sua organização do Azure AD (Azure Active Directory) para grupos do Microsoft 365 criados pelo usuário, para ajudar você a classificar e pesquisar os grupos da sua organização. Por exemplo, você poderia usar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajude a categorizar grupos no catálogo de endereços.
* Bloqueie o uso de palavras específicas em nomes de grupo e alias.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Configurar a política de nomenclatura de grupo no portal do Azure

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta de administrador.
1. Para abrir a página da política de nomenclatura, escolha **Grupos** e **Política de nomenclatura**.

    ![Abrir a página da política de nomenclatura no centro de administração](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Exibir ou editar a política de nomenclatura de prefixo/sufixo

1. Na página **Política de nomenclatura**, escolha **Política de nomenclatura de grupos**.
1. Para exibir ou editar individualmente as políticas de nomenclatura de prefixo ou sufixo atuais, escolha os atributos ou cadeias de caracteres que você deseja aplicar como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo da lista, escolha o prefixo ou sufixo e, em seguida, escolha **Excluir**. É possível excluir vários itens ao mesmo tempo.
1. Escolha **Salvar** para que as alterações da política entrem em vigor.

### <a name="view-or-edit-the-custom-blocked-words"></a>Exibir ou editar palavras bloqueadas personalizadas

1. Na página **Política de nomenclatura**, escolha **Palavras bloqueadas**.

    ![Editar e carregar uma lista de palavras bloqueadas da política de nomenclatura](./media/groups-quickstart-naming-policy/blockedwords.png)

1. Para exibir ou editar a lista atual de palavras bloqueadas personalizadas, escolha **Baixar**.
1. Carregue a nova lista de palavras bloqueadas personalizadas escolhendo o ícone de arquivo.
1. Escolha **Salvar** para que as alterações da política entrem em vigor.

É isso. Você definiu sua política de nomenclatura e adicionou suas palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remover a política de nomenclatura usando o portal do Azure

1. Na página **Política de nomenclatura**, escolha **Excluir Política**.
1. Depois de confirmar a exclusão, a política de nomenclatura será removida, incluindo toda a política de nomenclatura de prefixo-sufixo e palavras bloqueadas personalizadas.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a definir a política de nomenclatura para uma organização do Azure AD no portal do Azure.

Avance para o próximo artigo para ver mais detalhes, incluindo os cmdlets do PowerShell da política de nomenclatura, restrições técnicas, como adicionar uma lista de palavras bloqueadas personalizadas e as experiências do usuário final nos aplicativos do Microsoft 365.
> [!div class="nextstepaction"]
> [PowerShell da política de nomenclatura](groups-naming-policy.md)
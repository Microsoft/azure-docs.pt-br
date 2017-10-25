---
title: Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 1345f77df1945d3fa5bc7adc185ee5e6b6c0cc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory
É fácil alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para fazer essas alterações, além de ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Como alterar o nome ou logotipo de um aplicativo empresarial?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
3. Na folha **Azure Active Directory – *nomedodiretório*** (ou seja, a folha do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. Na folha **Aplicativos empresariais**, escolha **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos** , escolha um aplicativo.
6. Na folha ***nomedoaplicativo*** (ou seja, a folha com o nome do aplicativo escolhido no título), escolha **Propriedades**.

    ![Seleção do comando de propriedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. Na folha ***nomedoaplicativo*** **- Propriedades**, procure um arquivo para usar como um novo logotipo ou edite o nome do aplicativo, ou ambos.

    ![Alteração do logotipo do aplicativo ou do comando nameproperties](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)

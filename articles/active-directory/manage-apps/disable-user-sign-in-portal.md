---
title: Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7256791c0b6bfbc72a26f6093cdd3c39410f702f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807598"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory

É fácil desabilitar um aplicativo empresarial para que nenhum usuário possa entrar no Azure Active Directory (Azure AD). Você precisará das permissões apropriadas para gerenciar o aplicativo empresarial. E, então, você deve ser um administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilitar entradas de usuário?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
1. Sobre o **Azure Active Directory** -  ***directoryname*** painel (ou seja, o Azure AD para o diretório que você está gerenciando), selecione **aplicativos empresariais**.
1. Sobre o **aplicativos empresariais – todos os aplicativos** painel, você ver uma lista de aplicativos você pode gerenciar. Selecione um aplicativo.
1. No painel ***nomedoaplicativo*** (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **Propriedades**.
1. No painel ***nomedoaplicativo*** -  **Propriedades**, selecione **Não** para **Habilitar para os usuários entrarem?** .
1. Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas

* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)

---
title: Visualizar aplicativos de locatário – Azure Active Directory | Microsoft Docs
description: Use o portal do Azure para exibir os aplicativos em seu locatário do Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764c7c048dd0398b495ee31421a8e2bc9f8169ee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477191"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Exibir seus aplicativos de locatário do Azure Active Directory

Este início rápido usa o portal do Azure para exibir os aplicativos em seu locatário do Azure AD (Azure Active Directory).

## <a name="before-you-begin"></a>Antes de começar

Para ver os resultados, você precisa ter pelo menos um aplicativo em seu locatário do Azure AD. Para adicionar um aplicativo, consulte o início rápido [Adicionar um aplicativo](add-application-portal.md).

Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure AD, administrador do aplicativo de nuvem ou administrador do aplicativo.

## <a name="find-the-list-of-tenant-applications"></a>Encontrar a lista de aplicativos de locatário

Seus aplicativos de locatário do Azure AD ficam visíveis na seção **Aplicativos empresariais** do portal do Azure.

Para localizar seus aplicativos de locatário:

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.
1. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.
1. No menu suspenso **Tipo de Aplicativo**, selecione **Todos os Aplicativos** e escolha **Aplicar**. Uma amostra aleatória de seus aplicativos de locatário é exibida.
1. Para exibir mais aplicativos, selecione **Carregar mais** na parte inferior da lista. Dependendo do número de aplicativos em seu locatário, poderá ser mais fácil [pesquisar determinado aplicativo](#search-for-a-tenant-application) em vez de percorrer a lista.

## <a name="select-viewing-options"></a>Selecionar opções de exibição

Selecionar as opções de acordo com o que você está procurando.

1. É possível exibir os aplicativos por **Tipo de Aplicativo**, **Status do Aplicativo** e **Visibilidade do Aplicativo**.
1. Em **Tipo de Aplicativo**, escolha uma destas opções:

    - **Aplicativos Empresariais** mostra os aplicativos que não são da Microsoft.
    - **Aplicativos da Microsoft** mostra aplicativos da Microsoft.
    - **Todos os aplicativos** mostra aplicativos da Microsoft e de outros.

1. Em **Status do Aplicativo**, escolha **Qualquer um**, **Desabilitado** ou **Habilitado**. A opção **Qualquer um** inclui aplicativos habilitados e desabilitados.
1. Em **Visibilidade do Aplicativo**, escolha **Qualquer um** ou **Oculto**. A opção **Oculto** mostra os aplicativos que estão no locatário, mas não estão visíveis para os usuários.
1. Depois de escolher as opções desejadas, selecione **Aplicar**.

## <a name="search-for-a-tenant-application"></a>Pesquisar um aplicativo de locatário

Para pesquisar aplicativo específico:

1. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e escolha **Aplicar**.
1. Insira o nome do aplicativo que você deseja encontrar. Se o aplicativo tiver sido adicionado ao locatário do Azure AD, ele será exibido nos resultados da pesquisa. Este exemplo mostra que o GitHub não foi adicionado aos aplicativos de locatário.

    ![O exemplo mostra que um aplicativo não foi adicionado ao locatário](media/view-applications-portal/search-for-tenant-application.png)

1. Tente inserir as primeiras letras do nome de um aplicativo. Este exemplo mostra todos os aplicativos que começam com **Vendas**.

    ![O exemplo mostra todos os aplicativos que começam com vendas](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como exibir os aplicativos no locatário do Azure AD. Você aprendeu como filtrar a lista de aplicativos por tipo de aplicativo, status e visibilidade. Você também aprendeu a procurar determinado aplicativo.

Agora você que localizou o aplicativo que está procurando, é possível passar para [Adicionar mais aplicativos ao seu locatário](add-application-portal.md). Ou é possível selecionar o aplicativo para exibir ou editar as opções de propriedades e de configuração. Por exemplo, você pode configurar o logon único.

> [!div class="nextstepaction"]
> [Configurar Logon Único](configure-single-sign-on-non-gallery-applications.md)

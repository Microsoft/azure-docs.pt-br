---
title: "Azure Active Directory B2C: criar um locatário do Azure Active Directory B2C | Microsoft Docs"
description: "Um tópico sobre como criar um locatário zure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: swkrish
ms.openlocfilehash: 213d532f484056f3833c743d25c5e6faa5b732e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Crie um locatário do Azure Active Directory B2C no portal do Azure

Este Guia de início rápido ajuda a criar um locatário do Microsoft Azure Active Directory (Azure AD) B2C em poucos minutos. Quando você terminar, terá um locatário B2C (também conhecido como um diretório) a ser usado para registrar aplicativos B2C.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

##  <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

Os recursos B2C não podem ser habilitados em seus locatários existentes. Você precisa um locatário do Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Parabéns, você criou um locatário do Azure Active Directory B2C. Você é um Administrador Global do locatário. Você pode adicionar outros Administradores Globais conforme necessário. Para alternar para o novo locatário, clique no *link gerenciar seu novo locatário*.

![Gerenciar seu novo link de locatário](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Se você está planejando usar um locatário B2C para um aplicativo de produção, leia o artigo sobre [escala de produção versus locatários de visualização do B2C](active-directory-b2c-reference-tenant-type.md). Há problemas conhecidos quando você exclui um locatário B2C existente e o recria com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Vincular seu locatário à sua assinatura

Você precisa vincular o seu locatário do Azure AD B2C à sua assinatura do Azure para habilitar todas as funcionalidades do B2C e pagar pelos encargos de uso. Para saber mais, leia este [artigo](active-directory-b2c-how-to-enable-billing.md). Se você não vincular seu locatário do Azure AD B2C à sua assinatura do Azure, alguma funcionalidade será bloqueada e você verá uma mensagem de aviso ("Nenhuma assinatura vinculada a este locatário B2C ou A assinatura precisa de sua atenção.") nas configurações do B2C. É importante que você execute esta etapa antes de enviar seus aplicativos para produção.

## <a name="easy-access-to-settings"></a>Acesso fácil a configurações

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Você também pode acessar a folha digitando `Azure AD B2C` em **Pesquisar recursos** na parte superior do portal. Na lista de resultados, selecione **Azure AD B2C** para acessar a folha de configurações do B2C.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registrar seu aplicativo B2C em um locatário B2C](active-directory-b2c-app-registration.md)
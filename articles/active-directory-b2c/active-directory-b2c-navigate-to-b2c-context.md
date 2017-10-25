---
title: "Azure Active Directory B2C: Alternar para um locatário B2C | Microsoft Docs"
description: "Como alternar para o contexto do seu locatário do Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: 40d8d57d974a949fbdc0a06eeceb2d06bfbaa09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Alternar para o locatário do Azure AD B2C

Para configurar o Azure AD B2C, você precisa estar no contexto do seu locatário Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Faça logon no locatário do Azure AD B2C

Para navegar até seu locatário do Azure AD B2C, você deve estar conectado ao portal do Azure como um administrador global do locatário do Azure AD B2C.

1. Faça logon no [Portal do Azure](http://portal.azure.com).
1. Alternar locatários clicando em seu endereço de email ou na imagem no canto superior direito.
1. Na lista `Directory` que aparece, selecione o locatário do Azure AD B2C que você deseja gerenciar.

O Portal do Azure será atualizado.  Agora você está conectado ao portal do Azure no contexto do locatário do Azure AD B2C.

## <a name="navigate-to-the-b2c-features-blade"></a>Navegue até a folha de recursos do B2C

1. Clique em **Procurar** na barra de navegação à esquerda.
1. Clique em **> Mais serviços** e, em seguida, pesquise por `Azure AD B2C` no painel de navegação esquerdo.  (Para fixar ao seu quadro inicial à esquerda, clique na estrela à esquerda do Azure AD B2C)
1. Clique em **Azure AD B2C** para acessar a folha de recursos do B2C.
   
    ![Captura de tela de Navegar para a folha de recursos do B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Você precisa ser um Administrador Global do locatário do B2C para ser capaz de acessar a folha de recursos do B2C. Um Administrador Global de qualquer outro locatário ou um Usuário de qualquer outro locatário não pode acessá-la.  Você pode alternar para o locatário B2C usando o seletor de locatário no canto superior direito do portal do Azure.

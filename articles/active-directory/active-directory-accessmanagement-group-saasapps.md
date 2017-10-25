---
title: Usar um grupo para gerenciar o acesso a aplicativos SaaS| Microsoft Docs
description: "Como usar grupos no Azure Active Directory Premium ou Basic para atribuir acesso a aplicativos SaaS que estão integrados ao Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 818f4b515926c35078b3118978f3accbf3bbb65b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Usar um grupo para gerenciar o acesso a aplicativos SaaS
Usando o Azure AD (Azure Active Directory) com a licença do Azure AD Premium ou do Azure AD Basic, você pode usar grupos para atribuir acesso a um aplicativo SaaS que esteja integrado ao Azure AD. Por exemplo, se você quiser atribuir acesso ao departamento de marketing usar cinco aplicativos SaaS diferentes, você pode criar um grupo que contém os usuários no departamento de marketing e atribuir esse grupo a esses cinco aplicativos SaaS que são necessários para o departamento de marketing. Dessa forma, você pode poupar tempo gerenciando a associação no departamento de marketing em um único lugar. Os usuários são atribuídos ao aplicativo quando eles são adicionados como membros do grupo de marketing, e sua atribuição é removida do aplicativo quando eles são removidos do grupo de marketing. Esse recurso pode ser usado com centenas de aplicativos que você adiciona na Galeria de aplicativos do Azure AD.

> [!IMPORTANT]
> Você pode usar esse recurso somente depois que você iniciar uma avaliação do Azure AD Premium ou adquirir licenças do Azure AD Premium ou Azure AD Basic.
> No momento, as associações de grupo aninhadas não têm suporte para atribuição com base em grupo de aplicativos.

**Para atribuir acesso de um usuário ou um grupo a um aplicativo SaaS**

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Aplicativos empresariais**.
2. Selecione um aplicativo que você adicionou da Galeria de Aplicativos para abri-lo.
3. Selecione **Usuários e grupos** e, em seguida, selecione **Adicionar usuário**.
4. Em **Adicionar atribuição**, selecione **Usuários e grupos** para abrir a lista de seleção **usuários e grupos**.
6. Selecione quantos grupos ou usuários desejar, em seguida, clique ou toque em **Selecione** para adicioná-los à lista de **Adicionar atribuição**. Você também pode atribuir uma função a um usuário neste estágio.
7. Selecione **Atribuir** para atribuir usuários ou grupos ao aplicativo empresarial selecionado.

### <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

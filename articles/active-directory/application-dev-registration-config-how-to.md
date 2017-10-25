---
title: "Como selecionar permissões para uma API | Microsoft Docs"
description: "Como localizar os pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 6966cf145375bf3d830d476564c428502ae40fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Como selecionar permissões para uma API

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [Portal do Azure](https://portal.azure.com).

-   Navegue até o [Portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory**.

-   Clique em **Registros do Aplicativo** e escolha **Pontos de Extremidade**.

-   Isso abre a página **Pontos de Extremidade**, que lista todos os pontos de extremidade de autenticação de seu locatário.

-   Use o ponto de extremidade específico ao protocolo de autenticação que você está usando, em conjunto com a ID do aplicativo, para criar a solicitação de autenticação específica ao aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)

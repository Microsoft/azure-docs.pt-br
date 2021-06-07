---
title: Obter os pontos de extremidade para um registro de aplicativo do Azure AD
titleSuffix: Microsoft identity platform
description: Como localizar os pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103304"
---
# <a name="how-to-discover-endpoints"></a>Como descobrir pontos de extremidade

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [Portal do Azure](https://portal.azure.com).

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Registros de aplicativo**, depois selecione **Pontos de extremidade** no menu superior.

    A página **Pontos de extremidade** é exibida, mostrando os pontos de extremidade de autenticação do locatário.
    
    Use o ponto de extremidade que corresponde ao protocolo de autenticação que você está usando, em conjunto com a **ID do aplicativo (cliente)** , para criar a solicitação de autenticação específica ao aplicativo.

As **nuvens nacionais** (por exemplo, Azure AD China, Alemanha e Governo dos EUA) têm seu próprio portal de registro de aplicativo e pontos de extremidade de autenticação do Azure AD. Saiba mais na [Visão geral das nuvens nacionais](authentication-national-cloud.md).

## <a name="next-steps"></a>Próximas etapas

Veja mais informações sobre pontos de extremidade em diferentes ambientes do Azure na [Visão geral das nuvens nacionais](authentication-national-cloud.md).

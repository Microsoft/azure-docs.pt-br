---
title: Aumente a resiliência dos aplicativos de autenticação e autorização que você desenvolve
titleSuffix: Microsoft identity platform
description: Visão geral de nossas diretrizes de resiliência para o desenvolvimento de aplicativos usando Azure Active Directory e a plataforma de identidade da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317345"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Aumente a resiliência dos aplicativos de autenticação e autorização que você desenvolve

A identidade da Microsoft usa autenticação e autorização modernas e baseadas em token. Isso significa que um aplicativo adquire tokens de um provedor de identidade para autenticar o usuário e autorizar o aplicativo a chamar APIs protegidas.

Um token é válido por um determinado período de tempo antes que o aplicativo precise adquirir um novo. Raramente, uma chamada para recuperar um token pode falhar devido a um problema, como falha de rede ou de infraestrutura ou interrupção do serviço de autenticação. Neste documento, descreveremos as etapas que um desenvolvedor pode tomar para aumentar a resiliência em seus aplicativos se ocorrer uma falha de aquisição de token.

Esses artigos fornecem orientação sobre como aumentar a resiliência em aplicativos usando a plataforma de identidade da Microsoft e o Azure Active Directory. Há diretrizes para os aplicativos cliente que funcionam em nome de um usuário conectado, bem como aplicativos de daemon que funcionam em seu próprio nome. Eles contêm as práticas recomendadas para o uso de tokens, bem como a chamada de recursos.

- [Crie resiliência em aplicativos que conectam usuários](resilience-client-app.md)
- [Crie resiliência em aplicativos sem usuários](resilience-daemon-app.md)
- [Crie resiliência em sua infraestrutura de gerenciamento de identidade e acesso](resilience-in-infrastructure.md)
- [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)

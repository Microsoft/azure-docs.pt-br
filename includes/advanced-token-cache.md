---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94627993"
---
Você pode usar a implementação de cache de token do MSAL para permitir que aplicativos em segundo plano, APIs e serviços usem o cache de token de acesso para continuar a agir em nome dos usuários em sua ausência. Fazer isso é especialmente útil se os aplicativos e serviços em segundo plano precisarem continuar a trabalhar em nome do usuário depois que o usuário sair do aplicativo Web de front-end.

Atualmente, a maioria dos processos em segundo plano usa [permissões de aplicativo](/graph/auth/auth-concepts#microsoft-graph-permissions) quando eles precisam trabalhar com os dados de um usuário sem que estejam presentes para autenticação ou reautenticação. Como as permissões de aplicativo geralmente exigem o consentimento do administrador, o que requer elevação de privilégio, o conflito desnecessário é encontrado, pois o desenvolvedor não pretendia obter permissão além da qual o usuário originalmente consentiu para seu aplicativo.

Este exemplo de código no GitHub mostra como evitar esse conflito desnecessário acessando o cache de token do MSAL de aplicativos em segundo plano:

 [Acessando o cache de tokens do usuário conectado de aplicativos, APIs e serviços em segundo plano](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)
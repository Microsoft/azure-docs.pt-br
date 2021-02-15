---
title: API Web protegida-visão geral
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 434c255f0b9634e0d17bad1627ddd92af9498bfe
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582427"
---
# <a name="scenario-protected-web-api"></a>Cenário: API Web protegida

Nesse cenário, você aprende a expor uma API da Web. Você também aprende a proteger a API Web para que somente usuários autenticados possam acessá-la.

Para usar sua API Web, habilite usuários autenticados com contas corporativas e de estudante ou habilite contas pessoais da Microsoft.

## <a name="specifics"></a>Especificações

Aqui estão informações específicas que você precisa saber para proteger as APIs da Web:

- O registro do aplicativo deve expor pelo menos um *escopo* ou uma *função de aplicativo*.
  - Os escopos são expostos por APIs Web que são chamadas em nome de um usuário.
  - As funções de aplicativo são expostas por APIs da Web chamadas por aplicativos de daemon (que chama sua API da Web em seu próprio nome).
- Se você criar um novo registro de aplicativo de API Web, escolha a [versão de token de acesso](reference-app-manifest.md#accesstokenacceptedversion-attribute) aceita pela sua API Web para `2` . Para APIs da Web herdadas, a versão de token aceita pode ser `null` , mas esse valor restringe o público de entrada apenas para organizações e o MSA (contas pessoais da Microsoft) não terá suporte.
- A configuração de código para a API Web deve validar o token usado quando a API Web é chamada.
- O código nas ações do controlador deve validar as funções ou os escopos no token.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, registro de [aplicativo](scenario-protected-web-api-app-registration.md).

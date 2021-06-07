---
title: Registrar uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web downstream (registro de aplicativo).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584307"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API Web que chama APIs da Web: registro de aplicativo

Uma API da Web que chama APIs da Web downstream tem o mesmo registro de uma API Web protegida. Siga as instruções em [API Web protegida: registro de aplicativo](scenario-protected-web-api-app-registration.md).

Como o aplicativo Web agora chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que as informações de registro extras são necessárias: o aplicativo precisa compartilhar segredos (credenciais de cliente) com a plataforma Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome dos usuários para os quais o token de portador foi recebido. Os aplicativos Web precisam solicitar permissões delegadas. Para obter mais informações, consulte [adicionar permissões para acessar sua API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, configuração de [código do aplicativo](scenario-web-api-call-api-app-configuration.md).

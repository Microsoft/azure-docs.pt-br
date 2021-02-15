---
title: API REST de configuração de Azure App-autorização
description: Páginas de referência para autorização usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932635"
---
# <a name="authorization"></a>Autorização

A autorização refere-se ao procedimento usado para determinar as permissões que um chamador tem ao fazer uma solicitação. Há vários modelos de autorização. O modelo de autorização usado para uma solicitação depende do método de [autenticação](./rest-api-authentication-index.md) usado. Os modelos de autorização estão listados abaixo.

## <a name="hmac"></a>HMAC

O modelo de [modelo de autorização](./rest-api-authorization-hmac.md) associado à autenticação HMAC divide as permissões em somente leitura ou leitura/gravação. Consulte a página [autorização HMAC](./rest-api-authorization-hmac.md) para obter detalhes.

## <a name="azure-active-directory"></a>Azure Active Directory

O [modelo de autorização](./rest-api-authorization-azure-ad.md) associado à autenticação do Azure Active Directory (AD do Azure) usa o RBAC do Azure para controlar permissões. Consulte a página de [autorização do Azure ad](./rest-api-authorization-azure-ad.md) para obter detalhes.

---
title: API REST de configuração de Azure App-autenticação
description: Páginas de referência para autenticação usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932686"
---
# <a name="authentication"></a>Autenticação

Todas as solicitações HTTP devem ser autenticadas. Há suporte para os seguintes esquemas de autenticação.

## <a name="hmac"></a>HMAC

A [autenticação HMAC](./rest-api-authentication-hmac.md) usa um segredo gerado aleatoriamente para assinar as cargas de solicitação. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

A [autenticação do Azure Active Directory (AD do Azure)](../active-directory/authentication/overview-authentication.md) utiliza um token de portador que é obtido do Azure Active Directory para autenticar solicitações. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização do Azure ad](./rest-api-authorization-azure-ad.md) .
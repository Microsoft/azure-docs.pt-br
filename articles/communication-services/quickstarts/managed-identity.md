---
title: Usar identidades gerenciadas nos serviços de comunicação
titleSuffix: An Azure Communication Services quickstart
description: Identidades gerenciadas permitem autorizar o acesso dos serviços de comunicação do Azure de aplicativos em execução em VMs do Azure, aplicativos de funções e outros recursos.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110707"
---
# <a name="use-managed-identities"></a>Usar identidades gerenciadas
Introdução aos serviços de comunicação do Azure usando identidades gerenciadas. A identidade dos serviços de comunicação e os SDKs do SMS dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este guia de início rápido mostra como autorizar o acesso aos SDKs de identidade e de SMS de um ambiente do Azure que dá suporte a identidades gerenciadas. Ele também descreve como testar seu código em um ambiente de desenvolvimento.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o controle de acesso baseado em função do Azure](../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades do Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../concepts/telephony-sms/concepts.md)

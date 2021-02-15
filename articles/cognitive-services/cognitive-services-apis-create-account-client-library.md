---
title: Criar recurso dos Serviços Cognitivos usando a biblioteca de clientes de Gerenciamento do Azure
titleSuffix: Azure Cognitive Services
description: Crie e gerencie recursos dos Serviços Cognitivos do Azure usando a biblioteca de clientes de Gerenciamento do Azure.
services: cognitive-services
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços de IA
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 1fd748a0184c1718ac9450aaca3e2db1a185051a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368943"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Início Rápido: Criar um recurso dos Serviços Cognitivos usando a biblioteca de clientes do Gerenciamento do Azure

Use este guia de início rápido para criar e gerenciar recursos dos Serviços Cognitivos do Azure usando a biblioteca de clientes de Gerenciamento do Azure.

Os Serviços Cognitivos do Azure são serviços baseados em nuvem com APIs REST e SDKs de biblioteca de cliente disponíveis para ajudar os desenvolvedores a incutir a inteligência cognitiva nos aplicativos, sem ter conhecimentos ou habilidades diretas sobre os domínios de IA (inteligência artificial) ou ciência de dados. Os Serviços Cognitivos do Azure permitem que os desenvolvedores adicionem com facilidade recursos cognitivos a seus aplicativos, com soluções cognitivas capazes de ver, ouvir, falar, entender e até mesmo começar a racionalizar.

Os serviços de IA individuais são representados pelos [recursos](../azure-resource-manager/management/manage-resources-portal.md) do Azure que você cria em sua assinatura do Azure. Depois de criar um recurso, use as chaves e o ponto de extremidade gerados para autenticar seus aplicativos.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
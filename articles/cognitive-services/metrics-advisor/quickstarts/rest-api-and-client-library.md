---
title: API REST das bibliotecas de clientes do Assistente de Métricas
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para conectar seus aplicativos à API do Assistente de Métricas por meio dos Serviços Cognitivos do Azure.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186894"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Início Rápido: Usar as bibliotecas de clientes ou as APIs REST para personalizar sua solução

Introdução à API REST ou às bibliotecas de clientes do Assistente de Métricas. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

Use o Assistente de Métricas para:

* Adicionar um feed de dados de uma fonte de dados
* Verificar o status da ingestão
* Configurar a detecção e os alertas 
* Consultar os resultados da detecção de anomalias
* Diagnosticar anomalias


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

- [Usar o portal da Web](web-portal.md)
- [Integrar seus feeds de dados](../how-tos/onboard-your-data.md)
    - [Gerenciar feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Configurar métricas e ajustar a configuração de detecção](../how-tos/configure-metrics.md)
- [Ajustar a detecção de anomalias usando os comentários](../how-tos/anomaly-feedback.md)
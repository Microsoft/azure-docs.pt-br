---
title: Códigos de erro de disponibilidade externa - Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erro de disponibilidade externa.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020546"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Códigos de erro de disponibilidade externa do Azure Stream Analytics

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de disponibilidade externa. Os erros de disponibilidade externa ocorrem quando um serviço dependente está indisponível.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Causa**: Um serviço está temporariamente indisponível.
* **Recomendação**: O Stream Analytics continuará tentando acessar o serviço.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa**: O Stream Analytics encontrou um erro ao se comunicar com o EventHub. 


## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)

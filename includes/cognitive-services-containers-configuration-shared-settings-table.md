---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: ed512c0f56d8da5cb8e47b20f2495054fdedf020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815391"
---
Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Usado para rastrear informações de cobrança.|
|Não |[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|Sim|[Billing](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Sim|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não |[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não |[Proxy HTTP](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não |[Logging](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|Não |[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

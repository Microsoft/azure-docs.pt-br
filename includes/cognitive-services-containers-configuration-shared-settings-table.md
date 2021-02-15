---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006859"
---
O contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Rastreia informações de cobrança.|
|No|[ApplicationInsights](#applicationinsights-setting)|Permite a adição de suporte a dados telemétricos do [Azure Application Insights](/azure/application-insights) para seu contêiner.|
|Yes|[Billing](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Yes|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|No|[Fluentd](#fluentd-settings)|Grava log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|No|Proxy HTTP|Configura um proxy HTTP para fazer solicitações de saída.|
|No|[Logging](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|No|[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|
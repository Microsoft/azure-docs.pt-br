---
title: Visão geral de redirecionamento do Gateway de Aplicativo do Azure | Microsoft Docs
description: Saiba mais sobre a capacidade de redirecionamento do Gateway de Aplicativo do Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 07/18/2017
ms.date: 01/15/2019
ms.author: v-junlch
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831735"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicativo

É um cenário comum para muitos aplicativos da Web dar suporte a redirecionamento automático de HTTP para HTTPS para garantir que todas as comunicações entre o aplicativo e seus usuários ocorram em um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar as solicitações recebidas de HTTP para HTTPS.  O Gateway de Aplicativo agora dá suporte à capacidade de redirecionar tráfego no Gateway de Aplicativo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O suporte a redirecionamento do Gateway de Aplicativo não está limitado apenas a redirecionamento HTTP -> HTTPS. Esse é um mecanismo de redirecionamento genérico, que permite o redirecionamento do tráfego recebido em um ouvinte para outro ouvinte no Gateway de Aplicativo. Ele também dá suporte a redirecionamento para um site externo. O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

1. Redirecionamento global de um ouvinte para outro ouvinte no Gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
2. Redirecionamento baseado em caminho. Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras é denotada por /cart/*.
3. Redirecionamento para um site externo.

![redirecionamento](./media/application-gateway-redirect-overview/redirect.png)

Com essa alteração, os clientes precisam criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte de destino ou o site externo para o qual o redirecionamento é desejado. O elemento de configuração também dá suporte a opções para habilitar o acréscimo da cadeia de consulta e do caminho de URI à URL redirecionada. Os clientes também podem escolher se o redirecionamento é um redirecionamento temporário (código de status HTTP 302) ou permanente (código de status HTTP 301). Depois de criada, essa configuração de redirecionamento é anexada ao ouvinte de origem por meio de uma nova regra. Ao usar uma regra básica, a configuração de redirecionamento é associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra baseada em caminho é usada, a configuração de redirecionamento é definida no mapa de caminho da URL e, portanto, só se aplica à área de caminho específica de um site.

### <a name="next-steps"></a>Próximas etapas

[Configurar o redirecionamento de HTTP para HTTP em um gateway de aplicativo](redirect-http-to-https-portal.md)

<!-- Update_Description: wording update -->
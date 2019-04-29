---
title: Visão geral de redirecionamento do Gateway de Aplicativo do Azure
description: Saiba mais sobre a capacidade de redirecionamento do Gateway de Aplicativo do Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/19/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715786"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicativo

Você pode usar o gateway de aplicativo para redirecionar o tráfego.  Ele tem um mecanismo de redirecionamento genérico, que permite redirecionar o tráfego recebido em um ouvinte para outro ouvinte ou para um site externo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.

É um cenário comum de redirecionamento para muitos aplicativos web dar suporte automático de HTTP para redirecionamento a HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorre em um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar as solicitações recebidas de HTTP para HTTPS. Com o suporte de redirecionamento no Gateway de aplicativo, você pode fazer isso, basta adicionar uma nova configuração de redirecionamento para uma regra de roteamento e especificando outro ouvinte com o protocolo HTTPS como o ouvinte de destino.

Há suporte para os seguintes tipos de redirecionamento:

- 301 Permanent Redirect
- 302 Encontrado
- 303 ver outros
- 307 Temporary Redirect

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

-  **Redirecionamento Global**

   Redirecionamento global de um ouvinte para outro ouvinte no gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- **Redirecionamento baseado em caminho**

   Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras é denotada por /cart/*.
- **Redirecionamento para um site externo**

![redirecionamento](./media/redirect-overview/redirect.png)

Com essa alteração, os clientes precisam criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte de destino ou o site externo para o qual o redirecionamento é desejado. O elemento de configuração também dá suporte a opções para habilitar o acréscimo da cadeia de consulta e do caminho de URI à URL redirecionada. Você também pode escolher o tipo de redirecionamento. Depois de criada, essa configuração de redirecionamento é anexada ao ouvinte de origem por meio de uma nova regra. Ao usar uma regra básica, a configuração de redirecionamento é associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra de caminho é usada, a configuração de redirecionamento é definida no mapa de caminho de URL. Então ela se aplica somente a área de caminho específico de um site.

### <a name="next-steps"></a>Próximas etapas

[Como configurar o redirecionamento de URL em um gateway de aplicativo](tutorial-url-redirect-powershell.md)

<!-- Update_Description: wording update -->
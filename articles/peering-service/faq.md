---
title: Perguntas frequentes do serviço de emparelhamento do Azure
description: Saiba mais sobre as perguntas frequentes sobre o serviço de emparelhamento Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026688"
---
# <a name="peering-service-faq"></a>Perguntas frequentes sobre o serviço de emparelhamento

Este artigo explica as perguntas mais frequentes sobre as conexões do serviço de emparelhamento do Azure.


**Perguntas. Quem são os clientes-alvo?**

a. Clientes de destino são empresas que se conectam ao Microsoft Cloud usando a Internet como transporte.

**Perguntas. Os clientes podem se inscrever para o serviço de emparelhamento com vários provedores?** 

a. Sim, os clientes podem se inscrever no serviço de emparelhamento com vários provedores na mesma região ou em regiões diferentes, mas não para o mesmo prefixo.

**Perguntas. Os clientes podem selecionar um ISP exclusivo para seus sites por região geográfica?**

a. Sim, os clientes podem fazer isso. Selecione o provedor de provedores de parceiros por região que atenda às suas necessidades operacionais e de negócios.

**Perguntas. O que é um PoP do Microsoft Edge?**

a. É um local físico onde a Microsoft se conecta com outras redes. No local PoP do Microsoft Edge, serviços como a porta de front-end do Azure e a CDN do Azure são hospedados. Para saber mais, confira [CDN do Azure](../cdn/cdn-features.md).

## <a name="peering-service-unique-characteristics"></a>Serviço de emparelhamento: características exclusivas

**Perguntas. Como o serviço de emparelhamento é diferente do acesso normal à Internet?**

a. Os parceiros que se registraram com o serviço de emparelhamento da Microsoft estão trabalhando com a Microsoft para oferecer roteamento otimizado e conectividade confiável aos serviços da Microsoft.  

**Perguntas. Como o serviço de emparelhamento é diferente do ExpressRoute?**

a. O Azure ExpressRoute é uma conexão privada e dedicada de um ou vários locais de clientes. Enquanto o serviço de emparelhamento oferece conectividade pública otimizada e não dá suporte a nenhuma conectividade privada, ele também oferece conectividade otimizada para debates locais da Internet.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o serviço de emparelhamento, consulte [visão geral do serviço de emparelhamento](about.md).
- Para encontrar um provedor de serviços, consulte [parceiros e locais de serviço de emparelhamento](location-partners.md).
- Para carregar uma conexão de serviço de emparelhamento, consulte [serviço de emparelhamento de integração](onboarding-model.md).
- Para registrar uma conexão de serviço de emparelhamento, consulte [registrar uma conexão de serviço de emparelhamento-portal do Azure](azure-portal.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
---
title: Visão geral das marcas de serviço do Firewall do Azure
description: Este artigo é uma visão geral das marcas de serviço do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450165"
---
# <a name="azure-firewall-service-tags"></a>Marcas de serviço do Firewall do Azure

Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

As marcas de serviço de Firewall do Azure podem ser usadas no campo de destino de regras de rede. Você pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Marcas de serviço com suporte

Ver [grupos de segurança](../virtual-network/security-overview.md#service-tags) para obter uma lista de marcas de serviço que estão disponíveis para uso em regras de rede do firewall do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as regras do Firewall do Azure, confira [Lógica de processamento de regra do Firewall do Azure](rule-processing.md).
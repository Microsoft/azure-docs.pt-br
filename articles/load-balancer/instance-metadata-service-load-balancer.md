---
title: Recuperar informações do balanceador de carga usando o serviço de metadados de instância do Azure
titleSuffix: Azure Load Balancer
description: Comece a aprender a usar o serviço de metadados de instância do Azure para recuperar informações do balanceador de carga.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: dcc9f71404e5a7c6509e4a8e821d66831ba02382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417006"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Recuperar informações do balanceador de carga usando o serviço de metadados de instância do Azure

IMDS (serviço de metadados de instância do Azure) fornece informações sobre as instâncias de máquina virtual em execução no momento. O serviço é uma API REST que está disponível em um endereço IP conhecido e não roteável (169.254.169.254). 

Quando você coloca a máquina virtual ou as instâncias do conjunto de máquinas virtuais por trás de uma Standard Load Balancer do Azure, use o IMDS para recuperar metadados relacionados ao balanceador de carga e às instâncias.

Os metadados incluem as seguintes informações para as máquinas virtuais ou conjuntos de dimensionamento de máquinas virtuais:

* IP público do SKU Standard.
* Configurações de regra de entrada do balanceador de carga de cada IP privado da interface de rede.
* Configurações de regra de saída do balanceador de carga de cada IP privado da interface de rede.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Acessar os metadados do balanceador de carga usando o IMDS

Para obter mais informações sobre como acessar os metadados do balanceador de carga, consulte [usar o serviço de metadados da instância do Azure para acessar informações do balanceador de carga.](howto-load-balancer-imds.md) para acessar informações do balanceador de carga.

## <a name="troubleshoot-common-error-codes"></a>Solucionar problemas de códigos de erro comuns

Para obter mais informações sobre códigos de erro comuns e seus métodos de mitigação, consulte [solucionar problemas de códigos de erro comuns ao usar o IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Suporte

Se não for possível recuperar uma resposta de metadados após várias tentativas, crie um problema de suporte no portal do Azure.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [serviço de metadados de instância do Azure](/virtual-machines/windows/instance-metadata-service)

[Implantar um balanceador de carga padrão](quickstart-load-balancer-standard-public-portal.md)


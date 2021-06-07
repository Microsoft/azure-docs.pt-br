---
title: Preferência de roteamento não monitorada no Azure
description: Saiba mais sobre como você pode configurar a preferência de roteamento para seus recursos de saída de dados para o provedor de CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 00e0d1afa2aa035fe27e73dae60e291ea53bd6b8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042842"
---
# <a name="what-is-routing-preference-unmetered"></a>O que é a preferência de roteamento não medido?

A preferência de roteamento não medido está disponível para provedores de CDN (rede de distribuição de conteúdo) com clientes hospedados em seu conteúdo de origem no Azure. O serviço permite que os provedores de CDN estabeleçam conexão direta de emparelhamento com roteadores de borda de rede global da Microsoft em vários locais.

![Preferência de roteamento não medida](media/routing-preference-unmetered/unmetered.png)

A saída do tráfego de rede de origem no Azure destinado ao provedor CDN beneficia-se da conectividade direta.
* A fatura de transferência de dados para o tráfego de saída dos recursos do Azure que são roteados por meio desses links diretos é gratuita.
* A conexão direta entre o provedor de CDN e a origem no Azure fornece um desempenho ideal, pois não há saltos entre eles. Isso beneficia a carga de trabalho da CDN que frequentemente busca dados da origem.

## <a name="configuring-routing-preference-unmetered"></a>Configuração de preferência de roteamento não medida

Para aproveitar a preferência de roteamento não medido, seus provedores de CDN precisam fazer parte deste programa. Se o seu provedor de CDN não fizer parte do programa, entre em contato com seu provedor de CDN.

Em seguida, configure a preferência de roteamento para seus recursos e defina o tipo de preferência de roteamento como **Internet**. Você pode configurar a preferência de roteamento ao criar um endereço IP público e, em seguida, associar o IP público a recursos como máquinas virtuais, balanceadores de carga voltados para a Internet e muito mais. [Saiba como configurar a preferência de roteamento para um endereço IP público usando o portal do Azure](routing-preference-portal.md)

Você também pode habilitar a preferência de roteamento para sua conta de armazenamento e publicar um segundo ponto de extremidade, que precisa ser usado pelo provedor de CDN para buscar dados da origem de armazenamento. Por exemplo, publicar um ponto de extremidade específico de rota da Internet para a conta de armazenamento *StorageAccountA* publicará o segundo ponto de extremidade para seus serviços de armazenamento, conforme mostrado abaixo:

![Preferência de roteamento para contas de armazenamento](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Próximas etapas

* [Configurar a preferência de roteamento para uma VM usando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configurar a preferência de roteamento para uma VM usando a CLI do Azure](configure-routing-preference-virtual-machine-cli.md)
* [Configurar a preferência de roteamento para sua conta de armazenamento](../storage/common/network-routing-preference.md)
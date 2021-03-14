---
title: Introdução ao diagnóstico de configuração de rede no observador de rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral do observador de rede – diagnóstico de configuração de rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502001"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Introdução ao diagnóstico de configuração de rede no observador de rede do Azure

A ferramenta de diagnóstico de configuração de rede ajuda os clientes a entender quais fluxos de tráfego serão permitidos ou negados em sua rede virtual do Azure, juntamente com informações detalhadas de depuração. Ele pode ajudá-lo a entender se suas regras NSG estão configuradas corretamente. 

## <a name="pre-requisites"></a>Pré-requisitos
Para usar o diagnóstico de configuração de rede, o observador de rede deve estar habilitado em sua assinatura. Consulte [criar uma instância do observador de rede do Azure](./network-watcher-create.md) para habilitar.

## <a name="background"></a>Segundo plano

- Seus recursos no Azure são conectados por meio de redes virtuais (VNETs) e sub-redes. A segurança dessas VNets e sub-redes pode ser gerenciada usando um grupo de segurança de rede (NSG).
- Um NSG contém uma lista de regras de segurança que permitem ou negam o tráfego de rede aos recursos aos quais ele está conectado. NSGs pode ser associado a sub-redes, VMs individuais ou adaptadores de rede (NICs) individuais conectados às VMs. 
- Todos os fluxos de tráfego em sua rede são avaliados usando as regras no NSG aplicável.
- As regras são avaliadas com base no número de prioridade do menor para o mais alto 

## <a name="how-does-network-configuration-diagnostic-work"></a>Como funciona o diagnóstico de configuração de rede? 

Para um determinado fluxo, a ferramenta NCD executa uma simulação do fluxo e retorna se o fluxo seria permitido (ou negado) e informações detalhadas sobre as regras, permitindo/negando o fluxo.  Os clientes devem fornecer detalhes de um fluxo como origem, destino, protocolo, etc. A ferramenta retorna se o tráfego foi permitido ou negado, as regras NSG que foram avaliadas para o fluxo especificado e os resultados da avaliação para cada regra.

## <a name="next-steps"></a>Próximas etapas

Usar o diagnóstico de configuração de rede por meio de outras interfaces
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [CLI do Azure](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)
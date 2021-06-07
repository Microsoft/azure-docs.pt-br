---
title: Noções básicas sobre o endereço IP da sua instância do serviço de provisionamento de dispositivos IoT (DPS) | Microsoft Docs
description: Entenda como consultar seu endereço de serviço de provisionamento de dispositivos IoT (DPS) e suas propriedades. O endereço IP da instância do DPS pode ser alterado durante determinados cenários, como recuperação de desastre ou failover regional.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "79284922"
---
# <a name="iot-hub-dps-ip-addresses"></a>Endereços IP do DPS do Hub IoT

Os prefixos de endereço IP para os pontos de extremidade públicos de um serviço de provisionamento de dispositivos do Hub IoT (DPS) são publicados periodicamente sob a [marca de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Você pode usar esses prefixos de endereço IP para controlar a conectividade entre uma instância de DPS de IoT e dispositivos ou ativos de rede para implementar uma variedade de metas de isolamento de rede:

| Goal | Abordagem |
|------|----------|
| Verifique se seus dispositivos e serviços se comunicam somente com pontos de extremidade de DPS do Hub IoT | Use a marca de serviço _AzureIoTHub_ para descobrir instâncias de DPS do Hub IOT. Configure as regras de permissão na configuração de firewall dos dispositivos e dos serviços para os prefixos de endereço IP de acordo. Configure regras para remover o tráfego para outros endereços IP de destino aos quais você não deseja que os dispositivos ou serviços se comuniquem. |
| Verifique se o ponto de extremidade do DPS do Hub IoT recebe conexões somente de seus dispositivos e ativos de rede | Use o [recurso de filtro IP](iot-dps-ip-filtering.md) de DPS do IOT para criar regras de filtro para as APIs do dispositivo e do serviço de DPS. Essas regras de filtro podem ser usadas para permitir conexões somente de seus dispositivos e endereços IP de ativos de rede (consulte a seção [limitações](#limitations-and-workarounds) ). | 




## <a name="best-practices"></a>Práticas recomendadas

* Ao adicionar regras de permissão na configuração de firewall dos dispositivos, é melhor fornecer [portas específicas usadas pelos protocolos aplicáveis](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP das instâncias de DPS do IoT estão sujeitos a alterações. Essas alterações são publicadas periodicamente por meio de marcas de serviço antes de entrarem em vigor. Portanto, é importante que você desenvolva processos de recuperação e uso regulares das marcas de serviço mais recentes. Esse processo pode ser automatizado por meio da [API de descoberta de marcas de serviço](../virtual-network/service-tags-overview.md#service-tags-on-premises). A API de descoberta de marcas de serviço ainda está em visualização e, em alguns casos, pode não produzir a lista completa de marcas e endereços IP. Até que a API de descoberta esteja disponível para o público geral, considere usar as [marcas de serviço no formato JSON baixável](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Use o *AzureIoTHub. [ nome da região]* para identificar os prefixos IP usados pelos pontos de extremidade do DPS em uma região específica. Para considerar a recuperação de desastres do Datacenter ou o [failover regional](../iot-hub/iot-hub-ha-dr.md), verifique se a conectividade com os prefixos de IP da região de par geográfico da instância do DPS também está habilitada.

* A configuração de regras de firewall para uma instância de DPS pode bloquear a conectividade necessária para executar CLI do Azure e comandos do PowerShell em relação a ela. Para evitar esses problemas de conectividade, você pode adicionar regras de permissão para os prefixos de endereço IP de seus clientes para reabilitar os clientes da CLI ou do PowerShell para se comunicarem com sua instância do DPS.  


## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

* O recurso de filtro IP do DPS tem um limite de 100 regras. Esse limite pode ser aumentado via solicitações pelo atendimento ao cliente do Azure. 

* Suas [regras de filtragem de IP](iot-dps-ip-filtering.md) configuradas são aplicadas somente nos pontos de extremidade do DPS e não nos pontos de extremidade do Hub IOT vinculado. A filtragem de IP para hubs IoT vinculados deve ser configurada separadamente. Para obter mais informações, consulte [regras de filtragem de IP do Hub IOT](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Suporte para IPv6 

No momento, não há suporte para IPv6 no Hub IoT ou no DPS.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as configurações de endereço IP com o DPS, consulte:

* [Configurar a filtragem de IP](iot-dps-ip-filtering.md)

---
title: Noções básicas sobre o endereço IP do Hub IoT | Microsoft Docs
description: Entenda como consultar seu endereço IP do Hub IoT e as propriedades dele. O endereço IP do Hub IoT pode ser alterado durante determinados cenários, como recuperação de desastre ou failover regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83848221"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do Hub IoT

Os prefixos de endereço IP dos pontos de extremidade públicos do Hub IoT são publicados periodicamente sob a [marca de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_.

> [!NOTE]
> Para dispositivos implantados dentro de redes locais, o Hub IoT dá suporte à integração de conectividade VNET com pontos de extremidade privados. Consulte [Suporte ao Hub IoT para VNet](./virtual-network-support.md) para obter mais informações.


Você pode usar esses prefixos de endereço IP para controlar a conectividade entre o Hub IoT e seus dispositivos ou ativos de rede, com o objetivo de implementar uma variedade de metas de isolamento de rede:

| Goal | Cenários aplicáveis | Abordagem |
|------|-----------|----------|
| Garantir que seus dispositivos e serviços se comuniquem apenas com pontos de extremidade do Hub IoT | Troca de mensagens de [dispositivo para nuvem](./iot-hub-devguide-messaging.md) e [nuvem para dispositivo](./iot-hub-devguide-messages-c2d.md), [métodos diretos](./iot-hub-devguide-direct-methods.md), [dispositivo e módulos gêmeos](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivos](./iot-hub-device-streams-overview.md) | Use as marcas de serviço _AzureIoTHub_ e _EventHub_ para descobrir o Hub IoT e prefixos de endereço IP do hub de eventos e configure regras de permissão na configuração de firewall dos dispositivos e dos serviços para esses prefixos de endereço IP de acordo. Remova o tráfego para outros endereços IP de destino com os quais você não deseja que os dispositivos ou serviços se comuniquem. |
| Garanta que o ponto de extremidade do dispositivo do Hub IoT receba conexões somente de seus dispositivos e ativos de rede | Troca de mensagens de [dispositivo para nuvem](./iot-hub-devguide-messaging.md) e [nuvem para dispositivo](./iot-hub-devguide-messages-c2d.md), [métodos diretos](./iot-hub-devguide-direct-methods.md), [dispositivo e módulos gêmeos](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivos](./iot-hub-device-streams-overview.md) | Use o [recurso de filtro de IP](iot-hub-ip-filtering.md) do Hub IoT para permitir conexões de seus dispositivos e endereços IP de ativos de rede (consulte a seção de [limitações](#limitations-and-workarounds)). | 
| Garanta que os recursos de ponto de extremidade personalizados das rotas (contas de armazenamento, barramento de serviço e hubs de eventos) estejam acessíveis somente dos seus ativos de rede | [Roteamento de mensagem](./iot-hub-devguide-messages-d2c.md) | Siga as diretrizes do recurso sobre restrição de conectividade (por exemplo, por meio de [regras de firewall](../storage/common/storage-network-security.md), [links privados](../private-link/private-endpoint-overview.md) ou [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)). Use as marcas de serviço _AzureIoTHub_ para descobrir prefixos de endereço IP do Hub IoT e adicionar regras de permissão para esses prefixos de IP na configuração de firewall do recurso (consulte a seção [Limitações](#limitations-and-workarounds)). |



## <a name="best-practices"></a>Práticas recomendadas

* Ao adicionar regras de permissão na configuração de firewall dos dispositivos, é melhor fornecer [portas específicas usadas pelos protocolos aplicáveis](./iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP do Hub IoT estão sujeitos a alterações. Essas alterações são publicadas periodicamente por meio de marcas de serviço antes de entrarem em vigor. Portanto, é importante que você desenvolva processos de recuperação e uso regulares das marcas de serviço mais recentes. Esse processo pode ser automatizado por meio da [API de descoberta de marcas de serviço](../virtual-network/service-tags-overview.md#service-tags-on-premises). Observe que a API de descoberta de marcas de serviço ainda está em visualização. Além disso, em alguns casos, ela pode não produzir a lista completa de marcas e endereços IP. Até que a API de descoberta esteja disponível para o público geral, considere usar as [marcas de serviço no formato JSON baixável](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Use a marca *AzureIoTHub.[nome da região]* para identificar os prefixos de IP usados pelos pontos de extremidade do Hub IoT em uma região específica. Para proporcionar a recuperação de desastre do datacenter ou [failover regional](iot-hub-ha-dr.md), garanta que a conectividade com os prefixos de IP da região de par geográfico do Hub IoT também esteja habilitada.

* A configuração de regras de firewall no Hub IoT pode bloquear a conectividade necessária para executar a CLI do Azure e comandos do PowerShell em relação ao Hub IoT. Para evitar isso, você pode adicionar regras de permissão para os prefixos de endereço IP dos clientes, a fim de reabilitar os clientes da CLI ou do PowerShell a se comunicarem com o Hub IoT.  


## <a name="limitations-and-workarounds"></a>Limitações e Soluções Alternativas

* O recurso de filtro de IP do Hub IoT tem um limite de 10 regras. Esse limite pode ser aumentado via solicitações pelo atendimento ao cliente do Azure. 

* As [regras de filtragem de IP](iot-hub-ip-filtering.md) configuradas são aplicadas somente nos pontos de extremidade de IP do Hub IoT e não no ponto de extremidade do hub de eventos interno do Hub IoT. Se você também exigir que a filtragem de IP seja aplicada no hub de eventos em que as mensagens estão armazenadas, poderá fazer isso trazendo seu próprio recurso de hub de eventos, no qual você pode configurar as regras de filtragem de IP desejadas diretamente. Para fazer isso, você precisa provisionar seu próprio recurso de hub de eventos e configurar o [roteamento de mensagens](./iot-hub-devguide-messages-d2c.md) para enviar mensagens a esse recurso em vez do hub de eventos interno do Hub IoT. Por fim, conforme discutido na tabela acima, para habilitar a funcionalidade de roteamento de mensagens, você também precisará permitir a conectividade de prefixos de endereço IP do Hub IoT para o recurso de hub de eventos provisionado.

* Ao rotear para uma conta de armazenamento, permitir o tráfego dos prefixos de endereço IP do Hub IoT só é possível quando a conta de armazenamento está em uma região diferente da do Hub IoT.

## <a name="support-for-ipv6"></a>Suporte para IPv6 

No momento, não há suporte para IPv6 no Hub IoT.

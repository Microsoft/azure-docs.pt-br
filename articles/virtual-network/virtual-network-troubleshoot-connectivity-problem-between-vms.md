---
title: Solucionar problemas de conectividade entre VMs do Azure | Microsoft Docs
description: Saiba como solucionar os problemas de conectividade entre VMs do Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036208"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Solucionar problemas de conectividade entre VMs do Azure

Você pode enfrentar problemas de conectividade entre VMs (máquinas virtuais) do Azure. Este artigo apresenta etapas para a solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Uma VM do Azure não pode se conectar a outra VM do Azure.

## <a name="troubleshooting-guidance"></a>Diretriz de solução de problemas 

1. [Verifique se o NIC está configurado incorretamente](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede está bloqueado por NSG ou UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede está bloqueado por firewall de VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verifique se o aplicativo ou serviço de VM está escutando na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema é causado por SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verifique se o tráfego está bloqueado por ACLs para a VM clássica](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verifique se o ponto de extremidade é criado para a VM clássica](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tente se conectar a um compartilhamento de rede de VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verifique a conectividade entre VNets](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Execute estas etapas para solucionar o problema. Depois de concluir cada etapa, verifique se o problema foi resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Etapa 1: Verifique se o NIC está configurado incorretamente

Execute as etapas em [Como redefinir o adaptador de rede para uma VM Windows do Azure](../virtual-machines/windows/reset-network-interface.md). 

Se o problema ocorrer após você modificar o NIC (adaptador de rede), execute estas etapas:

**VMs com vários NICs**

1. Adicionar um NIC.
2. Corrija os problemas no NIC incorreto ou remova o NIC incorreto.  Em seguida, adicione novamente o NIC.

Para saber mais, confira [Adicionar ou remover adaptadores de rede de máquinas virtuais](virtual-network-network-interface-vm.md).

**VM com NIC único** 

- [Reimplantar VM do Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Reimplantar VM Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Etapa 2: Verifique se o tráfego de rede está bloqueado por NSG ou UDR

Use [Verificação de fluxo de IP do Observador de Rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [Registro em log do fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se há um NSG (Grupo de segurança de rede) ou UDR (Rota definida pelo usuário) que está interferindo com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Etapa 3: Verifique se o tráfego de rede está bloqueado por firewall de VM

Desabilite o firewall e teste o resultado. Se o problema for resolvido, verifique as configurações de firewall e, em seguida, reabilite o firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Etapa 4: Verifique se o aplicativo ou serviço de VM está escutando na porta

Você pode usar um dos métodos a seguir para verificar se o aplicativo ou serviço da VM está escutando na porta.

- Execute os seguintes comandos para verificar se o servidor está escutando nessa porta.

**VM Windows**

    netstat –ano

**VM Linux**

    netstat -l

- Execute o comando **telnet** na máquina virtual para testar a porta. Se o teste falhar, o aplicativo ou serviço não estará configurado para escutar nessa porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Etapa 5: Verifique se o problema é causado por SNAT

Em alguns cenários, a VM é colocada atrás de uma solução de balanceamento de carga que tem uma dependência de recursos fora do Azure. Nesses cenários, se você tiver problemas intermitentes de conexão, o problema pode ser causado por [esgotamento da porta SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para clássico) para cada VM que está por trás do balanceador de carga e proteja com ACL ou NSG. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Etapa 6: Verifique se o tráfego está bloqueado por ACLs para a VM clássica

Uma ACL (lista de controle de acesso) fornece a capacidade para permitir ou negar seletivamente o tráfego para um ponto de extremidade de máquina virtual. Para saber mais, confira [Gerenciar a ACL em um ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Etapa 7: Verifique se o ponto de extremidade é criado para a VM clássica

Todas as VMs criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores em outras redes virtuais exigem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Para saber mais, confira [Como configurar pontos de extremidade](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Etapa 8: Tente se conectar a um compartilhamento de rede de VM

Se você não puder se conectar a um compartilhamento de rede de VM, o problema pode ser causado por NICs não disponíveis na VM. Para excluir as NICs não disponíveis, consulte [Como excluir as NICs não disponíveis](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Etapa 9: Verifique a conectividade entre VNets

Use [Verificação de fluxo de IP do Observador de Rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [Registro em log do fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se há um NSG ou UDR que está interferindo com o fluxo de tráfego. Você também pode verificar a configuração entre Vnets [aqui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
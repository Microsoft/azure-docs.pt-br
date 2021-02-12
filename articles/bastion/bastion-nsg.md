---
title: Trabalhando com VMs e NSGs na bastiões do Azure
description: Você pode usar grupos de segurança de rede com a bastiões do Azure. Saiba mais sobre as sub-redes necessárias para essa configuração.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 4fe22e0dae73df7af4fc24ba508ecbecf72dfd05
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795353"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhando com acesso NSG e bastiões do Azure

Ao trabalhar com a bastiões do Azure, você pode usar NSGs (grupos de segurança de rede). Para obter mais informações, consulte [grupos de segurança](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário navega para a máquina virtual do Azure para RDP/SSH.
* Conectar integração-sessão RDP/SSH de clique único dentro do navegador
* Nenhum IP público é necessário na VM do Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede

Esta seção mostra o tráfego de rede entre o usuário e a bastiões do Azure e para as VMs de destino em sua rede virtual:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

A bastiões do Azure é implantada especificamente para ***AzureBastionSubnet** _.

_ **Tráfego de entrada:**

   * **Tráfego de entrada da Internet pública:** A bastiões do Azure criará um IP público que precisa da porta 443 habilitada no IP público para o tráfego de entrada. A porta 3389/22 não precisa ser aberta no AzureBastionSubnet.
   * **Tráfego de entrada do plano de controle de bastiões do Azure:** Para conectividade do plano de controle, habilite a porta 443 de entrada da marca de serviço do **gatewaymanager** . Isso permite que o plano de controle, ou seja, o Gerenciador de gateway seja capaz de se comunicar com a bastiões do Azure.
   * **Tráfego de entrada do plano de dados de bastiões do Azure:** Para a comunicação do plano de dados entre os componentes subjacentes da bastiões do Azure, habilite as portas 8080, 5701 de entrada da marca de serviço do **VirtualNetwork** para a marca de serviço do **VirtualNetwork** . Isso permite que os componentes da bastiões do Azure se comuniquem entre si.
   * **Tráfego de entrada de Azure Load Balancer:** Para investigações de integridade, habilite a porta 443 de entrada da marca de serviço **AzureLoadBalancer** . Isso permite que Azure Load Balancer detecte conectividade


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Captura de tela mostra regras de segurança de entrada para conectividade de bastiões do Azure.":::

* **Tráfego de saída:**

   * **Tráfego de saída para VMs de destino:** A bastiões do Azure alcançará as VMs de destino sobre o IP privado. O NSGs precisa permitir o tráfego de saída para outras sub-redes de VM de destino para a porta 3389 e 22.
   * **Tráfego de saída para o plano de dados de bastiões do Azure:** Para a comunicação do plano de dados entre os componentes subjacentes da bastiões do Azure, habilite as portas 8080, 5701 de saída da marca de serviço **VirtualNetwork** para a marca de serviço **VirtualNetwork** . Isso permite que os componentes da bastiões do Azure se comuniquem entre si.
   * **Tráfego de saída para outros pontos de extremidade públicos no Azure:** A bastiões do Azure precisa ser capaz de se conectar a vários pontos de extremidade públicos no Azure (por exemplo, para armazenar logs de diagnóstico e logs de medição). Por esse motivo, a bastiões do Azure precisa de saída para 443 para a marca de serviço **AzureCloud** .
   * **Tráfego de saída para a Internet:** A bastiões do Azure precisa ser capaz de se comunicar com a Internet para validação de sessão e certificado. Por esse motivo, é recomendável habilitar a saída da porta 80 para a **Internet.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Captura de tela mostra regras de segurança de saída para conectividade de bastiões do Azure.":::

### <a name="target-vm-subnet"></a>Sub-rede VM de destino
Essa é a sub-rede que contém a máquina virtual de destino para a qual você deseja RDP/SSH.

   * **Tráfego de entrada da bastiões do Azure:** A bastiões do Azure alcançará a VM de destino sobre o IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam ser abertas no lado da VM de destino sobre o IP privado. Como prática recomendada, você pode adicionar o intervalo de endereços IP da sub-rede de bastiões do Azure nesta regra para permitir que somente a bastiões possa abrir essas portas nas VMs de destino em sua sub-rede de VM de destino.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a bastiões do Azure, consulte as [perguntas frequentes](bastion-faq.md).

---
title: Preparar máquinas virtuais para um FCI
description: Prepare suas máquinas virtuais do Azure para usá-las com uma FCI (instância de cluster de failover) e SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 10f01fd5943928eda1f1e4518f30c8e3ccf56b46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737788"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Preparar máquinas virtuais para um FCI (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como preparar VMs (máquinas virtuais) do Azure para usá-las com uma SQL Server FCI (instância de cluster de failover). As definições de configuração variam de acordo com a solução de armazenamento FCI, portanto, valide que você está escolhendo a configuração correta para se adequar ao seu ambiente e aos seus negócios. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma assinatura do Microsoft Azure. Comece [gratuitamente](https://azure.microsoft.com/free/). 
- Um domínio do Windows em máquinas virtuais do Azure ou um datacenter local estendido para o Azure com emparelhamento de rede virtual.
- Uma conta que tem permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- Uma rede virtual do Azure e uma sub-rede com um espaço suficiente de endereços IP para estes componentes:
   - Ambas as máquinas virtuais
   - O endereço IP do cluster de failover do Windows
   - Um endereço IP para cada FCI
- DNS configurado na rede do Azure, apontando para os controladores de domínio.

## <a name="choose-an-fci-storage-option"></a>Escolha uma opção de armazenamento FCI

As definições de configuração para sua máquina virtual variam de acordo com a opção de armazenamento que você planeja usar para sua instância de cluster de failover SQL Server. Antes de preparar a máquina virtual, examine as [Opções de armazenamento do FCI disponíveis](failover-cluster-instance-overview.md#storage) e escolha a opção mais adequada ao seu ambiente e às suas necessidades comerciais. Em seguida, selecione cuidadosamente as opções de configuração de VM apropriadas em todo este artigo com base na sua seleção de armazenamento. 

## <a name="configure-vm-availability"></a>Configurar a disponibilidade da VM 

O recurso de cluster de failover requer que as máquinas virtuais sejam colocadas em um [conjunto de disponibilidade](../../../virtual-machines/linux/tutorial-availability-sets.md) ou em uma [zona de disponibilidade](../../../availability-zones/az-overview.md#availability-zones). Se você escolher conjuntos de disponibilidade, poderá usar [grupos de posicionamento de proximidade](../../../virtual-machines/co-location.md#proximity-placement-groups) para localizar as VMs de perto. Na verdade, os grupos de posicionamento de proximidade são um pré-requisito para usar os discos compartilhados do Azure. 

Selecione cuidadosamente a opção de disponibilidade de VM que corresponde à configuração de cluster pretendida: 

- **Discos compartilhados do Azure**: a opção de disponibilidade varia se você estiver usando SSDs ou UltraDisk Premium:
   - SSD Premium: [conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) em diferentes domínios de falha/atualização para SSDs Premium colocados dentro de um [grupo de posicionamento de proximidade](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
   - Ultra Disk: a [zona de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) , mas as VMs devem ser colocadas na mesma zona de disponibilidade, o que reduz a disponibilidade do cluster para 99,9%. 
- **Compartilhamentos de arquivos Premium**: [conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou [zona de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Espaços de armazenamento diretos**: [conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Você não pode definir nem alterar o conjunto de disponibilidade depois de criar uma máquina virtual.

## <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Depois de configurar a disponibilidade da VM, você estará pronto para criar suas máquinas virtuais. Você pode optar por usar uma imagem do Azure Marketplace que faz ou não tem SQL Server já instalado. No entanto, se você escolher uma imagem para SQL Server em VMs do Azure, será necessário desinstalar o SQL Server da máquina virtual antes de configurar a instância de cluster de failover. 

### <a name="considerations"></a>Considerações

Em um cluster de failover de convidado de VM do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A Rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure.

Coloque as duas máquinas virtuais:

- No mesmo grupo de recursos do Azure que seu conjunto de disponibilidade, se você estiver usando conjuntos de disponibilidade.
- Na mesma rede virtual que o controlador de domínio ou em uma rede virtual que tem conectividade adequada com seu controlador de domínio.
- Em uma sub-rede que tenha espaço de endereços IP suficiente para ambas as máquinas virtuais e todas as FCIs que você possa eventualmente usar nesse cluster.
- No conjunto de disponibilidade do Azure ou na zona de disponibilidade.

Você pode criar uma máquina virtual do Azure usando uma imagem [com](sql-vm-create-portal-quickstart.md) ou [sem](../../../virtual-machines/windows/quick-create-portal.md) SQL Server pré-instalado nela. Se você escolher a imagem de SQL Server, precisará desinstalar manualmente a instância de SQL Server antes de instalar a instância de cluster de failover. 


## <a name="uninstall-sql-server"></a>Desinstalar o SQL Server

Como parte do processo de criação de FCI, você instalará SQL Server como uma instância clusterizada no cluster de failover. *Se você implantou uma máquina virtual com uma imagem do Azure Marketplace sem SQL Server, você pode ignorar esta etapa.* Se você implantou uma imagem com SQL Server pré-instalado, precisará cancelar o registro da VM SQL Server da extensão do agente IaaS do SQL e, em seguida, desinstalar o SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Cancelar o registro da extensão do SQL IaaS Agent

SQL Server imagens de VM do Azure Marketplace são automaticamente registradas com a extensão do SQL IaaS Agent. Antes de desinstalar a instância do SQL Server pré-instalado, você deve primeiro [cancelar o registro de cada VM SQL Server da extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Desinstalar o SQL Server

Depois de cancelar o registro da extensão, você pode desinstalar o SQL Server. Siga estas etapas em cada máquina virtual: 

1. Conecte-se à máquina virtual usando o RDP.

   Quando você se conectar pela primeira vez a uma máquina virtual usando o RDP, um aviso perguntará se deseja permitir que o computador seja detectável na rede. Selecione **Sim** na barra superior.

1. Se você estiver usando uma das imagens de máquina virtual com base em SQL Server, remova a instância SQL Server:

   1. Em **Programas e Recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 201_ (64 bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova nada em **recursos compartilhados**. Você verá algo como a seguinte captura de tela:

      ![Selecionar recursos](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Selecione **Avançar** e **Remover**.
   1. Depois que a instância for removida com êxito, reinicie a máquina virtual. 

## <a name="open-the-firewall"></a>Abrir o firewall 

Em cada máquina virtual, abra a porta TCP do firewall do Windows que o SQL Server usa. Por padrão, essa é a porta 1433. Mas você pode alterar a porta SQL Server em uma implantação de VM do Azure, portanto, abra a porta que SQL Server usa em seu ambiente. Essa porta é aberta automaticamente em SQL Server imagens implantadas do Azure Marketplace. 

Se você usar um [balanceador de carga](failover-cluster-instance-vnn-azure-load-balancer-configure.md), também precisará abrir a porta que a investigação de integridade usa. Por padrão, essa é a porta 59999. Mas pode ser qualquer porta TCP que você especificar ao criar o balanceador de carga. 

Esta tabela detalha as portas que talvez você precise abrir, dependendo da configuração do FCI: 

   | Finalidade | Porta | Observações
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente. </br> </br> **Usado por**: todas as configurações de FCI. |
   | Investigação de integridade | TCP 59999 | Qualquer porta TCP aberta. Configure a [investigação de integridade](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) do balanceador de carga e o cluster para usar essa porta. </br> </br> **Usado por**: FCI com o balanceador de carga. |
   | Compartilhamento de arquivo | UDP 445 | Porta que o serviço de compartilhamento de arquivos usa. </br> </br> **Usado por**: FCI com compartilhamento de arquivos premium. |

## <a name="join-the-domain"></a>Ingressar no domínio

Você também precisa unir suas máquinas virtuais ao domínio. Você pode fazer isso usando um [modelo de início rápido](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Examinar a configuração de armazenamento

As máquinas virtuais criadas no Azure Marketplace são fornecidas com o armazenamento anexado. Se você planeja configurar o armazenamento do FCI usando compartilhamentos de arquivos Premium ou discos compartilhados do Azure, você pode remover o armazenamento anexado para economizar em custos porque o armazenamento local não é usado para a instância de cluster de failover. No entanto, é possível usar o armazenamento anexado para Espaços de Armazenamento Diretos soluções FCI, portanto, removê-las nesse caso pode não ser útil. Examine sua solução de armazenamento FCI para determinar se a remoção do armazenamento anexado é ideal para economizar custos. 


## <a name="next-steps"></a>Próximas etapas

Agora que você preparou seu ambiente de máquina virtual, você está pronto para configurar sua instância de cluster de failover. 

Escolha um dos seguintes guias para configurar o ambiente FCI apropriado para sua empresa: 
- [Configurar o FCI com discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Configurar o FCI com um compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Configurar o FCI com Espaços de Armazenamento Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [configurações HADR com suporte](hadr-cluster-best-practices.md). 

Para obter informações adicionais, consulte: 
- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
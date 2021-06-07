---
title: Criar imagens de VM para o dispositivo de GPU Azure Stack Edge Pro
description: Descreve como criar imagens de VM do Linux ou do Windows para usá-las com o dispositivo de GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437665"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Criar imagens de VM personalizadas para o dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implantar VMs no dispositivo Azure Stack Edge Pro, você precisará conseguir criar imagens de VM personalizadas que possam ser usadas para criar VMs. Este artigo descreve as etapas necessárias para criar imagens personalizadas de VM do Windows ou Linux que você pode usar para implantar VMs em seu dispositivo Azure Stack Edge pro.

## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem de VM

O fluxo de trabalho exige que você crie uma máquina virtual no Azure, personalize a VM, generalize-a e baixe o VHD correspondente a ela. Esse VHD generalizado é carregado no Azure Stack Edge pro. Um disco gerenciado é criado a partir desse VHD. Uma imagem é criada a partir do disco gerenciado. E, finalmente, as VMs são criadas a partir dessa imagem.

Para obter mais informações, acesse [Implantar uma VM no dispositivo Azure Stack Edge Pro usando o Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Criar uma imagem de VM personalizada do Windows

Execute as etapas a seguir para criar uma imagem de VM do Windows.

1. Crie uma Máquina Virtual do Windows. Para obter mais informações, acesse [Tutorial: Criar e gerenciar VMs do Windows com o Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Baixe um disco de sistema operacional existente.

    - Siga as etapas descritas em [Baixar um VHD](../virtual-machines/windows/download-vhd.md).

    - Use o comando `sysprep` a seguir em vez do que foi descrito no procedimento anterior.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Confira também [Visão geral do Sysprep (Preparação do Sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Agora use este VHD para criar e implantar uma VM no dispositivo Azure Stack Edge Pro.

## <a name="create-a-linux-custom-vm-image"></a>Criar uma imagem de VM personalizada do Linux

Execute as etapas a seguir para criar uma imagem de VM do Linux.

1. Criar uma Máquina Virtual Linux. Para obter mais informações, acesse [Tutorial: Criar e gerenciar VMs do Linux com a CLI do Azure](../virtual-machines/linux/tutorial-manage-vm.md).

1. Desprovisione a VM. Use o agente de VM do Azure para excluir arquivos e dados específicos do computador. Use o comando `waagent` com o `-deprovision+user` parâmetro em sua VM do Linux de origem. Para obter mais informações, confira [Entender e usar o Agente para Linux do Azure](../virtual-machines/extensions/agent-linux.md).

    1. Conecte-se à VM do Linux com um cliente SSH.
    2. Na janela SSH, digite o seguinte comando:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Execute este comando apenas em uma VM que você irá capturar como uma imagem. Esse comando não garante que a imagem é declarada com relação a todas as informações confidenciais ou está disponível para redistribuição. O `+user` parâmetro também remove a última conta de usuário provisionada. Para manter as credenciais de conta de usuário na VM, use apenas `-deprovision`.
     
    3. Insira **y** para continuar. Você pode adicionar o parâmetro `-force` para evitar esta etapa de confirmação.
    4. Depois de concluir o comando, insira **sair** para fechar o cliente SSH.  A VM ainda estará em execução neste ponto.


1. [Baixe um disco de sistema operacional existente](../virtual-machines/linux/download-vhd.md).

Agora use este VHD para criar e implantar uma VM no dispositivo Azure Stack Edge Pro. Use as duas seguintes imagens do Azure Marketplace para criar imagens personalizadas do Linux:

|Nome do item  |Descrição  |Editor  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |O Ubuntu Server é a distribuição do Linux mais popular do mundo para ambientes em nuvem.|Canônico|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |O Debian GNU/Linux é uma das distribuições mais populares do Linux.     |credativ|

Para obter uma lista completa das imagens do Azure Marketplace que podem funcionar (atualmente não testadas), acesse [Itens do Azure Marketplace disponíveis para o Azure Stack Hub](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Próximas etapas

[Implantar VMs no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
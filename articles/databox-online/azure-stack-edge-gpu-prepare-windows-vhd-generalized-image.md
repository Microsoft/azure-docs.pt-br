---
title: Criar imagens de VM da imagem generalizada do VHD do Windows para seu dispositivo de GPU pro Azure Stack Edge
description: Descreve como imagens de VM de imagens generalizadas a partir de um VHD do Windows ou de um VHDX. Use esta imagem generalizada para criar imagens de VM a serem usadas com VMs implantadas em seu dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962453"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Usar imagem generalizada do VHD do Windows para criar uma imagem de VM para seu dispositivo Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implantar VMs no dispositivo Azure Stack Edge Pro, você precisará conseguir criar imagens de VM personalizadas que possam ser usadas para criar VMs. Este artigo descreve as etapas necessárias para preparar um VHD ou VHDX do Windows para criar uma imagem generalizada. Essa imagem generalizada é usada para criar uma imagem de VM para seu dispositivo Azure Stack Edge pro. 

## <a name="about-preparing-windows-vhd"></a>Sobre como preparar o VHD do Windows

Um VHD ou VHDX do Windows pode ser usado para criar uma imagem *generalizada* ou uma imagem *especializada* . A tabela a seguir resume as principais diferenças entre as imagens *generalizadas* e *especializadas* .


|Tipo de imagem  |Generalizada  |Especializada  |
|---------|---------|---------|
|Destino     |Implantado em qualquer sistema         | Destinado a um sistema específico        |
|Instalação após a inicialização     | Instalação necessária na primeira inicialização da VM.          | A instalação não é necessária. <br> A plataforma ativa a VM.        |
|Configuração     |Nome de host, administrador-usuário e outras configurações específicas de VM necessárias.         |Completamente pré-configurado.         |
|Usado quando     |Criar várias novas VMs a partir da mesma imagem.         |Migrar uma máquina específica ou restaurar uma VM do backup anterior.         |


Este artigo aborda as etapas necessárias para implantar a partir de uma imagem generalizada. Para implantar de uma imagem especializada, consulte [usar VHD do Windows especializado](azure-stack-edge-placeholder.md) para seu dispositivo.

> [!IMPORTANT]
> Esse procedimento não abrange casos em que o VHD de origem está configurado com configurações e definições personalizadas. Por exemplo, podem ser necessárias ações adicionais para generalizar um VHD que contém regras de firewall ou configurações de proxy personalizadas. Para obter mais informações sobre essas ações adicionais, consulte [preparar um VHD do Windows para carregar no Azure-máquinas virtuais do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem de VM

O fluxo de trabalho de alto nível para preparar um VHD do Windows para uso como uma imagem generalizada tem as seguintes etapas:

1. Converta o VHD de origem ou VHDX em um VHD de tamanho fixo.
1. Crie uma VM no Hyper-V usando o VHD fixo.
1. Conecte-se à VM do Hyper-V.
1. Generalizar o VHD usando o utilitário *Sysprep* . 
1. Copie a imagem generalizada para o armazenamento de BLOBs.
1. Use a imagem generalizada para implantar VMs em seu dispositivo. Para obter mais informações, consulte como [implantar uma VM por meio de portal do Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) ou [implantar uma VM por meio do PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de preparar um VHD do Windows para uso como uma imagem generalizada no Azure Stack Edge, verifique se:

- Você tem um VHD ou um VHDX que contém uma versão com suporte do Windows. Consulte [sistemas operacionais convidados com suporte]() para seu Azure Stack Edge pro. 
- Você tem acesso a um cliente do Windows com o Gerenciador do Hyper-V instalado. 
- Você tem acesso a uma conta de armazenamento de BLOBs do Azure para armazenar seu VHD após sua preparação.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Preparar uma imagem do Windows generalizada do VHD

## <a name="convert-to-a-fixed-vhd"></a>Converter em um VHD fixo 

Para seu dispositivo, você precisará de VHDs de tamanho fixo para criar imagens de VM. Você precisará converter seu VHD do Windows de origem ou VHDX em um VHD fixo. Siga estas etapas:

1. Abra o Gerenciador do Hyper-V no seu sistema cliente. Vá para **Editar disco**.

    ![Abrir o Gerenciador do Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Na página **antes de começar** , selecione **Avançar>**.

1. Na página **localizar disco rígido virtual** , navegue até o local do VHD do Windows de origem ou VHDX que você deseja converter. Selecione **avançar>**.

    ![Página localizar disco rígido virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Na página **escolher ação** , selecione **converter** e selecione **Avançar>**.

    ![Página escolher ação](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Na página **escolher formato de disco** , selecione formato **VHD** e, em seguida, selecione **próximo>**.

   ![Página escolher formato de disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Na página **escolher tipo de disco** , escolha **tamanho fixo** e selecione **Avançar>**.

   ![Página escolher tipo de disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Na página **Configurar disco** , navegue até o local e especifique um nome para o disco VHD de tamanho fixo. Selecione **avançar>**.

   ![Página Configurar disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Examine o resumo e selecione **Concluir**. A conversão de VHD ou VHDX leva alguns minutos. O tempo de conversão depende do tamanho do disco de origem. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Você usará esse VHD fixo para todas as etapas subsequentes neste artigo.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Criar uma VM do Hyper-V do VHD fixo

1. No **Gerenciador do Hyper-V**, no painel escopo, clique com o botão direito do mouse no nó do sistema para abrir o menu de contexto e selecione **nova**  >  **máquina virtual**.

    ![Selecionar nova máquina virtual no painel escopo](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Na página **antes de começar** do assistente de nova máquina virtual, selecione **Avançar**.

1. Na página **especificar nome e local** , forneça um **nome** e um **local** para sua máquina virtual. Selecione **Avançar**.

    ![Especifique o nome e o local da sua VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Na página **especificar geração** , escolha **geração 1** para o tipo de imagem do dispositivo. vhd e, em seguida, selecione **Avançar**.    

    ![Especificar geração](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Atribua suas configurações de rede e de memória desejadas.

1. Na página **conectar disco rígido virtual** , escolha **usar um disco rígido virtual existente**, ESPECIFIQUE o local do VHD fixo do Windows que criamos anteriormente e, em seguida, selecione **Avançar**.

    ![Conecte-se à página de disco rígido virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Examine o **Resumo** e selecione **concluir** para criar a máquina virtual.

A máquina virtual leva vários minutos para ser criada.
     

## <a name="connect-to-the-hyper-v-vm"></a>Conectar-se à VM do Hyper-V

A VM é mostrada na lista de máquinas virtuais no seu sistema cliente. 


1. Selecione a VM e clique com o botão direito do mouse e selecione **Iniciar**. 

    ![Selecione a VM e inicie-a](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. A VM mostra mostrar como **em execução**. Selecione a VM e clique com o botão direito do mouse e selecione **conectar**.

    ![Conectar-se a uma VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Depois que você estiver conectado à VM, conclua o assistente de configuração do computador e entre na VM.


## <a name="generalize-the-vhd"></a>Generalizar o VHD  

Use o utilitário *Sysprep* para generalizar o VHD. 

1. Dentro da VM, abra um prompt de comando.
1. Execute o comando a seguir para generalizar o VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Para obter detalhes, consulte  [visão geral do Sysprep (preparação do sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Depois que o comando for concluído, a VM será desligada. **Não reinicie a VM**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Carregar o VHD no armazenamento de BLOBs do Azure

O VHD agora pode ser usado para criar uma imagem generalizada no Azure Stack Edge. 

1. Carregue o VHD no armazenamento de BLOBs do Azure. Consulte as instruções detalhadas em [carregar um VHD usando Gerenciador de armazenamento do Azure](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Depois que o upload for concluído, você poderá usar a imagem carregada para criar imagens de VM e VMs. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Próximas etapas

Dependendo da natureza da implantação, você pode escolher um dos procedimentos a seguir.

- [Implantar uma VM de uma imagem generalizada via portal do Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Implantar uma VM de uma imagem generalizada via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  

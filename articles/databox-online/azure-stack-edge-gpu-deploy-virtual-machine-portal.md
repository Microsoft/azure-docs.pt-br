---
title: Como implantar VMs em seu Azure Stack Edge pro por meio do portal do Azure
description: Saiba como criar e gerenciar VMs em seu Azure Stack Edge pro por meio do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a48abb34a201928185130fbe8fdf9592e77492d9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944952"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Implantar VMs em seu dispositivo Azure Stack Edge pro GPU via portal do Azure

Você pode criar e gerenciar máquinas virtuais (VMs) em um dispositivo Azure Stack Edge usando portal do Azure, modelos, Azure PowerShell cmdlets e por meio de scripts de CLI do Azure/Python. Este artigo descreve como criar e gerenciar uma VM em seu dispositivo Azure Stack Edge usando o portal do Azure. 

Este artigo aplica-se a Azure Stack Edge pro GPU, Azure Stack borda pro R e Azure Stack dispositivos mini R do Edge. 

> [!IMPORTANT] 
> Recomendamos que você habilite a autenticação multifator para o usuário que gerencia as VMs implantadas em seu dispositivo a partir da nuvem.
        
## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação da VM

O resumo de alto nível do fluxo de trabalho de implantação é o seguinte:

1. Habilite uma interface de rede para computação em seu dispositivo Azure Stack Edge. Isso cria um comutador virtual na interface de rede especificada.
1. Habilite o gerenciamento de nuvem de máquinas virtuais do portal do Azure.
1. Carregue um VHD em uma conta de armazenamento do Azure usando Gerenciador de Armazenamento. 
1. Use o VHD carregado para baixar o VHD no dispositivo e criar uma imagem de VM do VHD. 
1. Use os recursos criados nas etapas anteriores:
    1. Imagem de VM que você criou.
    1. VSwitch associado à interface de rede na qual você habilitou a computação.
    1. Sub-rede associada ao VSwitch.

    E crie ou especifique os seguintes recursos embutidos:
    1. Nome da VM, escolha um tamanho de VM com suporte, credenciais de entrada para a VM. 
    1. Crie novos discos de dados ou anexe discos de dados existentes.
    1. Configure o IP estático ou dinâmico para a VM. Se fornecer um IP estático, escolha um IP livre no intervalo de sub-rede da interface de rede habilitada para computação.

    Use os recursos acima para criar uma máquina virtual.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerenciar VMs em seu dispositivo por meio do portal do Azure, verifique se:

1. Você concluiu as configurações de rede em seu dispositivo Azure Stack Edge pro, conforme descrito na [etapa 1: configurar Azure Stack dispositivo pro Edge](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. Você habilitou uma interface de rede para computação. Esse IP do adaptador de rede é usado para criar um comutador virtual para a implantação da VM. Na interface do usuário local do seu dispositivo, vá para **computação**. Selecione o adaptador de rede que será usado para criar um comutador virtual.

        > [!IMPORTANT] 
        > Você só pode configurar uma porta para computação.

    1. Habilite a computação no adaptador de rede. O Azure Stack Edge Pro criará e gerenciará um comutador virtual correspondente a esse adaptador de rede.

1. Você tem acesso a um VHD do Windows ou Linux que será usado para criar a imagem da VM para a máquina virtual que você pretende criar.

## <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Siga estas etapas para criar uma máquina virtual em seu dispositivo de Azure Stack Edge.

### <a name="add-a-vm-image"></a>Adicionar uma imagem de VM

1. Carregar um VHD em uma conta de armazenamento do Azure. Siga as etapas em [carregar um VHD usando Gerenciador de armazenamento do Azure](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. Na portal do Azure, acesse o recurso de borda do Azure Stack para seu dispositivo de borda Azure Stack. Vá para a **computação de borda > máquinas virtuais**.

    ![Adicionar imagem de VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Selecione **máquinas virtuais** para ir para a página **visão geral** . **Habilite** o gerenciamento de nuvem de máquina virtual.
    ![Adicionar imagem de VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. A primeira etapa é adicionar uma imagem de VM. Você já carregou um VHD na conta de armazenamento na etapa anterior. Você usará esse VHD para criar uma imagem de VM.

    Selecione **Adicionar imagem** para baixar o VHD da conta de armazenamento e adicionar ao dispositivo. O processo de download leva vários minutos, dependendo do tamanho do VHD e da largura de banda da Internet disponível para o download. 

    ![Adicionar imagem de VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Na folha **Adicionar imagem** , insira os seguintes parâmetros. Selecione **Adicionar**.


    |Parâmetro  |Descrição  |
    |---------|---------|
    |Baixar do blob de armazenamento    |Navegue até o local do blob de armazenamento na conta de armazenamento em que você carregou o VHD.         |
    |Baixar para    | Definido automaticamente para o dispositivo atual em que você está implantando a máquina virtual.        |
    |Salvar imagem como      | O nome da imagem de VM que você está criando a partir do VHD que você carregou para a conta de armazenamento.        |
    |Tipo do SO     |Escolha entre Windows ou Linux como o sistema operacional do VHD que será usado para criar a imagem da VM.         |
   

    ![Adicionar imagem de VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. O VHD é baixado e a imagem da VM é criada. A criação da imagem leva vários minutos para ser concluída. Você verá uma notificação para a conclusão bem-sucedida da imagem da VM.

    ![Adicionar imagem de VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Depois que a imagem da VM for criada com êxito, ela será adicionada à lista de imagens na folha **imagens** .
    ![Adicionar imagem de VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    A folha **implantações** é atualizada para indicar o status da implantação.

    ![Adicionar imagem de VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    A imagem recém-adicionada também é exibida na página **visão geral** .
    ![Adicionar imagem de VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Adicionar uma VM

Siga estas etapas para criar uma VM depois de criar uma imagem de VM.

1. Na página **visão geral** , selecione **Adicionar máquina virtual**.

    ![Adicionar VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Na guia **noções básicas** , insira os parâmetros a seguir.


    |Parâmetro |Descrição  |
    |---------|---------|
    |Nome da máquina virtual     |         |
    |Imagem     | Selecione entre as imagens de VM disponíveis no dispositivo.        |
    |Tamanho     | Escolha entre os [tamanhos de VM com suporte](azure-stack-edge-gpu-virtual-machine-sizes.md).        |
    |Nome de Usuário     | Use o nome de usuário padrão *azureuser*.        |
    |Tipo de autenticação    | Escolha uma chave pública SSH ou uma senha definida pelo usuário.       |
    |Senha     | Insira uma senha para entrar na máquina virtual. A senha deve ter pelo menos 12 caracteres e atender aos requisitos de [complexidade](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)definidos.        |
    |Confirmar senha    | Digite a senha novamente.        |


    ![Adicionar VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Selecione **Avançar: Discos**.

1. Na guia **discos** , você anexará discos à sua VM. 
    
    1. Você pode optar por **criar e anexar um novo disco** ou **anexar um disco existente**.

        ![Adicionar VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Selecione **criar e anexar um novo disco**. Na folha **criar novo disco** , forneça um nome para o disco e o tamanho em Gib.

        ![Adicionar VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Repita o procedimento acima para ser processado para adicionar mais discos. Depois que os discos são criados, eles aparecem na guia **discos** .

        ![Adicionar VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Selecione **Avançar: Rede**.

1. Na guia **rede** , você configurará a conectividade de rede para sua VM.

    
    |Parâmetro  |Descrição |
    |---------|---------|
    |Rede virtual    | Na lista suspensa, selecione o comutador virtual criado no dispositivo Azure Stack Edge quando você habilitou a computação na interface de rede.    |
    |Sub-rede     | Esse campo é preenchido automaticamente com a sub-rede associada à interface de rede na qual você habilitou a computação.         |
    |Endereço IP     | Forneça um IP estático ou dinâmico para sua VM. O IP estático deve ser um IP disponível e livre do intervalo de sub-rede especificado.        |

    ![Adicionar VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Selecione **Avançar: Examinar + Criar**.

1. Na guia **revisar + criar** , examine as especificações da VM e selecione **criar**.

    ![Adicionar VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. A criação da VM é iniciada e pode levar até 20 minutos. Você pode ir para **implantações** para monitorar a criação da VM.

    ![Adicionar VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Depois que a VM for criada com êxito, a página **visão geral** será atualizada para exibir a nova VM.

    ![Adicionar VM 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Selecione a VM recém-criada para ir para **máquinas virtuais**.

    ![Adicionar VM 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Selecione a VM para ver os detalhes. 

    ![Adicionar VM 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Como conectar-se a uma VM

Dependendo se você criou uma VM do Windows ou do Linux, as etapas para conectar podem ser diferentes. Você não pode se conectar às VMs implantadas em seu dispositivo por meio do portal do Azure. Você precisa executar as etapas a seguir para se conectar à VM Linux ou Windows.

### <a name="connect-to-linux-vm"></a>Conectar-se à VM do Linux

Siga estas etapas para se conectar a uma VM do Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Conectar-se à VM do Windows

Siga estas etapas para se conectar a uma VM do Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber como administrar seu dispositivo Azure Stack Edge pro, consulte[usar interface do usuário da Web local para administrar um Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

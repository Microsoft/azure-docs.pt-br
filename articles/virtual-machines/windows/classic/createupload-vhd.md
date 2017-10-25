---
title: Criar e carregar uma imagem de VM usando o PowerShell | Microsoft Docs
description: "Saiba como criar e carregar uma imagem do Windows Server generalizada usando o modelo de implantação clássico e o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: bc75c8cdd98b0ea0fbff6483c0e3c9d4468d3941
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Criar e carregar um VHD do Windows Server no Azure
Esse artigo mostra como carregar sua própria imagem de VM generalizada como um VHD (disco rígido virtual) para que o use para criar outras máquinas virtuais. Para mais detalhes sobre discos e os VHDs no Microsoft Azure, confira a seção [Sobre discos e VHDs para Máquinas Virtuais](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Você também pode [carregar](../upload-generalized-managed.md) uma máquina virtual usando o modelo do Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você tem:

* **Uma assinatura do Azure** - se não tiver uma, você poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)** – você tem o módulo do Microsoft Azure PowerShell instalado e configurado para usar sua assinatura.
* **Um arquivo .VHD** - sistema operacional Windows para o qual há suporte, armazenado em um arquivo .vhd e anexado a uma máquina virtual. Verifique se há suporte para as funções de servidor em execução no VHD no Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet convert-vhd](http://technet.microsoft.com/library/hh848454.aspx). Consulte esta [publicação de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)para obter detalhes.

## <a name="step-1-prep-the-vhd"></a>Etapa 1: preparar o VHD
Antes de carregar o VHD no Azure, ele precisa ser generalizado usando a ferramenta Sysprep. Ela prepara o VHD a ser usado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). Faça backup da VM antes de executar o Sysprep.

Na máquina virtual na qual o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Entre no sistema operacional.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.

    ![Abrir una janela de Prompt de comando](./media/createupload-vhd/sysprep_commandprompt.png)
3. A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

   ![Inicie o Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE (Configuração inicial pelo usuário)** e verifique se a opção **Generalizar** está marcada.
5. Em **Opções de Desligamento**, selecione **Desligar**.
6. Clique em **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Etapa 2: criar uma conta de armazenamento e um contêiner
Você precisa de uma conta de armazenamento no Azure para que você tenha um local para carregar o arquivo .vhd. Esta etapa mostra como criar uma conta, ou obter as informações de que você precisa de uma conta existente. Substitua as variáveis entre &lsaquo; colchetes &rsaquo; com suas próprias informações.

1. Logon

    ```powershell
    Add-AzureAccount
    ```

2. Defina sua assinatura do Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Criar uma nova conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo, contendo 3 a 24 caracteres. O nome pode ser qualquer combinação de letras e números. Você também precisará especificar uma localização, como "Leste dos EUA"

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Defina a nova conta de armazenamento como a padrão.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Criar um novo contêiner.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Etapa 3: Carregar o arquivo .vhd
Você usará o cmdlet [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) para carregar o VHD.

Na janela do Azure PowerShell usada na etapa anterior, digite o comando a seguir e substitua as variáveis entre &lsaquo; colchetes &rsaquo; por suas próprias informações.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Etapa 4: adicionar a imagem à sua lista de imagens personalizadas
Use o cmdlet [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) para adicionar a imagem à lista de suas imagens personalizadas.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Próximas etapas
Agora você pode [criar uma VM personalizada](createportal.md) usando a imagem carregada.

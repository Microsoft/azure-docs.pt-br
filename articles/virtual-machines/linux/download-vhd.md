---
title: Baixar um VHD do Linux por meio do Azure | Microsoft Docs
description: Baixe um VHD do Linux usando a CLI do Azure e o portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: e7325282c6442a927f47df3e50efbfda481754fd
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667918"
---
# <a name="download-a-linux-vhd-from-azure"></a>Baixar um VHD do Linux por meio do Azure

Neste artigo, você aprende a baixar um arquivo de VHD (disco rígido virtual) do Linux por meio do Azure usando a CLI do Azure e o portal do Azure. 

Se você ainda não fez isso, instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Pare a VM.

Não é possível baixar um VHD por meio do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar um VHD. Se desejar usar um VHD como uma [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, você precisará desprovisionar e generalizar o sistema operacional contido no arquivo e parar a VM. Para usar o VHD como um disco de uma nova instância de uma VM existente ou um disco de dados, basta parar e desalocar a VM.

Para usar o VHD como uma imagem para criar outras VMs, conclua estas etapas:

1. Use o SSH, o nome da conta e o endereço IP público da VM para se conectar a ela e desprovisioná-la. É possível localizar o endereço IP público com [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). O parâmetro +user também remove a última conta de usuário provisionada. Se estiver trazendo as credenciais de conta para a VM, exclua esse parâmetro +user. O seguinte exemplo remove a última conta de usuário provisionada:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Entre em sua conta do Azure com [az login](https://docs.microsoft.com/cli/azure/reference-index).
3. Pare e desaloque a VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize a VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para usar o VHD como um disco de uma nova instância de uma VM existente ou um disco de dados, conclua estas etapas:

1.  Entre no [Portal do Azure](https://portal.azure.com/).
2.  No menu Hub, clique em **Máquinas Virtuais**.
3.  Selecione a VM na lista.
4.  Na folha da VM, clique em **Parar**.

    ![Parar a VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar a URL de SAS

Para baixar o arquivo VHD, você precisa gerar uma URL de [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando a URL é gerada, uma hora de expiração é atribuída à URL.

1.  No menu da folha da VM, clique em **Discos**.
2.  Selecione o disco do sistema operacional da VM e, em seguida, clique em **Exportar**.
3.  Clique em **Gerar URL**.

    ![Gerar a URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Baixar o VHD

1.  Na URL que foi gerada, clique em Baixar o arquivo VHD.

    ![Baixar o VHD](./media/download-vhd/export-download.png)

2.  Talvez você precise clicar em **Salvar** no navegador para iniciar o download. O nome padrão do arquivo VHD é *abcd*.

    ![Clicar em Salvar no navegador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [carregar e criar uma VM do Linux com base em um disco personalizado com a CLI do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerenciar discos do Azure com a CLI do Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


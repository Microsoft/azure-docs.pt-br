---
title: Criar e copiar da sua VM Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como criar uma cópia de sua máquina virtual Linux do Azure com a CLI do Azure 1.0 no modelo de implantação do Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Criar uma cópia de uma máquina virtual Linux em execução no Azure com a CLI do Azure 1.0
Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux no modelo de implantação do Resource Manager. Primeiro, você copia o sistema operacional e os discos de dados para um novo contêiner e, em seguida, configura os recursos de rede para criar a nova máquina virtual.

Você também pode [carregar e criar uma VM com base em uma imagem de disco personalizada](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- CLI 1.0 do Azure – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="before-you-begin"></a>Antes de começar
Verifique se você atende os seguintes pré-requisitos antes de iniciar as etapas:

* Você tem a [CLI do Azure](../../cli-install-nodejs.md) baixada e instalada em seu computador. 
* Você também precisa de algumas informações sobre sua VM Linux do Azure existente:

| Informações sobre a VM de origem | Onde obter |
| --- | --- |
| Nome da VM |`azure vm list` |
| Nome do Grupo de Recursos |`azure vm list` |
| Local |`azure vm list` |
| Nome da Conta de Armazenamento |`azure storage account list -g <resourceGroup>` |
| Nome do contêiner |`azure storage container list -a <sourcestorageaccountname>` |
| Nome do arquivo VHD da VM de origem |`azure storage blob list --container <containerName>` |

* Você precisará fazer algumas escolhas quanto a sua nova VM:    <br> -Nome do contêiner    <br> -Nome da VM    <br> -Tamanho da VM    <br> -Nome da VNet    <br> -Nome da sub-rede    <br> -Nome IP    <br> -Nome da NIC

## <a name="login-and-set-your-subscription"></a>Faça logon e configure sua assinatura
1. Faça logon na CLI.

    ```azurecli
    azure login
    ```
2. Certifique-se de estar no modo Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Configure a assinatura correta. Você pode usar “azure account list” para ver todas as suas assinaturas.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Pare a VM.
Pare e desaloque a VM de origem. Você pode usar “azure vm list” para obter uma lista de todas as VMs em sua assinatura e os nomes de seus grupos de recursos.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Copie o VHD
Você pode copiar o VHD do armazenamento de origem para o destino usando o `azure storage blob copy start`. Neste exemplo, vamos copiar o VHD para a mesma conta de armazenamento, mas para um contêiner diferente.

Para copiar o VHD para outro contêiner na mesma conta de armazenamento, digite:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configure a rede virtual para sua nova VM
Configure uma rede virtual e NIC para sua nova VM. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Crie a nova VM
Agora, você pode criar uma VM com base no disco virtual carregado [usando um modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou por meio da CLI, especificando o URI para seu disco copiado digitando:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Próximas etapas
Para saber como usar a CLI do Azure para gerenciar sua nova máquina virtual, consulte [Comandos da CLI do Azure para o Azure Resource Manager](../azure-cli-arm-commands.md).


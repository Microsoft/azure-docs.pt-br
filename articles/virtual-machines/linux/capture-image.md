---
title: Capturar uma imagem de uma VM Linux no Azure usando a CLI 2.0 | Microsoft Docs
description: "Capture uma imagem de uma VM do Azure a ser usada para implantações em massa usando a CLI do Azure 2.0."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 19b573f77f2ee84600955d00d30bdb16c84e3623
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou de um VHD

<!-- generalize, image - extended version of the tutorial-->

Para criar várias cópias de uma máquina virtual (VM) para usar no Azure, capture uma imagem de VM ou do VHD do sistema operacional. Para criar uma imagem, você precisa remover informações de conta pessoal, o que torna mais seguro implantar várias vezes. Nas etapas a seguir, desprovisione uma VM existente, desaloque e crie uma imagem. Você pode usar essa imagem para criar VMs em qualquer grupo de recursos dentro da sua assinatura.

Se você quiser criar uma cópia da VM Linux existente para backup ou depuração ou então carregar um VHD Linux especializado de uma VM local, consulte [Carregar e criar uma VM Linux com base em uma imagem de disco personalizada](upload-vhd.md).  

Você também pode usar o **Packer** para criar sua configuração personalizada. Para obter mais informações sobre como usar o Packer, consulte [Como usar o Packer para criar imagens de máquina virtual Linux no Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Antes de começar
Verifique se os seguintes pré-requisitos foram atendidos:

* Você precisa de uma VM do Azure criada no modelo de implantação do Resource Manager usando Managed Disks. Se você ainda não criou uma VM do Linux, pode usar o [portal](quick-create-portal.md), a [CLI do Azure](quick-create-cli.md) ou [modelos do Resource Manager](create-ssh-secured-vm-from-template.md). Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md), aplicar atualizações e instalar aplicativos. 

* Também é preciso ter a [CLI do Azure 2.0](/cli/azure/install-az-cli2) mais recente instalada e estar conectado a uma conta do Azure usando [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandos rápidos

Para uma versão simplificada deste tópico, para testar, avaliando ou aprender sobre VMs no Azure, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Etapa 1: desprovisionar a VM
Você desprovisiona a VM usando o agente de VM do Azure para excluir arquivos e dados específicos do computador. Use o comando `waagent` com o parâmetro *-deprovision+user* na VM Linux de origem. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](../windows/agent-user-guide.md).

1. Conecte-se à VM do Linux usando um cliente SSH.
2. Na janela SSH, digite o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Execute o comando apenas em uma VM que você pretende capturar como uma imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição. O parâmetro *+user* também remove a última conta de usuário provisionada. Se você quiser manter as credenciais de conta na VM, basta usar *-deprovision* para manter a conta de usuário.
 
3. Digite **y** para continuar. Você pode adicionar o parâmetro **-force** para evitar essa etapa de confirmação.
4. Após a conclusão do comando, digite **sair**. Esta etapa fecha o cliente SSH.

## <a name="step-2-create-vm-image"></a>Etapa 2: Criar a imagem de VM
Use a CLI do Azure 2.0 para marcar a VM como generalizada e capturar a imagem. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myVnet* e *myVM*.

1. Desaloque a VM desprovisionada com [az vm deallocate](/cli//azure/vm#deallocate). O exemplo a seguir desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Marque a VM como generalizada com [az vm generalize](/cli//azure/vm#generalize). O exemplo a seguir marca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup* como generalizada:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Agora crie uma imagem do recurso da VM com [az image create](/cli//azure/image#create). O exemplo a seguir cria uma imagem denominada *myImage* no grupo de recursos denominado *myResurceGroup* usando o recurso de VM denominado *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos da VM de origem. Você pode criar VMs em qualquer grupo de recursos em sua assinatura desde esta imagem. De uma perspectiva de gerenciamento, você poderá criar um grupo de recursos específicos para seus recursos de máquina virtual e imagens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Etapa 3: criar uma VM com base na imagem capturada
Criar uma máquina virtual usando a imagem criada com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVMDeployed* por meio da imagem chamada *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criação da VM em outro grupo de recursos 

Com os discos gerenciados, você pode criar VMs de uma imagem em qualquer grupo de recursos em sua assinatura. Para criar uma máquina virtual em um grupo de recursos diferente do da imagem, especifique a ID de recurso completa para a imagem. Use [az image list](/cli/azure/image#list) para exibir uma lista de imagens. A saída deverá ser semelhante ao seguinte exemplo:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo a seguir usa [az vm create](/cli/azure/vm#create) para criar uma máquina virtual em um grupo de recursos diferente do da imagem de origem, especificando a ID de recurso da imagem:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Etapa 4: verificar a implantação

Agora, use o SSH na máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP ou o FQDN da VM com [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Próximas etapas
Você pode criar várias máquinas virtuais da sua imagem de VM de origem. Se você precisar fazer alterações em sua imagem: 

- Crie uma VM usando sua imagem.
- Faça quaisquer atualizações ou alterações de configuração.
- Siga as etapas novamente para desprovisionar, desalocar, generalizar e criar uma imagem.
- Use essa nova imagem para implantações futuras. Se desejar, exclua a imagem original.

Para saber mais sobre como gerenciar suas VMs com a CLI, confira [CLI 2.0 do Azure](/cli/azure/overview).

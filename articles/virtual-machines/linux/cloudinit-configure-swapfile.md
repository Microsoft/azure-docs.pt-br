---
title: Usar cloud-init para configurar um arquivo de permuta em uma VM Linux | Microsoft Docs
description: Como usar o cloud-init para configurar um arquivo de permuta em uma VM Linux durante a criação com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: adf03ea912a028c1059683c49350dea3743ee7a6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671711"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Usar cloud-init para configurar um arquivo de permuta em uma VM Linux
Este artigo mostra como usar a [cloud-init](https://cloudinit.readthedocs.io) para configurar o arquivo de permuta em várias distribuições do Linux. O arquivo de permuta tradicionalmente foi configurado pelo Agente Linux (WALA) com base em quais distribuições exigem um.  Este documento detalhará o processo para criar o arquivo de permuta sob demanda durante o tempo de provisionamento usando a cloud-init.  Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Criar arquivo de permuta para imagens baseadas em Ubuntu
Por padrão no Azure, as imagens da galeria do Ubuntu não criam arquivos de permuta. Para habilitar a configuração de arquivo de permuta durante o tempo de provisionamento de VM usando a cloud-init, consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) na wiki do Ubuntu.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Criar o arquivo de permuta para Red Hat e imagens baseadas em CentOS

Crie um arquivo em seu shell atual chamado *cloud_init_swapfile.txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud_init_swapfile.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Verifique se o arquivo cloud-init inteiro foi copiado corretamente, principalmente a primeira linha.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_swapfile.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Verificar se o arquivo de permuta foi criado
Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Uma vez que você tiver se conectado por SSH à VM, verifique se o arquivo de permuta foi criado

```bash
swapon -s
```

A saída desse comando deve ter esta aparência:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se você tiver uma imagem do Azure que tem um arquivo de permuta configurado e desejar alterar a configuração do arquivo de permuta para novas imagens, você deverá remover o arquivo de permuta existente. Consulte o documento 'Personalizar imagens para provisionar por cloud-init' para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Adicionar um usuário do Linux adicional a uma VM](cloudinit-add-user.md)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)

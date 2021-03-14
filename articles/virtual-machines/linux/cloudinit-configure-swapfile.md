---
title: Usar Cloud-init para configurar uma partição de permuta em uma VM do Linux
description: Como usar Cloud-init para configurar uma partição de permuta em uma VM do Linux durante a criação com o CLI do Azure
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559368"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Usar Cloud-init para configurar uma partição de permuta em uma VM do Linux
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para configurar a partição de permuta em várias distribuições do Linux. A partição de permuta foi tradicionalmente configurada pelo agente do Linux (WALA) com base em quais distribuições exigiam uma.  Este documento descreverá o processo de criação da partição de permuta sob demanda durante o tempo de provisionamento usando Cloud-init.  Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Criar partição de permuta para imagens baseadas no Ubuntu
Por padrão no Azure, as imagens da galeria do Ubuntu não criam partições de permuta. Para habilitar a configuração da partição de permuta durante o tempo de provisionamento da VM usando Cloud-init, consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) no wiki do Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Criar partição de permuta para imagens baseadas em Red Hat e CentOS

Crie um arquivo em seu shell atual chamado *cloud_init_swappart.txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud_init_swappart.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Verifique se o arquivo cloud-init inteiro foi copiado corretamente, principalmente a primeira linha.  

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

Agora, crie uma VM com [az vm create](/cli/azure/vm) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_swappart.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verificar se a partição de permuta foi criada
Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Depois que você tiver SSH'ed na VM, verifique se a partição de permuta foi criada

```bash
swapon -s
```

A saída desse comando deve ter esta aparência:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se você tiver uma imagem do Azure existente que tenha uma partição de permuta configurada e quiser alterar a configuração da partição de permuta para novas imagens, deverá remover a partição de permuta existente. Consulte o documento 'Personalizar imagens para provisionar por cloud-init' para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)

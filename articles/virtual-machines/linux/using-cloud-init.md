---
title: Usar cloud-init para personalizar uma VM Linux | Microsoft Docs
description: "Como usar cloud-init para personalizar uma VM Linux durante a criação com a CLI do Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Usar inicialização de nuvem para personalizar uma VM Linux no Azure
Este artigo mostra como usar a [inicialização de nuvem](https://cloudinit.readthedocs.io) para definir o nome do host, atualizar pacotes instalados e gerenciar contas de usuário em uma VM (máquina virtual) no Azure. Esses scripts de inicialização de nuvem são executados na inicialização quando você cria uma VM com a CLI do Azure 2.0. Para obter uma visão mais detalhada sobre como instalar aplicativos, gravar arquivos de configuração e injetar chaves do Key Vault, consulte [este tutorial](tutorial-automate-vm-deployment.md). Você também pode executar essas etapas com a [CLI do Azure 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Visão geral da inicialização de nuvem
[Inicialização de nuvem](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como a inicialização de nuvem é executada durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Estamos trabalhando com parceiros para incluir a inicialização de nuvem e trabalhar nas imagens que eles fornecem para o Azure. A tabela a seguir descreve a disponibilidade de inicialização de nuvem atual nas imagens da plataforma Azure:

| Alias | Editor | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canônico |UbuntuServer |16.04-LTS |mais recente |
| UbuntuLTS |Canônico |UbuntuServer |14.04.5-LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |


## <a name="set-the-hostname-with-cloud-init"></a>Defina o nome do host com a inicialização de nuvem
Arquivos de inicialização de nuvem são gravados no [YAML](http://www.yaml.org). Para executar um script de inicialização de nuvem quando você cria uma VM no Azure com [az vm create](/cli/azure/vm#create), especifique o arquivo de inicialização de nuvem com a opção `--custom-data`. Examinaremos alguns exemplos do que você pode configurar com um arquivo de inicialização de nuvem. Um cenário comum é definir o nome do host de uma VM. Por padrão, o nome do blob é idêntico ao nome da VM. 

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria o grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

No shell atual, crie um arquivo chamado *cloud_init_hostname.txt* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Você pode usar qualquer editor que queira. No Cloud Shell, insira `sensible-editor cloud_init_hostname.txt` para criar o arquivo e ver uma lista de editores disponíveis. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
hostname: myhostname
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#create) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_hostname.txt` da seguinte maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Depois de criado, a CLI do Azure mostra informações sobre a VM. Use o `publicIpAddress` para conectar por SSH à VM. Insira seu próprio endereço da seguinte maneira:

```bash
ssh azureuser@publicIpAddress
```

Para ver o nome da VM, use o comando `hostname` da seguinte maneira:

```bash
hostname
```

A VM deve relatar o nome de host como esse valor definido no arquivo de inicialização de nuvem, conforme mostrado no exemplo de saída a seguir:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Atualizar uma VM com a inicialização de nuvem
Para fins de segurança, convém configurar uma VM para aplicar as atualizações mais recentes na primeira inicialização. Como a inicialização de nuvem funciona em diferentes distribuições de Linux, não é necessário especificar `apt` ou `yum` para o gerenciador de pacotes. Em vez disso, você define `package_upgrade` e permite que o processo de inicialização de nuvem determine o mecanismo apropriado para a distribuição em uso. Esse fluxo de trabalho permite que você use os mesmos scripts de inicialização de nuvem entre distribuições.

Para ver o processo de atualização em ação, crie um arquivo de inicialização de nuvem chamado *cloud_init_upgrade.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#create) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_upgrade.txt` da seguinte maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio endereço IP público da seguinte maneira:

```bash
ssh azureuser@publicIpAddress
```

Execute a ferramenta de gerenciamento de pacotes e verifique se há atualizações. O exemplo a seguir usa `apt-get` em uma VM do Ubuntu:

```bash
sudo apt-get upgrade
```

Já que a inicialização de nuvem verificou em busca de atualizações e instalou-as no ato da inicialização, não existem atualizações para aplicar, conforme mostrado no exemplo de saída a seguir:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Adicionar um usuário a uma VM com a inicialização de nuvem
Uma das primeiras tarefas em qualquer nova VM Linux é adicionar um usuário para você ou para evitar o uso de *root*. As chaves de SSH são uma prática recomendada para segurança e usabilidade. As chaves são adicionadas ao arquivo *~/.ssh/authorized_keys* com esse script de inicialização de nuvem.

Para adicionar um usuário a uma VM Linux, crie um arquivo de inicialização de nuvem chamado *cloud_init_add_user.txt* e cole a configuração a seguir. Forneça o valor de sua própria chave pública (assim como o conteúdo de *~/.ssh/id_rsa.pub*) para *ssh-authorized-keys*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#create) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_add_user.txt` da seguinte maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio endereço IP público da seguinte maneira:

```bash
ssh myadminuser@publicIpAddress
```

Para confirmar se o usuário foi adicionado para a VM e os grupos especificados, exiba o conteúdo do arquivo */etc/group* da seguinte maneira:

```bash
cat /etc/group
```

A saída de exemplo a seguir mostra que o usuário do arquivo *cloud_init_add_user.txt* foi adicionado à VM e ao grupo apropriado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Próximas etapas
A inicialização de nuvem é uma das maneiras padrão de modificar suas VMs Linux na inicialização. No Azure, você também pode usar extensões de VM para modificar sua VM Linux na inicialização ou quando ela estiver em execução. Por exemplo, você pode usar a extensão de VM do Azure para executar um script em uma VM em execução, não apenas na primeira inicialização. Para obter mais informações sobre extensões de VM, consulte [Extensões e recursos de VM](extensions-features.md) ou, para obter exemplos de como usar a extensão, consulte [Gerenciar usuários, conectar-se por SSH e verificar ou reparar discos em VMs Linux do Azure usando a extensão VMAccess](using-vmaccess-extension.md).
---
title: Usar Cloud-init para executar um script de bash em uma VM do Linux no Azure
description: Como usar o cloud-init para executar um script de bash em uma VM do Linux durante a criação com a CLI do Azure
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559419"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Usar cloud-init para executar um script de bash em uma VM Linux no Azure
Este artigo mostra como usar [cloud-init](https://cloudinit.readthedocs.io) para executar um script de bash existente em uma VM (máquina virtual) Linux ou VMSS (conjuntos de dimensionamento de máquinas virtuais) no tempo de provisionamento no Azure. Esses scripts de cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Executar um script de bash com cloud-init
Com a cloud-init você não precisa converter seus scripts existentes em uma cloud-config, a cloud-init aceita vários tipos de entrada, um dos quais é um script de bash.

Se você estiver usando a Extensão do Azure de Script Personalizado do Linux para executar seus scripts, poderá migrá-las para usar a cloud-init. No entanto, as Extensões do Azure integraram os relatórios para alertar para falhas de script, uma implantação de imagem de cloud-init NÃO falhará se o script falhar.

Para ver essa funcionalidade em ação, crie um script de bash simples para teste. Como o arquivo `#cloud-config` de cloud-init, esse script deve ser local para onde você executará os comandos de AzureCLI para provisionar sua máquina virtual.  Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor simple_bash.sh` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Verifique se o arquivo cloud-init inteiro foi copiado corretamente, principalmente a primeira linha.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm) e especifique o arquivo de script de bash com `--custom-data simple_bash.sh` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verificar se o script de bash foi executado
Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Altere para o diretório **/tmp** e verifique se o arquivo myScript.txt existe e tem o texto apropriado dentro dele.  Se não estiver, você poderá verificar o **/var/log/cloud-init.log** para obter mais detalhes.  Pesquise a seguinte entrada:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)

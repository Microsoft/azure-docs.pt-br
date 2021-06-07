---
title: 'Início Rápido: Usar a CLI do Azure para criar uma VM do Linux'
description: Neste início rápido, você aprende a usar a CLI do Azure para criar uma máquina virtual Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/30/2021
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: 3e8c1a06244c46f5789506e8a77d410f5493bbf3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058571"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Início Rápido: Criar uma máquina virtual Linux com a CLI do Azure

Este início rápido mostra como usar a CLI (interface de linha de comando) do Azure para implantar uma VM (máquina virtual) Linux no Azure. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts.

Neste tutorial, instalaremos a imagem mais recente do Ubuntu LTS. Para mostrar a VM em ação, você se conectará a ela usando SSH e instalará o servidor Web NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

Se preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm).

O exemplo a seguir cria uma VM chamada *myVM* e adiciona uma conta de usuário chamada *azureuser*. O parâmetro `--generate-ssh-keys` é usado para gerar automaticamente uma chave SSH e colocá-la no local de chave padrão ( *~/.ssh*). Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. O seguinte exemplo de saída mostra que a operação de criação de VM foi bem-sucedida.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Observe a sua própria `publicIpAddress` na saída da sua VM. Este endereço é usado para acessar a VM na próxima etapa.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Por padrão, somente conexões de SSH são abertas quando você criar uma VM do Linux no Azure. Use [az vm open-port](/cli/azure/vm) para abrir a porta TCP 80 para uso com o servidor web NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

SSH para sua VM, como de costume. Substitua o endereço IP do exemplo pelo endereço IP público da VM conforme indicado na saída anterior:

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do NGINX. Atualize suas fontes de pacote e, em seguida, instale o pacote mais recente do NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, digite `exit` para sair da sessão SSH.

## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Use um navegador da Web de sua escolha para exibir a página inicial padrão do NGINX. Use o endereço IP público de sua VM como o endereço Web. O seguinte exemplo mostra o site padrão do NGINX:

![Exibir a página de boas-vindas do NGINX](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e todos os recursos relacionados. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, abriu uma porta de rede para o tráfego da Web e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)

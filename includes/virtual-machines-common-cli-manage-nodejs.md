---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: a4c9ec133b3686a92cec7e7c8d4552c1302e3074
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171937"
---
Antes de usar a CLI do Azure com modelos e comandos do Gerenciador de Recursos para implantar recursos do Azure e cargas de trabalho usando grupos de recursos, será necessário ter uma conta com o Azure. Se você não tiver uma conta, você pode obter uma [avaliação gratuita do Azure aqui](https://azure.microsoft.com/pricing/free-trial/).

Se você ainda não tiver instalado a CLI do Azure e a conectado à sua assinatura, consulte [Instalar a CLI do Azure](../articles/cli-install-nodejs.md) defina o modo como `arm` com `azure config mode arm` e conecte-se ao Azure com o comando `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- CLI clássica do Azure – nossa CLI para os modelos de implantação de gerenciamento clássico e de recursos (este artigo)
- [CLI do Azure](../articles/virtual-machines/linux/cli-manage.md) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Comandos básicos do Azure Resource Manager na CLI clássica do Azure

Este artigo aborda os comandos básicos que você desejará usar com a CLI clássica do Azure para gerenciar e interagir com os recursos (principalmente as VMs) em sua assinatura do Azure.  Para obter ajuda mais detalhada com opções específicas de linha de comando, você pode usar as opções e a ajuda online sobre os comandos digitando `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

> [!NOTE]
> Esses exemplos não incluem operações baseadas em modelo que geralmente são recomendadas para implantações de VM no Gerenciador de Recursos. Para saber mais, confira [Usar a CLI do Azure com o Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) e [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Tarefa | Gerenciador de Recursos |
| --- | --- |
| Criar a VM mais básica |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenha o `image-urn` do comando `azure vm image list`. Confira [este artigo](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter exemplos.) |
| Criar uma VM do Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Criar uma VM do Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Listar VMs |`azure  vm list [options]` |
| Obter informações sobre uma VM |`azure  vm show [options] <resource_group> <name>` |
| Iniciar uma VM |`azure vm start [options] <resource_group> <name>` |
| Parar uma VM |`azure vm stop [options] <resource_group> <name>` |
| Desalocar uma VM |`azure vm deallocate [options] <resource-group> <name>` |
| Reiniciar uma VM |`azure vm restart [options] <resource_group> <name>` |
| Excluir uma VM |`azure vm delete [options] <resource_group> <name>` |
| Capturar uma VM |`azure vm capture [options] <resource_group> <name>` |
| Criar uma VM por meio de uma imagem do usuário |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Criar uma VM por meio de um disco especializado |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Adicionar um disco de dados a uma VM |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Remover um disco de dados de uma VM |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Adicionar uma extensão genérica a uma VM |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Adicionar a extensão de acesso de VM a uma VM |`azure vm reset-access [options] <resource-group> <name>` |
| Adicionar uma extensão Docker a uma VM |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Remover uma extensão de VM |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Conferir o uso dos recursos de VM |`azure vm list-usage [options] <location>` |
| Conferir todos os tamanhos de VM disponíveis |`azure vm sizes [options]` |

## <a name="next-steps"></a>Próximas etapas
* Para obter exemplos adicionais dos comandos da CLI que vão além do gerenciamento básico de VMs, consulte [Usando a CLI do Azure com o Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).

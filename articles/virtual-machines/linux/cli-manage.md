---
title: Comandos comuns da CLI do Azure
description: Aprenda alguns dos comandos comuns da CLI do Azure para começar a gerenciar suas VMs no modo do Azure Resource Manager
author: RicksterCDN
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 2084d79ecbbc53ef9e3c75bae0664eae7de0eccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559623"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Comandos comuns da CLI do Azure para gerenciar recursos do Azure

A CLI do Azure permite criar e gerenciar os recursos do Azure no Windows, Linux e macOS. Este artigo detalha alguns dos comandos mais comuns para criar e gerenciar máquinas virtuais (VMs).

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Você também pode usar o [Cloud Shell](../../cloud-shell/quickstart.md) no seu navegador.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandos básicos do Azure Resource Manager na CLI do Azure
Para obter ajuda mais detalhada com opções específicas de linha de comando, você pode usar as opções e a ajuda online sobre os comandos digitando `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Criar VMs
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Criar um grupo de recursos | `az group create --name myResourceGroup --location eastus` |
| Criar uma VM do Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Criar uma VM do Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Gerenciar o estado da VM
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Iniciar uma VM | `az vm start --resource-group myResourceGroup --name myVM` |
| Parar uma VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| Desalocar uma VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Reiniciar uma VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| Reimplantar uma VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Excluir uma VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Obter informações sobre a VM
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Listar VMs | `az vm list` |
| Obter informações sobre uma VM | `az vm show --resource-group myResourceGroup --name myVM` |
| Conferir o uso dos recursos de VM | `az vm list-usage --location eastus` |
| Conferir todos os tamanhos de VM disponíveis | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Discos e imagens
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Adicionar um disco de dados a uma VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Remover um disco de dados de uma VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Redimensionar um disco | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Instantâneo de um disco | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Criar imagem de uma VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Criar uma VM com base em uma imagem | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais dos comandos da CLI, consulte o tutorial [Criar e gerenciar VMs do Linux com a CLI do Azure](tutorial-manage-vm.md).

---
title: Exemplo de script da CLI do Azure – Criar uma VM com um VHD | Microsoft Docs
description: Amostra de script da CLI do Azure – criar uma VM usando um disco rígido virtual.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: douge
ms.custom: mvc
ms.openlocfilehash: 32f6794f6607fee04364d10a3f91128f169802b0
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875424"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Criar uma VM com um disco rígido virtual

Este exemplo cria uma máquina virtual usando um VHD.
Ele cria um grupo de recursos, uma conta de armazenamento e um contêiner, então cria uma VM carregando o VHD no contêiner.
Ele substitui a chave pública de SSH pela sua chave pública, de modo que você tem acesso à VM.

Você precisará de um VHD inicializável. O script procura `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account) | Lista contas de armazenamento |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account) | Verifica se um nome de conta de armazenamento é válido e se já não existe |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys) | Lista as chaves das contas de armazenamento |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob) | Verifica se o blob existe |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container) | Cria um contêiner em uma conta de armazenamento. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob) | Cria um blob no contêiner carregando o VHD. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | Usado com `--query`, verifica se o nome da VM está em uso. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Cria as máquinas virtuais. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) | Obtém o endereço IP da VM que foi criada. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

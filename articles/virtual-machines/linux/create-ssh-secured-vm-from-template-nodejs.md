---
title: Criar uma VM do Linux usando um modelo do Azure com a CLI 1.0 do Azure | Microsoft Docs
description: Crie uma VM do Linux no Azure usando a CLI 1.0 do Azure e um modelo do Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Como criar uma VM do Linux usando a CLI 1.0 do Azure e um modelo do Azure Resource Manager
Este artigo mostra como implantar rapidamente uma máquina virtual do Linux usando a CLI 1.0 do Azure e um Modelo do Azure Resource Manager. O artigo exige:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI 1.0 do Azure](../../cli-install-nodejs.md) conectada com o `azure login`.
* A CLI do Azure *deve estar no* modo Azure Resource Manager `azure config mode arm`.

Você também pode implantar rapidamente um modelo de VM do Linux usando o [portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-command-summary) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](create-ssh-secured-vm-from-template.md) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="quick-command-summary"></a>Resumo rápido do comando
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado
Os modelos permitem criar VMs no Azure com as configurações que você deseja personalizar durante a inicialização, como nomes de usuário e nomes de host. Neste artigo, estamos iniciando um modelo do Azure utilizando uma VM do Ubuntu junto com um grupo de segurança de rede (NSG) com a porta 22 aberta para o SSH.

Os modelos do Azure Resource Manager são arquivos JSON que podem ser usados para tarefas únicas simples, como iniciar uma VM Ubuntu, como feito neste artigo.  Os Modelos do Azure também podem ser usados para construir configurações complexas do Azure de ambientes inteiros como uma pilha de implantação de teste, desenvolvimento ou produção.

## <a name="create-the-linux-vm"></a>Criar a VM Linux
O exemplo de código a seguir mostra como chamar o `azure group create` para criar um grupo de recursos e implantar uma VM do Linux protegida por SSH ao mesmo tempo usando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que, em seu exemplo, você precisará usar nomes que sejam exclusivos para seu ambiente. Esse exemplo usa *myResourceGroup* como o nome do grupo de recursos e *myVM* como o nome da VM.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

A saída deve ser semelhante ao bloco de saída a seguir:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Este exemplo implantou uma VM usando o parâmetro `--template-uri` .  Você também pode baixar ou criar um modelo localmente e passar o modelo usando o parâmetro `--template-file` com um caminho para o arquivo de modelo como um argumento. A CLI do Azure solicita os parâmetros necessários ao modelo.

## <a name="next-steps"></a>Próximas etapas
Pesquise a [galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir quais estruturas de aplicativos implantar em seguida.


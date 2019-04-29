---
title: Criar e gerenciar máquinas virtuais em DevTest Labs com a CLI do Azure | Microsoft Docs
description: Saiba como usar o Azure DevTest Labs para criar e gerenciar máquinas virtuais com a CLI do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622313"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure
Neste início rápido orienta a criação, iniciar, conectar-se, atualizando e limpeza de um computador de desenvolvimento em seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, as instruções poderão ser encontradas [aqui](devtest-lab-create-lab.md).

* [Instale a CLI do Azure](/cli/azure/install-azure-cli). Para iniciar, execute az login para criar uma conexão com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Antes de executar comandos relacionados do DevTest Labs, definir o contexto apropriado do Azure usando o `az account set` comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma máquina virtual é: `az lab vm create`. O grupo de recursos para o laboratório, o nome do laboratório e o nome da máquina virtual são obrigatórios. O restante dos argumentos é alterado dependendo do tipo de máquina virtual.

O comando a seguir cria uma imagem baseada em Windows de mercado do Azure. O nome da imagem é o mesmo que você veria durante a criação de uma máquina virtual usando o portal do Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

O comando a seguir cria uma máquina virtual baseada em uma imagem personalizada disponível no laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O **tipo de imagem** argumento foi alterado de **galeria** para **personalizado**. O nome da imagem corresponde ao que você vê se você criar a máquina virtual no portal do Azure.

O comando a seguir cria uma VM de uma imagem do marketplace com ssh autenticação:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Você também pode criar máquinas virtuais baseadas em fórmulas, definindo o **tipo de imagem** parâmetro **fórmula**. Se você precisa escolher uma rede virtual específica para sua máquina virtual, use o **vnet-name** e **sub-rede** parâmetros. Para obter mais informações, consulte [az lab vm criar](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verifique se a VM está disponível.
Use o `az lab vm show` comando para verificar se a VM está disponível antes de iniciar e conectar-se a ele. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e conectar-se à máquina virtual
O comando de exemplo a seguir inicia uma VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Conecte-se a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [área de trabalho remota](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
O comando de exemplo a seguir se aplica a artefatos para uma VM:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Lista de artefatos disponíveis no laboratório.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e excluir a máquina virtual    
O comando de exemplo a seguir interrompe uma VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Exclua uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte conteúdo: [Documentação da CLI do Azure para o Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
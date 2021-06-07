---
title: Implantar o OKD no Azure
description: Implante o OKD no Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667212"
---
# <a name="deploy-okd-in-azure"></a>Implantar o OKD no Azure

Você pode usar uma de duas maneiras para implantar o OKD (anteriormente conhecido como Origem do OpenShift) no Azure:

- Você pode implantar manualmente todos os componentes de infraestrutura do Azure necessários e, em seguida, seguir a [documentação do OKD](https://docs.okd.io).
- Você também pode usar um [modelo do Resource Manager](https://github.com/Microsoft/openshift-origin) existente que simplifica a implantação do cluster do OKD.

## <a name="deploy-using-the-okd-template"></a>Implantar usando o modelo OLD

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, bifurque o repositório do GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não estão limitadas a:

- Tamanho de VM de bastião (variável em azuredeploy.json)
- Convenções de nomenclatura (variáveis em azuredeploy.json)
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts (deployOpenShift.sh)

O [modelo OKD](https://github.com/Microsoft/openshift-origin) tem várias ramificações disponíveis para diferentes versões do OKD.  Com base nas suas necessidades, você pode implantar diretamente do repositório ou pode bifurcar o repositório e fazer alterações personalizadas antes da implantação.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

A seguir, um exemplo de um arquivo de parâmetros chamado azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Substitua os parâmetros por suas informações específicas.

Versões diferentes podem ter parâmetros diferentes, portanto, verifique os parâmetros necessários para o ramo que você usa.

### <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure


> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

O exemplo a seguir implanta o cluster OKD e todos os recursos relacionados em um grupo de recursos denominado openshiftrg, com um nome de implantação myOpenShiftCluster. O modelo é referenciado diretamente no repositório do GitHub ao usar um arquivo de parâmetros local chamado azuredeploy.parameters.json.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 30 minutos para ser concluída, com base no número total de nós implantados. A URL do console openShift e o nome DNS do mestre OpenShift são impressas no terminal quando a implantação é concluída. Como alternativa, você pode exibir a seção de saídas da implantação no portal do Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se você não quiser amarrar a linha de comando esperando a conclusão da implementação, inclua `--no-wait` como uma das opções para a implementação do grupo. A saída da implantação pode ser recuperada do portal do Azure na seção de implantação do grupo de recursos.

## <a name="connect-to-the-okd-cluster"></a>Conectar-se ao cluster do OKD

Quando a implementação terminar, conecte-se ao console do OpenShift com seu navegador usando o `OpenShift Console Url`. Como alternativa, você pode SSH para o mestre de OKD. A seguir, um exemplo que usa a saída da implementação:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Introdução ao OKD](https://docs.okd.io)

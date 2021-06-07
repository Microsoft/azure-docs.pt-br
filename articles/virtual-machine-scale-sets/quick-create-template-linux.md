---
title: Início Rápido – Criar um conjunto de dimensionamento de máquinas virtuais do Linux com um modelo do Azure Resource Manager
description: Saiba como criar rapidamente um dimensionamento de máquinas virtuais do Linux com um modelo do Azure Resource Manager que implante um aplicativo de exemplo e configure regras de dimensionamento automático
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs, devx-track-azurecli
ms.openlocfilehash: eaebaa958da558b207775d2f535c825d49384c7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932311"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-arm-template"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais do Linux com um modelo do Resource Manager

Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais de dimensionamento automático. É possível dimensionar o número de VMs manualmente no conjunto de dimensionamento ou definir as regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Um balanceador de carga do Azure então distribui o tráfego para as instâncias de VM no conjunto de dimensionamento. Neste guia de início rápido, você criará um conjunto de dimensionamento de máquinas virtuais e implantará um aplicativo de exemplo com um modelo do Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Os modelos do Resource Manager permitem implantar grupos de recursos relacionados. Em um único modelo, é possível criar o conjunto de dimensionamento de máquinas virtuais, instalar aplicativos e configurar regras de dimensionamento automático. Com o uso de variáveis e parâmetros, esse modelo pode ser reutilizado para atualizar conjuntos de dimensionamento existentes ou criar conjuntos adicionais. Você pode implantar modelos por meio do portal do Azure, do Azure CLI ou do Azure PowerShell ou de pipelines de integração contínua / entrega contínua (CI / CD).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json":::

Esses recursos do Azure estão definidos no modelo:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Definir um conjunto de dimensionamento

Para criar uma escala com um modelo, defina os recursos apropriados. As partes principais do tipo de recurso de conjunto de dimensionamento de máquinas virtuais são:

| Propriedade                     | Descrição da propriedade                                  | Exemplo de valor do modelo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo de recurso do Azure a ser criado                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | O nome do conjunto de dimensionamento                                       | myScaleSet                                |
| local                     | O local para criar o conjunto de dimensionamento                     | Leste dos EUA                                   |
| sku.name                     | O tamanho da VM para cada instância do conjunto de dimensionamento                  | Standard_A1                               |
| sku.capacity                 | O número de instâncias de VM a serem criadas inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de atualização de instância de VM quando ocorrem alterações              | Automático                                 |
| imageReference               | A plataforma ou a imagem personalizada a ser usada nas instâncias de VM | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | O prefixo do nome de cada instância de VM                     | myvmss                                    |
| osProfile.adminUsername      | O nome de usuário de cada instância de VM                        | azureuser                                 |
| osProfile.adminPassword      | A senha de cada instância de VM                        | P@ssw0rd!                                 |

Para personalizar um modelo de conjunto de dimensionamento, você pode alterar o tamanho da VM ou a capacidade inicial. Outra opção é usar outra plataforma ou uma imagem personalizada.

### <a name="add-a-sample-application"></a>Adicionar um aplicativo de exemplo

Para testar seu conjunto de dimensionamento, instale um aplicativo Web básico. Quando você implanta um conjunto de dimensionamento, as extensões da VM podem fornecer tarefas de configuração e de automação pós-implantação, tais como instalar um aplicativo. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. Para aplicar uma extensão ao conjunto de dimensionamento, adicione a seção *extensionProfile* ao exemplo anterior do recurso. O perfil da extensão normalmente define as propriedades a seguir:

- Tipo de extensão
- Editor de extensão
- Versão da extensão
- Local dos scripts de configuração ou de instalação
- Comandos a serem executados nas instâncias da VM

O modelo usa a Extensão de Script Personalizado para instalar o [Bottle](https://bottlepy.org/docs/dev/), uma estrutura da Web Python e um servidor HTTP simples.

Dois scripts são definidos em **fileUris** - *installserver.sh* e *workserver.py*. Esses arquivos são baixados do GitHub e, em seguida, *commandToExecute* executa `bash installserver.sh` para instalar e configurar o aplicativo.

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo selecionando o botão **Implantar no Azure** a seguir. Esse botão abre o Portal do Azure, carrega o modelo completo e solicita alguns parâmetros, como o nome de um conjunto de dimensionamento, a contagem de instâncias e as credenciais de administrador.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Implante também um modelo do Resource Manager usando a CLI do Azure:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Responda os prompts para fornecer nome do conjunto de dimensionamento, uma contagem de instâncias e credenciais de administrador para as instâncias de VM. Levará alguns minutos para o conjunto de dimensionamento e os recursos de suporte serem criados.

## <a name="validate-the-deployment"></a>Validar a implantação

Para ver seu conjunto de dimensionamento em ação, acesse o aplicativo Web de exemplo em um navegador da Web. Obtenha o endereço IP público do balanceador de carga com a [az network public-ip list](/cli/azure/network/public-ip) da seguinte maneira:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web no formato *http:\//publicIpAddress:9000/do_work*. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web padrão em NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group) para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados, como demonstrado a seguir. O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um conjunto de dimensionamento do Linux com um modelo do Resource Manager e usou uma Extensão de Script Personalizado para instalar um servidor Web básico do Python nas instâncias de VM. Para saber mais, siga para o tutorial sobre como criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-cli.md)

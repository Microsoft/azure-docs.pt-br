---
title: Tutorial – Instalar aplicativos em um conjunto de dimensionamento com a CLI do Azure
description: Saiba como usar a CLI do Azure para instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizado
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: cf383d41df5d41ea52065f3651b2068861631116
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934605"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Para executar aplicativos em instâncias de VM (máquina virtual) em um conjunto de dimensionamento, primeiro é necessário instalar os componentes de aplicativo e os arquivos necessários. Em um tutorial anterior, você aprendeu a criar e usar uma imagem de VM personalizada para implantar suas instâncias de VM. Essa imagem personalizada incluía instalações manuais de aplicativos e configurações. Você também pode automatizar a instalação de aplicativos para um conjunto de dimensionamento após a implantação de cada instância de VM ou atualizar um aplicativo que já é executado em um conjunto de dimensionamento. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar automaticamente os aplicativos para o conjunto de dimensionamento
> * Use a Extensão de Script Personalizado do Azure
> * Atualizar um aplicativo em execução em um conjunto de dimensionamento

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.29 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 


## <a name="what-is-the-azure-custom-script-extension"></a>O que é a Extensão de Script Personalizado do Azure?
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão.

A extensão de script personalizado integra-se aos modelos do Azure Resource Manager e também pode ser usada com a CLI do Azure, o Azure PowerShell, o portal do Azure ou a API REST. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/extensions/custom-script-linux.md).

Para usar a Extensão de Script Personalizado com a CLI do Azure, você deve criar um arquivo JSON que define quais arquivos para obter e os comandos a serem executados. Essas definições JSON podem ser reutilizadas em implantações de conjunto de dimensionamento para aplicar instalações de aplicativo consistentes.


## <a name="create-custom-script-extension-definition"></a>Criar definição de Extensão de Script Personalizado
Para ver a Extensão de Script Personalizado em ação, crie um conjunto de dimensionamento que instala o servidor Web NGINX e gera o nome do host da instância de VM do conjunto de dimensionamento. A definição de Extensão de Script Personalizado a seguir faz o download de um script de exemplo do GitHub, instala os pacotes necessários e grava o nome de host da instância de VM em uma página HTML básica.

No shell atual, crie um arquivo chamado *customConfig.json* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Você pode usar qualquer editor que queira. No Cloud Shell, insira `sensible-editor customConfig.json` para criar o arquivo e ver uma lista de editores disponíveis.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> Talvez seja necessário inverter o uso das aspas simples (') e duplas (") no bloco JSON caso você queira referenciar o JSON diretamente (em vez de referenciar o arquivo *customConfig.json*) no parâmetro *--settings* abaixo. 


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo a seguir criará um conjunto de dimensionamento chamado *myScaleSet* e gerará chaves SSH se elas não existirem:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="apply-the-custom-script-extension"></a>Aplicar a Extensão de Script Personalizado
Aplique a Configuração de Extensão de Script personalizado às instâncias de VM no conjunto de dimensionamento com [az vmss extension set](/cli/azure/vmss/extension). O exemplo a seguir aplica a configuração *customConfig.json* às instâncias de VM de *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Cada instância de VM no conjunto de dimensionamento baixa e executa o script do GitHub. Em um exemplo mais complexo, vários componentes de aplicativos e arquivos poderiam ser instalados. Se o conjunto de dimensionamento estiver escalado verticalmente, as novas instâncias de VM serão aplicadas automaticamente à mesma definição de Extensão de Script Personalizado e instalarão o aplicativo necessário.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para permitir o tráfego acessar o servidor Web, crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo a seguir cria uma regra chamada *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver seu servidor Web em ação, obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O seguinte exemplo obtém o endereço IP de *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web básica em Nginx](media/tutorial-install-apps-cli/running-nginx.png)

Deixe o navegador da Web aberto para que você possa ver uma versão atualizada na próxima etapa.


## <a name="update-app-deployment"></a>Atualizar a implantação do aplicativo
Em todo o ciclo de vida de um conjunto de dimensionamento, talvez seja necessário implantar uma versão atualizada de seu aplicativo. Com a Extensão de Script Personalizado, você pode fazer referência a um script de implantação atualizado e, em seguida, aplicar novamente a extensão ao seu conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado em uma etapa anterior, o `--upgrade-policy-mode` foi definido como *automático*. Essa configuração permite que as instâncias de VM no conjunto de dimensionamento se atualizem automaticamente e apliquem a versão mais recente de seu aplicativo.

No shell atual, crie um arquivo chamado *customConfigv2.json* e cole a configuração a seguir. Essa definição executa uma versão *v2* atualizada do script de instalação do aplicativo:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Aplique a configuração de Extensão de Script Personalizado às instâncias de VM no conjunto de dimensionamento novamente com [az vmss extension set](/cli/azure/vmss/extension). O *customConfigv2.json* é usado para aplicar a versão atualizada do aplicativo:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Todas as instâncias de VM no conjunto de dimensionamento são atualizadas automaticamente com a versão mais recente da página da Web de exemplo. Para ver a versão atualizada, atualize o site em seu navegador:

![Página da Web atualizada em Nginx](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a instalar e atualizar aplicativos automaticamente em seu conjunto de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Instalar automaticamente os aplicativos para o conjunto de dimensionamento
> * Use a Extensão de Script Personalizado do Azure
> * Atualizar um aplicativo em execução em um conjunto de dimensionamento

Avance para o próximo tutorial para aprender a dimensionar automaticamente seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente seus conjuntos de dimensionamento](tutorial-autoscale-cli.md)

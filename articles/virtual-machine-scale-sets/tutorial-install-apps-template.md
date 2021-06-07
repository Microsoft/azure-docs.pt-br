---
title: 'Tutorial: Instalar aplicativos em um conjunto de dimensionamento com modelos do Azure'
description: Saiba como usar modelos do Azure Resource Manager para instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizada
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 1ce7795f1cb823be7940f065c9dba4ecc51926c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934582"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Tutorial: Instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com um modelo do Azure
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

Para ver a Extensão de Script Personalizado em ação, crie um conjunto de dimensionamento que instala o servidor Web NGINX e gera o nome do host da instância de VM do conjunto de dimensionamento. A definição de Extensão de Script Personalizado a seguir faz o download de um script de exemplo do GitHub, instala os pacotes necessários e grava o nome de host da instância de VM em uma página HTML básica.


## <a name="create-custom-script-extension-definition"></a>Criar definição de Extensão de Script Personalizado
Quando você define um conjunto de dimensionamento de máquinas virtuais com um modelo do Azure, o provedor de recursos *Microsoft.Compute/virtualMachineScaleSets* pode incluir uma seção nas extensões. O *extensionsProfile* fornece detalhes sobre o que é aplicado às instâncias de VM em um conjunto de dimensionamento. Para usar a Extensão de Script Personalizado, você especifica um publicador do *Microsoft.Azure.Extensions* e um tipo de *CustomScript*.

A propriedade *fileUris* é usada para definir os pacotes ou scripts de instalação de origem. Para iniciar o processo de instalação, os scripts necessários são definidos em *commandToExecute*. O exemplo a seguir define um script de exemplo do GitHub que instala e configura o servidor Web NGINX:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Para obter um exemplo completo de um modelo do Azure que implanta um conjunto de dimensionamento e a Extensão de Script Personalizado, confira [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Este modelo de exemplo é usado na próxima seção.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Vamos usar o modelo de exemplo para criar um conjunto de dimensionamento e aplicar a Extensão de Script Personalizado. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais usando [az deployment group create](/cli/azure/deployment/group). Quando solicitado, forneça seu próprio nome de usuário e senha que são usados como as credenciais para cada instância de VM:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

Cada instância de VM no conjunto de dimensionamento baixa e executa o script do GitHub. Em um exemplo mais complexo, vários componentes de aplicativos e arquivos poderiam ser instalados. Se o conjunto de dimensionamento estiver escalado verticalmente, as novas instâncias de VM serão aplicadas automaticamente à mesma definição de Extensão de Script Personalizado e instalarão o aplicativo necessário.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver seu servidor Web em ação, obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo a seguir obtém o endereço IP de *myScaleSetPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web básica em Nginx](media/tutorial-install-apps-template/running-nginx.png)

Deixe o navegador da Web aberto para que você possa ver uma versão atualizada na próxima etapa.


## <a name="update-app-deployment"></a>Atualizar a implantação do aplicativo
Em todo o ciclo de vida de um conjunto de dimensionamento, talvez seja necessário implantar uma versão atualizada de seu aplicativo. Com a Extensão de Script Personalizado, você pode fazer referência a um script de implantação atualizado e, em seguida, aplicar novamente a extensão ao seu conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado em uma etapa anterior, o *upgradePolicy* foi definido como *Automático*. Essa configuração permite que as instâncias de VM no conjunto de dimensionamento se atualizem automaticamente e apliquem a versão mais recente de seu aplicativo.

Para atualizar a definição de Extensão de Script Personalizado, edite seu modelo para fazer referência a um novo script de instalação. Um novo nome de arquivo deve ser usado para a Extensão de Script Personalizado para reconhecer a alteração. A Extensão de Script Personalizado não examina o conteúdo do script para determinar as alterações. Esta definição a seguir usa um script de instalação atualizado com *_v2* acrescentado ao nome:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Aplique a configuração de Extensão de Script Personalizado às instâncias de VM no conjunto de dimensionamento novamente com [az deployment group create](/cli/azure/deployment/group). O modelo *azuredeployv2.json* é usado para aplicar a versão atualizada do aplicativo. Na prática, você edita o modelo *azuredeploy.json* existente para fazer referência ao script de instalação atualizado, como mostrado na seção anterior. Quando solicitado, insira o mesmo nome de usuário e as credenciais de senha conforme usado ao criar o conjunto de dimensionamento:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Todas as instâncias de VM no conjunto de dimensionamento são atualizadas automaticamente com a versão mais recente da página da Web de exemplo. Para ver a versão atualizada, atualize o site em seu navegador:

![Página da Web atualizada em Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a instalar e atualizar aplicativos automaticamente em seu conjunto de dimensionamento com os modelos do Azure:

> [!div class="checklist"]
> * Instalar automaticamente os aplicativos para o conjunto de dimensionamento
> * Use a Extensão de Script Personalizado do Azure
> * Atualizar um aplicativo em execução em um conjunto de dimensionamento

Avance para o próximo tutorial para aprender a dimensionar automaticamente seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente seus conjuntos de dimensionamento](tutorial-autoscale-template.md)

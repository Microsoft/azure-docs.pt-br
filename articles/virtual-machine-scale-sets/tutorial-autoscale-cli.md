---
title: Tutorial – Dimensionar automaticamente um conjunto de dimensionamento com a CLI do Azure
description: Saiba como usar a CLI do Azure para dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais conforme a demanda de CPU aumenta e diminui
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: b7fdf6d4893a6f6a970223671b28fdae6db3ef3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762975"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure

Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionar automaticamente conforme a demanda é reduzida

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.32 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Crie um grupo de recursos com [az group create](/cli/azure/group) da seguinte forma:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo abaixo criará um conjunto de dimensionamento com contagem de instâncias de *2* e gerará chaves SSH se elas não existirem:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Definir um perfil de autoescala

Para habilitar o dimensionamento automático em um conjunto de dimensionamento, primeiro você define um perfil de dimensionamento automático. Esse perfil define a capacidade padrão, mínima e máxima do conjunto de dimensionamento. Esses limites permitem controlar o custo ao não criar instâncias de VM de forma contínua e equilibra o desempenho aceitável com um número mínimo de instâncias que permanecem em um evento de redução. Crie um perfil de dimensionamento automático com [az monitor autoscale create](/cli/azure/monitor/autoscale#az_monitor_autoscale_create). O exemplo a seguir define a capacidade padrão e mínima de *2* instâncias de VM, bem como um máximo de *10*:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Criar uma regra de dimensionamento automático para aumento

Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra com [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) que aumenta o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for maior que 70% por um período de 5 minutos. Quando a regra é disparada, a quantidade de instâncias de VM aumenta por três.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Criar uma regra de dimensionamento automático para redução

À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

Crie outra regra com [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) que diminua o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média ficar abaixo de 30% por um período de 5 minutos. O exemplo a seguir define a regra para reduzir o número de instâncias de VM em um:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>Gerar carga de CPU no conjunto de dimensionamento

Para testar as regras de dimensionamento automático, gere alguma carga de CPU nas instâncias de VM no conjunto de dimensionamento. Essa carga de CPU simulada faz com que o dimensionamento automático escale horizontalmente e aumente o número de instâncias de VM. Conforme a carga simulada de CPU vai sendo reduzida, as regras de dimensionamento automático reduzem horizontalmente e diminuem o número de instâncias de VM.

Primeiro, liste os endereços e as portas para se conectar a instâncias de VM em um conjunto de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número da porta para onde as regras NAT (Conversão de Endereços de Rede) encaminham o tráfego:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH para a primeira instância de VM. Especifique seu próprio endereço IP público e o número da porta com o parâmetro `-p`, conforme mostrado no comando anterior:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Depois de conectado, instale o utilitário **stress**. Inicie *10* **trabalhos** de stress que geram carga de CPU. Esses trabalhos são executados por *420* segundos, o que é suficiente para fazer com que as regras de dimensionamento automático implementem a ação desejada.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Quando **stress** exibe uma saída semelhante a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, pressione a tecla *Enter* para retornar ao prompt.

Para confirmar que o **stress** gera carga de CPU, examine a carga do sistema ativo com o utilitário **top**:

```console
top
```

Saia do **top** e feche a conexão com a instância de VM. **stress** continuará a ser executado na instância de VM.

```console
Ctrl-c
exit
```

Conecte-se à segunda instância de VM com o número da porta listado no [az vmss list-instance-connection-info](/cli/azure/vmss) anterior:

```console
ssh azureuser@13.92.224.66 -p 50003
```

Instale e execute **stress** e inicie os dez trabalhos nessa segunda instância de VM.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Novamente, quando **stress** exibe uma saída semelhante a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, pressione a tecla *Enter* para retornar ao prompt.

Feche a conexão com a segunda instância de VM. **stress** continuará a ser executado na instância de VM.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorar as regras de dimensionamento automático ativas

Para monitorar o número de instâncias de VM em seu conjunto de dimensionamento, use **watch**. Demoram 5 minutos para as regras de dimensionamento automático começarem o processo de escala horizontal em resposta à carga da CPU gerada pelo **stress** em cada uma das instâncias de VM:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Assim que o limite de CPU for atingido, as regras de dimensionamento automático aumentam o número de instâncias de VM no conjunto de dimensionamento. A saída a seguir mostra três VMs criadas à medida que conjunto de dimensionamento é escalado horizontal e automaticamente:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Assim que o **stress** é interrompido nas instâncias de VM inicias, a carga média da CPU volta ao normal. Após mais 5 minutos, as regras de dimensionamento automático reduzem horizontalmente o número de instâncias de VM. As ações de redução horizontal removem instâncias de VM começando pelas IDs mais altas. Quando um conjunto de dimensionamento usa Conjuntos de Disponibilidade ou Zonas de Disponibilidade, as ações de reduzir horizontalmente são distribuídas uniformemente entre essas instâncias de VM. A saída de exemplo a seguir mostra uma instância de VM excluída conforme o conjunto de dimensionamento reduz horizontal e automaticamente:

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Saia do *watch* usando `Ctrl-c`. O conjunto de dimensionamento continua a reduzir horizontalmente a cada 5 minutos e a remover uma instância de VM até que a contagem mínima de duas instâncias seja alcançada.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como dimensionar automaticamente ou reduzir horizontalmente um conjunto de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionar automaticamente conforme a demanda é reduzida

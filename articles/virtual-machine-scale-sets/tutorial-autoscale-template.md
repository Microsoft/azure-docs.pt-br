---
title: Tutorial – Dimensionar automaticamente um conjunto de dimensionamento com modelos do Azure
description: Saiba como usar modelos do Azure Resource Manager para dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais conforme aumentam e diminuem as demandas de CPU
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705244"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Tutorial: dimensione automaticamente um conjunto de dimensionamento de máquinas virtuais com um modelo do Azure
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionar automaticamente conforme a demanda é reduzida

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.29 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 


## <a name="define-an-autoscale-profile"></a>Definir um perfil de autoescala
Defina um perfil de dimensionamento automático em um modelo do Azure com o provedor de recursos *Microsoft.insights/autoscalesettings*. Um *perfil* detalha a capacidade da conjunto de dimensionamento e quaisquer regras associadas. O exemplo a seguir define um perfil chamado *Dimensionamento automático por porcentagem com base no uso da CPU* e define a capacidade padrão e mínima de *2* instâncias de VM e um máximo de *10*:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Definir uma regra para escalar horizontal e automaticamente
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

No exemplo a seguir, é definida uma regra que aumenta a quantidade de instâncias de VM em um conjunto de dimensionamento quando a carga da CPU média for maior que 70% por um período de 5 minutos. Quando a regra é disparada, a quantidade de instâncias de VM aumenta por três.

Os seguintes parâmetros são usados para essa regra:

| Parâmetro         | Explicação                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU  |
| *timeGrain*       | Com que frequência as métricas são coletadas para análise.                                                                   | 1 minuto        |
| *timeAggregation* | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média         |
| *timeWindow*      | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 5 minutos       |
| *operator*        | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que    |
| *threshold*       | O valor que faz com que a regra de autoescala dispare uma ação.                                                      | 70%             |
| *direction*       | Define se o conjunto de dimensionamento deve ser reduzido ou escalado horizontalmente quando a regra se aplicar.                                              | Aumento        |
| *tipo*            | Indica que a quantidade de instâncias de VM deve ser modificada por um valor específico.                                    | Alterar contagem    |
| *value*           | Quantas instâncias de VM devem ser reduzidas ou escaladas horizontalmente quando a regra se aplicar.                                             | 3               |
| *cooldown*        | O tempo de espera antes da regra ser aplicada novamente para que as ações de autoescala tenham tempo para entrar em vigor. | 5 minutos       |

A regra a seguir seria adicionada na seção de perfil do provedor de recursos *Microsoft.insights/autoscalesettings* na seção anterior:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Definir uma regra para reduzir horizontal e automaticamente
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

O exemplo a seguir define uma regra para reduzir horizontalmente o número de instâncias de VM em um quando a carga da CPU média for menor que 30% por um período de 5 minutos. Essa regra seria adicionada ao perfil de dimensionamento automático após a regra anterior para escalar horizontalmente:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Criar um conjunto de dimensionamento de dimensionamento automático
Vamos usar um modelo de exemplo para criar um conjunto de dimensionamento e aplicar regras de dimensionamento automático. Você pode [revisar o modelo completo](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) ou [consultar a seção *Microsoft.insights/autoscalesettings* do provedor de recursos](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) do modelo.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais usando [az deployment group create](/cli/azure/deployment/group). Quando solicitado, forneça seu próprio nome de usuário e senha, como *azureuser*, que são usados como as credenciais para cada instância de VM:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="generate-cpu-load-on-scale-set"></a>Gerar carga de CPU no conjunto de dimensionamento
Para testar as regras de dimensionamento automático, gere alguma carga de CPU nas instâncias de VM no conjunto de dimensionamento. Essa carga de CPU simulada faz com que as regras de dimensionamento automático escalem horizontalmente e aumentem o número de instâncias de VM. Conforme a carga simulada de CPU vai sendo reduzida, as regras de dimensionamento automático reduzem horizontalmente e diminuem o número de instâncias de VM.

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
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Assim que o **stress** é interrompido nas instâncias de VM inicias, a carga média da CPU volta ao normal. Após mais 5 minutos, as regras de dimensionamento automático reduzem horizontalmente o número de instâncias de VM. As ações de redução horizontal removem instâncias de VM começando pelas IDs mais altas. Quando um conjunto de dimensionamento usa Conjuntos de Disponibilidade ou Zonas de Disponibilidade, as ações de reduzir horizontalmente são distribuídas uniformemente entre essas instâncias de VM. A saída de exemplo a seguir mostra uma instância de VM excluída conforme o conjunto de dimensionamento reduz horizontal e automaticamente:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Saia do *watch* usando `Ctrl-c`. O conjunto de dimensionamento continua a reduzir horizontalmente a cada 5 minutos e a remover uma instância de VM até que a contagem mínima de duas instâncias seja alcançada.


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group):

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

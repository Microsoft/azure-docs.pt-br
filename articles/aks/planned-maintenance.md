---
title: Usar a manutenção planejada para seu cluster do AKS (serviço kubernetes do Azure) (versão prévia)
titleSuffix: Azure Kubernetes Service
description: Saiba como usar a manutenção planejada no AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670368"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Usar a manutenção planejada para agendar janelas de manutenção para seu cluster do AKS (serviço kubernetes do Azure) (versão prévia)

O cluster AKS tem manutenção regular executada automaticamente. Por padrão, esse trabalho pode acontecer a qualquer momento. A manutenção planejada permite que você agende as janelas de manutenção semanais que atualizarão seu plano de controle, bem como os pods de Kube do sistema em uma instância do VMSS e minimizar o impacto da carga de trabalho. Depois de agendado, toda a manutenção ocorrerá durante a janela selecionada. Você pode agendar uma ou mais janelas semanais em seu cluster especificando um intervalo de dia ou hora em um dia específico. As janelas de manutenção são configuradas usando o CLI do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Limitações

Ao usar a manutenção planejada, as seguintes restrições se aplicam:

- AKS reserva o direito de dividir essas janelas para operações de manutenção não planejadas/reativas que são urgentes ou críticas.
- Atualmente, a execução de operações de manutenção é considerada *somente o melhor esforço* e não é garantido que ocorra dentro de uma janela especificada.
- As atualizações não podem ser bloqueadas por mais de sete dias.

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Você também precisa do *AKs-preview* CLI do Azure versão 0.5.4 ou posterior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Permitir manutenção em todas as segundas-feiras em 1:10:00 a 2:10:00

Para adicionar uma janela de manutenção, você pode usar o `az aks maintenanceconfiguration add` comando.

> [!IMPORTANT]
> As janelas de manutenção planejadas são especificadas em UTC (tempo Universal Coordenado).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

A saída de exemplo a seguir mostra a janela de manutenção de 1:10:00 para 2:10:00 a cada segunda-feira.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Para permitir a manutenção a qualquer momento durante um dia, omita o parâmetro de *hora de início* . Por exemplo, o comando a seguir define a janela de manutenção para o dia inteiro toda segunda-feira:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Adicionar uma configuração de manutenção com um arquivo JSON

Você também pode usar um arquivo JSON criar uma janela de manutenção em vez de usar parâmetros. Crie um `test.json` arquivo com o seguinte conteúdo:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

O arquivo JSON acima Especifica as janelas de manutenção todas as terças-feiras às 10:00-3:10:00 e todas as quartas-feiras em 1:10:00-2:10:00 e em 6:10:00-7:10:00. Também há uma exceção de *2021-05-26T03:00:00Z* para *2021-05-30T12:00:00Z* em que a manutenção não é permitida mesmo que se sobreponha a uma janela de manutenção. O comando a seguir adiciona as janelas de manutenção do `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Atualizar uma janela de manutenção existente

Para atualizar uma configuração de manutenção existente, use o `az aks maintenanceconfiguration update` comando.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Listar todas as janelas de manutenção em um cluster existente

Para ver todas as janelas de configuração de manutenção atuais no cluster AKS, use o `az aks maintenanceconfiguration list` comando.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Na saída abaixo, você pode ver que há duas janelas de manutenção configuradas para myAKSCluster. Uma janela está nas segundas-feiras em 1:10:00 e outra janela está na sexta-feira, às 4:10:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Mostrar uma janela de configuração de manutenção específica em um cluster AKS

Para ver uma janela de configuração de manutenção específica em seu cluster AKS, use o `az aks maintenanceconfiguration show` comando.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

A saída de exemplo a seguir mostra a janela de manutenção por *padrão*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Excluir uma determinada janela de configuração de manutenção em um cluster AKS existente

Para excluir uma determinada janela de configuração de manutenção em seu cluster AKS, use o `az aks maintenanceconfiguration delete` comando.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Próximas etapas

- Para começar a atualizar seu cluster AKS, consulte [atualizar um cluster AKs][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md

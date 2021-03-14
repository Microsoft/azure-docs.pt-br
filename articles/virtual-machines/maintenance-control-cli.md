---
title: Controle de manutenção para máquinas virtuais do Azure usando a CLI
description: Saiba como controlar quando a manutenção é aplicada às suas VMs do Azure usando o controle de manutenção e a CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 9425759de1e08bc83cac80cd1b56c602edb59fb1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562955"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Controlar atualizações com o controle de manutenção e o CLI do Azure

O controle de manutenção permite que você decida quando aplicar atualizações de plataforma à infraestrutura de host de suas VMs isoladas e hosts dedicados do Azure. Este tópico aborda as opções de CLI do Azure para o controle de manutenção. Para obter mais informações sobre os benefícios de usar o controle de manutenção, suas limitações e outras opções de gerenciamento, consulte [gerenciando atualizações de plataforma com o controle de manutenção](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Use `az maintenance configuration create` para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myconfig* com escopo para o host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Copie a ID de configuração da saída para usar mais tarde.

O uso de `--maintenance-scope host` garante que a configuração de manutenção seja usada para controlar atualizações na infraestrutura de host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, receberá um erro. Os nomes de configuração devem ser exclusivos para seu grupo de recursos.

Você pode consultar as configurações de manutenção disponíveis usando `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Criar uma configuração de manutenção com a janela agendada
Você também pode declarar uma janela agendada quando o Azure aplicará as atualizações em seus recursos. Este exemplo cria uma configuração de manutenção chamada myconfig com uma janela agendada de 5 horas na quarta segunda-feira de cada mês. Depois de criar uma janela agendada, você não precisa mais aplicar as atualizações manualmente.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> A **duração** da manutenção deve ser de *2 horas* ou mais. A **recorrência** de manutenção deve ser definida para pelo menos ocorrer uma vez em 35 dias.

A recorrência da manutenção pode ser expressa como diária, semanal ou mensal. Alguns exemplos incluem:
- **diário**-manutenção-janela-recorrência-a cada: "dia" **ou** "3Days"
- **semanalmente**-manutenção-janela-recorrência-a cada: "3Weeks" **ou** "semana sábado, domingo"
- **mensal**-manutenção-janela-recorrência-a cada: "month day23, day24" **ou** "month Last domingo" **ou** "month quarta segunda-feira"


## <a name="assign-the-configuration"></a>Atribuir a configuração

Use `az maintenance assignment create` para atribuir a configuração à VM isolada ou ao host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolada

Aplique a configuração a uma VM usando a ID da configuração. Especifique `--resource-type virtualMachines` e forneça o nome da VM para `--resource-name` , e o grupo de recursos para a VM no `--resource-group` , e o local da VM para `--location` . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Host dedicado

Para aplicar uma configuração a um host dedicado, você precisa incluir `--resource-type hosts` , `--resource-parent-name` com o nome do grupo de hosts e `--resource-parent-type hostGroups` . 

O parâmetro `--resource-id` é a ID do host. Você pode usar [AZ VM host Get-Instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) para obter a ID do host dedicado.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Verificar configuração

Você pode verificar se a configuração foi aplicada corretamente ou verificar qual configuração está aplicada no momento usando `az maintenance assignment list` .

### <a name="isolated-vm"></a>VM isolada

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Host dedicado 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Verificar se há atualizações pendentes

Use `az maintenance update list` para ver se há atualizações pendentes. Update--Subscription para ser a ID da assinatura que contém a VM.

Se não houver nenhuma atualização, o comando retornará uma mensagem de erro, que conterá o texto: `Resource not found...StatusCode: 404` .

Se houver atualizações, apenas uma será retornada, mesmo se houver várias atualizações pendentes. Os dados desta atualização serão retornados em um objeto:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>VM isolada

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Host dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura. Substitua os valores dos recursos pelos seus próprios.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Aplicar atualizações

Use `az maintenance apply update` para aplicar atualizações pendentes. Em caso de sucesso, esse comando retornará JSON contendo os detalhes da atualização.

### <a name="isolated-vm"></a>VM isolada

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Host dedicado

Aplicar atualizações a um host dedicado.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Verificar o status da aplicação de atualizações 

Você pode verificar o progresso das atualizações usando `az maintenance applyupdate get` . 

Você pode usar `default` como o nome da atualização para ver os resultados da última atualização ou substituir `myUpdateName` pelo nome da atualização que foi retornada quando você executou `az maintenance applyupdate create` .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será a hora em que a atualização foi concluída, iniciada por você ou pela plataforma caso a janela de automanutenção não tenha sido usada. Se nunca houvesse uma atualização aplicada por meio do controle de manutenção, o valor padrão será exibido.

### <a name="isolated-vm"></a>VM isolada

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Host dedicado

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Excluir uma configuração de manutenção

Use `az maintenance configuration delete` para excluir uma configuração de manutenção. A exclusão da configuração remove o controle de manutenção dos recursos associados.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).

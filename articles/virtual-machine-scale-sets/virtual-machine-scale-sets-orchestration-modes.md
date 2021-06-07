---
title: Modos de orquestração para conjuntos de dimensionamento de máquinas virtuais no Azure
description: Saiba como usar modos de orquestração flexíveis e uniformes para conjuntos de dimensionamento de máquinas virtuais no Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: cc862759ce28c4d23dbc2197f63311e29ba82709
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607495"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Visualização: modos de orquestração para conjuntos de dimensionamento de máquinas virtuais no Azure 

Os conjuntos de dimensionamento de máquinas virtuais fornecem um agrupamento lógico de máquinas virtuais gerenciadas por plataforma. Com os conjuntos de dimensionamento, você cria um modelo de configuração de máquina virtual, adiciona ou remove automaticamente instâncias adicionais com base na carga de CPU ou memória e atualiza automaticamente para a versão mais recente do sistema operacional. Tradicionalmente, os conjuntos de dimensionamento permitem que você crie máquinas virtuais usando um modelo de configuração de VM fornecido no momento da criação do conjunto de dimensionamento e o conjunto de dimensionamento só pode gerenciar máquinas virtuais criadas implicitamente com base no modelo de configuração. 

Os modos de orquestração do conjunto de dimensionamento permitem que você tenha mais controle sobre como as instâncias de máquina virtual são gerenciadas pelo conjunto de dimensionamento. 

> [!IMPORTANT]
> O modo de orquestração é definido quando você cria o conjunto de dimensionamento e não pode ser alterado ou atualizado posteriormente.


## <a name="scale-sets-with-uniform-orchestration"></a>Conjuntos de dimensionamento com orquestração uniforme
Otimizado para cargas de trabalho sem estado de grande escala com instâncias idênticas.

Os conjuntos de dimensionamento de máquinas virtuais com orquestração uniforme usam um perfil ou modelo de máquina virtual para escalar verticalmente para a capacidade desejada. Embora haja alguma capacidade de gerenciar ou personalizar instâncias de máquinas virtuais individuais, o uniforme usa instâncias de VM idênticas. As instâncias de VM uniformes individuais são expostas por meio dos comandos da API VM do conjunto de dimensionamento de máquinas virtuais. Instâncias individuais não são compatíveis com os comandos da API de VM IaaS do Azure padrão, recursos de gerenciamento do Azure, como Azure Resource Manager as permissões RBAC de marcação de recursos, backup do Azure ou Azure Site Recovery. A orquestração uniforme fornece garantias de alta disponibilidade de domínio de falha quando configuradas com menos de 100 instâncias. A orquestração uniforme está disponível e dá suporte a uma gama completa de gerenciamento e orquestração de conjunto de dimensionamento, incluindo dimensionamento automático baseado em métrica, proteção de instância e atualizações automáticas de so. 


## <a name="scale-sets-with-flexible-orchestration"></a>Conjuntos de dimensionamento com orquestração flexível 
Alcance alta disponibilidade em escala com tipos de máquina virtual idênticos ou múltiplos.

Com a orquestração flexível, o Azure fornece uma experiência unificada no ecossistema de VM do Azure. A orquestração flexível oferece garantias de alta disponibilidade (até 1000 VMs), distribuindo VMs entre domínios de falha em uma região ou em uma zona de disponibilidade. Isso permite que você Escale horizontalmente seu aplicativo enquanto mantém o isolamento de domínio de falha que é essencial para executar cargas de trabalho com e sem monitoração de estado, incluindo:
- Cargas de trabalho baseadas em quorum
- Bancos de dados Open-Source
- Aplicativos com estado
- Serviços que exigem alta disponibilidade e grande escala
- Serviços que desejam combinar tipos de máquina virtual ou aproveitar VMs pontuais e sob demanda juntas
- Aplicativos de conjunto de disponibilidade existentes  

> [!IMPORTANT]
> Os conjuntos de dimensionamento de máquinas virtuais no modo de orquestração flexível estão atualmente em visualização pública. Um procedimento de aceitação é necessário para usar a funcionalidade de visualização pública descrita abaixo.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>O que mudou com o modo de orquestração flexível?
Uma das principais vantagens da orquestração flexível é que ela fornece recursos de orquestração em VMs IaaS padrão do Azure, em vez de máquinas virtuais filho do conjunto de dimensionamento. Isso significa que você pode usar todas as APIs de VM padrão ao gerenciar instâncias de orquestração flexíveis, em vez das APIs de VM do conjunto de dimensionamento de máquinas virtuais que você usa com orquestração uniforme. Durante o período de visualização, há várias diferenças entre o gerenciamento de instâncias em orquestração flexível versus orquestração uniforme. Em geral, recomendamos que você use as APIs de VM IaaS do Azure padrão quando possível. Nesta seção, destacamos exemplos de práticas recomendadas para o gerenciamento de instâncias de VM com orquestração flexível.  

### <a name="assign-fault-domain-during-vm-creation"></a>Atribuir domínio de falha durante a criação da VM
Você pode escolher o número de domínios de falha para o conjunto de escala de orquestração flexível. Por padrão, quando você adiciona uma VM a um conjunto de dimensionamento flexível, o Azure espalha uniformemente as instâncias entre domínios de falha. Embora seja recomendável permitir que o Azure atribua o domínio de falha, para cenários avançados ou de solução de problemas, você pode substituir esse comportamento padrão e especificar o domínio de falha onde a instância irá parar.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nomenclatura de instância 
Ao criar uma VM e adicioná-la a um conjunto de dimensionamento flexível, você tem controle total sobre os nomes de instância dentro das regras da Convenção de nomenclatura do Azure. Quando as VMs são adicionadas automaticamente ao conjunto de dimensionamento por meio de dimensionamento automático, você fornece um prefixo e o Azure acrescenta um número exclusivo ao final do nome.

### <a name="query-instances-for-power-state"></a>Instâncias de consulta para o estado de energia
O método preferencial é usar o grafo de recursos do Azure para consultar todas as VMs em um conjunto de dimensionamento de máquinas virtuais. O grafo de recursos do Azure fornece recursos de consulta eficientes para recursos do Azure em escala entre assinaturas. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Consultar recursos com o [Azure Resource Graph](../governance/resource-graph/overview.md) é uma maneira conveniente e eficiente de consultar recursos do Azure e minimizar as chamadas de API para o provedor de recursos. O grafo de recursos do Azure é um cache eventualmente consistente em que os recursos novos ou atualizados podem não ser refletidos por até 60 segundos. Você pode:
- Listar VMs em um grupo de recursos ou assinatura.
- Use a opção expandir para recuperar o modo de exibição de instância (atribuição de domínio de falha, Estados de energia e provisionamento) para todas as VMs em sua assinatura.
- Use a API Get VM e os comandos para obter o modo de exibição de modelo e instância para uma única instância.

### <a name="scale-sets-vm-batch-operations"></a>Operações de lote de VM de conjuntos de dimensionamento
Use os comandos padrão da VM para iniciar, parar, reiniciar, excluir instâncias, em vez das APIs de VM do conjunto de dimensionamento de máquinas virtuais. As operações de lote da VM do conjunto de dimensionamento de máquinas virtuais (iniciar tudo, parar tudo, refazer a imagem de tudo, etc.) não são usadas com o modo de orquestração flexível. 

### <a name="monitor-application-health"></a>Monitorar a integridade do aplicativo 
O monitoramento de integridade do aplicativo permite que seu aplicativo forneça ao Azure uma pulsação para determinar se seu aplicativo está íntegro ou não íntegro. O Azure pode substituir automaticamente as instâncias de VM que não estão íntegras. Para instâncias do conjunto de dimensionamento flexível, você deve instalar e configurar a extensão de integridade do aplicativo na máquina virtual. Para instâncias do conjunto de dimensionamento uniforme, você pode usar a extensão de integridade do aplicativo ou medir a integridade com uma Azure Load Balancer investigação de integridade personalizada. 

### <a name="list-scale-sets-vm-api-changes"></a>Listar os conjuntos de dimensionamento da API de VM 
Os conjuntos de dimensionamento de máquinas virtuais permitem listar as instâncias que pertencem ao conjunto de dimensionamento. Com a orquestração flexível, o comando listar conjuntos de dimensionamento de máquinas virtuais VM fornece uma lista de IDs de VM de conjuntos de dimensionamento. Em seguida, você pode chamar os comandos de VM obter conjuntos de dimensionamento de máquinas virtuais para obter mais detalhes sobre como o conjunto de dimensionamento está funcionando com a instância de VM. Para obter os detalhes completos da VM, use os comandos GET VM padrão ou o [grafo de recursos do Azure](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Recuperar dados de diagnóstico de inicialização 
Use os comandos e as APIs de VM padrão para recuperar dados de diagnóstico de inicialização de instância e capturas de tela. Os comandos e as APIs de diagnóstico de inicialização da máquina virtual não são usados com instâncias do modo de orquestração flexível.

### <a name="vm-extensions"></a>Extensões de VM 
Use extensões destinadas a máquinas virtuais padrão, em vez de extensões destinadas a instâncias do modo de orquestração uniformes.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Uma comparação de conjuntos flexíveis, uniformes e de disponibilidade 
A tabela a seguir compara o modo de orquestração flexível, o modo de orquestração uniforme e os conjuntos de disponibilidade por seus recursos.

| Recurso | Com suporte pela orquestração flexível (versão prévia) | Com suporte pela orquestração uniforme (disponibilidade geral) | Com suporte do AvSets (disponibilidade geral) |
|-|-|-|-|
|         Tipo de máquina virtual  | VM IaaS do Azure padrão (Microsoft. Compute/VirtualMachines)  | VMs específicas do conjunto de dimensionamento (Microsoft. Compute/virtualmachinescalesets/VirtualMachines)  | VM IaaS do Azure padrão (Microsoft. Compute/VirtualMachines)  |
|         SKUs compatíveis  |            Série D, série E, série F, série A, série B, Intel, AMD  |            Todos os SKUs  |            Todos os SKUs  |
|         Zonas de Disponibilidades  |            Opcionalmente, especifique todas as instâncias que se esterram em uma única zona de disponibilidade |            Especificar instâncias que se esterram entre 1, 2 ou 3 zonas de disponibilidade  |            Sem suporte  |
|         Controle total sobre VM, NICs, discos  |            Sim  |            Controle limitado com a API de VM dos conjuntos de dimensionamento de máquinas virtuais  |            Sim  |
|         Dimensionamento automático  |            Não  |            Sim  |            Não  |
|         Atribuir uma VM a um domínio de falha específico  |            Sim  |             Não   |            Não  |
|         Remover NICs e discos ao excluir instâncias de VM  |            Não  |            Sim  |            Não  |
|         Política de atualização (conjuntos de dimensionamento de VM) |            Não  |            Automático, sem interrupção, manual  |            N/D  |
|         Atualizações automáticas do sistema operacional (conjuntos de dimensionamento de VM) |            Não  |            Sim  |            N/D  |
|         Em patches de segurança de convidado  |            Sim  |            Não  |            Sim  |
|         Encerrar notificações (conjuntos de dimensionamento de VM) |            Não  |            Sim  |            N/D  |
|         Reparo de instância (conjuntos de dimensionamento de VM) |            Não  |            Sim   |            N/D  |
|         Redes aceleradas  |            Sim  |            Sim  |            Sim  |
|         Instâncias e preços especiais   |            Sim, você pode ter instâncias de prioridade regular e especial  |            Sim, as instâncias devem ser todas pontuais ou regulares  |            Não, somente instâncias de prioridade regular  |
|         Misturar sistemas operacionais  |            Sim, o Linux e o Windows podem residir no mesmo conjunto de dimensionamento flexível |            Não, as instâncias são o mesmo sistema operacional  |               Sim, o Linux e o Windows podem residir no mesmo conjunto de dimensionamento flexível |
|         Monitorar a integridade do aplicativo  |            Extensão de integridade do aplicativo  |            Extensão de integridade do aplicativo ou investigação do Azure Load Balancer  |            Extensão de integridade do aplicativo  |
|         Discos UltraSSD   |            Sim  |            Sim, somente para implantações zonais  |            Não  |
|         Infiniband   |            Não  |            Sim, somente grupo de posicionamento único  |            Sim  |
|         Acelerador de Gravação   |            Não  |            Sim  |            Sim  |
|         Grupos de posicionamento de proximidade   |            Sim  |            Sim  |            Sim  |
|         Hosts dedicados do Azure   |            Não  |            Sim  |            Sim  |
|         SLB básico   |            Não  |            Sim  |            Sim  |
|         Azure Load Balancer SKU Standard |            Sim  |            Sim  |            Sim  |
|         Gateway de Aplicativo  |            Não  |            Sim  |            Sim  |
|         Controle de manutenção   |            Não  |            Sim  |            Sim  |
|         Listar VMs em conjunto  |            Sim  |            Sim  |            Sim, listar VMs em AvSet  |
|         Alertas do Azure  |            Não  |            Sim  |            Sim  |
|         VM Insights  |            Não  |            Sim  |            Sim  |
|         Serviço de Backup do Azure  |            Sim  |            Sim  |            Sim  |
|         Azure Site Recovery  |     Não  |            Não  |            Sim  |
|         Adicionar/remover VM existente para o grupo  |            Não  |            Não  |            Não  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registrar-se para o modo de orquestração flexível
Antes de implantar conjuntos de dimensionamento de máquinas virtuais no modo de orquestração flexível, você deve primeiro registrar sua assinatura para o recurso de visualização. O registro pode levar vários minutos para ser concluído. Você pode usar os comandos a seguir Azure PowerShell ou CLI do Azure para se registrar.

### <a name="azure-portal"></a>Portal do Azure
Navegue até a página de detalhes da assinatura que você gostaria de criar um conjunto de dimensionamento no modo de orquestração flexível e selecione recursos de visualização no menu. Selecione os dois recursos do Orchestrator a serem habilitados: _VMOrchestratorSingleFD_ e _VMOrchestratorMultiFD_ e pressione o botão registrar. O registro de recursos pode levar até 15 minutos.

![Registro de recurso.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Depois que os recursos tiverem sido registrados para sua assinatura, conclua o processo de aceitação propagando a alteração no provedor de recursos de computação. Navegue até a guia provedores de recursos para sua assinatura, selecione Microsoft. Compute e clique em registrar novamente.

![Registrar novamente](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Use o cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar a visualização para sua assinatura. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0 
Use o [registro de recurso AZ](/cli/azure/feature#az-feature-register) para habilitar a visualização para sua assinatura. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Introdução ao modo de orquestração flexível

Comece a usar o modo de orquestração flexível para seus conjuntos de dimensionamento por meio do portal do Azure, CLI do Azure, Terraform ou API REST.

### <a name="azure-portal"></a>Portal do Azure

Crie um conjunto de dimensionamento de máquinas virtuais no modo de orquestração flexível por meio do portal do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **conjuntos de dimensionamento de máquinas virtuais**. 
1. Selecione **criar** na página **conjuntos de dimensionamento de máquinas virtuais** .
1. Na página **criar um conjunto de dimensionamento de máquinas virtuais** , exiba a seção **orquestração** .
1. Para o **modo de orquestração**, selecione a opção **flexível** .
1. Defina a **contagem de domínios de falha**.
1. Conclua a criação do conjunto de dimensionamento. Consulte [criar um conjunto de dimensionamento no portal do Azure](quick-create-portal.md#create-virtual-machine-scale-set) para obter mais informações sobre como criar um conjunto de dimensionamento.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Modo de orquestração no portal ao criar um conjunto de dimensionamento":::

Em seguida, adicione uma máquina virtual ao conjunto de dimensionamento no modo de orquestração flexível.

1. Na barra de pesquisa, procure e selecione **máquinas virtuais**.
1. Selecione **Adicionar** na página **máquinas virtuais** .
1. Na guia **noções básicas** , exiba a seção **detalhes da instância** .
1. Adicione sua VM ao conjunto de dimensionamento no modo de orquestração flexível selecionando o conjunto de dimensionamento nas **Opções de disponibilidade**. Você pode adicionar a máquina virtual a um conjunto de dimensionamento na mesma região, zona e grupo de recursos.
1. Conclua a criação de sua máquina virtual. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Adicionar VM ao conjunto de dimensionamento flexível do modo orquestração":::


### <a name="azure-cli-20"></a>CLI do Azure 2.0
Crie um conjunto de dimensionamento de máquinas virtuais flexível com CLI do Azure. O exemplo a seguir mostra a criação de um conjunto de dimensionamento flexível em que a contagem de domínios de falha está definida como 3, uma máquina virtual é criada e adicionada ao conjunto de dimensionamento flexível. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Crie um conjunto de dimensionamento de máquinas virtuais flexível com Terraform. Esse processo requer o **Terraform Azurerm Provider v 2.15.0** ou posterior. Observe os seguintes parâmetros:
- Quando nenhuma zona é especificada, `platform_fault_domain_count` pode ser 1, 2 ou 3, dependendo da região.
- Quando uma zona é especificada, `the fault domain count` pode ser 1.
- `single_placement_group` o parâmetro deve ser `false` para conjuntos de dimensionamento de máquinas virtuais flexíveis.
- Se você estiver fazendo uma implantação regional, não será necessário especificar `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>API REST

1. Crie um conjunto de dimensionamento vazio. Os seguintes parâmetros são necessários:
    - Versão da API 2019-12-01 (ou superior) 
    - O grupo de posicionamento único deve ser `false` ao criar um conjunto de dimensionamento flexível

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Adicione máquinas virtuais ao conjunto de dimensionamento.
    1. Atribua a `virtualMachineScaleSet` propriedade ao conjunto de dimensionamento que você criou anteriormente. É necessário especificar a `virtualMachineScaleSet` propriedade no momento da criação da VM. 
    1. Você pode usar a função de modelo **Copy ()** Azure Resource Manager para criar várias VMs ao mesmo tempo. Consulte [iteração de recurso](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) em modelos de Azure Resource Manager. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Consulte [início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) para obter um exemplo completo.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quanto escala o suporte à orquestração flexível?**

Você pode adicionar até 1000 VMs a um conjunto de dimensionamento no modo de orquestração flexível.

**Como a disponibilidade com orquestração flexível é comparada aos conjuntos de disponibilidade ou à orquestração uniforme?**

|   | Orquestração flexível  | Orquestração uniforme  | Conjuntos de Disponibilidade  |
|-|-|-|-|
| Implantar entre zonas de disponibilidade  | Não  | Sim  | Não  |
| Garantias de disponibilidade de domínio de falha dentro de uma região  | Sim, até 1000 instâncias podem ser distribuídas entre até três domínios de falha na região. A contagem máxima de domínios de falha varia por região  | Sim, até 100 instâncias  | Sim, até 200 instâncias  |
| Grupos de posicionamento  | O modo flexível sempre usa vários grupos de posicionamento (singlePlacementGroup = false)  | Você pode escolher um grupo de posicionamento único ou vários grupos de posicionamento | N/D  |
| Atualizar domínios  | Nenhuma, manutenção ou atualizações de host são concluídas domínio de falha por domínio de falha  | Até 5 domínios de atualização  | Até 20 domínios de atualização  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Solucionar problemas de conjuntos de dimensionamento com orquestração flexível
Encontre a solução certa para o cenário de solução de problemas. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Causa:** A assinatura não está registrada para a visualização pública do modo de orquestração flexível. 

**Solução:** Siga as instruções acima para se registrar para a visualização pública do modo de orquestração flexível. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Causa:** O `platformFaultDomainCount` parâmetro é inválido para a região ou zona selecionada. 

**Solução:** Você deve selecionar um `platformFaultDomainCount` valor válido. Para implantações zonais, o `platformFaultDomainCount` valor máximo é 1. Para implantações regionais em que nenhuma zona é especificada, o máximo `platformFaultDomainCount` varia dependendo da região. Consulte [gerenciar a disponibilidade de VMs para scripts](../virtual-machines/availability.md) para determinar a contagem máxima de domínios de falha por região. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Causa:** Tentativa de excluir um conjunto de dimensionamento em modo de orquestração flexível associado a uma ou mais máquinas virtuais. 

**Solução:** Exclua todas as máquinas virtuais associadas ao conjunto de dimensionamento no modo de orquestração flexível e, em seguida, você poderá excluir o conjunto de dimensionamento.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Causa:** A assinatura está registrada para a versão prévia do modo de orquestração flexível; no entanto, o `singlePlacementGroup` parâmetro é definido como *true*. 

**Solução:** O `singlePlacementGroup` deve ser definido como *false*. 


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Saiba como implantar seu aplicativo no conjunto de dimensionamento.](virtual-machine-scale-sets-deploy-app.md)
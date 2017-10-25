---
title: "Visão Geral dos Conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba mais sobre os Conjuntos de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fa08049fd0b13945de307e9d28224ea0d5a1307
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>O que são conjuntos de dimensionamento de máquinas virtuais no Azure?
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas igualmente, os conjuntos de dimensionamento são projetados para dar suporte total ao dimensionamento e nenhum pré-provisionamento de VMs é necessário. Portanto, é mais fácil criar serviços em larga escala direcionados para Big Compute, Big Data e cargas de trabalho em contêineres.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização. Para ver uma introdução aos conjuntos de dimensionamento, confira o [Comunicado no blog do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Para saber mais sobre conjuntos de dimensionamento, assista a estes vídeos:

* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Criando e gerenciando conjuntos de dimensionamento
Você pode criar um conjunto de dimensionamento no [Portal do Azure](https://portal.azure.com) selecionando **novo** e digitando **escala** na barra de pesquisa. **Conjunto de dimensionamento de máquinas virtuais** está listado nos resultados. A partir daí, você pode preencher os campos obrigatórios para personalizar e implantar o conjunto de dimensionamento. Você também tem opções para configurar as regras básicas de dimensionamento automático baseadas no uso da CPU no portal. 

Os conjuntos de escala podem ser implantados em uma [zona disponibilidade](../availability-zones/az-overview.md).

> [!NOTE]
> No momento, os Conjuntos de Dimensionamento de Máquinas Virtuais só oferecem suporte à implantação em uma única zona de disponibilidade. A implantação de várias zonas terá suporte no futuro.

Você pode definir e implantar conjuntos de dimensionamento usando modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx), assim como as VMs individuais do Azure Resource Manager. Portanto, você pode usar qualquer método de implantação do Azure Resource Manager padrão. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Você pode encontrar um conjunto de modelos de exemplo para conjunto de dimensionamento de máquinas virtuais no [repositório GitHub de modelos do Guia de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates). (Procure modelos com **vmss** no título.)

Para exemplos do modelo Início Rápido, um botão "Implantação do Azure" no arquivo leiame para cada modelo os vincula ao recurso de implantação no portal. Para implantar o conjunto de dimensionamento, clique no botão e preencha todos os parâmetros obrigatórios no portal. 

## <a name="scaling-a-scale-set-out-and-in"></a>Expandindo e reduzindo um conjunto de dimensionamento
Você pode alterar a capacidade de um conjunto de dimensionamento definido no portal do Azure clicando na seção **Dimensionamento** em **Configurações**. 

Para alterar a capacidade do conjunto de dimensionamento na linha de comando, use o comando **scale** na [CLI do Azure](https://github.com/Azure/azure-cli). Por exemplo, use este comando para definir um conjunto de dimensionamento definido com capacidade de 10 VMs:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Para definir o número de VMs em um conjunto de dimensionamento definido usando o PowerShell, use o comando **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de dimensionamento, usando um modelo do Azure Resource Manager, altere a propriedade **capacity** e reimplante o modelo. Essa simplicidade facilita a integração de conjuntos de dimensionamento com o dimensionamento automático do Azure ou a criação de sua própria camada de dimensionamento personalizada se você precisar definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure. 

Se estiver reimplantando um modelo do Azure Resource Manager para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas o pacote de propriedades **SKU** com a capacidade atualizada. [Aqui está um exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## <a name="autoscale"></a>Autoscale

Um conjunto de dimensionamento pode ser configurado opcionalmente com configurações de dimensionamento automático quando é criado no portal do Azure. O número de VMs pode ser aumentado ou diminuído com base na utilização média da CPU. 

Muitos dos modelos de conjunto de dimensionamento nos [modelos de Início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) definem configurações de dimensionamento automático. Você também pode adicionar configurações de dimensionamento automático a um conjunto de dimensionamento existente. Por exemplo, este script do Azure PowerShell adiciona o dimensionamento automático de CPU com base em um conjunto de dimensionamento:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

Você pode encontrar uma lista de métricas válidas para dimensionar nas [Métricas com suporte no Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) sob o título “Microsoft.Compute/virtualMachineScaleSets”. Existem opções de dimensionamento automático mais avançadas disponíveis, incluindo o dimensionamento automático com base em agendamento e o uso de webhooks para integração com sistemas de alertas.

## <a name="monitoring-your-scale-set"></a>Monitorando seu conjunto de dimensionamento
O [portal do Azure](https://portal.azure.com) lista os conjuntos de dimensionamento e mostra suas propriedades. O portal também dá suporte a operações de gerenciamento. Você pode executar operações de gerenciamento nos conjuntos de dimensionamento e nas VMs individuais em um conjunto de dimensionamento. O portal também fornece um gráfico de uso de recursos personalizável. 

Se você precisar ver ou editar a definição JSON subjacente de um recurso do Azure, também poderá usar o [Gerenciador de Recursos do Azure](https://resources.azure.com). Os conjuntos de dimensionamento são um recurso no provedor de recursos do Azure Microsoft.Compute. Nesse site, você pode vê-los expandindo os links abaixo:

**Assinaturas** > **sua assinatura** > **resourceGroups** > **provedores** > **Microsoft.Compute** > **virtualMachineScaleSets** > **seu conjunto de dimensionamento** > etc.

## <a name="scale-set-scenarios"></a>Cenários do conjunto de dimensionamento
Esta seção lista alguns cenários típicos de conjunto de dimensionamento. Além disso, alguns serviços do Azure de nível mais alto (como Lote, Service Fabric e Serviço de Contêiner) usam esses cenários.

* **Use RDP/SSH para se conectar a instâncias de conjunto de dimensionamento**: um conjunto de dimensionamento é criado dentro de uma rede virtual e as VMs individuais dentro dele não são endereços IP públicos alocados por padrão. Essa política evita a sobrecarga de despesas e de gerenciamento da alocação de endereços IP públicos diferentes para todos os nós na sua grade de computação. Se você precisa direcionar conexões externas para VMs de conjunto de dimensionamento, pode configurar um conjunto de dimensionamento para atribuir endereços IP públicos para novas VMs automaticamente. Como alternativa, você pode se conectar às VMs de outros recursos em sua rede virtual que possam receber endereços IP públicos, por exemplo, balanceadores de carga e máquinas virtuais autônomas. 
* **Conectar-se às VMs usando regras NAT**: você pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir um pool NAT de entrada. Essas ações mapeiam portas no endereço IP para uma porta em uma VM no conjunto de dimensionamento. Por exemplo:
  
  | Fonte | Porta de origem | Destino | Porta de destino |
  | --- | --- | --- | --- |
  |  IP público |Porta 50000 |vmss\_0 |Porta 22 |
  |  IP público |Porta 50001 |vmss\_1 |Porta 22 |
  |  IP público |Porta 50002 |vmss\_2 |Porta 22 |
  
   [Neste exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat), as regras NAT são definidas para habilitar uma conexão SSH para todas as VMs em um conjunto de dimensionamento usando um único endereço IP público.
  
   [Este exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) faz o mesmo com RDP e Windows.
* **Conectar às VMs usando uma "jumpbox"** - Se você criar um conjunto de dimensionamento e uma VM autônoma na mesma rede virtual, a VM autônoma e o conjunto de dimensionamento podem se conectar um ao outro usando seus endereços IP internos, conforme definido pela sub-rede ou rede virtual. Se você criar um endereço IP público e atribuí-lo à VM autônoma, poderá usar RDP ou SSH para se conectar à VM autônoma. Em seguida, você pode conectar-se dessa máquina às instâncias de conjunto de dimensionamento. Você pode perceber, nesse momento, que um simples conjunto de dimensionamento é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.
  
   Por exemplo, [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) implanta um conjunto de dimensionamento simples com uma VM autônoma. 
* **Balanceamento de carga para instâncias de conjunto de dimensionamento**: se desejar entregar trabalho a um cluster de VMs de cálculo usando uma abordagem round-robin, você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga de camada 4 apropriadas. É possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação. O [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/) do Azure também oferece suporte aos conjuntos de dimensionamento, além de cenários de balanceamento de carga de camada 7 e mais sofisticados.
  
   [Este exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) cria um conjunto de dimensionamento que executa servidores Web Apache e usa um balanceador de carga para balancear a carga que cada VM recebe. (observe o tipo de recurso Microsoft.Network/loadBalancers, bem como networkProfile e extensionProfile em virtualMachineScaleSet)

   [Este exemplo de Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) e [Este exemplo de Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) usam o Gateway de Aplicativo.  

* **Implantar um conjunto de dimensionamento como um cluster de cálculo em um gerenciador de cluster de PaaS**: os conjuntos de dimensionamento são muitas vezes descritos como uma função de trabalho de próxima geração. Embora seja uma descrição válida, eles correm o risco de confundir os recursos de conjunto de dimensionamento com recursos dos Serviços de Nuvem do Azure. De certa forma, os conjuntos de dimensionamento oferecem uma verdadeira função ou recurso de trabalho. Eles são um recurso de computação generalizado que independe da plataforma/do tempo de execução, é personalizável e se integra ao IaaS do Azure Resource Manager.
  
   Uma função de trabalho dos Serviços de Nuvem é limitada em termos de suporte de plataforma/tempo de execução (somente imagens da plataforma Windows). Mas ele também inclui serviços como permuta VIP, configurações de atualização editáveis e configurações específicas de implantação de aplicativo/tempo de execução. Esses serviços não estão *ainda* disponíveis nos conjuntos de dimensionamento ou são fornecidos por outros serviços de PaaS de nível mais alto, como o Azure Service Fabric. Você pode examinar os conjuntos de dimensionamento como uma infraestrutura que dá suporte a PaaS. Soluções PaaS como o [Service Fabric](https://azure.microsoft.com/services/service-fabric/) criam com base nessa infraestrutura.
  
   [Neste exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) da abordagem, o [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/) implanta um cluster com base em conjuntos de dimensionamento com um orquestrador de contêiner.

## <a name="scale-set-performance-and-scale-guidance"></a>Orientação de dimensionamento e desempenho do conjunto de dimensionamento
* Um conjunto de escala dá suporte a até 1.000 VMs. Se você criar e carregar suas próprias imagens VM personalizadas, o limite será 300. Para considerações sobre o uso de conjuntos de dimensionamento grandes, confira [Trabalhando com conjuntos de dimensionamento de máquinas virtuais grandes](virtual-machine-scale-sets-placement-groups.md).
* Você não precisa criar contas de armazenamento do Azure previamente para usar conjuntos de dimensionamento. Os conjuntos de dimensionamento dão suporte a discos gerenciados do Azure, o que elimina as preocupações de desempenho em relação ao número de discos por conta de armazenamento. Para saber mais, confira [Conjuntos de dimensionamento de máquinas virtuais do Azure e discos gerenciados](virtual-machine-scale-sets-managed-disks.md).
* Considere o uso de armazenamento Premium do Azure, em vez de Armazenamento do Azure para ter tempos de provisionamento de VM mais rápidos e mais previsíveis, além de um melhor desempenho de E/S.
* A cota de núcleos na região em que você está implantando limita o número de VMs que você pode criar. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com Serviços de Nuvem do Azure. Para consultar a cota, execute este comando da CLI do Azure: `azure vm list-usage`. Ou execute este comando do PowerShell: `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>Perguntas frequentes sobre os conjuntos de dimensionamento
**P.** Quantas VMs posso ter em um conjunto de dimensionamento?

**A.** Um conjunto de dimensionamento pode ter de 0 a 1.000 VMs baseadas em imagens da plataforma ou de 0 a 300 VM máquinas virtuais baseadas em imagens personalizadas. 

**P.** Há suporte para os discos de dados nos conjuntos de dimensionamento?

**A.** Sim. Um conjunto de dimensionamento pode definir uma configuração de discos de dados anexados que se aplica a todas as máquinas virtuais no conjunto. Para saber mais, confira [Conjuntos de dimensionamento do Azure e discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). Outras opções para armazenamento de dados incluem:

* Arquivos do Azure (unidades compartilhada de SMB)
* Unidade do sistema operacional
* Unidade TEMP (local, não tem relação com o Armazenamento do Azure)
* Serviço de dados do Azure (por exemplo, Tabelas e Blobs do Azure)
* Serviço de dados externo (por exemplo, banco de dados remoto)

**P.** Quais são as regiões do Azure que dão suporte aos conjuntos de dimensionamento?

**A.** Todas as regiões dão suporte aos conjuntos de dimensionamento.

**P.** Como posso criar um conjunto de dimensionamento usando uma imagem personalizada?

**A.** Crie um disco gerenciado com base em seu VHD de imagem personalizada e faça referência a ele em seu modelo de conjunto de dimensionamento. [Aqui está um exemplo](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**P.** Se eu reduzir a capacidade do meu conjunto de dimensionamento de 20 para 15, quais VMs serão removidas?

**A.** Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade. VMs com as IDs mais altas são removidas primeiro.

**P.** E se eu aumentar a capacidade de 15 para 18?

**A.** Se você aumentar a capacidade para 18, serão criadas três novas VMs. Em casa caso, a ID da instância VM será incrementada do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre domínios de falha e domínios de atualização.

**P.** Ao usar várias extensões em um conjunto de dimensionamento, posso impor uma sequência de execução?

**A.** Não diretamente, mas para a extensão de customScript, o script poderia aguardar por outra extensão ser concluída. Encontre orientações adicionais sobre o sequenciamento de extensão nesta postagem de blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Sequenciamento de extensão em conjuntos de dimensionamento de VMs do Azure).

**P.** Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

**A.** Sim. Um conjunto de dimensionamento é um conjunto de disponibilidade implícita com cinco domínios de falha e cinco domínios de atualização. Os conjuntos de dimensionamento com mais de 100 máquinas virtuais abrangem vários *grupos de posicionamento* que são equivalentes a vários conjuntos de disponibilidade. Para saber mais sobre grupos de posicionamento, confira [Como trabalhar com conjuntos de dimensionamento grandes de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de máquinas virtuais pode existir na mesma rede virtual como um conjunto de escala de VMs. Uma configuração comum é colocar as VMs que geralmente exigem configuração exclusiva no conjunto de disponibilidade do nó de controle e colocar nós de dados no conjunto de dimensionamento.

Você pode encontrar mais respostas para perguntas sobre conjuntos de dimensionamento nas [Perguntas frequentes sobre conjuntos de dimensionamento de máquinas virtuais do Azure](virtual-machine-scale-sets-faq.md).

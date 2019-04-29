---
title: Entender as IDs de instância para VMs do conjunto de dimensionamento de VMs do Azure | Microsoft Docs
description: Entender as IDs de instância para VMs do conjunto de dimensionamento de VMs do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618483"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Entender as IDs de instância para VMs do conjunto de dimensionamento de VMs do Azure
Este artigo descreve as IDs de instância para conjuntos de dimensionamento e as suas várias formas de exibição.

## <a name="scale-set-instance-ids"></a>IDs de instância do conjunto de dimensionamento

Cada VM em um conjunto de dimensionamento obtém uma ID de instância que a identifica com exclusividade. Essa ID de instância é usada nas APIs do conjunto de dimensionamento para realizar operações em uma determinada VM no conjunto de dimensionamento. Por exemplo, você pode especificar uma ID de instância específica para refazer a imagem ao usar a API de recriação de imagem:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Você pode obter a lista de IDs de instância listando todas as instâncias em um conjunto de dimensionamento:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para obter mais informações, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para obter mais informações, consulte a [Documentação do Powershell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para obter mais informações, consulte a [Documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Você também pode usar [resources.azure.com](https://resources.azure.com) ou [SDKs do Azure](https://azure.microsoft.com/downloads/) para listar as VMs em conjunto de dimensionamento.

A apresentação exata da saída depende das opções que você fornecer para o comando, mas alguns exemplos de saídas da CLI são apresentados a seguir:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Como você pode ver, a propriedade "instanceId" é apenas um número decimal. As IDs de instância poderão ser reutilizadas para novas instâncias quando instâncias antigas forem excluídas.

>[!NOTE]
> Não há **nenhuma garantia** na forma como as IDs de instância são atribuídas às VMs no conjunto de dimensionamento. Às vezes, pode parecer que elas aumentam sequencialmente, mas esse não é sempre o caso. Não utilize uma dependência de uma forma específica em que as IDs de instância sejam atribuídas às VMs.

## <a name="scale-set-vm-names"></a>Nomes de VMs do conjunto de dimensionamento

Na saída de exemplo acima, também há um "nome" para a VM. Esse nome assume a forma de "{scale-set-name}_{instance-id}". Esse nome é o que você vê no portal do Azure quando você lista instâncias em um conjunto de dimensionamento:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A parte {instance-id} do nome é o mesmo número decimal que a propriedade "instanceId" abordada anteriormente.

## <a name="instance-metadata-vm-name"></a>Nome da VM de Metadados da Instância

Se você consultar os [metadados da instância](../virtual-machines/windows/instance-metadata-service.md) em uma VM de conjunto de dimensionamento, você verá um "nome" na saída:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Esse nome é o mesmo que o discutido anteriormente.

## <a name="scale-set-vm-computer-name"></a>Nome do computador da VM de conjunto de dimensionamento

Cada VM em um conjunto de dimensionamento também tem um nome de computador atribuído a ela. Esse nome de computador é o nome do host da VM na [resolução de nome DNS fornecida pelo Azure na rede virtual](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nome de computador está no formato "{computer-name-prefix}{base-36-instance-id}".

{base-36-instance-id} está em [base 36](https://en.wikipedia.org/wiki/Base36) e tem sempre seis dígitos de comprimento. Se a representação base 36 do número tiver menos de seis dígitos, {base-36-instance-id} será preenchido com zeros para que tenha seis dígitos de comprimento. Por exemplo, uma instância com {computer-name-prefix} "nsgvmss" e ID de instância 85 terá o nome de computador "nsgvmss00002D".

>[!NOTE]
> O prefixo do nome do computador é uma propriedade do modelo do conjunto de dimensionamento que você definir, para que ele possa ser diferente do próprio nome do conjunto de dimensionamento.

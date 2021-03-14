---
title: Recursos com suporte para o Red Hat OpenShift 3.11 no Azure
description: Entenda quais regiões do Azure e tamanhos de máquina virtual têm suporte Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635601"
---
# <a name="azure-red-hat-openshift-resources"></a>Recursos do Azure Red Hat OpenShift

Este tópico lista as regiões do Azure e os tamanhos de máquina virtual com suporte no serviço Microsoft Azure Red Hat OpenShift 3,11.

## <a name="azure-regions"></a>Regiões do Azure

Consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para obter uma lista atual de regiões em que você pode implantar clusters do Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual

Aqui estão os tamanhos de máquina virtual com suporte que você pode especificar para os nós de computação em seu cluster do Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de unidades que podem ser anexadas. Isso pode não estar tão claro quanto o tamanho da memória ou da CPU.
> Nem todos os tamanhos de VM estão disponíveis em todas as regiões. Mesmo que a API ofereça suporte ao tamanho especificado, você poderá receber um erro se o tamanho não estiver disponível na região que você especificar.
> Consulte a [lista atual de tamanhos de VM com suporte por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos de nó de computação

Os seguintes tamanhos de nó de computação têm suporte da API REST do Azure Red Hat OpenShift:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Tamanhos de nó mestre

Os seguintes tamanhos de nó mestre/infraestrutura têm suporte da API REST do Azure Red Hat OpenShift:

|Tamanho|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

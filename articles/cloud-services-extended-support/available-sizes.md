---
title: Tamanhos disponíveis para os serviços de nuvem do Azure (suporte estendido)
description: Tamanhos disponíveis para implantações de serviços de nuvem do Azure (suporte estendido)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd8011782d134031393731a29594d44aba41b2ef
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713296"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tamanhos disponíveis para os serviços de nuvem do Azure (suporte estendido)

Este artigo descreve os tamanhos de máquina virtual disponíveis para instâncias de serviços de nuvem (suporte estendido).   

| Família de SKU |  ACU/Core | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190*
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[H](../virtual-machines/h-series.md) | 290 – 300* | 

>[!NOTE]
> ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho. A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configurar tamanhos para serviços de nuvem (suporte estendido)

Você pode especificar o tamanho da máquina virtual de uma instância de função como parte do modelo de serviço no arquivo de definição de serviço. O tamanho da função determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local.

Por exemplo, definir o tamanho da instância da função Web como `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Alterar o tamanho de uma função existente

Para alterar o tamanho de uma função existente, altere o tamanho da máquina virtual no arquivo de definição de serviço (csdef), reempacote o serviço de nuvem e reimplante-o. 

## <a name="get-a-list-of-available-sizes"></a>Obter uma lista de tamanhos disponíveis 

Para recuperar uma lista de tamanhos disponíveis [, consulte SKUs de recursos-List](/rest/api/compute/resourceskus/list) e aplique os seguintes filtros:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
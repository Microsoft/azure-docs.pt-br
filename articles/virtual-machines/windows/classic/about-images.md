---
title: "Sobre imagens de máquinas virtuais do Windows | Microsoft Docs"
description: "Saiba mais sobre como as imagens são usadas com máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="about-images-for-windows-virtual-machines"></a>Sobre imagens de máquinas virtuais do Windows
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como localizar e usar imagens no modelo do Resource Manager, consulte [aqui](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Trabalhando com imagens

É possível usar o módulo do Azure PowerShell e o portal do Azure para gerenciar as imagens disponíveis para sua assinatura do Azure. O módulo do Azure PowerShell fornece mais opções de comando, para que você possa identificar exatamente o que deseja ver ou fazer. O portal do Azure fornece uma GUI para muitas das tarefas administrativas diárias.

Estes são alguns exemplos que usam o módulo do Azure PowerShell.

* **Obter todas as imagens**: `Get-AzureVMImage`retorna uma lista de todas as imagens disponíveis na assinatura atual: suas imagens e as imagens fornecidas pelo Azure ou por parceiros. A lista resultante pode ser grande. Os exemplos a seguir mostram como obter uma lista menor.
* **Obter as famílias de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista das famílias de imagem, mostrando as cadeias de caracteres da propriedade **ImageFamily**.
* **Obtenha todas as imagens em uma família específica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Localizar Imagens de VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` esse cmdlet funciona com a filtragem da propriedade DataDiskConfiguration, que só se aplica a imagens de VM. Este exemplo também filtra a saída para apenas o nome do rótulo e da imagem.
* **Salvar uma imagem generalizada**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Salvar uma imagem especializada**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > O parâmetro OSState é obrigatório para criar uma imagem de VM, que inclui discos do sistema operacional e de dados anexados. Se você não usar o parâmetro, o cmdlet criará uma imagem do SO. O valor do parâmetro indica se a imagem é generalizada ou especializada, com base em se o disco do sistema operacional foi preparado para reutilização.

* **Excluir uma imagem**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Próximas etapas
Você também pode [criar um computador Windows usando o portal do Azure](tutorial.md).

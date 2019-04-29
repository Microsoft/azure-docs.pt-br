---
title: Baixar o modelo de uma VM do Azure | Microsoft Docs
description: Baixe o modelo de uma VM para ajudar a automatizar as implantações no modelo de implantação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075873"
---
# <a name="download-the-template-for-a-vm"></a>Baixar o modelo para uma VM
Quando você cria uma VM no Azure usando o portal ou o PowerShell, um modelo do Resource Manager é criado automaticamente para você. Você pode usar este modelo para duplicar rapidamente uma implantação. O modelo contém informações sobre todos os recursos em um grupo de recursos. Para uma máquina virtual, isso significa que o modelo contém tudo o que é criado para dar suporte à VM desse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Baixar o modelo usando o portal
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **exportar modelo**.
5. Selecione **Baixar** no menu na parte superior e salve o arquivo .zip em seu computador local.
6. Abra o arquivo zip e extraia os arquivos para uma pasta. O arquivo .zip contém:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

O arquivo template.json é o modelo.

## <a name="download-the-template-using-powershell"></a>Baixar o modelo usando o PowerShell
Você também pode baixar o arquivo de modelo .json usando o cmdlet [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup). Você pode usar o parâmetro `-path` para fornecer o nome de arquivo e o caminho para o arquivo .json. Este exemplo mostra como baixar o modelo para o grupo de recursos denominado **myResourceGroup** para a pasta **C:\users\public\downloads** no computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implantar recursos usando modelos, veja o [passo a passo do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).


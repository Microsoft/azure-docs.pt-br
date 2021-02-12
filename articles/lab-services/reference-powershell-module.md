---
title: Módulo do PowerShell para Azure Lab Services | Microsoft Docs
description: Este artigo fornece informações sobre um módulo do PowerShell que ajuda no gerenciamento de artefatos no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646518"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo Az.LabServices do PowerShell (versão prévia)
AZ. LabServices é um módulo do PowerShell que simplifica o gerenciamento dos serviços de laboratório do Azure. Ele fornece funções combináveis para criar, consultar, atualizar e excluir contas de laboratório, laboratórios, VMs e imagens. Para obter mais informações sobre esse módulo, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em versão **prévia**. 

## <a name="example-command"></a>Comando de exemplo
Aqui está um exemplo de como usar um comando do PowerShell para interromper todas as VMs em execução em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale [Azure PowerShell](/powershell/azure/) se ele não existir em seu computador. 
2. Baixe [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) em seu computador.
3. Importe o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Execute o comando a seguir para listar todos os laboratórios em sua assinatura.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Próximas etapas
Consulte o [Home Page AZ. LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
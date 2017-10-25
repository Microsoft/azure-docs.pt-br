---
title: "Gerenciar tempo de execução de integração do Azure-SSIS | Microsoft Docs"
description: "Saiba como reconfigurar o tempo de execução de integração do Azure-SSIS no Azure Data Factory depois de já ter provisionado."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: 780e5673b5ed159a7f170373d54fea7c1713a910
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Gerenciar um tempo de execução de integração do Azure-SSIS
O artigo [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra como criar um o tempo de execução de integração do Azure-SSIS usando o Azure Data Factory. Este artigo o complementa fornecendo informações sobre como parar, iniciar, reconfigurar ou remover um tempo de execução de integração do Azure-SSIS.  


## <a name="stop"></a>Parar 
Pare o tempo de execução de integração do Azure-SSIS. Este comando libera todos os seus nós e interrompe a cobrança.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Iniciar 
Inicie o tempo de execução de integração do Azure-SSIS. Este comando aloca todos os seus nós e inicia a cobrança.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>Reconfigurar
Depois de provisionar e iniciar uma instância do tempo de execução de integração do Azure-SSIS, você pode reconfigurá-lo executando uma sequência de cmdlets do PowerShell `Stop` - `Set` - `Start` consecutivamente. Por exemplo, o script do PowerShell a seguir altera o número de nós alocados para a instância de tempo de execução de integração do Azure-SSIS para 5.

1. Primeiro, pare o tempo de execução de integração do Azure-SSIS executando o seguinte comando:

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Depois, expanda o tempo de execução de integração do Azure-SSIS para cinco nós.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, inicie o tempo de execução de integração do Azure-SSIS. Isso aloca todos os seus nós para executar pacotes do SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Remover
Para remover um tempo de execução de integração do Azure-SSIS, execute o seguinte comando: 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>Próximas etapas
Confira estes tópicos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Unir um IR do Azure-SSIS a uma VNet](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual (VNet) do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a VNet para que o IR do Azure-SSIS possa unir-se à VNet. 

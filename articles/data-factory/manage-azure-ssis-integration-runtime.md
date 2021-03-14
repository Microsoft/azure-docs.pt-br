---
title: Reconfigurar o runtime de integração do Azure-SSIS
description: Saiba como reconfigurar o runtime de integração do Azure-SSIS no Azure Data Factory depois de já tê-lo provisionado.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370675"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o runtime de integração do Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como reconfigurar um runtime de integração existente do Azure-SSIS. Para criar um IR (runtime de integração) do Azure-SSIS no Azure Data Factory, confira [Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Use a interface do usuário do Data Factory para parar, editar/reconfigurar ou excluir um IR do Azure SSIS. 

1. Abra Data Factory interface do usuário selecionando o bloco **criar & monitor** na home page do data Factory.
2. Selecione o Hub **gerenciar** abaixo **de página inicial**, **Editar** e **monitorar** hubs para mostrar o painel **conexões** .

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um IR do Azure SSIS
No painel **Conexões** do hub **Gerenciar**, mude para a página **Runtimes de integração** e selecione **Atualizar**. 

   ![Painel Conexões](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Você pode editar/reconfigurar seu Azure-SSIS IR selecionando seu nome. Você também pode selecionar os botões relevantes para monitorar/iniciar/parar/excluir seu Azure-SSIS IR, gerar automaticamente um pipeline do ADF com a atividade Executar Pacote do SSIS para execução em seu Azure-SSIS IR e exibir o código/conteúdo JSON de seu Azure-SSIS IR.  Editar/excluir seu Azure-SSIS IR só pode ser feito quando for ele estiver interrompido.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de provisionar e iniciar uma instância do runtime de integração do Azure-SSIS, você pode reconfigurá-lo executando uma sequência de cmdlets do PowerShell `Stop` - `Set` - `Start` consecutivamente. Por exemplo, o script do PowerShell a seguir altera o número de nós alocados para a instância de runtime de integração do Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um IR do Azure-SSIS

1. Primeiro, pare o tempo de execução de integração do Azure-SSIS usando o cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Este comando libera todos os seus nós e interrompe a cobrança.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Em seguida, reconfigure o Azure-SSIS IR usando o cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . O comando de exemplo a seguir escala horizontalmente um runtime de integração do Azure-SSIS para cinco nós.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Em seguida, inicie o tempo de execução de integração do Azure-SSIS usando o cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Esse comando aloca todos os seus nós para executar pacotes do SSIS.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Excluir um IR do Azure-SSIS
1. Primeiro, liste todos os IRs do Azure SSIS em seu data factory.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Em seguida, pare todos os IRs do Azure SSIS em seu data factory.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Em seguida, remova todos os IRs do Azure SSIS de seu data factory um por um.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Por fim, remova o data factory.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Se tiver criado um novo grupo de recursos, remova-o.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Próximas etapas
Confira estes tópicos para obter mais informações sobre o runtime do Azure-SSIS: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e usa o banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o SQL Instância Gerenciada do Azure e ingressar o IR em uma rede virtual. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas.
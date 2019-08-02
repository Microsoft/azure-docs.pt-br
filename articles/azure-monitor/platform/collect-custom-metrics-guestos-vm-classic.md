---
title: Enviar métricas do sistema operacional convidado para o armazenamento de dados do Monitor do Azure para uma máquina virtual do Windows (clássica)
description: Enviar métricas do sistema operacional convidado para o armazenamento de dados do Monitor do Azure para uma máquina virtual do Windows (clássica)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 57212da1a8da7ee6c57faf2413b88a413df04817
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129585"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Enviar métricas do sistema operacional convidado para o armazenamento de dados do Monitor do Azure para uma máquina virtual do Windows (clássica)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A extensão de diagnóstico do [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (conhecida como "WAD" ou "Diagnóstico") permite coletar métricas e logs do sistema operacional convidado (sistema operacional convidado) em execução como parte de uma máquina virtual, serviço em nuvem ou Cluster do Service Fabric. A extensão pode enviar telemetria para [muitos locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do sistema operacional convidado para uma máquina virtual do Windows (clássica) ao repositório de métricas do Monitor do Azure. A partir da versão 1.11 do Diagnostics, você pode gravar métricas diretamente no repositório de métricas do Monitor do Azure, onde métricas de plataforma padrão já foram coletadas. 

Armazená-los nesse local permite acessar as mesmas ações que você faz para as métricas da plataforma. As ações incluem alertas em tempo quase real, gráficos, roteamento, acesso a uma API REST e muito mais. No passado, a extensão Diagnostics gravava no Armazenamento do Azure, mas não no armazenamento de dados do Monitor do Azure. 

O processo descrito neste artigo funciona somente em máquinas virtuais clássicas que executam o sistema operacional Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador ou co-administrador de serviços](../../billing/billing-add-change-azure-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura precisará ser registrada com [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisará ter o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Criar uma conta clássica de máquina virtual e armazenamento

1. Crie uma VM clássica usando o portal do Azure.
   ![Criar VM clássica](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Ao criar esta VM, escolha a opção para criar uma nova conta de armazenamento clássica. Usaremos essa conta de armazenamento em etapas posteriores.

1. No portal do Azure, vá para o blade de recursos **Storage accounts**. Selecione **chaves**e anote o nome da conta de armazenamento e a chave de conta de armazenamento. Você precisará dessas informações em etapas posteriores.
   ![Chaves de acesso de armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Crie uma entidade de serviço em seu locatário do Active Directory do Azure usando as instruções em [Criar uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). Observe o seguinte ao percorrer este processo: 
- Crie um novo segredo de cliente para este aplicativo.
- Salve a chave e o ID do cliente para uso nas etapas posteriores.

Conceda a este aplicativo as permissões “Monitoring Metrics Publisher” para o recurso que você deseja emitir métricas. Você pode usar um grupo de recursos ou uma assinatura inteira.  

> [!NOTE]
> A extensão Diagnostics usa a entidade de serviço para autenticar no Monitor do Azure e emitir métricas para sua VM clássica.

## <a name="author-diagnostics-extension-configuration"></a>Configuração da extensão Author Diagnostics

1. Prepare o seu arquivo de configuração da extensão Diagnostics. Esse arquivo determina quais logs e contadores de desempenho a extensão Diagnostics deve coletar para sua VM clássica. A seguir está um exemplo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Na seção “SinksConfig” do seu arquivo de diagnósticos, defina um novo coletor do Azure Monitor, da seguinte maneira:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Na seção do arquivo de configuração em que a lista de contadores de desempenho a serem coletados está listada, direcione os contadores de desempenho para o coletor do Monitor do Azure "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Na configuração privada, defina a conta do Monitor do Azure. Em seguida, adicione as informações do principal de serviço para usar para emitir métricas.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Salve esse arquivo localmente.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implantar a Extensão de diagnóstico para seu serviço de nuvem

1. Inicie o PowerShell e entre.

    ```powershell
    Login-AzAccount
    ```

1. Comece definindo o contexto para sua VM clássica.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Defina o contexto da conta de armazenamento clássico criada com a VM.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Defina o caminho do arquivo Diagnostics para uma variável usando o seguinte comando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Prepare a atualização para sua VM clássica com o arquivo de diagnóstico que possui o coletor do Monitor do Azure configurado.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implante a atualização em sua VM executando o seguinte comando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão Diagnostics. Todos os logs ou contadores de desempenho especificados no arquivo de configuração Diagnostics serão gravados na conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Plotar as métricas no portal do Azure

1.  Vá para o portal do Azure. 

1.  No menu esquerdo, selecione **Monitor**.

1.  Sobre o **Monitor** folha, selecione **métricas**.

    ![Navegue de métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu suspenso de recursos, selecione sua VM clássica.

1. No menu suspenso namespaces, selecione **azure.vm.windows.guest**.

1. No menu suspenso de métricas, selecione **Memory \ Committed Bytes in Use**.
   ![Plotar as métricas](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


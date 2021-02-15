---
title: Enviar métricas de VM do Windows clássicas para Azure Monitor banco de dados de métricas
description: Enviar métricas do sistema operacional convidado para o armazenamento de dados do Monitor do Azure para uma máquina virtual do Windows (clássica)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 87277c0c61f6d63e453386724dd472d2663e3148
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045212"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Enviar métricas do sistema operacional convidado para o banco de dados de métricas de Azure Monitor para uma máquina virtual do Windows (clássica)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A extensão de diagnóstico do [Azure Monitor](./diagnostics-extension-overview.md) (conhecida como "WAD" ou "Diagnóstico") permite coletar métricas e logs do sistema operacional convidado (sistema operacional convidado) em execução como parte de uma máquina virtual, serviço em nuvem ou Cluster do Service Fabric. A extensão pode enviar telemetria para [muitos locais diferentes.](./data-platform.md?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado para uma máquina virtual do Windows (clássica) para o banco de dados de métricas Azure Monitor. A partir da versão 1.11 do Diagnostics, você pode gravar métricas diretamente no repositório de métricas do Monitor do Azure, onde métricas de plataforma padrão já foram coletadas. 

Armazená-los nesse local permite acessar as mesmas ações que você faz para as métricas da plataforma. As ações incluem alertas em tempo quase real, gráficos, roteamento, acesso a uma API REST e muito mais. Anteriormente, a Extensão de diagnóstico gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor. 

O processo descrito neste artigo funciona somente em máquinas virtuais clássicas que executam o sistema operacional Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador ou co-administrador de serviços](../../cost-management-billing/manage/add-change-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura deve ser registrada com [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Você precisará ter o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell](../../cloud-shell/overview.md) instalado.

- O recurso de VM deve estar em uma [região com suporte para métricas personalizadas](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Criar uma conta clássica de máquina virtual e armazenamento

1. Crie uma VM clássica usando o portal do Azure.
   ![Criar VM clássica](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Ao criar esta VM, escolha a opção para criar uma nova conta de armazenamento clássica. Usaremos essa conta de armazenamento em etapas posteriores.

1. No portal do Azure, vá para o blade de recursos **Storage accounts**. Selecione **chaves**e anote o nome da conta de armazenamento e a chave de conta de armazenamento. Você precisará dessas informações em etapas posteriores.
   ![Chaves de acesso de armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Crie uma entidade de serviço em seu locatário do Azure Active Directory usando as instruções em [criar uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). Observe o seguinte ao percorrer este processo: 
- Crie um novo segredo de cliente para este aplicativo.
- Salve a Chave e a ID do cliente para serem usadas em etapas posteriores.

Conceda a este aplicativo as permissões “Monitoring Metrics Publisher” para o recurso que você deseja emitir métricas. Você pode usar um grupo de recursos ou uma assinatura inteira.  

> [!NOTE]
> A extensão Diagnostics usa a entidade de serviço para autenticar no Monitor do Azure e emitir métricas para sua VM clássica.

## <a name="author-diagnostics-extension-configuration"></a>Criar configuração de Extensão de diagnóstico

1. Prepare o arquivo de configuração de Extensão de diagnóstico. Esse arquivo determina quais logs e contadores de desempenho a extensão Diagnostics deve coletar para sua VM clássica. A seguir está um exemplo:

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

1.  Acesse o portal do Azure. 

1.  No menu à esquerda, selecione **Monitor.**

1.  Sobre o **Monitor** folha, selecione **métricas**.

    ![Navegue de métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu suspenso de recursos, selecione sua VM clássica.

1. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**.

1. No menu suspenso de métricas, selecione **Bytes de Memória\Confirmados em Uso**.
   ![Plotar as métricas](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

---
title: Enviar as métricas do SO convidado aos Serviços de Nuvem clássicos de armazenamento de métricas do Azure Monitor
description: Enviar as métricas do SO convidado aos Serviços de Nuvem de armazenamento de métricas do Azure Monitor
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 90e841628d989a16f504d2efd7a2c7b18335ff48
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129499"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Enviar as métricas do SO convidado aos Serviços de Nuvem clássicos de armazenamento de métricas do Azure Monitor 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com a [Extensão de diagnóstico](diagnostics-extension-overview.md) do Azure Monitor, você pode coletar logs e métricas do SO (sistema operacional) convidado executado como parte de uma máquina virtual, do serviço de nuvem ou do cluster do Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado dos Serviços de Nuvem clássicos do Azure para o armazenamento de métricas do Azure Monitor. A partir do Diagnóstico versão 1.11, você pode gravar as métricas diretamente no armazenamento de métricas do Azure Monitor, no qual as métricas da plataforma padrão já são coletadas. 

Armazená-las nesse local permite que você acesse as mesmas ações possíveis para as métricas da plataforma. As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso a partir de uma API REST e muito mais.  Anteriormente, a Extensão de diagnóstico gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.  

O processo descrito neste artigo só funciona para contadores de desempenho nos Serviços de Nuvem do Azure. Ele não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador ou co-administrador de serviços](~/articles/billing/billing-add-change-azure-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura precisará ser registrada com [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisará ter o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

## <a name="provision-a-cloud-service-and-storage-account"></a>Provisionar um serviço de nuvem e uma conta de armazenamento 

1. Crie e implante um serviço de nuvem clássico. Um exemplo de aplicativo e implantação de Serviços de Nuvem clássicos pode ser encontrado em [Introdução aos Serviços de Nuvem do Azure e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. É possível usar uma conta de armazenamento existente ou implantar uma nova. É melhor se a conta de armazenamento estiver na mesma região que o serviço de nuvem clássico que você criou. No portal do Azure, vá até a folha de recursos **Contas de armazenamento** e escolha **Chaves**. Anote o nome da conta de armazenamento e a chave da conta de armazenamento. Você precisará dessas informações em etapas posteriores.

   ![Chaves da conta de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 

Crie uma entidade de serviço em seu locatário do Azure Active Directory usando as instruções em [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que pode acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Observe o seguinte ao percorrer esse processo: 

- Você pode colocar qualquer URL na URL de entrada.  
- Crie um novo segredo do cliente para esse aplicativo.  
- Salve a chave e a ID do cliente para uso em etapas posteriores.  

Ao aplicativo criado na etapa anterior *Editor de métricas de monitoramento*, dê as permissões ao recurso do qual deseja emitir métricas. Se você planeja usar o aplicativo para emitir métricas personalizadas de vários recursos, conceda essas permissões no nível da assinatura ou do grupo de recursos.  

> [!NOTE]
> A Extensão de diagnóstico usa a entidade de serviço para autenticação no Azure Monitor e emissão de métricas para seu serviço de nuvem.

## <a name="author-diagnostics-extension-configuration"></a>Criar configuração de Extensão de diagnóstico 

Prepare o arquivo de configuração de Extensão de diagnóstico. Esse arquivo determina quais logs e contadores de desempenho a Extensão de diagnóstico deve coletar para seu serviço de nuvem. Veja a seguir um arquivo de exemplo de configuração de diagnóstico:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Na seção "SinksConfig" do seu arquivo de diagnóstico, defina um novo coletor do Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Na seção de seu arquivo de configuração na qual você lista os contadores de desempenho a serem coletados, adicione o coletor do Azure Monitor. Essa entrada garante que todos os contadores de desempenho especificados sejam roteados para o Azure Monitor como métricas. Você pode adicionar ou remover contadores de desempenho de acordo com as suas necessidades. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Por fim, na configuração privada, adicione uma seção *Conta do Azure Monitor*. Insira a ID do cliente da entidade de serviço e o segredo criados anteriormente. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Salve esse arquivo de diagnóstico localmente.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implantar a Extensão de diagnóstico para seu serviço de nuvem 

Inicie o PowerShell e faça logon no Azure. 

```powershell
Login-AzAccount 
```

Use os comandos a seguir para armazenar os detalhes da conta de armazenamento que você criou anteriormente. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

De modo semelhante, defina o caminho do arquivo de diagnóstico para uma variável usando o comando a seguir:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Usando o comando a seguir, implante a Extensão de diagnóstico ao serviço de nuvem com o arquivo de diagnóstico com o coletor do Azure Monitor configurado:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da Extensão de diagnóstico. Todos os logs ou contadores de desempenho especificados no arquivo de configuração de diagnóstico são gravados na conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Plotar métricas no portal do Azure 

1. Vá para o portal do Azure. 

   ![Métricas do portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu esquerdo, selecione **Monitor**.

3. Na folha **Monitor**, selecione a guia **Métricas (versão prévia)** .

4. No menu suspenso do recurso, selecione seu serviço de nuvem clássico.

5. No menu suspenso de namespaces, selecione **azure.vm.windows.guest**. 

6. No menu suspenso de métricas, selecione **Bytes de Memória\Confirmados em Uso**. 

Use a filtragem de dimensão e os recursos de divisão para exibir a memória total usada por uma função específica ou instância de função. 

 ![Métricas do portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


---
title: Enviar métricas clássicas dos serviços de nuvem para Azure Monitor banco de dados de métricas
description: Descreve o processo para enviar métricas de desempenho do SO convidado para serviços de nuvem clássicos do Azure para o repositório de métrica Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: d6866361b78656d99888c4df70cc0c92ed096425
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737045"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Enviar as métricas do SO convidado aos Serviços de Nuvem clássicos de armazenamento de métricas do Azure Monitor 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com a [Extensão de diagnóstico](../agents/diagnostics-extension-overview.md) do Azure Monitor, você pode coletar logs e métricas do SO (sistema operacional) convidado executado como parte de uma máquina virtual, do serviço de nuvem ou do cluster do Service Fabric. A extensão pode enviar telemetria para [muitos locais diferentes.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado dos Serviços de Nuvem clássicos do Azure para o armazenamento de métricas do Azure Monitor. A partir da versão 1.11 do Diagnostics, você pode gravar métricas diretamente no repositório de métricas do Monitor do Azure, onde métricas de plataforma padrão já foram coletadas. 

Armazená-las nesse local permite que você acesse as mesmas ações possíveis para as métricas da plataforma. As ações incluem alertas em tempo quase real, gráficos, roteamento, acesso a uma API REST e muito mais.  Anteriormente, a Extensão de diagnóstico gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.  

O processo descrito neste artigo só funciona para contadores de desempenho nos Serviços de Nuvem do Azure. Ele não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador ou co-administrador de serviços](../../cost-management-billing/manage/add-change-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura deve ser registrada com [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Você precisará ter o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell](../../cloud-shell/overview.md) instalado.

- Seu serviço de nuvem deve estar em uma [região que dá suporte a métricas personalizadas](./metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Provisionar um serviço de nuvem e uma conta de armazenamento 

1. Crie e implante um serviço de nuvem clássico. Um exemplo de aplicativo e implantação de Serviços de Nuvem clássicos pode ser encontrado em [Introdução aos Serviços de Nuvem do Azure e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. É possível usar uma conta de armazenamento existente ou implantar uma nova. É melhor se a conta de armazenamento estiver na mesma região que o serviço de nuvem clássico que você criou. No portal do Azure, vá até a folha de recursos **Contas de armazenamento** e escolha **Chaves**. Anote o nome da conta de armazenamento e a chave da conta de armazenamento. Você precisará dessas informações em etapas posteriores.

   ![Chaves de conta de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 

Crie uma entidade de serviço em seu locatário do Azure Active Directory usando as instruções em [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md). Observe o seguinte ao percorrer esse processo: 

- Você pode colocar qualquer URL na URL de entrada.  
- Crie um novo segredo de cliente para este aplicativo.  
- Salve a Chave e a ID do cliente para serem usadas em etapas posteriores.  

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

1. Acesse o portal do Azure. 

   ![Captura de tela mostra o portal do Azure com monitor e, em seguida, métricas selecionadas.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu à esquerda, selecione **Monitor.**

3. Na folha **Monitor**, selecione a guia **Métricas (versão prévia)**.

4. No menu suspenso do recurso, selecione seu serviço de nuvem clássico.

5. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**. 

6. No menu suspenso de métricas, selecione **Bytes de Memória\Confirmados em Uso**. 

Use a filtragem de dimensão e os recursos de divisão para exibir a memória total usada por uma função específica ou instância de função. 

 ![Captura de tela mostra dados de métricas.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [métricas personalizadas](./metrics-custom-overview.md).
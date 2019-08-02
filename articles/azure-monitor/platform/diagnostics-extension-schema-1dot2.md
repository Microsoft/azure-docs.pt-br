---
title: Esquema de configuração do Diagnóstico do Azure extensão 1.2
description: Relevante APENAS se você estiver usando o SDK do Azure 2.5 com Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou Serviços de Nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237829"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Esquema de configuração do Azure Diagnostics 1.2
> [!NOTE]
> Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de Máquinas Virtuais do Azure, Conjuntos de Escala de Máquina Virtual, Service Fabric e Serviços de Nuvem.  Esta página só é relevante se você estiver usando um desses serviços.
>

O Diagnóstico do Azure é usado com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics.

Esse esquema define os possíveis valores que podem ser usados para inicializar as definições de configuração de diagnóstico quando o monitor de diagnóstico é iniciado.  


 Baixe a definição do esquema do arquivo de configuração pública ao executar o seguinte comando PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Para saber mais sobre o uso do Diagnóstico do Azure, confira [Habilitando o Diagnóstico nos Serviços de Nuvem do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Namespace de Configuração de Diagnóstico  
 O namespace XML para o arquivo de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 O elemento primário do arquivo de configuração de diagnóstico. A tabela a seguir descreve os elementos do arquivo de configuração.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**WadCfg**|Obrigatória. Definições de configuração para os dados de telemetria a serem coletados.|  
|**StorageAccount**|O nome da conta do Armazenamento do Azure para armazenar os dados. Isso também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|O diretório na máquina virtual a ser usado pelo agente de monitoramento para armazenar dados de evento. Caso não seja definido, será usado o seguinte diretório padrão:<br /><br /> Para uma função de Trabalho/da Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos obrigatórios são:<br /><br /> -                      **path** - o diretório no sistema a ser usado pelo Diagnóstico do Azure.<br /><br /> -                      **expandEnvironment** - controla se as variáveis de ambiente estão expandidas ou não no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
Define as configurações para que os dados de telemetria sejam coletados. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Obrigatória. Os atributos opcionais são:<br /><br /> -                     **overallQuotaInMB** - a quantidade máxima de espaço em disco local que pode ser consumido pelos diversos tipos de dados de diagnóstico coletados pelo Diagnóstico do Azure. A configuração padrão é 5120MB.<br /><br /> -                     **useProxyServer** - configure o Diagnóstico do Azure para usar as configurações de servidor proxy como definido nas configurações do IE.|  
|**CrashDumps**|Habilite a coleta de despejos de memória. Os atributos opcionais são:<br /><br /> -                     **containerName** - o nome do contêiner de blobs na sua conta do Armazenamento do Azure a ser usado para armazenar os despejos de memória.<br /><br /> -                     **crashDumpType** - configura o Diagnóstico do Azure para coletar minidespejos de memória ou despejos completos de memória.<br /><br /> -                     **directoryQuotaPercentage** - configura o percentual de **overallQuotaInMB** a ser reservado para despejos de memória na VM (Máquina Virtual).|  
|**DiagnosticInfrastructureLogs**|Habilite a coleta de logs gerados pelo Diagnóstico do Azure. Os logs de infraestrutura de diagnóstico são úteis para solucionar problemas de sistema de diagnóstico. Os atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** - configura o nível de severidade mínimo dos logs coletados.<br /><br /> -                     **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o Armazenamento do Azure arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Diretórios**|Habilita a coleta do conteúdo de um diretório, logs de solicitação de acesso com falha do IIS e/ou logs do IIS. Atributo opcional:<br /><br /> **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Configura a coleta de eventos ETW do EventSource e/ou os provedores baseados no Manifesto ETW.|  
|**Métricas**|Este elemento permite gerar uma tabela de contador de desempenho otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela Métricas e também na tabela Contador de Desempenho. Atributo obrigatório:<br /><br /> **resourceId** - esta é a ID de recurso da Máquina Virtual na qual você está implantando o Diagnóstico do Azure. Obtenha o **resourceID** do [portal do Azure](https://portal.azure.com). Selecione **Procurar** -> **Grupos de Recursos** ->  **<Nome\>** . Clique no bloco **Propriedades** e copie o valor do campo **ID**.|  
|**PerformanceCounters**|Habilita a coleta de contadores de desempenho. Atributo opcional:<br /><br /> **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Habilita a coleta de Logs de Eventos do Windows. Atributo opcional:<br /><br /> **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Habilita a coleta de despejos de memória. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Obrigatória. Atributo obrigatório:<br /><br /> **processName** - o nome do processo para o qual você deseja que o Diagnóstico do Azure colete um despejo de memória.|  
|**crashDumpType**|Configura o Diagnóstico do Azure para coletar minidespejos de memória ou despejos completos de memória.|  
|**directoryQuotaPercentage**|Configura o percentual de **overallQuotaInMB** a ser reservado para despejos de memória na VM.|  

## <a name="directories-element"></a>Elemento Directories  
 Habilita a coleta do conteúdo de um diretório, logs de solicitação de acesso com falha do IIS e/ou logs do IIS. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSources**|Uma lista de diretórios para monitorar.|  
|**FailedRequestLogs**|A inclusão desse elemento na configuração habilita a coleta de logs sobre solicitações com falha para um site ou aplicativo do IIS. Você também deve habilitar as opções de rastreamento em **system.WebServer** em **Web.config**.|  
|**IISLogs**|A inclusão deste elemento na configuração habilita a coleta de logs do IIS:<br /><br /> **containerName** - o nome do contêiner de blobs na sua conta do Armazenamento do Azure a ser usado para armazenar os logs do IIS.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Uma lista de diretórios para monitorar. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Obrigatória. Atributo obrigatório:<br /><br /> **containerName** - o nome do contêiner de blob na sua conta do Armazenamento do Azure na ser usado para armazenar os arquivos de log.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 **DirectoryConfiguration** pode incluir o elemento **Absolute** ou **LocalResource**, mas não ambos. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Absolute**|O caminho absoluto para o diretório a ser monitorado. Os atributos a seguir são obrigatórios:<br /><br /> -                     **Path** - o caminho absoluto para o diretório a ser monitorado.<br /><br /> -                      **expandEnvironment** - configura se as variáveis de ambiente em Path são expandidas ou não.|  
|**LocalResource**|O caminho relativo a um recurso local a ser monitorado. Os atributos obrigatórios são:<br /><br /> -                     **Name** - o recurso local que contém o diretório a ser monitorado<br /><br /> -                     **relativePath** - o caminho relativo a Name que contém o diretório a ser monitorado|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura a coleta de eventos ETW do EventSource e/ou os provedores baseados no Manifesto ETW. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a coleta de eventos gerados desde a [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo obrigatório:<br /><br /> **provider** - o nome da classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** - o nível mínimo de severidade a transferir para sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML Tipo de Dados de Duração](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Atributo obrigatório:<br /><br /> **provider** - o GUID do provedor de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** - o nível mínimo de severidade a transferir para sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML Tipo de Dados de Duração](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura a coleta de eventos gerados desde a [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - a id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - a id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  

## <a name="metrics-element"></a>Elemento Metrics  
 Permite gerar uma tabela de contador de desempenho otimizada para consultas rápidas. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**MetricAggregation**|Atributo obrigatório:<br /><br /> **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML Tipo de Dados de Duração](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Habilita a coleta de contadores de desempenho. A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os atributos a seguir são obrigatórios:<br /><br /> -                     **counterSpecifier** - o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu host, execute o comando `typeperf`.<br /><br /> -                     **sampleRate** - Com que frequência o contador deve ser testado.<br /><br /> Atributo opcional:<br /><br /> **unit** - a unidade de medida do contador.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**anotação**|Atributo obrigatório:<br /><br /> **displayName** - o nome de exibição para o contador<br /><br /> Atributo opcional:<br /><br /> **locale** - local onde exibir o nome do contador|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 A tabela abaixo descreve os elementos filhos:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSource**|Os logs de Eventos do Windows a serem coletados. Atributo obrigatório:<br /><br /> **name** - a consulta XPath que descreve os eventos do windows a serem coletados. Por exemplo:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Para coletar todos os eventos, especifique "*".|


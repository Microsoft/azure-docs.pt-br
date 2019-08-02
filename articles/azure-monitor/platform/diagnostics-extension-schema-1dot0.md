---
title: Esquema de Configuração do Azure Diagnostics 1.0
description: Relevante APENAS se você estiver usando o SDK do Azure 2.4 e abaixo com Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou Serviços de Nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237851"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Esquema de Configuração do Azure Diagnostics 1.0
> [!NOTE]
> Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de Máquinas Virtuais do Azure, Conjuntos de Escala de Máquina Virtual, Service Fabric e Serviços de Nuvem.  Esta página só é relevante se você estiver usando um desses serviços.
>

O Diagnóstico do Azure é usado com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics.

O arquivo de configuração do Diagnóstico do Azure define os valores que são usados para inicializar o Monitor de Diagnóstico. Esse arquivo é usado para inicializar as definições de configuração de diagnóstico quando o monitor de diagnóstico é iniciado.  

 Por padrão, o arquivo de esquema de configuração do Diagnóstico do Azure é instalado no diretório `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`. Substitua `<version>` pela versão instalada do [SDK do Azure](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  O arquivo de configuração de diagnóstico é normalmente usado com tarefas de inicialização que exigem a coleta prévia de dados de diagnóstico no processo de inicialização. Para obter mais informações sobre como usar o Diagnóstico do Azure, consulte [Coletar dados do log usando o Diagnóstico do Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Namespace DiagnosticsConfiguration  
 O namespace XML para o arquivo de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementos de esquema  
 O arquivo de configuração de diagnóstico inclui os elementos a seguir.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration  
O elemento de nível superior do arquivo de configuração de diagnóstico.  

Atributos:

|Atributo  |Tipo   |Necessário| Padrão | Descrição|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Opcional | PT1M| Especifica o intervalo no qual o monitor de diagnóstico sonda em busca de alterações de configuração de diagnóstico.|  
|**overallQuotaInMB**|unsignedInt|Opcional| 4000 MB. Se você fornecer um valor, ele não deverá exceder esse valor |A quantidade total de armazenamento de sistema de arquivos alocado para todos os buffers de registro em log.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento DiagnosticInfrastructureLogs  
Define a configuração de buffer para os logs gerados pela infraestrutura de diagnóstico subjacente.

Elemento pai: Elemento DiagnosticMonitorConfiguration.  

Atributos:

|Atributo|Tipo|Descrição|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|cadeia de caracteres|Opcional. Especifica o nível de severidade mínimo para as entradas de log transferidas. O valor padrão é **Indefinido**. Outros possíveis valores são **Detalhado**, **Informação**, **Aviso**, **Erro** e **Crítico**.|  
|**scheduledTransferPeriod**|duração|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="logs-element"></a>Elemento Logs  
 Define a configuração de buffer para logs básicos do Azure.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|cadeia de caracteres|Opcional. Especifica o nível de severidade mínimo para as entradas de log transferidas. O valor padrão é **Indefinido**. Outros possíveis valores são **Detalhado**, **Informação**, **Aviso**, **Erro** e **Crítico**.|  
|**scheduledTransferPeriod**|duração|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="directories-element"></a>Elemento Directories  
Define a configuração de buffer para logs baseados em arquivo que você pode definir.

Elemento pai: Elemento DiagnosticMonitorConfiguration.  


Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferPeriod**|duração|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Define o diretório de despejos de falha.

 Elemento pai: Elemento Directories.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contêiner**|cadeia de caracteres|O nome do contêiner para onde o conteúdo do diretório será transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="failedrequestlogs-element"></a>Elemento FailedRequestLogs  
 Define o diretório de log de solicitação com falha.

 Elemento pai Elemento Directories.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contêiner**|cadeia de caracteres|O nome do contêiner para onde o conteúdo do diretório será transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

##  <a name="iislogs-element"></a>Elemento IISLogs  
 Define o diretório de log do IIS.

 Elemento pai Elemento Directories.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contêiner**|cadeia de caracteres|O nome do contêiner para onde o conteúdo do diretório será transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Define zero ou mais diretórios de log adicionais.

 Elemento pai: Elemento Directories.

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Define o diretório de arquivos de log a ser monitorado.

 Elemento pai: Elemento DataSources.

Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contêiner**|cadeia de caracteres|O nome do contêiner para onde o conteúdo do diretório será transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="absolute-element"></a>Elemento Absolute  
 Define um caminho absoluto do diretório a ser monitorado com opção de expansão de ambiente.

 Elemento pai: Elemento DirectoryConfiguration.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**path**|cadeia de caracteres|Obrigatória. O caminho absoluto para o diretório a ser monitorado.|  
|**expandEnvironment**|boolean|Obrigatória. Se definido como **true**, as variáveis de ambiente no caminho serão expandidas.|  

## <a name="localresource-element"></a>Elemento LocalResource  
 Define um caminho relativo para um recurso local indicado na definição do serviço.

 Elemento pai: Elemento DirectoryConfiguration.  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**name**|cadeia de caracteres|Obrigatória. O nome do recurso local que contém o diretório a ser monitorado.|  
|**relativePath**|cadeia de caracteres|Obrigatória. O caminho relativo a um recurso local a ser monitorado.|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Define o caminho para coleta do contador de desempenho.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.


 Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferPeriod**|duração|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 Define o contador de desempenho a ser coletado.

 Elemento pai: Elemento PerformanceCounters.  

 Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**counterSpecifier**|cadeia de caracteres|Obrigatória. O caminho para coleta do contador de desempenho.|  
|**sampleRate**|duração|Obrigatória. A taxa de coleta do contador de desempenho.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Define os logs de eventos a serem monitorados.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.

  Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|cadeia de caracteres|Opcional. Especifica o nível de severidade mínimo para as entradas de log transferidas. O valor padrão é **Indefinido**. Outros possíveis valores são **Detalhado**, **Informação**, **Aviso**, **Erro** e **Crítico**.|  
|**scheduledTransferPeriod**|duração|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="datasource-element"></a>Elemento DataSource  
 Define o log de eventos a ser monitorado.

 Elemento pai: Elemento WindowsEventLog.  

 Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**name**|cadeia de caracteres|Obrigatória. Uma expressão XPath que especifica o log para coleta.|  


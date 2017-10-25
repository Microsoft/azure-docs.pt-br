---
title: "Extensão 1.3 do Diagnóstico do Azure e esquema de configuração mais recente | Microsoft Docs"
description: "A versão de esquema 1.3 e posterior do Diagnóstico do Azure é fornecida como parte do SDK 2.4 e posteriores do Microsoft Azure."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 2ee66e0f41868d7d5411605596a22c00b5712896
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Esquema de configuração 1.3 e posterior do Diagnóstico do Azure
> [!NOTE]
> A extensão do Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure 
> - Conjuntos de Escala de Máquina Virtual
> - Service Fabric 
> - Serviços de Nuvem 
> - Grupos de segurança de rede
> 
> Esta página só é relevante se você estiver usando um desses serviços.

Esta página é válida para as versões 1.3 e mais recentes (SDK 2.4 e mais recente do Azure). As seções de configuração mais recentes são comentadas para mostrar em qual versão eles foram adicionados.  

O arquivo de configuração descrito aqui é usado para definir as configurações de diagnóstico quando o monitor de diagnóstico é iniciado.  

A extensão é usada em conjunto com outros produtos de diagnóstico da Microsoft, como o Azure Monitor, o Application Insights e o Log Analytics.



Baixe a definição do esquema do arquivo de configuração pública ao executar o seguinte comando PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Para saber mais sobre como usar o Diagnóstico do Azure, confira [Extensão do Diagnóstico do Azure](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 --> 
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
   
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
   
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Equivalente em JSON do arquivo de configuração XML anterior. 

O PublicConfig e PrivateConfig são separados, pois na maioria dos casos de uso de json, eles são passados como variáveis diferentes. Esses casos incluem modelos do Resource Manager, conjunto de dimensionamento de máquinas virtuais, PowerShell e Visual Studio. 

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Leitura desta página  
 As marcas a seguir estão aproximadamente na ordem mostrada no exemplo anterior.  Se você não vir uma descrição completa onde você espera, procure a página para o elemento ou atributo.  

## <a name="common-attribute-types"></a>Tipos comuns de atributo  
 O atributo **scheduledTransferPeriod** aparece em vários elementos. É o intervalo entre transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento DiagnosticsConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration*

Adicionado na versão 1.3.  

O elemento de nível superior do arquivo de configuração de diagnóstico.  

**Atributo** xmlns - O namespace XML para o arquivo de configuração de diagnóstico é:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**PublicConfig**|Obrigatório. Veja a descrição em outro lugar nesta página.|  
|**PrivateConfig**|Opcional. Veja a descrição em outro lugar nesta página.|  
|**IsEnabled**|Booliano. Veja a descrição em outro lugar nesta página.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig*

 Descreve a configuração de diagnóstico pública.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**WadCfg**|Obrigatório. Veja a descrição em outro lugar nesta página.|  
|**StorageAccount**|O nome da conta do Armazenamento do Azure para armazenar os dados. Pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Pode ser *Table*, *Blob* ou *TableAndBlob*. Tabela é o padrão. Quando TableAndBlob for escolhido, os dados de diagnóstico serão gravados duas vezes, uma vez para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual em que o Agente de Monitoramento armazena dados de evento. Caso contrário, o diretório padrão definido será usado:<br /><br /> Para uma função de Trabalho/da Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos obrigatórios são:<br /><br /> - **path** - o diretório no sistema a ser usado pelo Diagnóstico do Azure.<br /><br /> - **expandEnvironment** - controla se as variáveis de ambiente estão expandidas ou não no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 Identifica e configura os dados de telemetria a serem coletados.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration 
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Obrigatório 

|Atributos|Descrição|  
|----------------|-----------------|  
| **overallQuotaInMB** | A quantidade máxima de espaço em disco local que pode ser consumido pelos diversos tipos de dados de diagnóstico coletados pelo Diagnóstico do Azure. A configuração padrão é 5120 MB.<br />
|**useProxyServer** | Configure o Diagnóstico do Azure para usar as configurações de servidor proxy como definido nas configurações do IE.|  

<br /> <br />

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**CrashDumps**|Veja a descrição em outro lugar nesta página.|  
|**DiagnosticInfrastructureLogs**|Habilite a coleta de logs gerados pelo Diagnóstico do Azure. Os logs de infraestrutura de diagnóstico são úteis para solucionar problemas de sistema de diagnóstico. Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** - configura o nível de severidade mínimo dos logs coletados.<br /><br /> - **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Diretórios**|Veja a descrição em outro lugar nesta página.|  
|**EtwProviders**|Veja a descrição em outro lugar nesta página.|  
|**Métricas**|Veja a descrição em outro lugar nesta página.|  
|**PerformanceCounters**|Veja a descrição em outro lugar nesta página.|  
|**WindowsEventLog**|Veja a descrição em outro lugar nesta página.| 
|**DockerSources**|Veja a descrição em outro lugar nesta página. | 



## <a name="crashdumps-element"></a>Elemento CrashDumps  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*
 
 Habilite a coleção de despejos de memória.  

|Atributos|Descrição|  
|----------------|-----------------|  
|**containerName**|Opcional. O nome do contêiner de blobs em sua conta do Armazenamento do Azure para ser usado para armazenar despejos de memória.|  
|**crashDumpType**|Opcional.  Configura o Diagnóstico do Azure para coletar minidespejos de memória ou despejos de memória completos.|  
|**directoryQuotaPercentage**|Opcional.  Configura o percentual de **overallQuotaInMB** a ser reservado para despejos de memória na VM.|  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Obrigatório. Define os valores de configuração para cada processo.<br /><br /> O atributo a seguir também é obrigatório:<br /><br /> **processName** - o nome do processo para o qual você deseja que o Diagnóstico do Azure colete um despejo de memória.|  

## <a name="directories-element"></a>Elemento Directories 
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories*

 Habilita a coleta do conteúdo de um diretório, logs de solicitação de acesso com falha do IIS e/ou logs do IIS.  

 Atributo **scheduledTransferPeriod** opcional. Veja a explicação anterior.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**IISLogs**|A inclusão deste elemento na configuração habilita a coleta de logs do IIS:<br /><br /> **containerName** - o nome do contêiner de blobs na sua conta do Armazenamento do Azure a ser usado para armazenar os logs do IIS.|   
|**FailedRequestLogs**|A inclusão desse elemento na configuração habilita a coleta de logs sobre solicitações com falha para um site ou aplicativo do IIS. Você também deve habilitar as opções de rastreamento em **system.WebServer** em **Web.config**.|  
|**DataSources**|Uma lista de diretórios para monitorar.| 




## <a name="datasources-element"></a>Elemento DataSources  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 Uma lista de diretórios para monitorar.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Obrigatório. Atributo obrigatório:<br /><br /> **containerName** - o nome do contêiner de blob no armazenamento do Azure na conta a ser usada para armazenar os arquivos de log.|  





## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 Pode incluir o elemento **Absolute** ou **LocalResource**, mas não ambos.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**Absolute**|O caminho absoluto para o diretório a ser monitorado. Os atributos a seguir são obrigatórios:<br /><br /> - **Path** - o caminho absoluto para o diretório a ser monitorado.<br /><br /> - **expandEnvironment** - configura se as variáveis de ambiente em Path são expandidas ou não.|  
|**LocalResource**|O caminho relativo a um recurso local a ser monitorado. Os atributos obrigatórios são:<br /><br /> - **Name** - o recurso local que contém o diretório a ser monitorado<br /><br /> - **relativePath** - o caminho relativo a Name que contém o diretório a ser monitorado|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Configura a coleta de eventos ETW do EventSource e/ou os provedores baseados no Manifesto ETW.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a coleta de eventos gerados desde a [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo obrigatório:<br /><br /> **provider** - o nome da classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** - o nível mínimo de severidade a transferir para sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o Armazenamento do Azure arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Atributo obrigatório:<br /><br /> **provider** - o GUID do provedor de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** - o nível mínimo de severidade a transferir para sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o Armazenamento do Azure arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Configura a coleta de eventos gerados desde a [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination** - o nome da tabela para armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - a id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - a id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - o nome da tabela para armazenar os eventos|  



## <a name="metrics-element"></a>Elemento Metrics  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Permite gerar uma tabela de contador de desempenho otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela Métricas, além de na tabela Contador de Desempenho.  

 O atributo **resourceId** é necessário.  A ID de recurso da Máquina Virtual ou Conjunto de Dimensionamento de Máquinas Virtuais na qual o Diagnóstico do Azure está sendo implantado. Obtenha o **resourceID** do [portal do Azure](https://portal.azure.com). Selecione **Procurar** -> **Grupos de Recursos** -> **<Nome\>**. Clique no bloco **Propriedades** e copie o valor do campo **ID**.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**MetricAggregation**|Atributo obrigatório:<br /><br /> **scheduledTransferPeriod** - o intervalo entre transferências agendadas para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Habilita a coleta de contadores de desempenho.  

 Atributo opcional:  

 Atributo **scheduledTransferPeriod** opcional. Veja a explicação anterior.

|Elemento filho|Descrição|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os atributos a seguir são obrigatórios:<br /><br /> - **counterSpecifier** - o nome do contador de desempenho. Por exemplo: `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu host, execute o comando `typeperf`.<br /><br /> - **sampleRate** - Com que frequência o contador deve ser testado.<br /><br /> Atributo opcional:<br /><br /> **unidade** - a unidade de medida do contador.|  




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*
 
 Habilita a coleta de Logs de Eventos do Windows.  

 Atributo **scheduledTransferPeriod** opcional. Veja a explicação anterior.  

|Elemento filho|Descrição|  
|-------------------|-----------------|  
|**DataSource**|Os logs de Eventos do Windows a serem coletados. Atributo obrigatório:<br /><br /> **name** - a consulta XPath que descreve os eventos do windows a serem coletados. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para coletar todos os eventos, especifique "*"|  




## <a name="logs-element"></a>Elemento Logs  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Presente na versão 1.0 e 1.1. Ausente na 1.2. Adicionado novamente na versão 1.3.  

 Define a configuração de buffer para logs básicos do Azure.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|Opcional. Especifica o nível de severidade mínimo para as entradas de log transferidas. O valor padrão é **Indefinido**, que transfere todos os logs. Outros possíveis valores (na ordem de mais informações para menos) são **Detalhado**, **Informações**, **Aviso**, **Erro**, e **Crítico**.|  
|**scheduledTransferPeriod**|**duration**|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  
|**coletores** Adicionados na 1.5|**string**|Opcional. Aponta para um local de coletor para também enviar dados de diagnóstico. Por exemplo, o Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Adicionado na versão 1.9.

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Stats**|Informa ao sistema para coletar estatísticas para contêineres do Docker|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Uma lista de locais para enviar dados de diagnóstico e a configuração associada a esses locais.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Coletor**|Veja a descrição em outro lugar nesta página.|  

## <a name="sink-element"></a>Elemento Sink
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Adicionado na versão 1.5.  

 Define os locais para os quais os dados de diagnóstico devem ser enviados. Por exemplo, o serviço Application Insights.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**name**|string|Uma cadeia de caracteres que identifica o nome do coletor.|  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Usado somente durante o envio de dados para o Application Insights. Contém a Chave de Instrumentação para uma conta ativa do Application Insights a que você tem acesso.|  
|**Channels**|string|Uma para cada filtragem adicional que o fluxo que você|  

## <a name="channels-element"></a>Elemento Channels  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Adicionado na versão 1.5.  

 Define filtros para fluxos de dados de log que passam por um coletor.  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Canal**|string|Veja a descrição em outro lugar nesta página.|  

## <a name="channel-element"></a>Elemento Channel
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 Adicionado na versão 1.5.  

 Define os locais para os quais os dados de diagnóstico devem ser enviados. Por exemplo, o serviço Application Insights.  

|Atributos|Tipo|Descrição|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Especifica o nível de severidade mínimo para as entradas de log transferidas. O valor padrão é **Indefinido**, que transfere todos os logs. Outros possíveis valores (na ordem de mais informações para menos) são **Detalhado**, **Informações**, **Aviso**, **Erro**, e **Crítico**.|  
|**name**|**string**|Um nome exclusivo do canal que será mencionado|  


## <a name="privateconfig-element"></a>Elemento PrivateConfig 
 *Árvore: Raiz - DiagnosticsConfiguration - PrivateConfig*

 Adicionado na versão 1.3.  

 Opcional  

 Armazena os detalhes privados da conta de armazenamento (nome, chave e ponto de extremidade). Essa informação é enviada para a máquina virtual, mas não pode ser recuperada dela.  

|Elementos filho|Descrição|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a ser usada. Os atributos a seguir são necessários<br /><br /> - **name** - o nome da conta de armazenamento.<br /><br /> - **chave** - a chave para a conta de armazenamento.<br /><br /> - **ponto de extremidade** - o ponto de extremidade para acessar a conta de armazenamento. <br /><br /> -**sasToken** (adicionado na versão 1.8.1)- Você pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração privada. Se for fornecida, a chave da conta de armazenamento será ignorada. <br />Requisitos para o Token SAS: <br />– Oferece suporte apenas ao token SAS da conta <br />Os tipos de serviço - *b*, *t* são obrigatórios. <br /> As permissões - *a*, *c*, *u*, *w* são obrigatórias. <br /> Os tipos de recurso - *c*, *o* são obrigatórios. <br /> – Oferece suporte somente ao protocolo HTTPS <br /> – A hora de início e de expiração deve ser válida.|  


## <a name="isenabled-element"></a>Elemento IsEnabled  
 *Árvore: Raiz - DiagnosticsConfiguration - IsEnabled*

 Booliano. Use `true` para habilitar o diagnóstico ou `false` para desabilitar o diagnóstico.

---
title: Referência host.json para o Azure Functions 2.x
description: Documentação de referência do arquivo host.json do Azure Functions com o runtime v2.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 735c92720f4a3f871499ad3a0565446a02b438eb
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654805"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Referência ao host.json para Azure Functions 2.x e versões posteriores 

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2 +](functions-host-json.md)

O arquivo de metadados *host.json* contém opções de configuração global que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações que estão disponíveis a partir da versão 2. x do tempo de execução de Azure Functions.  

> [!NOTE]
> Este artigo é para o Azure Functions 2. x e versões posteriores.  Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicativo de funções são gerenciadas em suas [configurações de aplicativo](functions-app-settings.md) (para aplicativos implantados) ou seu [local.settings.jsno](functions-run-local.md#local-settings-file) arquivo (para desenvolvimento local).

As configurações no host.jsrelacionadas às associações são aplicadas igualmente a cada função no aplicativo de funções. 

Você também pode [substituir ou aplicar configurações por ambiente](#override-hostjson-values) usando as configurações do aplicativo.

## <a name="sample-hostjson-file"></a>Arquivo host.json de exemplo

O exemplo a seguir *host.jsno* arquivo para a versão 2. x + tem todas as opções possíveis especificadas (excluindo as que são apenas para uso interno).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

As seções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que seja indicado o contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Essa configuração é a filha de [Registro em log](#logging).

Controla opções para Application Insights, incluindo [Opções de amostragem](./configure-monitoring.md#configure-sampling).

Para obter a estrutura JSON completa, consulte o [exemplo anterior host.jsno arquivo](#sample-hostjson-file).

> [!NOTE]
> A amostragem de log pode fazer com que algumas execuções não apareçam na folha do Monitor do Application Insights. Para evitar a amostragem de log, adicione `excludedTypes: "Request"` ao `samplingSettings` valor.

| Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| samplingSettings | N/D | Consulte [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Habilita a coleta de métricas em tempo real. |
| enableDependencyTracking | true | Habilita o rastreamento de dependência. |
| enablePerformanceCountersCollection | true | Habilita a coleta de contadores de desempenho kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Somente para uso interno. |
| httpAutoCollectionOptions | N/D | Consulte [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | N/D | Consulte [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

Para obter mais informações sobre essas configurações, consulte [amostragem em Application insights](../azure-monitor/app/sampling.md). 

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| isEnabled | true | Habilita ou desabilita a amostragem. | 
| maxTelemetryItemsPerSecond | 20 | O número de destino de itens de telemetria registrados por segundo em cada host do servidor. Se seu aplicativo for executado em vários hosts, reduza esse valor para permanecer dentro da taxa de destino de tráfego geral. | 
| evaluationInterval | 01:00:00 | O intervalo no qual a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos. |
| initialSamplingPercentage| 100.0 | A porcentagem de amostragem inicial aplicada no início do processo de amostragem para variar dinamicamente a porcentagem. Não reduza o valor enquanto estiver depurando. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Quando o valor da porcentagem de amostragem é alterado, essa propriedade determina quanto tempo depois Application Insights tem permissão para aumentar a porcentagem de amostragem novamente para capturar mais dados. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Quando o valor da porcentagem de amostragem é alterado, essa propriedade determina quanto tempo depois Application Insights tem permissão para reduzir a porcentagem de amostragem novamente para capturar menos dados. |
| minSamplingPercentage | 0,1 | À medida que a porcentagem de amostragem varia, essa propriedade determina a porcentagem de amostragem mínima permitida. |
| maxSamplingPercentage | 100.0 | À medida que a porcentagem de amostragem varia, essa propriedade determina a porcentagem máxima de amostragem permitida. |
| movingAverageRatio | 1.0 | No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor menor ou igual a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas. |
| excludedTypes | nulo | Uma lista delimitada por ponto e vírgula de tipos que você não deseja que sejam amostrados. Os tipos reconhecidos são: `Dependency` , `Event` , `Exception` ,, `PageView` `Request` e `Trace` . Todas as instâncias dos tipos especificados são transmitidas; os tipos que não são especificados são amostrados. |
| includedTypes | nulo | Uma lista delimitada por ponto e vírgula dos tipos que você deseja que sejam amostrados; uma lista vazia implica todos os tipos. Tipo listado em `excludedTypes` tipos de substituição listados aqui. Os tipos reconhecidos são: `Dependency` , `Event` , `Exception` ,, `PageView` `Request` e `Trace` . As instâncias dos tipos especificados são amostradas; os tipos que não são especificados ou implícitos são transmitidos sem amostragem. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Habilita ou desabilita informações de solicitação HTTP estendidas para gatilhos HTTP: cabeçalhos de correlação de solicitação de entrada, suporte para chaves de várias instrumentação, método HTTP, caminho e resposta. |
| enableW3CDistributedTracing | true | Habilita ou desabilita o suporte do protocolo de rastreamento distribuído W3C (e ativa o esquema de correlação herdado). Habilitado por padrão se `enableHttpTriggerExtendedInfoCollection` for verdadeiro. Se `enableHttpTriggerExtendedInfoCollection` for false, esse sinalizador se aplicará somente a solicitações de saída, não a solicitações de entrada. |
| enableResponseHeaderInjection | true | Habilita ou desabilita a injeção de cabeçalhos de correlação de vários componentes em respostas. Habilitar a injeção permite que Application Insights Construa um mapa de aplicativo para quando várias chaves de instrumentação são usadas. Habilitado por padrão se `enableHttpTriggerExtendedInfoCollection` for verdadeiro. Essa configuração não se aplica se `enableHttpTriggerExtendedInfoCollection` for false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Para obter mais informações sobre instantâneos, consulte [depurar instantâneos em exceções em aplicativos .net](../azure-monitor/app/snapshot-debugger.md) e [solucionar problemas ao habilitar Application insights depurador de instantâneos ou exibir instantâneos](../azure-monitor/app/snapshot-debugger-troubleshoot.md).

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| agentEndpoint | nulo | O ponto de extremidade usado para se conectar ao serviço de Depurador de Instantâneos de Application Insights. Se for NULL, será usado um ponto de extremidade padrão. |
| captureSnapshotMemoryWeight | 0,5 | O peso dado ao tamanho da memória do processo atual ao verificar se há memória suficiente para tirar um instantâneo. O valor esperado é maior que 0 fração correta (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | O limite do número de solicitações com falha para solicitar instantâneos antes que o processador de telemetria seja desabilitado.|
| handleUntrackedExceptions | true | Habilita ou desabilita o rastreamento de exceções que não são controladas pela telemetria do Application Insights. |
| isEnabled | true | Habilita ou desabilita a coleta de instantâneos | 
| isEnabledInDeveloperMode | false | Habilita ou desabilita a coleta de instantâneos habilitada no modo de desenvolvedor. |
| isEnabledWhenProfiling | true | Habilita ou desabilita a criação de instantâneos, mesmo se o Application Insights Profiler estiver coletando uma sessão detalhada de criação de perfil. |
| isExceptionSnappointsEnabled | false | Habilita ou desabilita a filtragem de exceções. |
| isLowPrioritySnapshotUploader | true | Determina se o processo SnapshotUploader deve ser executado na prioridade normal abaixo. |
| maximumCollectionPlanSize | 50 | O número máximo de problemas que podemos rastrear a qualquer momento em um intervalo de um a 9999. |
| maximumSnapshotsRequired | 3 | O número máximo de instantâneos coletados para um único problema, em um intervalo de um a 999. Um problema pode ser considerado uma instrução Throw individual em seu aplicativo. Depois que o número de instantâneos coletados para um problema atingir esse valor, nenhum instantâneo será coletado para esse problema até que os contadores de problema sejam redefinidos (consulte `problemCounterResetInterval` ) e o `thresholdForSnapshotting` limite seja atingido novamente. |
| problemCounterResetInterval | 24:00:00 | Com que frequência os contadores de problema são redefinidos em um intervalo de um minuto a sete dias. Quando esse intervalo é atingido, todas as contagens de problemas são redefinidas para zero. Problemas existentes que já atingiram o limite para fazer instantâneos, mas que ainda não geraram o número de instantâneos em `maximumSnapshotsRequired` , permanecem ativos. |
| provideAnonymousTelemetry | true | Determina se o uso anônimo e a telemetria de erro devem ser enviados à Microsoft. Essa telemetria poderá ser usada se você entrar em contato com a Microsoft para ajudar a solucionar problemas com o Depurador de Instantâneos. Ele também é usado para monitorar padrões de uso. |
| reconnectInterval | 00:15:00 | Com que frequência reconectamos ao ponto de extremidade de Depurador de Instantâneos. O intervalo permitido é de um minuto a um dia. |
| shadowCopyFolder | nulo | Especifica a pasta a ser usada para binários de cópia de sombra. Se não estiver definido, as pastas especificadas pelas seguintes variáveis de ambiente serão tentadas em ordem: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se for true, apenas uma instância de SnapshotUploader coletará e carregará instantâneos para vários aplicativos que compartilham o InstrumentationKey. Se definido como false, o SnapshotUploader será exclusivo para cada tupla (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se os instantâneos devem ou não ser processados em um thread de prioridade baixa e/s. A criação de um instantâneo é uma operação rápida, mas, para carregar um instantâneo no serviço de Depurador de Instantâneos, ele deve ser gravado primeiro no disco como um minidespejo. Isso acontece no processo de SnapshotUploader. Definir esse valor como true usa a e/s de baixa prioridade para gravar o minidespejo, que não concorrerá em seu aplicativo para recursos. Definir esse valor como false acelera a criação de minidespejos com a despesa de reduzir o seu aplicativo. |
| snapshotsPerDayLimit | 30 | O número máximo de instantâneos permitido em um dia (24 horas). Esse limite também é imposto no lado do serviço Application Insights. Os carregamentos têm uma taxa limitada a 50 por dia por aplicativo (isto é, por chave de instrumentação). Esse valor ajuda a impedir a criação de instantâneos adicionais que eventualmente serão rejeitados durante o carregamento. Um valor de zero remove totalmente o limite, o que não é recomendado. |
| snapshotsPerTenMinutesLimit | 1 | O número máximo de instantâneos permitido em 10 minutos. Embora não haja nenhum limite superior nesse valor, tome cuidado ao aumentá-lo nas cargas de trabalho de produção, pois isso pode afetar o desempenho do seu aplicativo. Criar um instantâneo é rápido, mas criar um minidespejo do instantâneo e carregá-lo para o serviço de Depurador de Instantâneos é uma operação muito mais lenta que conseguirá competir com seu aplicativo para recursos (CPU e e/s). |
| tempFolder | nulo | Especifica a pasta para gravar minidespejos e arquivos de log do carregador. Se não for definido, *%Temp%\Dumps* será usado. |
| thresholdForSnapshotting | 1 | Quantas vezes Application Insights precisa ver uma exceção antes de solicitar instantâneos. |
| uploaderProxy | nulo | Substitui o servidor proxy usado no processo de carregador de instantâneo. Talvez seja necessário usar essa configuração se o aplicativo se conectar à Internet por meio de um servidor proxy. O Snapshot Collector é executado no processo do aplicativo e usará as mesmas configurações de proxy. No entanto, o carregador de instantâneos é executado como um processo separado e talvez seja necessário configurar o servidor proxy manualmente. Se esse valor for nulo, Snapshot Collector tentará detectar automaticamente o endereço do proxy examinando System .net. WebRequest. DefaultWebProxy e passando o valor para o carregador de instantâneos. Se esse valor não for nulo, a detecção automática não será usada e o servidor proxy especificado aqui deverá ser usado no carregador de instantâneos. |

## <a name="cosmosdb"></a>cosmosDb

A definição de configuração pode ser encontrada em [Associações e gatilhos do Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="customhandler"></a>customHandler

Definições de configuração para um manipulador personalizado. Para obter mais informações, consulte [Azure Functions manipuladores personalizados](functions-custom-handlers.md#configuration).

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- |
| defaultExecutablePath | N/D | O executável a ser iniciado como o processo do manipulador personalizado. É uma configuração necessária ao usar manipuladores personalizados e seu valor é relativo à raiz do aplicativo de funções. |
| workingDirectory | *raiz do aplicativo de funções* | O diretório de trabalho no qual iniciar o processo de manipulador personalizado. É uma configuração opcional e seu valor é relativo à raiz do aplicativo de funções. |
| argumentos | N/D | Uma matriz de argumentos de linha de comando para passar para o processo de manipulador personalizado. |
| enableForwardingHttpRequest | false | Se definido, todas as funções que consistem apenas em um gatilho HTTP e saída HTTP serão encaminhadas a solicitação HTTP original em vez da [carga de solicitação](functions-custom-handlers.md#request-payload)do manipulador personalizado. |

## <a name="durabletask"></a>durableTask

A definição de configuração pode ser encontrada em [Associações para Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas em [Associações e gatilhos do Hub de Eventos](functions-bindings-event-hubs-trigger.md#host-json). 

## <a name="extensions"></a>extensions

Propriedade que retorna um objeto que contém todas as configurações específicas de associação, como [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Os pacotes de extensão permitem que você adicione um conjunto compatível de extensões de associação de funções ao seu aplicativo de funções. Para saber mais, confira [pacotes de extensão para o desenvolvimento local](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funções

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa que todas as funções serão executadas. Para uso somente quando [em execução localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [Como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Ele segue o formato de cadeia de caracteres TimeSpan. 

| Tipo de plano | Padrão (min) | Máximo (min) |
| -- | -- | -- |
| Consumo | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (não associado)<sup>2</sup> |
| Dedicado (serviço de aplicativo) | 30 | -1 (não associado)<sup>2</sup> |

<sup>1</sup> a execução do plano Premium só é garantida por 60 minutos, mas tecnicamente desassociada.   
<sup>2</sup> um valor de `-1` indica a execução não associada, mas é recomendável manter um limite superior fixo.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de integridade de Host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|Habilitado|true|Especifica se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade em segundo plano. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes de uma reciclagem de host ser iniciada.| 
|counterThreshold|0.80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

As definições de configuração podem ser encontradas em [Associações e gatilhos HTTP](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>registro em log

Controla os comportamentos de registro em log do aplicativo de funções, incluindo o Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Define qual nível de log de arquivos está habilitado.  As opções são: `never`, `always` e `debugOnly`. |
|logLevel|N/D|Objeto que define a filtragem da categoria de log para funções no aplicativo. As versões 2. x e posteriores seguem o layout de ASP.NET Core para filtragem de categorias de log. Essa configuração permite filtrar o registro em log para funções específicas. Para obter mais informações, consulte [Filtragem de logs](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering) na documentação do ASP.NET Core. |
|console|N/D| A configuração de log do [console](#console). |
|applicationInsights|N/D| A configuração [applicationInsights](#applicationinsights). |

## <a name="console"></a>console

Essa configuração é a filha de [Registro em log](#logging). Ela controla o log do console quando não está no modo de depuração.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|isEnabled|false|Habilita ou desabilita o log de console.| 

## <a name="manageddependency"></a>managedDependency

A dependência gerenciada é um recurso que atualmente só tem suporte com funções baseadas no PowerShell. Ele permite que as dependências sejam gerenciadas automaticamente pelo serviço. Quando a `enabled` propriedade é definida como `true` , o `requirements.psd1` arquivo é processado. As dependências são atualizadas quando qualquer versão secundária é lançada. Para obter mais informações, consulte [dependência gerenciada](functions-reference-powershell.md#dependency-management) no artigo do PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas em [Associações e gatilhos da fila de armazenamento](functions-bindings-storage-queue-output.md#host-json).  

## <a name="retry"></a>tentar novamente

Controla as opções de [política de repetição](./functions-bindings-error-pages.md#retry-policies-preview) para todas as execuções no aplicativo.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|usada|nulo|Obrigatórios. Usar qual estratégia de repetição. Os valores válidos são `fixedDelay` ou `exponentialBackoff`.|
|maxRetryCount|nulo|Obrigatórios. O número máximo de repetições permitidas por execução de função. `-1` significa repetir indefinidamente.|
|delayInterval|nulo|O atraso usado entre as repetições com uma `fixedDelay` estratégia.|
|minimumInterval|nulo|O atraso mínimo de repetição ao usar a `exponentialBackoff` estratégia.|
|maximumInterval|nulo|O atraso máximo de repetição ao usar a `exponentialBackoff` estratégia.| 

## <a name="sendgrid"></a>sendGrid

A definição de configuração pode ser encontrada em [Associações e gatilhos do SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

A definição de configuração pode ser encontrada em [Associações e gatilhos do Barramento de Serviço](functions-bindings-service-bus-output.md#host-json).

## <a name="singleton"></a>singleton

Parâmetro de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [Problema com o GitHub referente ao suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período em que ocorrem os bloqueios de nível de função. Os bloqueios têm renovação automática.| 
|listenerLockPeriod|00:01:00|O período em que ocorrem os bloqueios de ouvinte.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo usado para a recuperação do bloqueio de ouvinte caso não tenha sido possível adquirir um bloqueio de ouvinte durante a inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo em que o runtime tenta adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|N/D|O intervalo entre as tentativas de aquisição de bloqueio.| 

## <a name="version"></a>version

Esse valor indica a versão do esquema do host.jsno. A cadeia de caracteres de versão `"version": "2.0"` é necessária para um aplicativo de funções que tenha como destino o tempo de execução v2 ou uma versão posterior. Não há host.jsem alterações de esquema entre V2 e v3.

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que devem ser monitorados quanto a alterações.  Garante que, quando o código nesses diretórios é alterado, as alterações sejam coletadas pelas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

Uma matriz de um ou mais nomes de arquivos que são monitorados para alterações que exigem que seu aplicativo seja reiniciado.  Isso garante que, quando o código desses arquivos for alterado, as atualizações serão coletadas por suas funções.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>Substituir host.jsem valores

Pode haver instâncias em que você deseja configurar ou modificar configurações específicas em um host.jsno arquivo para um ambiente específico, sem alterar o host.jsno próprio arquivo.  Você pode substituir host.jsespecíficos em valores para criar um valor equivalente como uma configuração de aplicativo. Quando o tempo de execução encontra uma configuração de aplicativo no formato `AzureFunctionsJobHost__path__to__setting` , ele substitui a host.jsequivalente na configuração localizada em `path.to.setting` no JSON. Quando expressa como uma configuração de aplicativo, o ponto ( `.` ) usado para indicar a hierarquia JSON é substituído por um sublinhado duplo ( `__` ). 

Por exemplo, digamos que você quisesse desabilitar a amostragem do Application Insight ao executar localmente. Se você alterou o host.jslocal no arquivo para desabilitar a Application Insights, essa alteração poderá ser enviada por push ao seu aplicativo de produção durante a implantação. A maneira mais segura de fazer isso é, em vez disso, criar uma configuração de aplicativo como `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` no `local.settings.json` arquivo. Você pode ver isso no arquivo a seguir `local.settings.json` , que não é publicado:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte as configurações globais em variáveis de ambiente](functions-app-settings.md)

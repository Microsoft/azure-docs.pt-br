---
title: Opções de configuração-Azure Monitor Application Insights para Java
description: Como configurar o Application Insights de Azure Monitor para Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: f349d260fff32427712442615cabf6d3958468ac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640042"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Opções de configuração-Azure Monitor Application Insights para Java

> [!WARNING]
> **Se você estiver atualizando da versão prévia do 3,0**
>
> Examine todas as opções de configuração abaixo com cuidado, pois a estrutura JSON foi completamente alterada, além do nome do arquivo em si, que ficou com todas as letras minúsculas.

## <a name="connection-string-and-role-name"></a>Cadeia de conexão e nome da função

A cadeia de conexão e o nome da função são as configurações mais comuns necessárias para começar:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

A cadeia de conexão é necessária e o nome da função é importante sempre que você estiver enviando dados de aplicativos diferentes para o mesmo recurso de Application Insights.

Você encontrará mais detalhes e opções de configuração adicionais abaixo.

## <a name="configuration-file-path"></a>Caminho do arquivo de configuração

Por padrão, Application Insights Java 3,0 espera que o arquivo de configuração seja nomeado `applicationinsights.json` e esteja localizado no mesmo diretório que `applicationinsights-agent-3.0.2.jar` .

Você pode especificar seu próprio caminho de arquivo de configuração usando o

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variável de ambiente ou
* `applicationinsights.configuration.file` Propriedade do sistema Java

Se você especificar um caminho relativo, ele será resolvido em relação ao diretório onde `applicationinsights-agent-3.0.2.jar` está localizado.

## <a name="connection-string"></a>Cadeia de conexão

A cadeia de conexão é necessária. Você pode encontrar a cadeia de conexão em seu recurso de Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights cadeia de conexão":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Você também pode definir a cadeia de conexão usando a variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` (que, em seguida, terá precedência se a cadeia de conexão também for especificada na configuração JSON).

Se a cadeia de conexão não for definida, o agente Java será desabilitado.

## <a name="cloud-role-name"></a>Nome da função de nuvem

O nome da função de nuvem é usado para rotular o componente no mapa do aplicativo.

Se você quiser definir o nome da função de nuvem:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Se o nome da função de nuvem não for definido, o nome do recurso de Application Insights será usado para rotular o componente no mapa do aplicativo.

Você também pode definir o nome da função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_NAME` (que, em seguida, terá precedência se o nome da função de nuvem também for especificado na configuração JSON).

## <a name="cloud-role-instance"></a>Instância de função de nuvem

A instância de função de nuvem usa como padrão o nome do computador.

Se você quiser definir a instância de função de nuvem para algo diferente, em vez do nome da máquina:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Você também pode definir a instância de função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_INSTANCE` (que, em seguida, terá precedência se a instância de função de nuvem também for especificada na configuração JSON).

## <a name="sampling"></a>amostragem

A amostragem será útil se você precisar reduzir o custo.
A amostragem é executada como uma função na ID da operação (também conhecida como ID de rastreamento), para que a mesma ID de operação sempre resulte na mesma decisão de amostragem. Isso garante que você não obterá partes de uma transação distribuída amostrada enquanto outras partes dela são amostradas.

Por exemplo, se você definir a amostragem como 10%, verá apenas 10% das suas transações, mas cada uma dessas 10% terá detalhes completos da transação de ponta a ponta.

Veja um exemplo de como definir a amostragem para capturar aproximadamente **1/3 de todas as transações** – certifique-se de definir a taxa de amostragem que está correta para seu caso de uso:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Você também pode definir o percentual de amostragem usando a variável de ambiente `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (que, em seguida, terá precedência se a porcentagem de amostragem também for especificada na configuração JSON).

> [!NOTE]
> Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro. Atualmente, a amostragem não dá suporte a outros valores.

## <a name="sampling-overrides-preview"></a>Substituições de amostragem (visualização)

Este recurso está em versão prévia, Iniciando em 3.0.3-BETA. 2.

As substituições de amostragem permitem que você substitua a [porcentagem de amostragem padrão](#sampling), por exemplo:
* Defina o percentual de amostragem como 0 (ou algum valor pequeno) para verificações de integridade ruidosas.
* Defina o percentual de amostragem como 0 (ou algum valor pequeno) para chamadas de dependência com ruído.
* Defina o percentual de amostragem como 100 para um tipo de solicitação importante (por exemplo, `/login` ) mesmo que você tenha a amostragem padrão configurada para algo menor.

Para obter mais informações, confira a documentação de [substituições de amostragem](./java-standalone-sampling-overrides.md) .

## <a name="jmx-metrics"></a>Métricas JMX

Se você quiser coletar algumas métricas JMX adicionais:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` é o nome da métrica que será atribuída a essa métrica JMX (pode ser qualquer coisa).

`objectName` é o [nome do objeto](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) do MBean JMX que você deseja coletar.

`attribute` é o nome do atributo dentro do MBean JMX que você deseja coletar.

Há suporte para valores de métrica JMX numéricos e boolianos. As métricas do JMX booliano são mapeadas para `0` for false e `1` para true.

## <a name="custom-dimensions"></a>Dimensões personalizadas

Se você quiser adicionar dimensões personalizadas a toda a sua telemetria:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` pode ser usado para ler o valor da variável de ambiente especificada na inicialização.

> [!NOTE]
> A partir da versão 3.0.2, se você adicionar uma dimensão personalizada chamada `service.version` , o valor será armazenado na `application_Version` coluna na tabela de Logs de Application insights em vez de como uma dimensão personalizada.

## <a name="telemetry-processors-preview"></a>Processadores de telemetria (visualização)

Esse recurso está em visualização.

Ele permite que você configure regras que serão aplicadas à telemetria de solicitação, dependência e rastreamento, por exemplo:
 * Mascarar dados confidenciais
 * Adicionar dimensões personalizadas condicionalmente
 * Atualize o nome do span, que é usado para agregar telemetria semelhante no portal do Azure.
 * Descartar atributos de span específicos para controlar os custos de ingestão.

Para obter mais informações, confira a documentação do [processador de telemetria](./java-standalone-telemetry-processors.md) .

> [!NOTE]
> Se você pretende descartar spans (inteiros) específicos para controlar o custo de ingestão, consulte [substituições de amostragem](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Registro em log coletado automaticamente

Log4J, Logback e Java. util. Logging são instrumentados automaticamente e o registro em log realizado por meio dessas estruturas de log é automaticamente coletado.

O registro em log só será capturado se ele atender primeiro ao nível configurado para a estrutura de registro em log e, segundo, também atender ao nível configurado para Application Insights.

Por exemplo, se sua estrutura de registro em log estiver configurada para registrar `WARN` (e acima) do pacote `com.example` e Application insights estiver configurada para capturar `INFO` (e acima), Application insights só capturará `WARN` (e acima) do pacote `com.example` .

O nível padrão configurado para Application Insights é `INFO` . Se você quiser alterar este nível:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Você também pode definir o nível usando a variável de ambiente `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (que, em seguida, terá precedência se o nível também for especificado na configuração JSON).

Esses são os `level` valores válidos que você pode especificar no `applicationinsights.json` arquivo e como eles correspondem aos níveis de log em diferentes estruturas de log:

| nível             | Log4J  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAIS             | FATAIS  | ERROR   | SEVERE  |
| ERRO (ou grave) | ERROR  | ERROR   | SEVERE  |
| AVISAR (ou aviso) | AVISAR   | AVISAR    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| Depurar (ou bem)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| RASTREAMENTO (ou melhor) | RASTREAMENTO  | RASTREAMENTO   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Se um objeto de exceção for passado para o agente, a mensagem de log (e os detalhes do objeto de exceção) aparecerá na portal do Azure na `exceptions` tabela em vez da `traces` tabela.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Métricas de micrometer coletadas automaticamente (incluindo as métricas do acionador do Spring boot)

Se seu aplicativo usar [micrometer](https://micrometer.io), as métricas enviadas para o registro global micrometer serão coletadas automaticamente.

Além disso, se seu aplicativo usar o [acionador do Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), as métricas configuradas pelo acionador do Spring boot também serão coletadas automaticamente.

Para desabilitar a coleta automática de métricas do micrometer (incluindo as métricas do acionador do Spring boot):

> [!NOTE]
> As métricas personalizadas são cobradas separadamente e podem gerar custos adicionais. Certifique-se de verificar as [informações de preços](https://azure.microsoft.com/pricing/details/monitor/)detalhadas. Para desabilitar as métricas do micrometer e do acionador Spring, adicione a configuração abaixo ao arquivo de configuração.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Suprimindo telemetria de coleta automática específica

A partir da versão 3.0.2, a telemetria específica de coleta automática pode ser suprimida usando estas opções de configuração:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

> Observação Se você estiver procurando um controle mais refinado, por exemplo, para suprimir algumas chamadas Redis, mas não todas as chamadas Redis, consulte [substituições de amostragem](./java-standalone-sampling-overrides.md).


## <a name="heartbeat"></a>Pulsação

Por padrão, Application Insights Java 3,0 envia uma métrica de pulsação uma vez a cada 15 minutos. Se você estiver usando a métrica de pulsação para disparar alertas, poderá aumentar a frequência dessa pulsação:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Não é possível aumentar o intervalo para mais de 15 minutos, pois os dados de pulsação também são usados para rastrear Application Insights uso.

## <a name="http-proxy"></a>Proxy HTTP

Se seu aplicativo estiver protegido por um firewall e não puder se conectar diretamente a Application Insights (consulte [endereços IP usados por Application insights](./ip-addresses.md)), você poderá configurar Application Insights Java 3,0 para usar um proxy http:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3,0 também respeita o global `-Dhttps.proxyHost` e `-Dhttps.proxyPort` se eles estão definidos.

## <a name="metric-interval"></a>Intervalo de métrica

Esse recurso está em visualização.

Por padrão, as métricas são capturadas a cada 60 segundos.

A partir da versão 3.0.3-BETA, você pode alterar este intervalo:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

A configuração se aplica a todas essas métricas:

* Contadores de desempenho padrão, por exemplo, CPU e memória
* Métricas personalizadas padrão, por exemplo, tempo de coleta de lixo
* Métricas JMX configuradas ([veja acima](#jmx-metrics))
* Métricas de micrometer ([veja acima](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Observação o suporte do OpenTelemetry está em visualização privada até a API do OpenTelemetry atingir 1,0"

[//]: # "# # Suporte para versões anteriores a 1,0 da API OpenTelemetry"

[//]: # "O suporte para versões anteriores à 1,0 da API do OpenTelemetry é opcional, pois a API do OpenTelemetry ainda não está estável"
[//]: # "e, portanto, cada versão do agente dá suporte apenas a versões anteriores a 1,0 da API do OpenTelemetry"
[//]: # "(essa limitação não será aplicada quando a API 1,0 do OpenTelemetry for lançada)."

[//]: # "JSON ' ' '"
[//]: # "{"
[//]: # "  \"versão prévia \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Autodiagnóstico

"Autodiagnostics" refere-se ao log interno de Application Insights Java 3,0.

Essa funcionalidade pode ser útil para descobrir e diagnosticar problemas com Application Insights si mesmo.

Por padrão, Application Insights logs do Java 3,0 em nível `INFO` tanto para o arquivo `applicationinsights.log` quanto para o console, correspondendo a essa configuração:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` pode ser um de `file` , `console` ou `file+console` .

`level` pode ser uma das `OFF` , `ERROR` , `WARN` , `INFO` , `DEBUG` ou `TRACE` .

`path` pode ser um caminho absoluto ou relativo. Os caminhos relativos são resolvidos em relação ao diretório onde `applicationinsights-agent-3.0.2.jar` está localizado.

`maxSizeMb` é o tamanho máximo do arquivo de log antes de ele ser revertido.

`maxHistory` é o número de arquivos de log substituídos que são retidos (além do arquivo de log atual).

A partir da versão 3.0.2, você também pode definir o autodiagnóstico `level` usando a variável de ambiente `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (que, em seguida, terá precedência se o autodiagnóstico `level` também for especificado na configuração JSON).

## <a name="an-example"></a>Um exemplo

Este é apenas um exemplo para mostrar a aparência de um arquivo de configuração com vários componentes.
Configure opções específicas com base em suas necessidades.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

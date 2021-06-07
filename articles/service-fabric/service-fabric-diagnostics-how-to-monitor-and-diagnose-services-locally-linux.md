---
title: Depurar aplicativos Service Fabric do Azure no Linux
description: Saiba como monitorar e diagnosticar seus serviços do Service Fabric em um computador de desenvolvimento Linux local.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88999760"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador Linux local


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. O monitoramento e o diagnóstico são essenciais em um ambiente de produção implantado. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funcionará quando você se deslocar para um ambiente de produção. O Service Fabric facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente tanto em configurações de desenvolvimento local de computador único, quanto em configurações reais de cluster de produção.


## <a name="debugging-service-fabric-java-applications"></a>Depuração de aplicativos Java do Service Fabric

Para aplicativos Java, [várias estrutura de registros](https://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Como `java.util.logging` é a opção padrão com o JRE, ele também é usado para os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). A discussão a seguir explica como configurar a estrutura `java.util.logging` .

Ao usar java.util.logging, você pode redirecionar os logs do aplicativo para a memória, os fluxos de saída, os arquivos de consoles ou os soquetes. Para cada uma dessas opções, há manipuladores padrão já fornecidos na estrutura. Você pode criar um arquivo `app.properties` para configurar o manipulador de arquivo para o seu aplicativo redirecionar todos os logs para um arquivo local.

O snippet de código a seguir contém um exemplo de configuração:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A pasta apontada pelo arquivo `app.properties` deve existir. Depois que o arquivo `app.properties` for criado, você também precisará modificar o script de ponto de entrada `entrypoint.sh` na pasta `<applicationfolder>/<servicePkg>/Code/` para definir a propriedade `java.util.logging.config.file` para o arquivo `app.properties`. A entrada deverá se parecer com o seguinte snippet:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta em logs sendo coletados em um modo de rotação em `/tmp/servicefabric/logs/`. O arquivo de log nesse caso é chamado mysfapp%u.%g.log, em que:
* **%u** é um número exclusivo para resolver conflitos entre processos Java simultâneos.
* **%g** é o número de geração para distinguir entre logs rotativos.

Por padrão, se nenhum manipulador for configurado explicitamente, o manipulador de console será registrado. Estes logs podem ser vistos no syslog, em /var/log/syslog.

Para obter mais informações, consulte os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Depuração de aplicativos C# do Service Fabric


Várias estruturas estão disponíveis para rastreamento de aplicativos CoreCLR no Linux. Para obter mais informações, consulte [extensões .net para registro em log](https://github.com/dotnet/extensions/tree/master/src/Logging).  Uma vez que o EventSource é familiar aos desenvolvedores do C#, este artigo usa EventSource para rastreamento em exemplos de CoreCLR no Linux.

A primeira etapa é incluir System.Diagnostics.Tracing para que você possa escrever seus logs na memória, fluxos de saída ou arquivos de console.  Para registrar em log usando o EventSource, adicione o projeto a seguir a seu project.json:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Você pode usar um EventListener personalizado para ouvir o evento de serviço e redirecioná-lo apropriadamente para arquivos de rastreamento. O snippet de código a seguir mostra uma implementação de exemplo de registo em log usando EventSource e um EventListener personalizado:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


O snippet anterior gera os logs para um arquivo em `/tmp/MyServiceLog.txt`. Esse nome de arquivo precisa ser atualizado adequadamente. Caso você deseje redirecionar os logs para o console, use o snippet a seguir em sua classe EventListener personalizada:

```csharp
public static TextWriter Out = Console.Out;
```

Os exemplos neste [Exemplos de C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) usam EventSource e um EventListener personalizado para registrar eventos em um arquivo.



## <a name="next-steps"></a>Próximas etapas
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico do seu aplicativo em um cluster do Azure. Consulte estes artigos que discutem as diferentes opções para as ferramentas e descrevem como configurá-las.
* [Como coletar logs com o Diagnóstico do Azure](./service-fabric-diagnostics-event-aggregation-lad.md)

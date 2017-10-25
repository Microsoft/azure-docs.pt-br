---
title: "Depurar microsserviços do Azure no Linux | Microsoft Docs"
description: "Saiba como monitorar e diagnosticar seus serviços escritos com o Service Fabric do Microsoft Azure em um computador de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 4bc73f581f4855ebc724df19dd56fab8bf103854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. O monitoramento e o diagnóstico são essenciais em um ambiente de produção implantado. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funcionará quando você se deslocar para um ambiente de produção. O Service Fabric facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente tanto em configurações de desenvolvimento local de computador único, quanto em configurações reais de cluster de produção.


## <a name="debugging-service-fabric-java-applications"></a>Depuração de aplicativos Java do Service Fabric

Para aplicativos Java, [várias estrutura de registros](http://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma vez que `java.util.logging` é a opção padrão com o JRE, ele também é usado nos [códigos de exemplos no GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  A discussão a seguir explica como configurar a estrutura `java.util.logging` .

Ao usar java.util.logging, você pode redirecionar os logs do aplicativo para a memória, os fluxos de saída, os arquivos de consoles ou os soquetes. Para cada uma dessas opções, há manipuladores padrão já fornecidos na estrutura. Você pode criar um arquivo `app.properties` para configurar o manipulador de arquivo para o seu aplicativo redirecionar todos os logs para um arquivo local.

O trecho de código a seguir contém um exemplo de configuração:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A pasta apontada pelo arquivo `app.properties` deve existir. Depois que o arquivo `app.properties` for criado, você também precisará modificar o script de ponto de entrada `entrypoint.sh` na pasta `<applicationfolder>/<servicePkg>/Code/` para definir a propriedade `java.util.logging.config.file` para o arquivo `app.propertes`. A entrada deverá se parecer com o seguinte trecho:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta em logs sendo coletados em um modo de rotação em `/tmp/servicefabric/logs/`. O arquivo de log nesse caso é chamado mysfapp%u.%g.log, em que:
* **%u** é um número exclusivo para resolver conflitos entre processos Java simultâneos.
* **%g** é o número de geração para distinguir entre logs rotativos.

Por padrão, se nenhum manipulador for configurado explicitamente, o manipulador de console será registrado. Estes logs podem ser vistos no syslog, em /var/log/syslog.

Para obter mais informações, confira os [códigos de exemplos no GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Depuração de aplicativos C# do Service Fabric


Várias estruturas estão disponíveis para rastreamento de aplicativos CoreCLR no Linux. Para saber mais, consulte [GitHub: registro em log](http:/github.com/aspnet/logging).  Uma vez que o EventSource é familiar aos desenvolvedores do C#, este artigo usa EventSource para rastreamento em exemplos de CoreCLR no Linux.

A primeira etapa é incluir System.Diagnostics.Tracing para que você possa escrever seus logs na memória, fluxos de saída ou arquivos de console.  Para registrar em log usando o EventSource, adicione o projeto a seguir a seu project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Você pode usar um EventListener personalizado para ouvir o evento de serviço e redirecioná-lo apropriadamente para arquivos de rastreamento. O trecho de código a seguir mostra uma implementação de exemplo de registo em log usando EventSource e um EventListener personalizado:


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
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


O trecho anterior gera os logs para um arquivo em `/tmp/MyServiceLog.txt`. Esse nome de arquivo precisa ser atualizado adequadamente. Caso você deseje redirecionar os logs para o console, use o trecho a seguir em sua classe EventListener personalizada:

```csharp
public static TextWriter Out = Console.Out;
```

Os exemplos neste [Exemplos de C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) usam EventSource e um EventListener personalizado para registrar eventos em um arquivo.



## <a name="next-steps"></a>Próximas etapas
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico do seu aplicativo em um cluster do Azure. Consulte estes artigos que discutem as diferentes opções para as ferramentas e descrevem como configurá-las.
* [Como coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)

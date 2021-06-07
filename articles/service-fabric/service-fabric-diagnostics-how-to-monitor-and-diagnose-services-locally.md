---
title: Depurar aplicativos Service Fabric do Azure no Windows
description: Saiba como monitorar e diagnosticar seus serviços escritos com o Service Fabric do Microsoft Azure em um computador de desenvolvimento local.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: a2502c24ef233c286872e2c265dcfdae6883f8ed
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628825"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. Embora o monitoramento e o diagnóstico sejam essenciais em um ambiente de produção implantado, a eficiência dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que eles funcionem quando você os mover para uma configuração real. O Service Fabric facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente tanto em configurações de desenvolvimento local de computador único, quanto em configurações reais de cluster de produção.

## <a name="event-tracing-for-windows"></a>Rastreamento de Eventos para Windows
[Rastreamento de Eventos para Windows](/windows/win32/etw/event-tracing-portal) (ETW) é a tecnologia recomendada para mensagens de rastreamento na Malha do Serviço. Alguns benefícios de usar ETW são:

* **O ETW é rápido.**  Ele foi criado como uma tecnologia de rastreamento que tem impacto mínimo sobre os tempos de execução de código.
* **O rastreamento ETW funciona perfeitamente nos ambientes de desenvolvimento locais e também nas configurações de cluster reais.** Isso significa que você não precisa reescrever o código de rastreamento quando estiver pronto para implantar seu código em um cluster real.
* **O código do sistema da Malha do Serviço também usa o ETW para rastreamento interno.**  Isso permite exibir os rastreamentos do aplicativo intercalados com os rastreamentos do sistema do Service Fabric. Ele também ajuda você a entender mais facilmente as sequências e as inter-relações entre o código do aplicativo e os eventos no sistema subjacente.
* **Há suporte interno nas ferramentas do Visual Studio da Malha do Serviço para exibir os eventos do ETW.** Os eventos do ETW aparecem na exibição de Eventos de Diagnóstico do Visual Studio depois que o Visual Studio estiver configurado corretamente com o Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Exibir eventos do sistema da Malha do Serviço no Visual Studio
O Service Fabric emite eventos do ETW para ajudar os desenvolvedores de aplicativos a entender o que está acontecendo na plataforma. Se você ainda não tiver feito isso, vá em frente e siga as etapas em [Criando seu primeiro aplicativo no Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Essas informações ajudarão você a colocar um aplicativo para funcionar com o Visualizador de Eventos de Diagnóstico mostrando as mensagens de rastreamento.

1. Se a janela de eventos de diagnóstico não for mostrada automaticamente, acesse a guia **Exibir** no Visual Studio, escolha **Outras Janelas** e **Visualizador de Eventos de Diagnóstico**.
2. Cada evento tem informações de metadados padrão que informam de que nó, aplicativo e serviço ele surge. Também é possível filtrar a lista de eventos usando a caixa **Filtrar eventos** na parte superior da janela de eventos. Por exemplo, você pode filtrar por **Nome do Nó** ou **Nome do Serviço.** E quando estiver examinando os detalhes de um evento, você também poderá pausar usando o botão **Pausar** na parte superior da janela e continuar mais tarde sem qualquer perda de eventos.
   
   ![Visualizador de Eventos de Diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicionar seus próprios rastreamentos personalizados ao código do aplicativo
Os modelos de projeto do Visual Studio da Malha do Serviço contêm amostra de código. O código mostra como adicionar rastreamentos personalizados do ETW do código de aplicativo, que é mostrado no visualizador do ETW no Visual Studio ao lado dos rastreamentos do sistema do Service Fabric. A vantagem desse método é que os metadados são adicionados automaticamente aos rastreamentos, e o Visualizador de Eventos de Diagnóstico do Visual Studio já está configurado para exibi-los.

Para projetos criados dos **modelos de serviço** (com ou sem estado), basta procurar a implementação de `RunAsync`:

1. A chamada para `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` mostra um exemplo de rastreamento ETW personalizado do código de aplicativo.
2. No arquivo **ServiceEventSource.cs**, você encontrará uma sobrecarga para o método `ServiceEventSource.ServiceMessage` que deve ser usado para eventos de alta frequência por motivos de desempenho.

Para projetos criados de **modelos de ator** (com ou sem estado):

1. Abra o arquivo **"ProjectName".cs** , onde *ProjectName* é o nome escolhido para seu projeto do Visual Studio.  
2. Localize o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no método *DoWorkAsync* .  Este é um exemplo de um rastreamento ETW personalizado escrito a partir do código do aplicativo.  
3. No arquivo **ActorEventSource.cs**, você encontrará uma sobrecarga para o método `ActorEventSource.ActorMessage` que deve ser usado para eventos de alta frequência por motivos de desempenho.

Depois de adicionar o rastreamento ETW personalizado ao código de serviço, você pode criar, implantar e executar o aplicativo novamente para ver seus eventos no Visualizador de Eventos de Diagnóstico. Se você depurar o aplicativo com **F5**, o Visualizador de Eventos de Diagnóstico será aberto automaticamente.

## <a name="next-steps"></a>Próximas etapas
O mesmo código de rastreamento que você adicionou ao aplicativo acima para diagnóstico local funcionará com ferramentas que podem ser usadas para exibir esses eventos ao executar o aplicativo em um cluster do Azure. Consulte estes artigos que abordam as diferentes opções para as ferramentas e descrevem como configurá-las.

* [Como coletar logs com o Diagnóstico do Azure](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Agregação e coleta de eventos usando o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

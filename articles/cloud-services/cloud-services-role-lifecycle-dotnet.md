---
title: Tratar eventos de ciclo de vida do Serviço de Nuvem | Microsoft Docs
description: Saiba como os métodos de ciclo de vida de uma função de Serviço de Nuvem podem ser usados no .NET
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: fa4eebfa64a296e6830db3730de31ca9b0565678
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358977"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o ciclo de vida de uma função Web ou de trabalho no .NET
Quando você cria uma função de trabalho, estende a classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) que oferece métodos a serem substituídos, permitindo que você responda a eventos de ciclo de vida. Para funções Web, essa classe é opcional e, portanto, deve ser usada para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Estender a classe RoleEntryPoint
A classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) inclui métodos chamados pelo Azure quando ele **inicia**, **executa** ou **para** uma função Web ou de trabalho. Opcionalmente, você pode substituir esses métodos para gerenciar a inicialização de função, as sequências de desligamento de função ou o thread de execução da função. 

Ao estender **RoleEntryPoint**, você deverá estar ciente dos seguintes comportamentos dos métodos:

* Os métodos [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) e [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) retornam um valor booliano e, portanto, é possível retornar **false** desses métodos.
  
   Se seu código retornar **false**, o processo da função será encerrado abruptamente, sem executar nenhuma sequência de desligamento que possa existir. Em geral, você deve evitar o retorno de **false** do método **OnStart**.
* Qualquer exceção não capturada em uma sobrecarga de um método **RoleEntryPoint** será tratada como uma exceção sem tratamento.
  
   Se ocorrer uma exceção em um dos métodos do ciclo de vida, o Azure acionará o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e o processo será encerrado. Depois que sua função ficar offline, ela será reiniciada pelo Azure. Quando ocorre uma exceção não tratada, o evento [Stopping](/previous-versions/azure/reference/ee758136(v=azure.100)) não é acionado e o método **OnStop** não é chamado.

Se a sua função não for iniciada, ou se estiver reciclando os estados de inicialização, ocupado e de parada, seu código pode estar lançando uma exceção sem tratamento em um dos eventos do ciclo de vida sempre que a função é reiniciada. Nesse caso, use o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) para determinar a causa da exceção e tratá-la adequadamente. Sua função também pode estar retornando do método [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , que faz com que a função reinicie. Para obter mais informações sobre os estados de implantação, confira [Problemas comuns que causam a reciclagem de funções](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se você estiver usando as **Ferramentas do Azure para o Microsoft Visual Studio** para desenvolver seu aplicativo, os modelos de projeto de função estenderão automaticamente a classe **RoleEntryPoint** para você nos arquivos *WebRole.cs* e *WorkerRole.cs*.
> 
> 

## <a name="onstart-method"></a>Método OnStart
O método **OnStart** é chamado quando sua instância de função é colocada online pelo Azure. Durante a execução do código OnStart, a instância de função é marcada como **Ocupada** e o balanceador de carga não direcionará nenhum tráfego externo para ela. Você pode substituir esse método para executar um trabalho de inicialização, como a implementação de manipuladores de eventos e a inicialização do [Diagnóstico do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** retornar **true**, a instância será inicializada com êxito e o Azure chamará o método **RoleEntryPoint.Run**. Se **OnStart** retornar **false**, a função será imediatamente encerrada sem executar nenhuma sequência de desligamento planejada.

O exemplo de código a seguir mostra como substituir o método **OnStart** . Esse método configura e inicia um monitor de diagnóstico quando a instância de função é iniciada e configura uma transferência de dados de log para uma conta de armazenamento:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método OnStop
O método **OnStop** será chamado depois que uma instância de função tiver sido colocada offline pelo Azure e antes que o processo tenha sido encerrado. Você pode substituir esse método para chamar o código necessário para que a instância de função seja desligada corretamente.

> [!IMPORTANT]
> A execução do código no método **OnStop** tem um tempo limitado para ser encerrado quando chamado por motivos diferentes de um desligamento iniciado pelo usuário. Depois de decorrido esse tempo, o processo é encerrado e você deve garantir que o código do método **OnStop** possa ser executado rapidamente ou tolere não ser executado para ser concluído. O método **OnStop** é chamado depois que o evento **Stopping** é acionado.
> 
> 

## <a name="run-method"></a>Método Run
Você pode substituir o método **Run** para implementar um thread de execução longa para sua instância de função.

A substituição do método **Run** não é necessária; a implementação padrão inicia um thread em suspensão permanente. Se você substituir o método **Run** , seu código deverá ser bloqueado indefinidamente. Se o método **Run** retornar, a função será reciclada normalmente de forma automática; em outras palavras, o Azure aciona o evento **Stopping** e chama o método **OnStop** para que as sequências de desligamento possam ser executadas antes que a função fique offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementando os métodos de ciclo de vida do ASP.NET para uma função Web
Você pode usar os métodos de ciclo de vida do ASP.NET além daqueles fornecidos pela classe **RoleEntryPoint** para gerenciar sequências de inicialização e de desligamento para uma função Web. Isso poderá ser útil para fins de compatibilidade se você estiver portando um aplicativo ASP.NET existente para o Azure. Os métodos de ciclo de vida do ASP.NET são chamados de dentro dos métodos **RoleEntryPoint** . O método **Application\_Start** é chamado após o término do método **RoleEntryPoint.OnStart**. O método **Application\_End** é chamado antes da chamada ao método **RoleEntryPoint.OnStop**.

## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um pacote de serviços de nuvem](cloud-services-model-and-package.md).


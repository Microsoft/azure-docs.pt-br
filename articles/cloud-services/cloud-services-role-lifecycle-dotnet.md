---
title: Manipular eventos do ciclo de vida do serviço de nuvem (clássico) | Microsoft Docs
description: Saiba como usar os métodos de ciclo de vida de uma função de serviço de nuvem no .NET, incluindo RoleEntryPoint, que fornece métodos para responder a eventos de ciclo de vida.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741325"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o ciclo de vida de uma função Web ou de trabalho no .NET

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Quando você cria uma função de trabalho, estende a classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) que oferece métodos a serem substituídos, permitindo que você responda a eventos de ciclo de vida. Para funções Web, essa classe é opcional e, portanto, deve ser usada para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Estender a classe RoleEntryPoint
A classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) inclui métodos chamados pelo Azure quando ele **inicia**, **executa** ou **para** uma função Web ou de trabalho. Opcionalmente, você pode substituir esses métodos para gerenciar a inicialização de função, as sequências de desligamento de função ou o thread de execução da função. 

Ao estender **RoleEntryPoint**, você deve estar atento aos seguintes comportamentos dos métodos:

* O método [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) retorna um valor booliano, portanto, é possível retornar **false** a partir desse método.
  
   Se seu código retornar **false**, o processo da função será encerrado abruptamente, sem executar nenhuma sequência de desligamento que possa existir. Em geral, você deve evitar o retorno de **false** do método **OnStart**.
* Qualquer exceção não percebida dentro de uma sobrecarga de um método **RoleEntryPoint** é considerada uma exceção não tratada.
  
   Se ocorrer uma exceção em um dos métodos do ciclo de vida, o Azure acionará o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e o processo será encerrado. Depois que sua função ficar offline, ela será reiniciada pelo Azure. Quando ocorre uma exceção não tratada, o evento [Stopping](/previous-versions/azure/reference/ee758136(v=azure.100)) não é acionado e o método **OnStop** não é chamado.

Se a sua função não for iniciada, ou se estiver reciclando os estados de inicialização, ocupado e de parada, seu código pode estar lançando uma exceção sem tratamento em um dos eventos do ciclo de vida sempre que a função é reiniciada. Neste caso, use o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) para determinar a causa da exceção e trate-a adequadamente. Sua função também pode estar retornando do método [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , que faz com que a função reinicie. Para obter mais informações sobre os estados de implantação, confira [Problemas comuns que causam a reciclagem de funções](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se você estiver usando as **Ferramentas do Azure para o Microsoft Visual Studio** para desenvolver seu aplicativo, os modelos de projeto de função estenderão automaticamente a classe **RoleEntryPoint** para você nos arquivos *WebRole.cs* e *WorkerRole.cs*.
> 
> 

## <a name="onstart-method"></a>Método OnStart
O método **OnStart** é chamado quando sua instância de função é trazida online pelo Azure. Durante a execução do código OnStart, a instância de função é marcada como **Ocupada** e o balanceador de carga não direcionará nenhum tráfego externo para ela. Você pode substituir esse método para executar um trabalho de inicialização, como a implementação de manipuladores de eventos e a inicialização do [Diagnóstico do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** retornar **true**, a instância será inicializada com êxito e o Azure chamará o método **RoleEntryPoint.Run**. Se **OnStart** retornar **false**, a função será imediatamente encerrada sem executar nenhuma sequência de desligamento planejada.

O exemplo de código a seguir mostra como substituir o método **OnStart**. Esse método configura e inicia um monitor de diagnóstico quando a instância de função é iniciada e configura uma transferência de dados de log para uma conta de armazenamento:

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
O método **OnStop** é chamado depois que uma instância de função foi colocada offline pelo Azure e antes de o processo sair. Você pode substituir esse método para chamar o código necessário para que a instância de função seja desligada corretamente.

> [!IMPORTANT]
> O código que está sendo executado no método **OnStop** método tem uma hora limitada para concluir quando é chamado para razões diferentes de um desligamento iniciado pelo usuário. Depois que esta hora decorrer, o processo é finalizado e, portanto, você deverá ter certeza de que aquele código no método **OnStop** pode ser executado rapidamente ou tolerar a não execução até o final. O método **OnStop** é chamado depois que o evento **Stopping** é gerado.
> 
> 

## <a name="run-method"></a>Método Run
Você pode anular o método **Run** para implementar um thread longo para sua instância de função.

Substituir o método **Run** não é necessário; a implementação padrão inicia um thread que está sempre em suspensão. Se você substituir o método **Run**, seu código deverá ser bloqueado indefinidamente. Se o método **Run** retornar, a função será reciclada automaticamente normalmente; em outras palavras, o Azure ocasiona o evento **Stopping** e chama o método **OnStop** para que as sequências de desligamento possam ser executadas antes de a função ser colocada offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementando os métodos de ciclo de vida do ASP.NET para uma função Web
Você pode usar os métodos de ciclo de vida do ASP.NET, além dos fornecidos pela classe **RoleEntryPoint**, gerenciar a inicialização e as sequências de desligamento para uma função Web. Isso poderá ser útil para fins de compatibilidade se você estiver portando um aplicativo ASP.NET existente para o Azure. Os métodos do ciclo de vida do ASP.NET são chamados de dentro dos métodos **RoleEntryPoint**. O método **Application\_Start** é chamado após o término do método **RoleEntryPoint.OnStart**. O método **Application\_End** é chamado antes da chamada ao método **RoleEntryPoint.OnStop**.

## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um pacote de serviços de nuvem](cloud-services-model-and-package.md).





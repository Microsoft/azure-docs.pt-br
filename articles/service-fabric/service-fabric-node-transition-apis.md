---
title: Iniciar e interromper nós de cluster para testar os aplicativos do Azure Service Fabric |Microsoft Docs
description: Saiba como usar a injeção de falha para testar um aplicativo do Service Fabric iniciando e interrompendo nós de cluster.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: df0e53736c08fd2c26c467def7328e85f2989f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718131"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Substituir as APIs de Nó de Início e Nó de Parada pela API de Nó de Transição

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que as APIs de Nó de Parada e de Início fazem?

A API de Nó de Parada (gerenciada: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) para um nó do Service Fabric.  Um nó de Service Fabric é o processo e não uma VM ou máquina: a VM ou a máquina ainda estará em execução.  No restante do documento, "nó" significa um nó do Service Fabric.  Parar um nó coloca-o em um estado *parado* em que ele não é membro do cluster e não pode hospedar serviços, simulando assim um nó *inoperante*.  Isso é útil para injetar falhas no sistema para testar o aplicativo.  A API de Nó de Início (gerenciada: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) reverte a API de Nó de Parada, que restitui o nó de volta para um estado normal.

## <a name="why-are-we-replacing-these"></a>Por que os estamos substituindo?

Conforme descrito anteriormente, um nó *parado* do Service Fabric é um nó direcionado intencionalmente usando a API de Nó de Parada.  Um nó *inoperante* é um nó que está inativo por qualquer motivo (por exemplo, a VM ou o computador está desativado).  Com a API de Nó de Parada, o sistema não expõe informações para diferenciar entre nós *parados* e nós *inoperantes*.

Além disso, alguns erros retornados por essas APIs não são mais tão descritivos quanto poderiam ser.  Por exemplo, se for invocada a API de Nó de Parada em um nó já *parado*, isso retornará o erro *InvalidAddress*.  Essa experiência poderia ser melhorada.

Além disso, a duração pela qual um nó é interrompido é "infinita" até que a API de Nó de Início seja invocada.  Descobrimos que isso pode causar problemas e pode ser propenso a erros.  Por exemplo, houve problemas em que um usuário invocou a API de Nó de Parada em um nó e depois se esqueceu.  Posteriormente, não estava claro se o nó estava *inoperante* ou *parado*.


## <a name="introducing-the-node-transition-apis"></a>Introdução às APIs de Transição de Nó

Abordamos esses problemas acima em um novo conjunto de APIs.  A nova API de Transição de Nó (gerenciada: [StartNodeTransitionAsync()][snt]) pode ser usada para a transição de um nó do Service Fabric a um estado *parado* ou transição de um estado *parado* para um estado normal.  Observe que "Start" no nome da API não se refere a iniciar um nó.  Isso se refere a iniciar uma operação assíncrona que o sistema executará para fazer a transição do nó como estado *parado* ou iniciado.

**Uso**

Se a API de Transição de Nó não lançar uma exceção quando invocada, o sistema aceitou a operação assíncrona e a executará.  Uma chamada bem-sucedida não implica que a operação já foi concluída.  Para obter informações sobre o estado atual da operação, chame a API de Progresso de Transição de Nó (gerenciada: [GetNodeTransitionProgressAsync()][gntp]) com o guid usado ao invocar a API de Transição de Nó para essa operação.  A API de Progresso de Transição de Nó retorna um objeto NodeTransitionProgress.  A propriedade do objeto State especifica o estado atual da operação.  Se o estado for "Em Execução", a operação estará em execução.  Se for Concluído, a operação foi concluída sem erros.  Se for Falha, houve um problema ao executar a operação.  A propriedade Exception da propriedade Result indicará qual foi o problema.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate para obter mais informações sobre a propriedade de Estado e a seção "Exemplo de Uso" abaixo para obter exemplos de código.


**Diferenciação entre um nó parado e um nó inoperante** Se um nó for *parado* usando a API de Transição de Nó, a saída de uma consulta de nó (gerenciada: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) mostrará que esse nó tem um valor da propriedade *IsStopped* como verdadeiro.  Observe que isso é diferente do valor da propriedade *NodeStatus*, que indicará *Down*.  Se a propriedade *NodeStatus* tem um valor de *Down*, mas *IsStopped* é false, o nó não foi interrompido usando a API de transição de nó e é *Inoperante* devido a algum outro motivo.  Se a propriedade *IsStopped* for true e a propriedade *NodeStatus* for *Down*, ele foi interrompido usando a API de Transição de Nó.

Iniciar um nó *parado* usando a API de Transição de Nó o retornará para o funcionamento como um membro normal do cluster novamente.  A saída da API de consulta de nó mostrará *IsStopped* como false e *NodeStatus* como algo diferente de Down (por exemplo, Up).


**Duração limitada** Ao usar a API de Transição de Nó para um nó, um dos parâmetros necessários, *stopNodeDurationInSeconds*, representa a quantidade de tempo em segundos para manter o nó *parado*.  Esse valor deve estar no intervalo permitido, que tem um mínimo de 600 e um máximo de 14400.  Depois que esse tempo expirar, o nó será reiniciado com estado Operante automaticamente.  Confira o exemplo 1 abaixo para obter um exemplo de uso.

> [!WARNING]
> Evite misturar APIs de Transição de Nó e APIs de Nó de Parada e Nó de Início.  A recomendação é usar somente a API de Transição de Nó.  > Se um nó já foi interrompido usando a API de Nó de Parada, ele deve ser iniciado usando a API de Nó de Início antes de usar as > APIs de Transição de Nó.

> [!WARNING]
> Não é possível fazer várias chamadas de APIs de Transição de Nó no mesmo nó em paralelo.  Nessa situação, a API de Transição de Nó > lançará um FabricException com um valor de propriedade ErrorCode de NodeTransitionInProgress.  Depois que uma transição de nó em um nó específico > tiver sido iniciada, você deverá aguardar até que a operação atinja um estado terminal (Concluído, Falha ou ForceCancelled) antes de iniciar uma > nova transição no mesmo nó.  São permitidas chamadas de transição de nó paralelas em nós diferentes.


#### <a name="sample-usage"></a>Exemplo de uso


**Exemplo 1** - o exemplo a seguir usa a API de Transição de Nó para parar um nó.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exemplo 2** - o exemplo a seguir inicia um nó *parado*.  Ele usa alguns métodos auxiliares do primeiro exemplo.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exemplo 3** - o exemplo a seguir mostra o uso incorreto.  Esse uso é incorreto porque *stopDurationInSeconds* é maior do que o intervalo permitido.  Como StartNodeTransitionAsync() falhará com um erro fatal, a operação não foi aceita, e a API de progresso não deverá ser chamada.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Exemplo 4** - o exemplo a seguir mostra as informações de erro que serão retornadas da API de Progresso de Transição de Nó quando a operação iniciada pela API de Transição de Nó for aceita, mas falhar posteriormente durante a execução.  Nesse caso, ele falhará porque a API de Transição de Nó tenta iniciar um nó que não existe.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient

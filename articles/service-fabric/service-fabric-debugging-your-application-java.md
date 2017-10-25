---
title: Depurar o aplicativo do Azure Service Fabric no Eclipse | Microsoft Docs
description: "Melhore a confiabilidade e o desempenho dos seus serviços desenvolvendo-os e depurando-os no Eclipse em um cluster de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: f3bcee3794de35005bd387ecfae7e6707f3cb5ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depurar seu aplicativo Java do Service Fabric usando o Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [Configurando o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started-linux.md).

2. Atualize entryPoint.sh do serviço que você quer depurar para que ele inicie o processo java com parâmetros de depuração remota. Esse arquivo pode ser encontrado no seguinte local: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. Neste exemplo, a porta 8001 foi definida para depuração.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Atualize o Manifesto do Aplicativo, definindo a contagem de instâncias ou a contagem de réplicas para o serviço que está sendo depurado como 1. Essa configuração evita conflitos para a porta que é usada para depuração. Por exemplo, para serviços sem estado, defina ``InstanceCount="1"`` e, para serviços com estado, defina o destino e o tamanho mínimo do conjunto de réplicas para 1, como se segue: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Implante o aplicativo.

5. No IDE do Eclipse, escolha **Executar -> Configurações de Depuração -> Aplicativo Java Remoto e propriedades de conexão de entrada** e defina as propriedades da seguinte maneira:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Defina pontos de interrupção nos pontos desejados e depure o aplicativo.

Se o aplicativo estiver falhando, talvez seja conveniente habilitar os despejos de núcleo. Execute ``ulimit -c`` em um shell e, se retornar 0, os despejos de núcleo não estão habilitados. Para habilitar despejos de núcleo ilimitados, execute o seguinte comando: ``ulimit -c unlimited``. Você também pode verificar o status usando o comando ``ulimit -a``.  Se quiser atualizar o caminho de geração do despejo de núcleo, execute ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Próximas etapas

* [Coletar logs usando o Diagnóstico do Azure no Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

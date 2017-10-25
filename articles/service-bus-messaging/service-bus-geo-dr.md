---
title: "Recuperação de desastre em área geográfica do Barramento de Serviço do Azure | Microsoft Docs"
description: "Como usar regiões geográficas para fazer failover e executar a recuperação de desastre no Barramento de Serviço do Azure"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 2c509b56282ace92e536dc85f1a28f83a4701940
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Recuperação de desastre em área geográfica do Barramento de Serviço do Azure (versão prévia)

Quando datacenters regionais enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Como tal, *a recuperação de desastre em área geográfica* e a *replicação geográfica* são recursos importantes para qualquer empresa. O Barramento de serviço do Azure dá suporte à recuperação de desastre em área geográfica e à replicação geográfica no nível do namespace. 

A versão prévia da recuperação de desastre em área geográfica está disponível atualmente apenas em duas regiões (**Centro-Norte dos EUA** e **Centro-Sul dos EUA)**.

## <a name="outages-and-disasters"></a>Interrupções e desastres

O artigo [Melhores práticas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço](service-bus-outages-disasters.md) faz uma distinção entre "interrupções" e "desastres" que é importante observar. Uma *interrupção* é uma indisponibilidade temporária do Barramento de Serviço do Azure e pode afetar alguns componentes do serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. No entanto, depois que o problema for corrigido, o Barramento de Serviço ficará disponível novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de tal interrupção pode ser uma falha de energia no datacenter.

Um *desastre* é definido como a perda permanente de uma [unidade de escala](service-bus-architecture.md#service-bus-scale-units) ou de um datacenter do Barramento de Serviço. O datacenter pode ou não ficar disponível novamente ou pode ficar inativo por horas ou dias. Um desastre normalmente causa a perda de algumas ou de todas as mensagens, ou de outros dados. Outros exemplos desses desastres são incêndios, enchentes ou terremoto.

O recurso de recuperação de desastre em área geográfica do Barramento de Serviço do Azure é uma solução de recuperação de desastre. Os conceitos e o fluxo de trabalho descrito neste artigo se aplicam a cenários de desastre e não a falhas transitórias ou temporárias.  

## <a name="basic-concepts-and-terms"></a>Termos e conceitos básicos

O recurso de recuperação de desastre implementa a recuperação de desastre dos metadados e se baseia em namespaces de recuperação de desastre primário e secundário. Observe que o recurso de recuperação de desastre em área geográfica está disponível somente para [namespaces Premium](service-bus-premium-messaging.md). Você não precisa fazer nenhuma alteração de cadeia de conexão, já que a conexão é feita por meio de um alias.

Os seguintes termos são usados neste artigo:

-  *Alias*: a cadeia de conexão principal.

-  *Namespace primário/secundário*: descreve os namespaces que correspondem ao alias. O primário é "ativo" e recebe mensagens, o secundário é "passivo" e não recebe mensagens. Os metadados entre os dois estão sincronizados, para que ambos possam aceitar mensagens continuamente sem qualquer alteração no código do aplicativo.

-  *Metadados*: sua representação de objetos no Barramento de Serviço do Azure. No momento, só há suporte para metadados.

-  *Failover*: o processo de ativação do namespace secundário. Você deve receber mensagens do seu namespace primário antigo depois que ele ficar disponível novamente e, em seguida, excluir o namespace. Para criar outro failover, você pode adicionar um novo namespace secundário ao emparelhamento.

## <a name="failover-workflow"></a>Fluxo de trabalho de failover

A seção a seguir é uma visão geral de todo o processo de configuração do failover inicial e como avançar a partir desse ponto.

![1][]

Primeiro, configure um namespace primário e um secundário; em seguida, crie um emparelhamento. Esse emparelhamento fornece um alias que você pode usar para se conectar. Como você usa um alias, não precisa alterar cadeias de conexão. Somente novos namespaces podem ser adicionados ao emparelhamento de failover. Por fim, você precisa adicionar alguma lógica de gatilho (por exemplo, lógica de negócios que detecta se o namespace não está disponível e inicia o failover). Você pode verificar a disponibilidade do namespace usando a funcionalidade de [procura de mensagens](message-browsing.md) do Barramento de Serviço.

Depois de configurar o monitoramento e a recuperação de desastre, você pode examinar o processo de failover. Se o gatilho inicia um failover ou se você inicia o failover manualmente, estas duas etapas são necessárias:

1. Em caso de outra interrupção, você precisa ser capaz de fazer failover novamente. Portanto, configure um segundo namespace passivo e atualize o emparelhamento. 
2. Faça pull das mensagens do namespace primário antigo depois que o novo namespace estiver disponível. Depois disso, reutilize ou exclua o namespace primário antigo.

![2][]

## <a name="set-up-disaster-recovery"></a>Configurar recuperação de desastre

Esta seção descreve como criar seu próprio código de recuperação de desastre em área geográfica do Barramento de Serviço. Para fazer isso, você precisa de dois namespaces em locais independentes, por exemplo, Sul dos EUA e Centro-Norte dos EUA. O exemplo a seguir usa o Visual Studio 2017.

1.  Crie um novo projeto de **aplicativo de console (.Net Framework)** do no Visual Studio e dê a ele um nome, por exemplo, **SBGeoDR**.

2.  Instale os seguintes pacotes NuGet:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Verifique se a versão do pacote NuGet Newtonsoft.Json que você está usando é a 10.0.3.

3.  Adicione as seguintes instruções `using` ao arquivo:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Modifique o método `main()` para adicionar dois namespaces premium:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Habilite o emparelhamento entre os dois espaços e obtenha o alias que você usará posteriormente para se conectar às suas entidades:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Você configurou com êxito dois namespaces emparelhados. Agora você pode criar entidades para observar a sincronização de metadados. Se você quiser executar um failover imediatamente a seguir, dê algum tempo para que os metadados façam a sincronização. Você pode adicionar um tempo de suspensão curto, por exemplo:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

Agora, você pode adicionar entidades por meio do portal ou pelo Azure Resource Manager e ver como eles sincronizam. A menos que seu plano seja fazer failover manualmente, você deve criar um aplicativo para monitorar o namespace primário e iniciar o failover se ele ficar indisponível. 

## <a name="initiate-a-failover"></a>Iniciar um failover

O código abaixo mostra como iniciar um failover:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Após disparar o failover, adicione um novo namespace passivo e restabeleça o emparelhamento. O código para criar um novo emparelhamento é mostrado na seção anterior. Além disso, você deve remover as mensagens do namespace primário anterior após a conclusão do failover. Para obter exemplos de como receber mensagens de uma fila, confira [Introdução às filas](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Como desabilitar a recuperação de desastre em área geográfica

O código abaixo mostra como desabilitar um emparelhamento de namespace:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

O código abaixo exclui o alias que você criou:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Etapas após um failover (failback)

Após um failover, execute as etapas abaixo:

1.  Crie um novo namespace secundário passivo. O código é mostrado em uma seção anterior.
2.  Remova as mensagens restantes da fila.

## <a name="alias-connection-string-and-test-code"></a>Código de teste e de cadeia de conexão de alias

Se você quer testar o processo de failover, pode escrever um aplicativo de exemplo que envia mensagens para o namespace primário usando o alias. Para fazer isso, obtenha a cadeia de conexão do alias de um namespace ativo. Com a versão prévia atual, não há nenhuma outra interface para obter diretamente a cadeia de conexão. O seguinte exemplo de código conecta antes e depois do failover:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Próximas etapas

- Consulte a [referência da API REST de recuperação de desastre em área geográfica aqui](/rest/api/servicebus/disasterrecoveryconfigs).
- Execute o [exemplo no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) da recuperação de desastre em área geográfica.
- Consulte o [exemplo que envia mensagens a um alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) da recuperação de desastre em área geográfica.

Para saber mais sobre as mensagens do Barramento de Serviço, confira os artigos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png

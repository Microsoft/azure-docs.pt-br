---
title: Gerenciamento de estado dos Reliable Actors | Microsoft Docs
description: Descreve como o estado dos Reliable Actors é gerenciado, persistido e replicado para alta disponibilidade.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 65dd47ab21ca4b1c50e0f17b73e7bc4eae8a96e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725730"
---
# <a name="reliable-actors-state-management"></a>Gerenciamento de estado dos Reliable Actors
Reliable Actors são objetos single-threaded que podem encapsular a lógica e o estado. Como os atores são executados nos Reliable Services, eles podem manter o estado de modo confiável usando os mesmos mecanismos de persistência e replicação. Dessa forma, os atores não perdem seu estado após falhas, na reativação após a coleta de lixo ou quando são movidos entre nós em um cluster devido ao balanceamento de recursos ou às atualizações.

## <a name="state-persistence-and-replication"></a>Replicação e persistência de estado
Todos os Reliable Actors são considerados *com estado* , pois cada instância de ator é mapeada para uma ID exclusiva. Isso significa que as chamadas repetidas para a mesma ID de ator são encaminhadas para a mesma instância de ator. Em um sistema sem estado, por outro lado, as chamadas de cliente não têm garantia de serem encaminhadas sempre para o mesmo servidor. Por esse motivo, os serviços de ator são sempre serviços com estado.

Mesmo que os atores sejam considerados com estado, isso não significa que eles devem armazenar o estado de modo confiável. Os atores podem escolher o nível de persistência de estado e replicação com base em seus requisitos de armazenamento de dados:

* **Estado persistente**: Estado é persistido em disco e replicado para três ou mais réplicas. O estado persistente é a opção de armazenamento de estado mais duradoura, na qual o estado pode persistir após a interrupção completa do cluster.
* **Estado volátil**: Estado é replicado para três ou mais réplicas e mantido apenas na memória. O estado volátil proporciona resiliência contra falhas de nó e de ator, e durante atualizações e balanceamento de recursos. No entanto, o estado não é persistido no disco. Portanto, se todas as réplicas forem perdidas ao mesmo tempo, o estado também será perdido.
* **Nenhum estado persistente**: Estado não é replicado ou gravado no disco, use somente para atores que não precisam manter o estado de modo confiável.

Cada nível de persistência é apenas um *provedor de estado* e uma configuração de *replicação* diferentes do serviço. A decisão de gravar ou não o estado em disco depende do provedor de estado – o componente que armazena o estado em um serviço confiável. A replicação depende de quantas réplicas são implantadas com um serviço. Assim como acontece com os Reliable Services, o provedor de estado e a contagem de réplicas podem ser definidos manualmente com facilidade. A estrutura de ator fornece um atributo, que, quando usado em um ator, seleciona automaticamente um provedor de estado padrão e gera automaticamente as configurações de contagem de réplicas, a fim de alcançar uma dessas três configurações de persistência. O atributo StatePersistence não será herdado pela classe derivada. Cada tipo de Ator deve fornecer seu nível de StatePersistence.

### <a name="persisted-state"></a>Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Essa configuração usa um provedor de estado que armazena dados em disco e define automaticamente a contagem de réplicas do serviço como 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 3.

### <a name="no-persisted-state"></a>Nenhum estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 1.

### <a name="defaults-and-generated-settings"></a>Padrões e configurações geradas
Ao usar o atributo `StatePersistence`, um provedor de estado será selecionado automaticamente para você em tempo de execução quando o serviço de ator for iniciado. No entanto, a contagem de réplicas é definida no tempo de compilação pelas ferramentas de build de ator do Visual Studio. As ferramentas de build geram automaticamente um *serviço padrão* para o serviço de ator no ApplicationManifest.xml. Os parâmetros são criados para **tamanho mín. do conjunto de réplicas** e **tamanho de destino do conjunto de réplicas**.

Você pode alterar esses parâmetros manualmente. Mas, sempre que o atributo `StatePersistence` for alterado, os parâmetros serão definidos como os valores padrão de tamanho do conjunto de réplicas para o atributo `StatePersistence` selecionado, substituindo os valores anteriores. Em outras palavras, os valores definidos em ServiceManifest.xml serão substituídos *somente* no momento do build quando você alterar o valor do atributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Gerenciador de estado
Cada instância de ator tem seu próprio gerenciador de estado: uma estrutura de dados semelhante a um dicionário que armazena pares chave-valor de forma confiável. O gerenciador de estado é um wrapper em torno de um provedor de estado. Você pode usá-lo para armazenar dados, independentemente da configuração de persistência utilizada. Ele não dá nenhuma garantia de que um serviço de ator em execução poderá ser alterado de uma configuração de estado volátil (somente na memória) para uma configuração de estado persistente por meio de uma atualização sem interrupção, ao mesmo tempo que preserva os dados. No entanto, é possível alterar a contagem de réplicas de um serviço em execução.

As chaves do gerenciador de estado devem ser cadeias de caracteres. Os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gerenciador de estado devem ser serializáveis pelo contrato de dados, pois poderão ser transmitidos pela rede a outros nós durante a replicação e gravados em disco, dependendo da configuração de persistência de estado de um ator.

O gerenciador de estado expõe métodos comuns de dicionário para o gerenciamento do estado, semelhantes àqueles encontrados no Dicionário Confiável.

Para obter exemplos de gerenciamento de estado de ator, leia [Acessar, salvar e remover o estado de Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Práticas recomendadas
Aqui estão algumas práticas sugeridas e dicas de solução de problemas para gerenciar o estado do ator.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Faça o estado do ator tão granular quanto possível
Isso é essencial para o desempenho e uso de recursos do seu aplicativo. Sempre que houver qualquer gravação/atualização para o "estado denominado" de um ator, o valor de inteiro correspondente ao "estado denominado" será serializado e enviado pela rede para as réplicas secundárias.  As secundárias gravam em disco local e a respondem de volta para a réplica primária. Quando a primária recebe confirmações de um quorum de réplicas secundárias, ela grava o estado em seu disco local. Por exemplo, suponha que o valor é uma classe que tem 20 membros e tamanho de 1 MB. Mesmo se você modificou apenas um dos membros da classe que é do tamanho de 1 KB, você acaba pagando o custo da serialização e gravações de disco e em rede para todo o 1 MB. Da mesma forma, se o valor é uma coleção (por exemplo, uma lista, matriz ou dicionário), você paga o custo da coleção completa mesmo se você modificar um dos membros. A interface do StateManager da classe de ator é como um dicionário. Você sempre deve modelar a estrutura de dados que representa o estado de ator na parte superior desse dicionário.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gerencie o ciclo de vida do ator corretamente
Você deve ter uma diretiva clara sobre como gerenciar o tamanho do estado de cada partição de um serviço de ator. O serviço de ator deve ter um número fixo de atores e reutilizá-los tanto quanto possível. Se você cria o novo atores continuamente, você deve excluí-los depois que acabarem o trabalho. A estrutura de ator armazena alguns metadados sobre cada ator existente. Excluir todo o estado de um ator não remove os metadados sobre esse ator. Você deve excluir o ator (consulte [excluindo atores e seus estados](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) para remover todas as informações sobre ele armazenadas no sistema. Como verificação adicional, você deve consultar o serviço de ator (consulte [enumerando atores](service-fabric-reliable-actors-enumerate.md)) de vez em quando para verificar se os atores de número estão no intervalo esperado.
 
Se você vir que o tamanho do arquivo de banco de dados de um serviço de atores está aumentando para além do tamanho esperado, certifique-se de que você esteja seguindo as diretrizes anteriores. Se você estiver seguindo essas diretrizes e ainda houver problemas de tamanho de arquivo do banco de dados, você deverá [abrir um tíquete de suporte](service-fabric-support.md) com a equipe de produto para obter ajuda.

## <a name="next-steps"></a>Próximas etapas

O estado que é armazenado no Reliable Actors deve ser serializado antes de ser gravado no disco e replicado para alta disponibilidade. Saiba mais sobre a [Serialização de tipo de ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md).

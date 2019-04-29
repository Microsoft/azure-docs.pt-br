---
title: Serialização de objeto de Coleções Confiáveis no Azure Service Fabric | Microsoft Docs
description: Serialização de objeto de Coleções Confiáveis do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: ee19be45915b3ff1253ec721f4334fead19647b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723418"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialização de objeto de Coleções Confiáveis no Azure Service Fabric
As Coleções Confiáveis replicam e persistem seus itens para garantir que eles são duráveis durante falhas do computador e interrupções de energia.
Para replicar e persistir itens, as Coleções Confiáveis precisam serializá-los.

As Coleções Confiáveis obtêm o serializador adequado para determinado tipo do Gerenciador de Estado Confiável.
O Gerenciador de Estado Confiável contém serializadores internos e permite que os serializadores personalizados sejam registrados para determinado tipo.

## <a name="built-in-serializers"></a>Serializadores internos

O Gerenciador de Estado Confiável inclui um serializador interno para alguns tipos comuns, de forma que eles possam ser serializados com eficiência por padrão. Para outros tipos, o Gerenciador de Estado Confiável recorre ao uso do [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Serializadores internos são mais eficientes, pois sabem que seus tipos não podem ser alterados e não precisam incluir informações sobre o tipo, como seu nome de tipo.

O Gerenciador de Estado Confiável tem um serializador interno para os seguintes tipos: 
- Guid
- bool
- byte
- sbyte
- byte[]
- char
- string
- decimal
- double
- flutuante
- int
- uint
- longo
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Serialização personalizada

Os serializadores personalizados são geralmente usados para aumentar o desempenho ou criptografar os dados durante a transmissão e em disco. Entre outros motivos, de modo geral, os serializadores personalizados são mais eficientes do que os serializadores genéricos, já que eles não precisam serializar informações sobre o tipo. 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) é usado para registrar um serializador personalizado para o tipo T especificado. Esse registro deve ser feito na construção da StatefulServiceBase para garantir que, antes do início da recuperação, todas as Coleções Confiáveis têm acesso ao serializador relevante para ler seus dados persistentes.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Os serializadores personalizados têm precedência sobre os serializadores internos. Por exemplo, quando um serializador personalizado para int é registrado, ele é usado para serializar inteiros em vez do serializador interno para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializador personalizado

Um serializador personalizado precisa implementar a interface [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1).

> [!NOTE]
> IStateSerializer<T> inclui uma sobrecarga de Gravação e Leitura que usa um valor base chamado T adicional. Essa API destina-se à serialização diferencial. Atualmente, o recurso de serialização diferencial não está exposto. Portanto, essas duas sobrecargas só são chamadas quando a serialização diferencial é exposta e habilitada.

Veja a seguir um tipo personalizado de exemplo chamado OrderKey que contém quatro propriedades

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

A seguir está um exemplo de implementação de IStateSerializer\<OrderKey >.
Observe que as sobrecargas de Leitura e Gravação que usam o baseValue chamam sua respectiva sobrecarga para compatibilidade com versões posteriores.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Possibilidade de atualização
Em uma [atualização de aplicativo sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante esse processo, alguns domínios de atualização estarão na versão mais recente do seu aplicativo, e alguns domínios de atualização terão a versão mínima do seu aplicativo. Durante a distribuição, a nova versão do seu aplicativo deve ser capaz de ler a versão antiga dos dados, e a versão antiga do seu aplicativo deve ser capaz de ler a nova versão dos dados. Se o formato de dados não for compatível com versões anteriores e posteriores, a atualização poderá falhar ou, pior ainda, os dados poderão ser perdidos ou corrompidos.

Se você estiver usando o serializador interno, não precisará se preocupar com a compatibilidade.
No entanto, caso você esteja usando um serializador personalizado ou o DataContractSerializer, os dados precisam ser compatíveis com versões anteriores e futuras.
Em outras palavras, cada versão do serializador precisa conseguir serializar e desserializar qualquer versão do tipo.

Os usuários do Contrato de Dados devem seguir as regras de controle de versão bem definidas para adicionar, remover e alterar campos. O Contrato de Dados também tem suporte para lidar com campos desconhecidos, vinculando-se ao processo de serialização e desserialização e lidando com a herança de classe. Para saber mais, confira [Usando o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

Os usuários do serializador personalizado devem seguir as diretrizes do serializador que estiverem usando para garantir que ele é compatível com versões anteriores e posteriores.
Uma maneira comum de dar suporte a todas as versões é adicionar informações de tamanho ao início e adicionar somente propriedades opcionais.
Dessa forma, cada versão pode ler o máximo que puder e pular para a parte restante do fluxo.

## <a name="next-steps"></a>Próximas etapas
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.
  * [Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.
  * Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).
  * Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).
  * Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).

---
title: "Trabalhando com Coleções Confiáveis | Microsoft Docs"
description: "Conheça as práticas recomendadas para trabalhar com Reliable Collections."
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: 
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: f53f13e4fb83b1cd370ec673e86e5311cd93055f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-reliable-collections"></a>Trabalhando com Reliable Collections
O Service Fabric oferece um modelo de programação com estado disponível para desenvolvedores .NET por meio das Reliable Collections. Especificamente, o Service Fabric fornece as classes de dicionário confiável e fila confiável. Quando você usar essas classes, seu estado é particionado (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma partição (para semântica ACID). Vejamos um uso típico de um objeto de dicionário confiável para ver o que ele está fazendo realmente.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Todas as operações em objetos de dicionário confiável (exceto ClearAsync, que não pode ser desfeito) exigem um objeto ITransaction. Esse objeto tem associado a ele toda e qualquer alteração que você está tentando realizar em qualquer dicionário confiável e/ou objetos de fila confiável em uma mesma partição. Você adquire um objeto ITransaction chamando o método CreateTransaction do StateManager da partição.

No código acima, o objeto ITransaction é passado a um método AddAsync de um dicionário confiável. Internamente, os métodos de dicionário que aceitam uma chave usam um bloqueio de leitor/gravador associado à chave. Se o método modificar o valor da chave, ele usará um bloqueio de gravação nela e se ele apenas ler o valor da chave, um bloqueio de leitura será obtido na chave. Como o AddAsync modifica o valor da chave para o valor novo repassado, o bloqueio de gravação da chave será obtido. Desta maneira, se dois (ou mais) threads tentarem adicionar valores com a mesma chave ao mesmo tempo, um deles adquirirá o bloqueio de gravação e os outros ficarão bloqueados. Por padrão, os métodos ficam bloqueados por até quatro segundos para adquirir o bloqueio; após quatro segundos, os métodos gerarão uma TimeoutException. Há sobrecargas de método que permitem passar um valor de tempo limite explícito caso você prefira.

Normalmente você pode escrever seu código para reagir a uma TimeoutException capturando-a e repetindo toda a operação (conforme mostrado no código acima). No meu código simples, estou apenas chamando Task.Delay passando 100 milissegundos de cada vez. Porém, na realidade, é melhor usar algum tipo de atraso de recuo exponencial em vez disso.

Depois que o bloqueio é adquirido, AddAsync adiciona as referências de objeto de chave e de valor a um dicionário temporário interno associado ao objeto ITransaction. Isso é feito para fornecer a semântica de ler-suas-próprias-gravações. Ou seja, depois de você chamar AddAsync, uma chamada posterior para TryGetValueAsync (usando o mesmo objeto ITransaction) retornará o valor mesmo se você ainda não tiver confirmado a transação. Em seguida, AddAsync serializa os objetos de chave e de valor para matrizes de bytes e acrescenta essas matrizes de byte em um arquivo de log no nó local. Por fim, AddAsync envia as matrizes de bytes para todas as réplicas secundárias para que elas tenham as mesmas informações de chave/valor. Embora as informações de chave/valor tenham sido gravadas em um arquivo de log, as informações não são consideradas parte do dicionário até que a transação à qual elas estão associadas seja confirmada.

No código acima, a chamada para CommitAsync confirma todas as operações da transação. Especificamente, ela acrescenta informações de confirmação ao arquivo de log no nó local e também envia o registro de confirmação para todas as réplicas secundárias. Depois de o quórum (maioria) das réplicas responder, todas as alterações de dados são consideradas permanentes e os bloqueios associados às chaves que foram manipuladas por meio do objeto ITransaction são liberados para que outras threads/transações possam manipular as mesmas chaves e seus valores.

Se CommitAsync não for chamado (geralmente devido a uma exceção é gerada), o objeto ITransaction será descartado. Ao descartar um objeto ITransaction não confirmado, o Service Fabric acrescenta informações de anulação ao arquivo de log do nó local e nada mais precisa ser enviado para nenhuma das réplicas secundárias. Em seguida, todos os bloqueios associados às chaves que foram manipuladas por meio da transação são liberados.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-las
Agora que você entende como as coleções confiáveis funcionam internamente, vejamos alguns usos indevidos comuns. Veja o código a seguir:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ao trabalhar com um dicionário regular do .NET, você pode adicionar uma chave/valor ao dicionário e, em seguida, alterar o valor de uma propriedade (como LastLogin). No entanto, esse código não funcionará corretamente com um dicionário confiável. Lembre-se da discussão anterior: a chamada para AddAsync serializa os objetos de chave/valor para matrizes de bytes e, em seguida, salva as matrizes em um arquivo local e também as envia para as réplicas secundárias. Se você alterar uma propriedade posteriormente, isso apenas alterará o valor da propriedade na memória; isso não afeta o arquivo local ou os dados enviados para as réplicas. Se o processo falhar, o que estiver na memória será descartado. Quando um novo processo é iniciado ou se outra réplica se tornar primária, o valor antigo da propriedade será o que está disponível.

Nunca é demais enfatizar como é fácil cometer o tipo de erro mostrado acima. E você só descobrirá que o erro ocorreu se/quando o processo falhar. A maneira correta de escrever o código é simplesmente reverter as duas linhas:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Veja este outro exemplo que mostra um erro comum:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Novamente, com dicionários regulares do .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para pesquisar um valor. Se o valor existir, o desenvolvedor alterará o valor da propriedade. No entanto, com coleções confiáveis, esse código exibe o mesmo problema já abordado: **você NÃO DEVE modificar um objeto depois de atribui-lo a uma coleção confiável.**

A maneira correta de atualizar um valor em uma coleção confiável é obter uma referência ao valor existente e considerar imutável o objeto referenciado por esta referência. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, você pode modificar o estado deste novo objeto e gravá-lo na coleção para que ele seja serializado em matrizes de bytes, anexado ao arquivo local e enviado para as réplicas. Depois de confirmar as alterações, os objetos na memória, o arquivo local e todas as réplicas terão o mesmo estado exato. Parece que está tudo bem.

O código a seguir mostra a maneira correta de atualizar um valor em uma coleção confiável:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erro do programador
O ideal seria que o compilador relatasse erros quando você acidentalmente produzisse código que transforma o estado de um objeto que deve ser considerado imutável. Porém, o compilador C# não consegue fazer isso. Portanto, para evitar possíveis bugs do programador, é altamente recomendável que você defina os tipos usados com coleções confiáveis como tipos imutáveis. Especificamente, isso significa usar apenas tipos de valor principais (como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e assim por diante). E, claro, você também pode usar String. É melhor evitar propriedades de coleção, pois serializá-las e desserializá-las com frequência pode prejudicar o desempenho. No entanto, se você quiser usar propriedades de coleção, será altamente recomendável usar a biblioteca de coleções imutáveis do .NET ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Essa biblioteca está disponível para download em http://nuget.org. Também recomendamos lacrar suas classes e tornar os campos somente leitura sempre que possível.

O tipo de UserInfo abaixo demonstra como definir um tipo imutável tirando proveito das recomendações mencionadas anteriormente.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

O tipo ItemId também é um tipo imutável, conforme mostrado aqui:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Controle de versão do esquema (atualizações)
Internamente, as Reliable Collections serializam os objetos usando o DataContractSerializer do .NET. Objetos serializados são persistidos no disco local da réplica primária e também são transmitidos para as réplicas secundárias. À medida que seu serviço amadurece, é provável que você queira alterar o tipo de dados (esquema) que ele requer. Você deve abordar o controle de versão dos seus dados com muito cuidado. Antes de tudo, você deve sempre ser capaz de desserializar os dados antigos. Especificamente, isso significa que seu código de desserialização deve ser infinitamente compatível com versões anteriores: a versão 333 do seu código de serviço deve ser capaz de operar em dados colocados em uma coleção confiável pela versão 1 do seu código de serviço cinco anos atrás.

Além disso, o código de serviço é atualizado em um domínio de atualização por vez. Assim, durante uma atualização, você tem duas versões diferentes do seu código de serviço em execução simultaneamente. Você deve evitar que uma nova versão do seu código de serviço use o novo esquema, pois as versões antigas do seu código de serviço podem não ser capazes de lidar com o novo esquema. Quando possível, você deve projetar cada versão do seu serviço para ser compatível com a próxima versão. Especificamente, isso significa que a V1 do seu código de serviço deve ser capaz de simplesmente ignorar quaisquer elementos de esquema que não consegue manipular explicitamente. No entanto, ela deve ser capaz de salvar todos os dados não conhece explicitamente e simplesmente gravá-los de volta ao atualizar uma chave ou valor do dicionário.

> [!WARNING]
> Embora seja possível modificar o esquema de uma chave, você deve garantir que o código hash da chave e algoritmos igual a sejam estáveis. Se você alterar como algum desses algoritmos funciona, não será mais possível pesquisar a chave no dicionário confiável.
>
>

Como alternativa, você pode executar o que é normalmente conhecido como uma atualização de fase 2. Com uma atualização de fase 2, você atualiza seu serviço de V1 para V2: a V2 contém o código que sabe como lidar com a nova alteração de esquema, mas esse código não é executado. Quando o código V2 lê dados V1, ele opera nele e grava dados de V1. Em seguida, depois que a atualização for concluída em todos os domínios de atualização, você pode sinalizar de alguma forma para as instâncias V2 em execução que a atualização for concluída. (Uma forma de sinalizar isso é distribuir uma atualização de configuração; é isso que faz desta uma atualização de fase 2.) Agora, as instâncias V2 podem ler dados V1, convertê-los em dados V2, operá-los e gravá-los como dados V2. Quando outras instâncias lerem dados V2, elas não precisarão convertê-lo, elas simplesmente os operam e gravam dados V2.

## <a name="next-steps"></a>Próximas etapas
Para saber como criar contratos de dados compatíveis com versões futuras, consulte [Contratos de dados compatíveis com versões futuras](https://msdn.microsoft.com/library/ms731083.aspx).

Para conhecer as práticas recomendadas de contratos de dados de controle de versão, consulte [Controle de Versão de Contrato de Dados](https://msdn.microsoft.com/library/ms731138.aspx).

Para saber como implementar contratos de dados tolerantes a versões, consulte [Retornos de Chamada de Serialização Tolerantes a Versões](https://msdn.microsoft.com/library/ms733734.aspx).

Para saber como fornecer uma estrutura de dados interoperável em várias versões, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).

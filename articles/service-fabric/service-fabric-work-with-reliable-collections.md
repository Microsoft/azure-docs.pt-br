---
title: Trabalhando com Reliable Collections
description: Conheça as práticas recomendadas para trabalhar com coleções confiáveis em um aplicativo Service Fabric do Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787868"
---
# <a name="working-with-reliable-collections"></a>Trabalhando com Reliable Collections
O Service Fabric oferece um modelo de programação com estado disponível para desenvolvedores .NET por meio das Reliable Collections. Especificamente, o Service Fabric fornece as classes de dicionário confiável e fila confiável. Quando você usar essas classes, seu estado é particionado (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma partição (para semântica ACID). Vamos examinar um uso típico de um objeto Dictionary confiável e ver o que ele está realmente fazendo.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

Todas as operações em objetos de dicionário confiável (exceto ClearAsync, que não pode ser desfeito) exigem um objeto ITransaction. Este objeto está associado a ele qualquer e todas as alterações que você está tentando fazer em qualquer dicionário confiável e/ou objetos de fila confiável em uma única partição. Você adquire um objeto ITransaction chamando o método CreateTransaction de StateManager's da partição.

No código acima, o objeto ITransaction é passado para um método addasync de um dicionário confiável. Internamente, os métodos de dicionário que aceitam uma chave usam um bloqueio de leitor/gravador associado à chave. Se o método modificar o valor da chave, o método usará um bloqueio de gravação na chave e, se o método ler apenas o valor da chave, um bloqueio de leitura será obtido na chave. Como addasync modifica o valor da chave para o novo valor passado, o bloqueio de gravação da chave é obtido. Dessa maneira, se dois (ou mais) threads tentarem adicionar valores com a mesma chave ao mesmo tempo, um deles adquirirá o bloqueio de gravação e os outros ficarão bloqueados. Por padrão, os métodos ficam bloqueados por até quatro segundos para adquirir o bloqueio; após quatro segundos, os métodos gerarão uma TimeoutException. As sobrecargas de método existem, permitindo que você passe um valor de tempo limite explícito se preferir.

Normalmente você pode escrever seu código para reagir a uma TimeoutException capturando-a e repetindo toda a operação (conforme mostrado no código acima). Nesse código simples, estamos apenas chamando Task. Delay passando 100 milissegundos a cada vez. Porém, na realidade, é melhor usar algum tipo de atraso de recuo exponencial em vez disso.

Depois que o bloqueio é adquirido, AddAsync adiciona as referências de objeto de chave e de valor a um dicionário temporário interno associado ao objeto ITransaction. Isso é feito para fornecer a semântica de ler-suas-próprias-gravações. Ou seja, depois que você chamar addasync, uma chamada posterior para TryGetValueAsync usando o mesmo objeto ITransaction retornará o valor mesmo que você ainda não tenha confirmado a transação.

> [!NOTE]
> Chamar TryGetValueAsync com uma nova transação retornará uma referência ao último valor confirmado. Não modifique essa referência diretamente, pois isso ignora o mecanismo para persistir e replicar as alterações. É recomendável tornar os valores somente leitura para que a única maneira de alterar o valor de uma chave seja por meio de APIs de dicionário confiáveis.

Em seguida, AddAsync serializa os objetos de chave e de valor para matrizes de bytes e acrescenta essas matrizes de byte em um arquivo de log no nó local. Por fim, AddAsync envia as matrizes de bytes para todas as réplicas secundárias para que elas tenham as mesmas informações de chave/valor. Embora as informações de chave/valor tenham sido gravadas em um arquivo de log, as informações não são consideradas parte do dicionário até que a transação à qual elas estão associadas seja confirmada.

No código acima, a chamada para CommitAsync confirma todas as operações da transação. Especificamente, ela acrescenta informações de confirmação ao arquivo de log no nó local e também envia o registro de confirmação para todas as réplicas secundárias. Depois de o quórum (maioria) das réplicas responder, todas as alterações de dados são consideradas permanentes e os bloqueios associados às chaves que foram manipuladas por meio do objeto ITransaction são liberados para que outras threads/transações possam manipular as mesmas chaves e seus valores.

Se CommitAsync não for chamado (geralmente devido a uma exceção é gerada), o objeto ITransaction será descartado. Ao descartar um objeto ITransaction não confirmado, Service Fabric acrescenta informações de anulação ao arquivo de log do nó local e nada precisa ser enviado a nenhuma das réplicas secundárias. Em seguida, todos os bloqueios associados às chaves que foram manipuladas por meio da transação são liberados.

## <a name="volatile-reliable-collections"></a>Coleções confiáveis voláteis 
Em algumas cargas de trabalho, como um cache replicado, por exemplo, a perda ocasional de dados pode ser tolerada. Evitar a persistência dos dados em disco pode permitir melhores latências e taxas de transferência ao gravar dicionários confiáveis. A compensação para uma falta de persistência é que, se ocorrer perda de quorum, ocorrerá uma perda de dados completa. Como a perda de quorum é uma ocorrência rara, o aumento do desempenho pode valer a uma possibilidade rara de perda de dados para essas cargas de trabalho.

Atualmente, o suporte a volátil só está disponível para dicionários confiáveis e filas confiáveis, e não ReliableConcurrentQueues. Consulte a lista de [advertências](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) para informar sua decisão sobre se deseja usar coleções voláteis.

Para habilitar o suporte volátil em seu serviço, defina o ```HasPersistedState``` sinalizador na declaração de tipo de serviço como ```false``` , desta forma:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Os serviços persistentes existentes não podem se tornar voláteis, e vice-versa. Se você quiser fazer isso, será necessário excluir o serviço existente e, em seguida, implantar o serviço com o sinalizador atualizado. Isso significa que você deve estar disposto a incorrer em perda de dados completa se desejar alterar o ```HasPersistedState``` sinalizador. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-las
Agora que você entende como as coleções confiáveis funcionam internamente, vamos dar uma olhada em algumas dessas reutilizações comuns. Veja o código a seguir:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ao trabalhar com um dicionário regular do .NET, você pode adicionar uma chave/valor ao dicionário e, em seguida, alterar o valor de uma propriedade (como LastLogin). No entanto, esse código não funcionará corretamente com um dicionário confiável. Lembre-se da discussão anterior: a chamada para AddAsync serializa os objetos de chave/valor para matrizes de bytes e, em seguida, salva as matrizes em um arquivo local e também as envia para as réplicas secundárias. Se você alterar uma propriedade posteriormente, isso altera o valor da propriedade somente na memória; Ele não afeta o arquivo local ou os dados enviados para as réplicas. Se o processo falhar, o que está na memória será jogado fora. Quando um novo processo é iniciado ou se outra réplica se tornar primária, o valor antigo da propriedade será o que está disponível.

Nunca é demais enfatizar como é fácil cometer o tipo de erro mostrado acima. E você só descobrirá que o erro ocorreu se/quando o processo falhar. A maneira correta de escrever o código é simplesmente reverter as duas linhas:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Veja este outro exemplo que mostra um erro comum:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Novamente, com dicionários regulares do .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para pesquisar um valor. Se o valor existir, o desenvolvedor alterará o valor de uma propriedade. No entanto, com coleções confiáveis, esse código exibe o mesmo problema já abordado: **você NÃO DEVE modificar um objeto depois de atribui-lo a uma coleção confiável.**

A maneira correta de atualizar um valor em uma coleção confiável é obter uma referência ao valor existente e considerar imutável o objeto referenciado por esta referência. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, você pode modificar o estado deste novo objeto e gravá-lo na coleção para que ele seja serializado em matrizes de bytes, anexado ao arquivo local e enviado para as réplicas. Depois de confirmar as alterações, os objetos na memória, o arquivo local e todas as réplicas terão o mesmo estado exato. Parece que está tudo bem.

O código a seguir mostra a maneira correta de atualizar um valor em uma coleção confiável:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erro do programador
Idealmente, gostaríamos que o compilador Relate erros quando você acidentalmente produza código que modificava o estado de um objeto que você deve considerar imutável. Porém, o compilador C# não consegue fazer isso. Portanto, para evitar possíveis bugs do programador, é altamente recomendável que você defina os tipos usados com coleções confiáveis como tipos imutáveis. Especificamente, isso significa usar apenas tipos de valor principais (como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e assim por diante). Você também pode usar String. É melhor evitar propriedades de coleção, pois serializá-las e desserializá-las com frequência pode prejudicar o desempenho. No entanto, se você quiser usar propriedades de coleção, é altamente recomendável o uso de. Biblioteca de coleções imutáveis da rede ([System. Collections. imutável](https://www.nuget.org/packages/System.Collections.Immutable/)). Esta biblioteca está disponível para download no https://nuget.org . Também recomendamos lacrar suas classes e fazer com que os campos sejam somente leitura sempre que possível.

O tipo de UserInfo abaixo demonstra como definir um tipo imutável tirando proveito das recomendações mencionadas anteriormente.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
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
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

O tipo ItemId também é um tipo imutável, conforme mostrado aqui:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Controle de versão do esquema (atualizações)
Internamente, as coleções confiáveis serializam seus objetos usando o. DataContractSerializer da NET. Os objetos serializados são persistidos no disco local da réplica primária e também são transmitidos para as réplicas secundárias. À medida que o serviço amadurece, é provável que você queira alterar o tipo de dados (esquema) exigido pelo seu serviço. Você deve abordar o controle de versão dos seus dados com muito cuidado. Antes de tudo, você deve sempre ser capaz de desserializar os dados antigos. Especificamente, isso significa que seu código de desserialização deve ser infinitamente compatível com versões anteriores: a versão 333 do seu código de serviço deve ser capaz de operar em dados colocados em uma coleção confiável pela versão 1 do seu código de serviço cinco anos atrás.

Além disso, o código de serviço é atualizado em um domínio de atualização por vez. Assim, durante uma atualização, você tem duas versões diferentes do seu código de serviço em execução simultaneamente. Você deve evitar que uma nova versão do seu código de serviço use o novo esquema, pois as versões antigas do seu código de serviço podem não ser capazes de lidar com o novo esquema. Quando possível, projete cada versão do seu serviço para ser compatível com a próxima versão. Especificamente, isso significa que a V1 do seu código de serviço deve ser capaz de ignorar quaisquer elementos de esquema que não consegue manipular explicitamente. No entanto, ele deve ser capaz de salvar quaisquer dados que não saiba explicitamente e escrevê-los novamente ao atualizar uma chave ou um valor de dicionário.

> [!WARNING]
> Embora seja possível modificar o esquema de uma chave, você deve garantir que o código de hash da chave e os algoritmos de Equals sejam estáveis. Se você alterar como algum desses algoritmos funciona, não será mais possível pesquisar a chave no dicionário confiável.
> As cadeias de caracteres do .NET podem ser usadas como uma chave, mas usam a própria cadeia de caracteres como a chave – não usam o resultado de String. GetHashCode como a chave.

Como alternativa, você pode executar o que é normalmente conhecido como uma atualização de fase 2. Com uma atualização de duas fases, você atualiza seu serviço de v1 para v2: v2 contém o código que sabe como lidar com a nova alteração de esquema, mas esse código não é executado. Quando o código V2 lê dados V1, ele opera nele e grava dados de V1. Em seguida, depois que a atualização for concluída em todos os domínios de atualização, você pode sinalizar de alguma forma para as instâncias V2 em execução que a atualização for concluída. (Uma maneira de sinalizar isso é distribuir uma atualização de configuração; isso é o que faz isso uma atualização de duas fases.) Agora, as instâncias v2 podem ler dados v1, convertê-los em dados v2, operar nele e escrevê-los como dados v2. Quando outras instâncias lerem dados V2, elas não precisarão convertê-lo, elas simplesmente os operam e gravam dados V2.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como criar contratos de dados compatíveis com o encaminhamento, consulte [contratos de dados compatíveis com o encaminhamento](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Para aprender as práticas recomendadas sobre o controle de versão de contratos de dados, consulte [controle de versão de contrato de dados](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Para saber como implementar contratos de dados tolerantes a versões, consulte [retornos de chamada de serialização tolerantes à versão](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Para saber como fornecer uma estrutura de dados que possa interoperar em várias versões, consulte [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject)

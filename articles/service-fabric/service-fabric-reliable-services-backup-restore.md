---
title: Backup e restauração do Service Fabric | Microsoft Docs
description: Documentação conceitual de backup e restauração do Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: cd40f59cfa7846911c68206c3bc1e85a770b0fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723829"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Fazer backup e restaurar Reliable Services e Reliable Actors
O Azure Service Fabric é uma plataforma de alta disponibilidade que replica o estado em vários nós a fim de manter essa alta disponibilidade.  Portanto, mesmo se um nó do cluster falhar, os serviços continuarão disponíveis. Embora essa redundância interna fornecida pela plataforma possa ser suficiente para algumas pessoas, em certos casos é recomendável fazer o backup dos dados do serviço (em um repositório externo).

> [!NOTE]
> É essencial fazer backup e restaurar seus dados (e testar se funcionam conforme esperado) para que você possa se recuperar de cenários de perda de dados.
> 

> [!NOTE]
> A Microsoft recomenda usar [Backup e restauração periódica](service-fabric-backuprestoreservice-quickstart-azurecluster.md) para configurar o backup de Reliable Actors e Serviços confiáveis com estado. 
> 


Por exemplo, convém fazer o backup dos dados de um serviço para se proteger nos seguintes cenários:

- No caso de perda permanente de um cluster inteiro do Service Fabric.
- Perda permanente da maioria das réplicas de uma partição de serviço
- Erros administrativos nos quais o estado é acidentalmente excluído ou corrompido. Por exemplo, isso pode ocorrer quando um administrador com privilégios suficientes exclui o serviço por engano.
- Bugs no serviço que venham a corromper os dados. Por exemplo, isso pode ocorrer quando uma atualização de código de serviço inicia a gravação de dados com falha em uma coleção confiável. Nesse caso, convém reverter o código e os dados para um estado anterior.
- Processamento de dados offline. Talvez seja conveniente processar no modo offline os dados de business intelligence que ocorrem separadamente do serviço que gera os dados.

O recurso de Backup/Restauração permite que os serviços criados na API de Reliable Services criem e restaurem os backups. As APIs de backup fornecidas pela plataforma permitem fazer backups do estado de uma partição de serviço, sem bloquear operações de leitura ou gravação. As APIs de restauração permitem que o estado de uma partição de serviço seja restaurado de um backup escolhido.

## <a name="types-of-backup"></a>Tipos de Backup
Há duas opções de backup: Completo e Incremental.
Um backup completo é um backup que contém todos os dados necessários para recriar o estado da réplica: pontos de verificação e todos os registros de log.
Como ele tem os pontos de verificação e o log, um backup completo pode ser restaurado por si só.

O problema com backups completos surge quando os pontos de verificação são grandes.
Por exemplo, uma réplica que tem 16 GB de estado terá pontos de verificação que podem chegar a aproximadamente 16 GB.
Caso tenhamos um Objetivo de Ponto de Recuperação de cinco minutos, deve-se fazer backup da réplica a cada cinco minutos.
Cada vez que o backup for feito, será necessário copiar 16 GB de pontos de verificação além de 50 MB (configurável usando `CheckpointThresholdInMB`) em logs.

![Exemplo de Backup Completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para esse problema é fazer backups incrementais, em que o backup contém apenas os registros de log alterados desde o último backup.

![Exemplo de Backup Incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Como os backups incrementais são apenas as alterações desde o último backup (não incluem os pontos de verificação), eles tendem a ser mais rápidos, mas não podem ser restaurados por conta própria.
Para restaurar um backup incremental, a cadeia de backup inteira será necessária.
Uma cadeia de backup é uma cadeia de backups, começando com um backup completo e seguido por um número de backups incrementais contíguos.

## <a name="backup-reliable-services"></a>Fazer backup de Reliable Services
O autor do serviço tem controle total sobre quando realizar backups e onde os backups serão armazenados.

Para iniciar um backup, o serviço precisa invocar a função de membro herdado `BackupAsync`.  
Os backups somente podem ser realizados a partir de réplicas primárias e exigem a concessão do status de gravação.

Conforme mostrado abaixo, `BackupAsync` captura um objeto `BackupDescription`, em que é possível especificar um backup completo ou incremental, bem como uma função de retorno de chamada, `Func<< BackupInfo, CancellationToken, Task<bool>>>`, que será invocada quando a pasta de backup tiver sido criada localmente e estiver pronta para ser movida para algum armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Uma solicitação para fazer um backup incremental pode falhar com `FabricMissingFullBackupException`. Esta exceção indica que uma das seguintes ações está ocorrendo:

- a réplica nunca realizou backup completo desde que se tornou primária,
- alguns dos registros de log desde o último backup foram truncados ou
- a réplica ultrapassou o limite `MaxAccumulatedBackupLogSizeInMB`.

Os usuários podem aumentar a probabilidade de conseguirem fazer backups incrementais configurando `MinLogSizeInMB` ou `TruncationThresholdFactor`.
Aumentar esses valores aumentará a utilização de disco por réplica.
Para mais informações, confira [Configuração do Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` fornece informações sobre o backup, incluindo a localização da pasta em que o tempo de execução salvou o backup (`BackupInfo.Directory`). A função de retorno de chamada pode mover `BackupInfo.Directory` para um repositório externo ou para outro local.  Além disso, esta função retorna um booliano que indica se ele foi capaz de mover a pasta de backup para seu local de destino.

O código a seguir demonstra como o método `BackupCallbackAsync` pode ser usado para carregar o backup no Armazenamento do Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo acima, `ExternalBackupStore` é a classe de exemplo usada para fazer a interface com o Armazenamento de Blobs do Azure e `UploadBackupFolderAsync` é o método que compacta a pasta e a coloca no repositório de Blobs do Azure.

Observe que:

  - Pode haver apenas uma operação de backup em execução por réplica em um determinado momento. Fazer mais de uma chamada `BackupAsync` por vez gerará `FabricBackupInProgressException` para limitar os backups em andamento a um.
  - Se uma réplica passar por failover enquanto um backup estiver em andamento, talvez o backup não tenha sido concluído. Portanto, após a conclusão do failover, é responsabilidade do serviço reiniciar o backup invocando `BackupAsync` conforme necessário.

## <a name="restore-reliable-services"></a>Restaurar Reliable Services
Em geral, os casos em que você talvez precise executar uma operação de restauração se enquadram em uma destas categorias:

  - A partição do serviço perdeu os dados. Por exemplo, o disco de duas entre três réplicas de uma partição (incluindo a réplica primária) é corrompido ou apagado. Pode ser necessário que a nova réplica primária restaure os dados a partir de um backup.
  - O serviço inteiro é perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam ser restaurados.
  - Os dados do aplicativo corrompidos replicados pelo serviço (por exemplo, devido a um bug de aplicativo). Nesse caso, o serviço precisa ser atualizado ou revertido para remover a causa do dano, e os dados não corrompidos precisam ser restaurados.

Embora muitas abordagens sejam possíveis, oferecemos alguns exemplos sobre como usar `RestoreAsync` para se recuperar dos cenários acima.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição em Reliable Services
Nesse caso, o tempo de execução detectaria automaticamente a perda de dados e invocaria a API `OnDataLossAsync`.

O autor do serviço precisa executar o seguinte para fazer a recuperação:

  - Substitua o método da classe base virtual `OnDataLossAsync`.
  - Localize o último backup no local externo que contém backups do serviço.
  - Baixe o backup mais recente (e descompacte o backup na pasta de backup se ele tiver sido compactado).
  - O método `OnDataLossAsync` fornece um `RestoreContext`. Chame a API `RestoreAsync` no `RestoreContext` fornecido.
  - Retorna verdadeiro se a restauração foi um sucesso.

Veja a seguir um exemplo de implementação do método `OnDataLossAsync`:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

O `RestoreDescription` passado para a chamada `RestoreContext.RestoreAsync` contém um membro chamado `BackupFolderPath`.
Ao restaurar um único backup completo, esse `BackupFolderPath` deve ser definido como o caminho local da pasta que contém o backup completo.
Ao restaurar um backup completo e um número de backups incrementais, `BackupFolderPath` deve ser definido como o caminho local da pasta que contém não apenas o backup completo, mas também todos os backups incrementais.
A chamada `RestoreAsync` poderá lançar `FabricMissingFullBackupException` se o fornecido `BackupFolderPath` não contiver um backup completo.
Ela também poderá lançar `ArgumentException` se `BackupFolderPath` tiver uma cadeia quebrada de backups incrementais.
Por exemplo, se ele contiver o backup completo, o primeiro e o terceiro backup incremental, mas não o segundo backup incremental.

> [!NOTE]
> O RestorePolicy é definido como Seguro por padrão.  Isso significa que a API `RestoreAsync` falhará com ArgumentException se detectar que a pasta de backups contém um estado mais antigo ou igual ao estado contido nesta réplica.  `RestorePolicy.Force` pode ser usado para ignorar essa verificação de segurança. Isso é especificado como parte de `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Serviço perdido ou excluído
Se um serviço for removido, primeiro recrie o serviço antes de restaurar os dados.  É importante criar o serviço com a mesma configuração, por exemplo, esquema de particionamento, para que os dados possam ser restaurados com perfeição.  Quando o serviço estiver funcionando, a API para restauração de dados (`OnDataLossAsync` acima) precisará ser invocada em todas as partições desse serviço. Uma maneira de conseguir isso é usando [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) em cada partição.  

Neste ponto, a implementação é igual à do cenário anterior. Cada partição deve restaurar o backup mais recente relevante do armazenamento externo. Uma limitação é que a ID de partição pode ter sido alterada, uma vez que o tempo de execução cria IDs de partição dinamicamente. Portanto, o serviço precisa armazenar o nome do serviço e as informações de partição apropriadas para identificar o backup correto mais recente para restauração em cada partição.

> [!NOTE]
> Não é recomendável usar `FabricClient.ServiceManager.InvokeDataLossAsync` em cada partição para restaurar o serviço inteiro, visto que isso pode corromper o estado do cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados de aplicativo corrompidos
Se a atualização de aplicativo implantado recentemente tiver um bug, poderá causar corrupção de dados. Por exemplo, uma atualização de aplicativo pode começar a atualizar todos os registros de número de telefone em um dicionário confiável com um código de área inválido.  Nesse caso, os números de telefone inválidos serão replicados, uma vez que o Service Fabric não tem conhecimento da natureza dos dados que estão sendo armazenados.

A primeira coisa a ser feita depois de detectar um bug tão sério e que causa a corrupção de dados é congelar o serviço no nível do aplicativo e, se possível, atualizar para a versão do código do aplicativo que não tenha o bug.  No entanto, mesmo depois que o código do serviço é corrigido, os dados ainda podem estar corrompidos e, portanto, talvez seja necessário restaurá-los.  Nesses casos, talvez não seja suficiente restaurar o backup mais recente, uma vez que os backups mais recentes também podem estar corrompidos.  Assim, você precisa localizar o backup mais recente realizado antes de os dados serem corrompidos.

Caso não saiba ao certo quais backups estão corrompidos, implemente um novo cluster do Service Fabric e restaure os backups das partições afetadas, assim como o cenário descrito anteriormente para "Serviço excluído ou interrompido".  Para cada partição, comece a restaurar os backups a partir do mais recente. Caso encontre um backup sem danos, mova ou exclua todos os outros backups mais recentes dessa partição. Repita esse processo para cada partição. Agora, quando `OnDataLossAsync` for chamado na partição do cluster de produção, o último backup localizado no repositório externo será aquele escolhido pelo processo acima.

Agora, as etapas na seção "Serviço perdido ou excluído" podem ser usadas para restaurar o estado do serviço para o estado anterior à corrupção do estado pelo código com bug.

Observe que:

  - Sempre que você restaura, há uma chance do backup restaurado ser mais antigo do que o estado da partição antes de os dados serem perdidos. Por isso, essa restauração deve ser usada apenas como último recurso para recuperar o máximo possível de dados.
  - A cadeia de caracteres que representa o caminho da pasta de backup e os caminhos dos arquivos dentro da pasta de backup podem ser maiores do que 255 caracteres, dependendo do caminho de FabricDataRoot e do comprimento do nome do Tipo de Aplicativo. Isso pode fazer com que alguns métodos .NET, como `Directory.Move`, lancem a exceção `PathTooLongException`. Uma solução alternativa é chamar diretamente as APIs do kernel32, como `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Fazer backup e restaurar Reliable Actors


A Estrutura Reliable Actors foi criada com base nos Reliable Services. O ActorService que hospeda os atores é um serviço confiável com estado. Desse modo, toda a funcionalidade de backup e restauração disponível em Reliable Services também está disponível em Reliable Actors (exceto comportamentos que são específicos do provedor de estado). Uma vez que os backups serão usados por partição, será feito backup dos estados de todos os atores nessa partição (e a restauração é semelhante e acontecerá de acordo com a partição). Para executar backup/restauração, o proprietário do serviço deve criar uma classe de serviço de ator personalizada derivada da classe ActorService e depois fazer backup/restauração semelhante para os Reliable Services, como descrito acima nas seções anteriores.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Quando você cria uma classe de serviço de ator personalizada, também é preciso registrá-la ao registrar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O provedor de estado padrão para Reliable Actors é `KvsActorStateProvider`. O backup incremental não é habilitado por padrão para `KvsActorStateProvider`. Você pode habilitar o backup incremental criando `KvsActorStateProvider` com a configuração apropriada em seu construtor e, em seguida, passando-o ao construtor ActorService, conforme mostrado no seguinte snippet de código:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Após a habilitação do backup incremental, fazer um backup incremental pode falhar com FabricMissingFullBackupException por um dos seguintes motivos, sendo preciso realizar um backup completo antes de usar backups incrementais:

  - Um backup completo da réplica nunca foi feito desde que esta se tornou primária.
  - Alguns dos registros de log foram truncados desde que o último backup foi feito.

Quando o backup incremental é habilitado, `KvsActorStateProvider` não usa o buffer circular para gerenciar seus registros de log e o trunca periodicamente. Se nenhum backup for feito pelo usuário por um período de 45 minutos, o sistema automaticamente truncará os registros de log. Esse intervalo pode ser configurado especificando `logTruncationIntervalInMinutes` no construtor `KvsActorStateProvider` (semelhante à habilitação do backup incremental). Os registros de log também poderão ser truncados se a réplica primária precisar compilar outra réplica enviando todos os seus dados.

Ao fazer a restauração usando uma cadeia de backup, semelhante aos Reliable Services, BackupFolderPath deve conter subdiretórios com um subdiretório contendo o backup completo e outros subdiretórios contendo backups incrementais. A API de restauração acionará FabricException com a mensagem de erro apropriada se a validação da cadeia de backup falhar. 

> [!NOTE]
> Atualmente, `KvsActorStateProvider` ignora a opção RestorePolicy.Safe. O suporte a esse recurso está planejado para uma versão futura.
> 

## <a name="testing-back-up-and-restore"></a>Testar backup e restauração
É importante garantir que o backup dos dados críticos esteja sendo feito e que os dados possam ser restaurados desse backup. Isso pode ser feito invocando o cmdlet `Start-ServiceFabricPartitionDataLoss` no PowerShell, que pode induzir à perda de dados em uma determinada partição para testar se a funcionalidade de backup e restauração de dados para seu serviço está funcionando conforme o esperado.  Também é possível invocar de modo programático a perda de dados e fazer a restauração a partir desse evento.

> [!NOTE]
> Você pode encontrar um exemplo de implementação da funcionalidade de backup e restauração no Aplicativo de Referência da Web no GitHub. Examine o serviço `Inventory.Service` obter mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Nos bastidores: mais detalhes sobre backup e restauração
Veja mais alguns detalhes sobre backup e restauração.

### <a name="backup"></a>Backup
O Gerenciador de Estado Confiável permite a criação de backups consistentes sem bloquear as operações de leitura e gravação. Para fazer isso, ele utiliza um mecanismo de ponto de verificação e persistência de log.  O Gerenciador de Estado Confiável usa pontos de verificação (leves) difusos em determinados pontos para aliviar a pressão do log transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é chamado, o Gerenciador de Estado Confiável instrui todos os objetos Reliable a copiar seus arquivos de ponto de verificação mais recentes em uma pasta de backup local.  Em seguida, o Gerenciador de Estado Confiável copia todos os registros de log, desde o "ponteiro inicial" até o registro de log mais recente, na pasta de backup.  Como todos os registros de log, até o mais recente, são incluídos no backup e o Gerenciador de Estado Confiável preserva o registro em log write-ahead, o Gerenciador de Estado Confiável garante que todas as transações confirmadas (`CommitAsync` retornou com êxito) sejam incluídas no backup.

As transações confirmadas após `BackupAsync` ser chamado podem ou não estar no backup.  Depois da pasta de backup local ter sido populada pela plataforma (ou seja, o backup local é concluído pelo tempo de execução), o retorno de chamada de backup do serviço é invocado.  Esse retorno de chamada é responsável por mover a pasta de backups para um local externo, por exemplo, o Armazenamento do Azure.

### <a name="restore"></a>Restaurar
O Gerenciador de Estado Confiável permite a restauração de um backup usando a API `RestoreAsync`.  
O método `RestoreAsync` em `RestoreContext` pode ser chamado somente dentro do método `OnDataLossAsync`.
O bool retornado por `OnDataLossAsync` indica se o serviço restaurou seu estado de uma fonte externa.
Se `OnDataLossAsync` retornar true, o Service Fabric recompilará todas as outras réplicas a partir da primária. O Service Fabric garante que as réplicas que receberão a chamada `OnDataLossAsync` primeiro façam a transição para a função primária, mas não recebam status de leitura ou de gravação.
Isso significa que, para os implementadores de StatefulService, `RunAsync` não será chamado até que `OnDataLossAsync` seja concluído com êxito.
Em seguida, `OnDataLossAsync` será invocado na nova primária.
A API continua sendo chamada, até que um serviço conclua essa API com êxito, retornando verdadeiro ou falso, e conclua a reconfiguração relevante.

Primeiro, `RestoreAsync` descarta todos os estados existentes na réplica primária na qual foi chamado. Depois, o Gerenciador de Estado Confiável cria todos os objetos Reliable que existem na pasta de backup. Em seguida, os objetos Reliable são instruídos a restaurar a partir dos pontos de verificação na pasta de backup. Finalmente, o Gerenciador de Reliable State recupera seu próprio estado a partir dos registros de log na pasta de backup e executa a recuperação. Como parte do processo de recuperação, as operações que começaram do "ponto inicial" e confirmaram os registros de log na pasta de backup serão reproduzidas para os objetos Reliable. Essa etapa garante que o estado recuperado seja consistente.

## <a name="next-steps"></a>Próximas etapas
  - [Coleções Confiáveis](service-fabric-work-with-reliable-collections.md)
  - [Início Rápido dos Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Configuração do Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Backup e restauração periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)


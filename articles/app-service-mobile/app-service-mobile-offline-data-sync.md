---
title: Sincronização de dados offline em aplicativos móveis do Azure | Microsoft Docs
description: Visão geral e referência conceitual do recurso de sincronização de dados offline para aplicativos móveis do Azure
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205097"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de dados offline em Aplicativos Móveis do Azure
## <a name="what-is-offline-data-sync"></a>O que é sincronização de dados offline?
A sincronização de dados offline é um recurso do SDK para cliente e servidor de Aplicativos Móveis do Azure que torna mais fácil para os desenvolvedores criarem aplicativos que funcionem sem uma conexão de rede do servidor.

Quando seu aplicativo estiver no modo offline, os usuários ainda poderão criar e modificar os dados, que serão salvos em um repositório local. Quando o aplicativo estiver online novamente, você pode sincronizar as alterações locais com o back-end do aplicativo Móvel do Azure. O recurso também inclui suporte para detectar conflitos quando o mesmo registro é alterado no cliente e no back-end. Os conflitos podem ser tratados no servidor ou cliente.

A sincronização offline proporciona vários benefícios:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Crie aplicativos robustos que permanecem úteis quando há problemas de rede
* Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
* Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos
* Limite o uso de rede em redes de alta latência ou monitoradas

Os tutoriais a seguir mostram como adicionar sincronização offline para os clientes móveis usando aplicativos móveis do Azure:

* [Android: Habilitar sincronização offline]
* [Apache Cordova: Habilitar sincronização offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Habilitar sincronização offline]
* [Xamarin iOS: Habilitar sincronização offline]
* [Xamarin Android: Habilitar sincronização offline]
* [Xamarin.Forms: Habilitar sincronização offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma universal do Windows: Habilitar sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?
Para acessar o ponto de extremidade "/tables", os SDKs do cliente móvel do Azure fornecem interfaces como `IMobileServiceTable` (SDK do cliente .NET) ou `MSTable` (cliente iOS). Essas APIs se conectam diretamente ao back-end do Aplicativo Móvel do Azure e falharão se o dispositivo cliente não tiver uma conexão de rede.

Para oferecer suporte a uso offline, seu aplicativo deve usar as APIs de *tabela de sincronização*, como `IMobileServiceSyncTable` (SDK do cliente .NET) ou `MSSyncTable` (cliente iOS). As mesmas operações CRUD (Criar, Ler, Atualizar e Excluir) funcionam com as APIs de tabela de sincronização, exceto que agora elas leem ou gravam em um *repositório local*. Antes de qualquer operação de tabela de sincronização poder ser executada, o armazenamento local deve ser inicializado.

## <a name="what-is-a-local-store"></a>O que é um armazenamento local?
Um armazenamento local é a camada de persistência de dados no dispositivo cliente. Os SDKs de cliente de aplicativos móveis do Azure oferecem uma implementação padrão do armazenamento local. No Windows, Xamarin e Android, ele se baseia em SQLite. No iOS, ele se baseia em Core Data.

Para usar a implementação baseada em SQLite no Windows Phone ou Microsoft Store, você precisa instalar uma extensão SQLite. Para obter mais informações, consulte [plataforma Universal do Windows: Habilitar sincronização offline]. Android e iOS são fornecidos com uma versão do SQLite no próprio sistema operacional do dispositivo; portanto, não é necessário fazer referência a sua própria versão do SQLite.

Os desenvolvedores também podem implementar seu próprio armazenamento local. Por exemplo, se você quiser armazenar dados em um formato criptografado no cliente móvel, poderá definir um armazenamento local que usa SQLCipher para criptografia.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?
Um *contexto de sincronização* é associado a um objeto de cliente móvel (como `IMobileServiceClient` ou `MSClient`) e rastreia as alterações que são feitas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação* que conserva uma lista ordenada de operações CUD (Criar, Atualizar e Excluir) que é posteriormente enviada ao servidor.

Um armazenamento local é associado ao contexto de sincronização usando um método initialize como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK do cliente do .NET].

## <a name="how-sync-works"></a>Como a sincronização offline funciona
Ao usar tabelas de sincronização, o código do cliente controla quando as alterações locais são sincronizadas com um back-end do aplicativo móvel do Azure. Nada será enviado ao back-end até que haja uma chamada para alterações locais de *push* . Da mesma forma, o armazenamento local é preenchido com dados novos somente quando há uma chamada para dados de *pull* .

* **Push**: Envio por push é uma operação no contexto de sincronização e envia todas as alterações CUD desde o último envio por push. Observe que não é possível enviar apenas alterações de uma tabela individual, pois as operações poderiam ser enviadas fora da ordem. O envio por push executa uma série de chamadas REST ao back-end do seu aplicativo móvel do Azure, que por sua vez, modifica o banco de dados do servidor.
* **Pull**: Pull é executado em uma base por tabela e pode ser personalizado com uma consulta para recuperar apenas um subconjunto dos dados do servidor. Os SDKs de cliente móvel do Azure inserem então os dados resultantes no armazenamento local.
* **Pushes implícitos**: Se um pull é executado em uma tabela que tenha atualizações locais pendentes, o pull primeiro executará um `push()` no contexto de sincronização. Esse envio por push ajuda a minimizar conflitos entre as alterações que já estão na fila e novos dados do servidor.
* **Sincronização Incremental**: o primeiro parâmetro para a operação de pull é um *nome de consulta* que é usado apenas no cliente. Se você usar um nome de consulta não nulo, o SDK móvel do Azure executará uma *sincronização incremental*. Cada vez que uma operação de pull retornar um conjunto de resultados, o carimbo de data/hora `updatedAt` mais recente desse conjunto de resultados será armazenado nas tabelas do sistema local do SDK. As operações de pull subsequentes recuperarão somente registros após esse carimbo de data/hora.

  Para usar a sincronização incremental, o servidor deve retornar valores `updatedAt` significativos e também deve oferecer suporte à classificação por este campo. No entanto, como o SDK adiciona sua própria classificação no campo updatedAt, não é possível usar uma consulta de pull que tem sua própria cláusula `orderBy` .

  O nome da consulta pode ser qualquer sequência de caracteres que você escolher, mas deve ser exclusivo para cada consulta lógica em seu aplicativo.
  Caso contrário, operações de pull diferentes poderiam substituir o mesmo carimbo de data/hora de sincronização incremental e as consultas poderiam retornar resultados incorretos.

  Se a consulta tiver um parâmetro, uma maneira de criar um nome de consulta exclusivo é incorporar o valor do parâmetro.
  Por exemplo, se você estiver filtrando userid, o nome da consulta pode ser da seguinte maneira (em C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se você deseja recusar a sincronização incremental, passe `null` como a ID da consulta. Nesse caso, todos os registros são recuperados em cada chamada de `PullAsync`, o que é potencialmente ineficiente.
* **Limpando**: Você pode limpar o conteúdo do repositório local usando `IMobileServiceSyncTable.PurgeAsync`.
  Pode ser necessário realizar limpeza se você tiver dados obsoletos no banco de dados do cliente ou se quiser descartar todas as alterações pendentes.

  Uma limpeza remove uma tabela do repositório local. Se houver operações que estão aguardando a sincronização com o banco de dados do servidor, a limpeza gerará uma exceção, a menos que o parâmetro *forçar limpeza* esteja definido.

  Como exemplo de dados obsoletos no cliente, suponha que no exemplo "lista de tarefas", Device1 mantém apenas os itens que não foram concluídos. Um todoitem “Comprar leite” é marcado como concluído no servidor por outro dispositivo. No entanto, o Device1 ainda terá o todoitem “Comprar leite” no repositório local, pois ele só está obtendo itens que não foram marcados como concluídos. Uma limpeza limpa esse item obsoleto.

## <a name="next-steps"></a>Próximas etapas
* [iOS: Habilitar sincronização offline]
* [Xamarin iOS: Habilitar sincronização offline]
* [Xamarin Android: Habilitar sincronização offline]
* [Plataforma universal do Windows: Habilitar sincronização offline]

<!-- Links -->
[SDK do cliente do .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Habilitar sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Habilitar sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Habilitar sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Habilitar sincronização offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Plataforma universal do Windows: Habilitar sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md

---
title: Ler e atualizar um backup de coleções confiáveis localmente
description: Use o Gerenciador de backup no Azure Service Fabric para ler e atualizar um backup de coleções confiáveis local.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048061"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Ler e atualizar um backup de coleções confiáveis usando o Gerenciador de backup

O Gerenciador de backup Service Fabric do Azure ajuda com a correção de dados se os dados estiverem corrompidos em coleções Service Fabric confiáveis. O estado atual dos dados pode ser corrompido por qualquer bug introduzido em um aplicativo ou por quaisquer entradas erradas feitas em um cluster ao vivo.

Com a ajuda do Gerenciador de backup, você pode executar as seguintes tarefas:
-   Consulte os metadados da coleção.
-   Exiba o estado atual e suas entradas na coleção do backup que está carregado.
-   Lista as transações realizadas desde o último ponto de verificação.
-   Atualize a coleção adicionando, atualizando ou excluindo entradas na coleção.
-   Faça um backup novo usando o estado atualizado.

> [!NOTE]
> Service Fabric o Gerenciador de backup atualmente dá suporte apenas à exibição e edição de coleções confiáveis no backup.
>

## <a name="access-the-backup"></a>Acessar o backup

Service Fabric Gerenciador de backup pode ser consumido em qualquer uma das seguintes maneiras de exibir ou atualizar coleções confiáveis no backup:
-   **Binary**: Use um pacote NuGet para exibir e alterar as coleções confiáveis.
-   **Http/REST**: Use um servidor REST baseado em http para exibir e alterar as coleções confiáveis.
-   **bkpctl**: Use a CLI (interface de linha de comando) do Gerenciador de Backup do Service Fabric para exibir e alterar um backup de coleções confiáveis.

O Gerenciador de backup tem uma biblioteca C# para usuários avançados. Você pode usar a biblioteca no aplicativo diretamente para trabalhar com coleções confiáveis, de forma semelhante à maneira como os clientes trabalham com o Gerenciador de estado em seus serviços com estado existentes. Para usuários básicos e em um caso de uso básico, o Explorer também tem um servidor REST autônomo que expõe APIs para inspecionar os backups. A ferramenta CLI do bkpctl funciona sobre as APIs REST e baseia-se no Python. Você pode usar a ferramenta CLI para ler e atualizar backups e fazer novos backups por meio da linha de comando.

Para obter mais informações, consulte o Service Fabric repositório GitHub do [Gerenciador de backup](https://github.com/microsoft/service-fabric-backup-explorer) . O repositório contém informações de origem e versão e instruções de instalação.

Você também pode criar o repositório localmente e trabalhar em backups.
 
O NuGet para o Gerenciador de backup (Microsoft. NuGet.org. ReliableCollectionBackup. Parser) estará disponível em [](https://www.nuget.org/). 

## <a name="next-steps"></a>Próximas etapas

* Leia mais sobre as [coleções confiáveis no Azure Service Fabric serviços com estado](service-fabric-reliable-services-reliable-collections.md).
* Examine [Service Fabric práticas recomendadas](./service-fabric-best-practices-security.md).
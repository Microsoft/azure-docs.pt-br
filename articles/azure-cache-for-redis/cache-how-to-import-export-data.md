---
title: Importar e exportar dados no Cache do Azure para Redis | Microsoft Docs
description: Saiba como importar e exportar dados para e de armazenamento de blobs com suas instâncias do Cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
origin.date: 07/31/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: dfa8b47ced70386efa1daa44af318f1da55f49e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542139"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importar e exportar dados no Cache do Azure para Redis
A Importação/Exportação é uma operação de gerenciamento de dados do Cache do Azure para Redis que permite importar dados para o Cache do Azure para Redis ou exportar dados do Cache do Azure para Redis, importando e exportando um instantâneo (RBD) do Banco de Dados do Cache do Azure para Redis de um cache premium para um blob em uma Conta de Armazenamento do Azure. 

- **Exportação** - é possível exportar os instantâneos do RDB do Cache do Azure para Redis para um Blob de Páginas.
- **Importação** - é possível importar os instantâneos do RDB do Cache do Azure para Redis a partir de um Blob de Páginas ou Blob de Blocos.

A Importação/Exportação permite migrar entre diferentes instâncias do Cache do Azure para Redis ou preencher o cache com dados, antes do uso.

Este artigo fornece um guia de importação e exportação de dados com Cache do Azure para Redis e fornece respostas para perguntas frequentes.

> [!IMPORTANT]
> Importação/Exportação está em preview e está disponível somente para caches da [camada premium](cache-premium-tier-intro.md) .
>
>

## <a name="import"></a>Importar
A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

> [!NOTE]
> Antes de iniciar a operação de importação, verifique se os arquivos ou o arquivo (RDB) do Banco de Dados Redis foram carregados nos blobs de blocos ou de páginas no armazenamento do Azure, na mesma região e assinatura da instância do Cache do Azure para Redis. Para saber mais, consulte [Introdução ao Armazenamento de Blobs do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se você exportou o arquivo RDB usando o recurso [Exportação do Cache do Azure para Redis](#export), o arquivo RDB já estará armazenado em um blob de páginas e pronto para importação.
>
>

1. Para importar um ou mais blobs de cache exportados, [navegue até seu cache](cache-configure.md#configure-azure-cache-for-redis-settings) no Portal do Azure e clique em **Importar dados** no menu **Recurso**.

    ![Importar dados][cache-import-data]
2. Clique em **Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados a serem importados.

    ![Escolher uma conta de armazenamento][cache-import-choose-storage-account]
3. Clique no contêiner que contém os dados a serem importados.

    ![Escolher um contêiner][cache-import-choose-container]
4. Selecione um ou mais blobs para importar clicando na área à esquerda do nome do blob e em **Selecionar**.

    ![Escolha os blobs][cache-import-choose-blobs]
5. Clique em **Importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > O cache não pode ser acessado pelos clientes do cache durante o processo de importação e todos os dados existentes no cache serão excluídos.
   >
   >

    ![Importar][cache-import-blobs]

    Você pode monitorar o progresso da operação de importação seguindo as notificações do Portal do Azure ou exibindo os eventos no [log de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Andamento da importação][cache-import-data-import-complete]

## <a name="export"></a>Exportação
A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outra ou para outro servidor de Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância do servidor do Cache do Azure para Redis, e o arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

1. Para exportar o conteúdo atual do cache para o armazenamento, [navegue até seu cache](cache-configure.md#configure-azure-cache-for-redis-settings) no Portal do Azure e clique em **Exportar dados** no menu **Recurso**.

    ![Escolher Contêiner de Armazenamento][cache-export-data-choose-storage-container]
2. Clique em **Escolher Contêiner de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar nas mesmas região e assinatura que seu cache.

   > [!IMPORTANT]
   > A exportação funciona com blobs de páginas, que têm suporte em contas de armazenamento clássicas e do Resource Manager, mas que atualmente não têm suporte em contas de armazenamento de blobs. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >
   >

    ![Conta de armazenamento][cache-export-data-choose-account]
3. Escolha o contêiner de blob desejado e clique em **Selecionar**. Para usar um novo contêiner, clique em **Adicionar Contêiner** para adicioná-lo primeiramente e selecione-o na lista.

    ![Escolher Contêiner de Armazenamento][cache-export-data-container]
4. Digite um **Prefixo do nome de blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome de blob é usado para prefixar os nomes dos arquivos gerados por esta operação de exportação.

    ![Exportação][cache-export-data]

    Você pode monitorar o progresso da operação de exportação seguindo as notificações do Portal do Azure ou exibindo os eventos no [log de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Conclusão da exportação de dados][cache-export-data-export-complete]

    Os caches permanecem disponíveis para uso durante o processo de exportação.

## <a name="importexport-faq"></a>Perguntas frequentes de Importação/Exportação
Esta seção contém perguntas frequentes sobre o recurso de Importação/Exportação.

- [Quais tipos de preço podem usar a Importação/Exportação?](#what-pricing-tiers-can-use-importexport)
- [Posso importar dados de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
- [Quais versões do RDB posso importar?](#what-rdb-versions-can-i-import)
- [Meu cache está disponível durante uma operação de Importação/Exportação?](#is-my-cache-available-during-an-importexport-operation)
- [Posso usar a Importação/Exportação com o cluster Redis?](#can-i-use-importexport-with-redis-cluster)
- [Como a Importação/Exportação funciona com uma configuração de bancos de dados personalizada?](#how-does-importexport-work-with-a-custom-databases-setting)
- [De que forma a Importação/Exportação difere da persistência do Redis?](#how-is-importexport-different-from-redis-persistence)
- [Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
- [Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
- [Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Quais tipos de preço podem usar a Importação/Exportação?
A Importação/Exportação está disponível apenas no tipo de preço premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor Redis?
Sim, além de importar dados exportados das instâncias de Cache do Azure para Redis, é possível importar arquivos RDB de qualquer servidor de Redis em execução em qualquer nuvem ou ambiente como Linux, Windows ou provedores de nuvem, como Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor de Redis desejado em um blob de blocos ou de páginas em uma Conta de Armazenamento do Azure e importe-o para a instância premium do Cache do Azure para Redis. Por exemplo, convém exportar os dados do cache de produção e importá-los para um cache usado como parte de um ambiente de preparo para testes ou migração.

> [!IMPORTANT]
> Para importar com êxito os dados exportados de servidores de Redis diferentes do Cache do Azure para Redis ao usar um blob de páginas, o tamanho do blob de páginas deverá estar alinhado em um limite de 512 bytes. Para obter o código de amostra para executar qualquer preenchimento de bytes necessários, veja [Exemplo de upload de blob de páginas](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Quais versões do RDB posso importar?

O Cache do Azure para Redis dá suporte à importação de RDB por meio do RDB versão 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Meu cache está disponível durante uma operação de Importação/Exportação?
- **Exportação** - Os caches permanecem disponíveis e você pode continuar a usá-los durante uma operação de exportação.
- **Importação** - Os caches ficam indisponíveis quando uma operação de importação é iniciada e ficam disponíveis para uso após ela ser concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso usar a Importação/Exportação com o cluster Redis?
Sim, é possível importar/exportar entre um cache clusterizado e um não clusterizado. Já que o cluster Redis [só dá suporte a banco de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), quaisquer dados em bancos de dados diferentes de 0 não serão importados. Quando os dados do cache clusterizados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como a Importação/Exportação funciona com uma configuração de bancos de dados personalizada?
Alguns tipos de preço têm diferentes [limites de bancos de dados](cache-configure.md#databases), portanto, haverá algumas considerações a fazer ao importar se você tiver configurado um valor personalizado para a configuração `databases` durante a criação do cache.

- Ao importar para um tipo de preço com um limite de `databases` menor do que o tipo do qual você exportou:
  - Se você estiver usando o número padrão de `databases`, que é 16 para todos os tipos de preço, nenhum dado será perdido.
  - Se você estiver usando um número personalizado de `databases` que se encaixar dentro dos limites para o tipo para o qual você está importando, nenhum dado será perdido.
  - Se os dados exportados continham dados em um banco de dados que excede os limites do novo tipo, os dados de um desses bancos de dados mais altos não serão importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>De que forma a Importação/Exportação difere da persistência do Redis?
A persistência do Cache do Azure para Redis permite que você persista dados armazenados no Redis para Armazenamento do Azure. Quando a persistência é configurada, o Cache do Azure para Redis persiste um instantâneo do Cache do Azure para Redis em um formato binário do Redis para o disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, os dados do cache serão restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [Como configurar persistência de dados para um Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

A Importação/Exportação permite trazer dados para ou exportar do Cache do Azure para Redis. Ele não configurar o backup e restauração usando a persistência do Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?
Sim, para instruções do PowerShell, consulte [Importar um Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [Exportar um Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?
Se você permanecer na folha **Importar dados** ou **Exportar dados** por mais de 15 minutos antes de iniciar a operação, receberá um erro com uma mensagem semelhante à seguinte:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver esse problema, inicie a operação de importação ou exportação dentro do prazo de 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?
A exportação funciona somente com arquivos RDB armazenados como blobs de páginas. Não há suporte para outros tipos de blobs no momento, incluindo contas de armazenamento de Blobs com as camadas dinâmicas e estáticas. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Próximas etapas
Aprenda a usar mais recursos de cache premium.

- [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png


<!-- Update_Description: update metedata properties -->
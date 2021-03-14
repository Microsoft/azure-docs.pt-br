---
title: Migrar para o Cache do Azure para Redis
description: Saiba como migrar seu cache existente para o cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451516"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrar para o Cache do Azure para Redis
Este artigo descreve várias abordagens para migrar um cache Redis existente em execução local ou em outro serviço de nuvem para o cache do Azure para Redis.

## <a name="migration-scenarios"></a>Cenários de migração
O Redis de código-fonte aberto pode ser executado em vários ambientes de computação. Exemplos comuns incluem:

- Caches Redis **locais** em execução em data centers privados.
- **VMs baseadas em nuvem** – caches Redis em execução em VMs do Azure, AWS EC2 e assim por diante.
- **Serviços de hospedagem** – serviços Rediss gerenciados, como AWS ElastiCache.
- **Regiões diferentes** – caches Redis localizados em outra região do Azure.

Se você tiver esse cache, talvez seja possível movê-lo para o cache do Azure para Redis com interrupção mínima ou tempo de inatividade.

## <a name="migration-options"></a>Opções de migração

Há diferentes maneiras pelas quais você pode alternar de um cache para outro. Dependendo de onde o cache é e como seu aplicativo interage com ele, um método será mais útil do que os outros. Algumas das estratégias de migração usadas com frequência são detalhadas abaixo.

   | Opção       | Vantagens | Desvantagens |
   | ------------ | ---------- | ------------- |
   | Criar um novo cache | Mais simples de implementar. | É necessário preencher novamente os dados para o novo cache, o que pode não funcionar com muitos aplicativos. |
   | Exportar e importar dados por meio do arquivo RDB | Compatível com qualquer cache Redis em geral. | Alguns dados podem ser perdidos, se eles forem gravados no cache existente depois que o arquivo RDB for gerado. | 
   | Dados de gravação dupla em dois caches | Nenhuma perda de dados ou tempo de inatividade Operações ininterruptas do cache existente. Teste mais fácil do novo cache. | Precisa de dois caches por um longo período de tempo. | 
   | Migrar dados programaticamente | Controle total sobre como os dados são movidos. | Requer código personalizado. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Criar um novo cache do Azure para Redis

Essa abordagem tecnicamente não é uma migração. Se a perda de dados não for uma preocupação, a maneira mais fácil de mover para o cache do Azure para Redis é criar a instância de cache e conectar seu aplicativo a ele. Por exemplo, se você usar Redis como um cache de aparências de registros de banco de dados, poderá facilmente recriar o cache do zero.

As etapas gerais para implementar essa opção são:

1. Crie um novo cache do Azure para a instância Redis.

2. Atualize seu aplicativo para usar a nova instância.

3. Exclua a instância antiga de Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Exportar dados para um arquivo RDB e importá-los para o cache do Azure para Redis

O Redis de código-fonte aberto define um mecanismo padrão para tirar um instantâneo do conjunto de armazenamento na memória de um cache e salvá-lo em um arquivo. Esse arquivo, chamado RDB, pode ser lido por outro cache Redis. O [cache do Azure para a camada Premium do Redis](cache-overview.md#service-tiers) oferece suporte à importação de dados em uma instância de cache por meio de arquivos RDB. Você pode usar um arquivo RDB para transferir dados de um cache existente para o cache do Azure para Redis.

> [!IMPORTANT]
> O formato de arquivo RDB pode ser alterado entre as versões Redis e pode não manter a compatibilidade com versões anteriores. A versão Redis do cache do qual você está exportando deve ser igual ou menor que a versão fornecida pelo cache do Azure para Redis.
>

As etapas gerais para implementar essa opção são:

1. Crie um novo cache do Azure para a instância Redis na camada Premium que tenha o mesmo tamanho de (ou maior que) o cache existente.

2. Salve um instantâneo do cache Redis existente. Você pode [Configurar o Redis para salvar os instantâneos](https://redis.io/topics/persistence) periodicamente ou executar o processo manualmente usando os comandos [Save](https://redis.io/commands/save) ou [BGSAVE](https://redis.io/commands/bgsave) . O arquivo RDB é denominado "dump. RDB" por padrão e estará localizado no caminho especificado no arquivo de configuração *Redis. conf* .

    > [!NOTE]
    > Se você estiver migrando dados no cache do Azure para Redis, consulte [estas instruções sobre como exportar um arquivo RDB](cache-how-to-import-export-data.md) ou use o [cmdlet de exportação do PowerShell](/powershell/module/azurerm.rediscache/export-azurermrediscache) em vez disso.
    >

3. Copie o arquivo RDB para uma conta de armazenamento do Azure na região em que o novo cache está localizado. Você pode usar o AzCopy para esta tarefa.

4. Importe o arquivo RDB para o novo cache usando essas [instruções de importação](cache-how-to-import-export-data.md) ou o [cmdlet de importação do PowerShell](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Atualize seu aplicativo para usar a nova instância de cache.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Gravar em dois caches Redis simultaneamente durante o período de migração

Em vez de mover dados diretamente entre os caches, você pode usar seu aplicativo para gravar dados em um cache existente e um novo que você está configurando. O aplicativo ainda lerá os dados do cache existente inicialmente. Quando o novo cache tem os dados necessários, você alterna o aplicativo para esse cache e desativa o antigo. Digamos, por exemplo, que você use Redis como um repositório de sessão e que as sessões de aplicativo sejam válidas por sete dias. Depois de gravar nos dois caches por uma semana, você terá certeza de que o novo cache contém todas as informações de sessão não expiradas. Você pode confiar nele com segurança a partir desse ponto em diante, sem preocupação com a perda de dados.

As etapas gerais para implementar essa opção são:

1. Crie um novo cache do Azure para a instância Redis na camada Premium que tenha o mesmo tamanho de (ou maior que) o cache existente.

2. Modifique o código do aplicativo para gravar nas instâncias nova e original.

3. Continue lendo dados da instância original até que a nova instância seja suficientemente populada com dados.

4. Atualize o código do aplicativo para ler e gravar apenas a partir da nova instância.

5. Exclua a instância original.

### <a name="migrate-programmatically"></a>Migrar programaticamente

Você pode criar um processo de migração personalizado lendo dados de forma programática de um cache existente e gravando-os no cache do Azure para Redis. Essa [ferramenta de código-fonte aberto](https://github.com/deepakverma/redis-copy) pode ser usada para copiar dados de um cache do Azure para instância Redis para outro. Essa ferramenta é útil para mover dados entre instâncias de cache em diferentes regiões de cache do Azure. Uma [versão compilada](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) também está disponível. Você também pode encontrar o código-fonte como um guia útil para escrever sua própria ferramenta de migração.

> [!NOTE]
> Essa ferramenta não é oficialmente suportada pela Microsoft. 
>

As etapas gerais para implementar essa opção são:

1. Crie uma VM na região onde o cache existente está localizado. Se seu conjunto de recursos for grande, escolha uma VM relativamente poderosa para reduzir o tempo de cópia.

2. Crie um novo cache do Azure para a instância Redis.

3. Libere dados do novo cache para garantir que ele esteja vazio. Essa etapa é necessária porque a ferramenta de cópia em si não substitui nenhuma chave existente no cache de destino.

    > [!IMPORTANT]
    > Certifique-se de não liberar do cache de origem.
    >

4. Use um aplicativo como a ferramenta de código-fonte aberto acima para automatizar a cópia de dados do cache de origem para o destino. Lembre-se de que o processo de cópia pode demorar um pouco para ser concluído, dependendo do tamanho do conjunto de seus conjuntos.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço Redis](cache-overview.md#service-tiers)
* [Importar dados](cache-how-to-import-export-data.md#import)
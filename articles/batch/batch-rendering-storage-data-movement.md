---
title: Movimentação de dados para renderização - Lote do Azure
description: Opções de armazenamento e movimentação de dados para as cargas de trabalho de renderização
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 5a0d4dc82995e63697cc673bc54695c9c6d586df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773988"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opções de armazenamento e movimentação de dados para ativo de renderização e arquivos de saída

Há várias opções para disponibilizar os arquivos de cena e ativo para os aplicativos de renderização no pool de VMs:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Arquivos de cena e ativo são carregados no armazenamento de blobs de um sistema de arquivos local. Quando o aplicativo é executado por uma tarefa, os arquivos necessários são copiados do armazenamento de BLOBs na máquina virtual para que possam ser acessados pelo aplicativo de renderização. Os arquivos de saída são gravados pelo aplicativo de renderização para o disco da VM e, em seguida, copiados para o armazenamento de BLOBs.  Se necessário, os arquivos de saída podem ser baixados do armazenamento de BLOBs para um sistema de arquivos local.
  * O armazenamento de BLOBs do Azure é uma opção simple e econômica para projetos menores.  Como ativo todos os arquivos são necessários em cada pool de VM, em seguida, depois que aumenta o número e tamanho dos arquivos de ativo, tome cuidado para garantir que as transferências de arquivo sejam tão eficientes quanto possíveis.  
* Armazenamento do Azure como um sistema de arquivos usando [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Para VMs do Linux, uma conta de armazenamento pode ser exposta e usada como um sistema de arquivos quando o driver do sistema de arquivos virtual blobfuse é usado.
  * Essa opção tem a vantagem de ser muito econômica, como nenhuma VM é necessária para o sistema de arquivos, além de o cache blobfuse nas VMs evitar downloads repetidos dos mesmos arquivos para vários trabalhos e tarefas.  A movimentação de dados também é simples, como os arquivos são simplesmente blobs e padrão de APIs e ferramentas, como o azcopy, podem ser usados para copiar arquivos entre um sistema de arquivos local e o armazenamento do Azure.
* Sistema de arquivos ou compartilhamento de arquivos:
  * Dependendo do sistema operacional da VM e requisitos de desempenho/escala, em seguida, as opções incluem [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), usando uma VM com discos anexados for NFS, usando várias VMs com discos anexados para um sistema de arquivos distribuído como GlusterFS, ou usando uma oferta de terceiros.
  * [Sistemas de Avere](https://www.averesystems.com/) agora faz parte da Microsoft e terá no futuro próximo soluções que são ideais para a renderização em larga escala e de alto desempenho.  A solução Avere permitirá que um NFS com base no Azure ou cache SMB seja criado para funcionar em conjunto com o armazenamento de BLOBs ou com dispositivos NAS locais.
  * Com um sistema de arquivos, os arquivos podem ser lidos ou gravados diretamente no sistema de arquivos ou podem ser copiados entre o sistema de arquivos e o pool de VMs.
  * Um sistema de arquivos compartilhados permite um grande número de ativos compartilhados entre projetos e trabalhos a serem utilizados com as tarefas de renderização apenas acessando o que é necessário.

## <a name="using-azure-blob-storage"></a>Usando o armazenamento de Blobs do Azure

Uma conta de armazenamento de blobs ou uma conta de armazenamento de uso geral v2 deve ser usada.  Esses dois tipos de conta de armazenamento podem ser configurados com limites significativamente mais altos comparados a uma conta de armazenamento de uso geral v1, conforme detalhado nesta [postagem no blog](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Quando configurados, os limites mais altos permitirão muito melhor desempenho e escalabilidade, especialmente quando há várias VMs do pool, acessando a conta de armazenamento.

### <a name="copying-files-between-client-and-blob-storage"></a>Copiar arquivos entre cliente e armazenamento de BLOBs

Para copiar arquivos para e do armazenamento do Azure, vários mecanismos podem ser usados incluindo o blob de armazenamento, API, a [biblioteca de movimentação de dados do armazenamento do Azure](https://github.com/Azure/azure-storage-net-data-movement), a ferramenta de linha de comando azcopy para [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/), e [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Por exemplo, usando o azcopy, todos os ativos em uma pasta podem ser transferidos da seguinte maneira:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Para copiar somente os arquivos modificados, o parâmetro /XO podem ser usados:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool de lote

Há duas abordagens diferentes para copiar arquivos com a melhor abordagem determinada pelo tamanho dos ativos de trabalho.
A abordagem mais simples é copiar todos os arquivos de ativo para o pool de VMs para cada trabalho:

* Quando houver arquivos exclusivos para um trabalho, mas que são necessários para todas as tarefas de um trabalho, em seguida, um [tarefa de preparação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) pode ser especificada para copiar todos os arquivos.  A tarefa de preparação de trabalho é executada uma vez quando a primeira tarefa de trabalho for executada em uma VM, mas não será executada novamente para tarefas de trabalho subsequente.
* Um [tarefa de liberação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) deve ser especificada para remover os arquivos por trabalho quando o trabalho for concluído; isso evitará que o disco da VM seja preenchido por todos os arquivos de ativo do trabalho.
* Quando houver vários trabalhos usando os mesmos recursos, com apenas alterações incrementais para os ativos para cada trabalho, em seguida, todos os arquivos de ativo serão ainda copiados, mesmo que apenas um subconjunto tenha sido atualizado.  Isso poderá ser ineficiente quando houver muitos arquivos grandes de ativo.

Quando os arquivos de ativo são reutilizados entre trabalhos, com apenas as alterações incrementais entre os trabalhos, é uma abordagem mais eficiente, mas um pouco mais envolvida armazenar ativos na pasta compartilhada na VM e sincronizar os arquivos alterados.

* A tarefa de preparação de trabalho deve executar a cópia usando o azcopy com o parâmetro /XO até a pasta compartilhada de VM especificado pela variável de ambiente AZ_BATCH_NODE_SHARED_DIR.  Isso copiará os arquivos alterados somente para cada VM.
* Ideias precisarão ser fornecidas para o tamanho de todos os ativos para garantir que se encaixarão na unidade temporária do pool de VMs.

O Lote do Azure tem suporte interno para copiar arquivos entre uma conta de armazenamento e VMs do pool de lote.  Arquivos de cópia dos [arquivos de recurso](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) da tarefa dos arquivos do armazenamento para VMs do pool e podem ser especificados para a tarefa de preparação de trabalho.  Infelizmente, quando houver centenas de arquivos é possível atingir um limite e falhas nas tarefas.  Quando houver grandes números de ativos é recomendável usar a linha de comando do azcopy na tarefa de preparação de trabalho, que pode usar caracteres curinga e não tem nenhum limite.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copiar arquivos de saída para o armazenamento de blobs de VMs do pool de lote

[Arquivos de saída](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) podem ser arquivos de cópia usada de uma VM do pool de armazenamento.  Um ou mais arquivos podem ser copiados da VM para uma conta de armazenamento especificada quando a tarefa for concluída.  A saída renderizada deve ser copiada, mas também pode ser desejável para armazenar arquivos de log.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Usando um sistema de arquivo virtual blobfuse para os pools de VM do Linux

Blobfuse é um driver de sistema de arquivos virtual para o Armazenamento de Blobs do Azure que permite acessar os arquivos armazenados como Blobs em uma conta de Armazenamento através do sistema de arquivos Linus.

Os nós do pool podem montar o sistema de arquivos quando iniciado ou a montagem pode ocorrer como parte de uma tarefa de preparação do trabalho – uma tarefa que é executada somente quando a primeira tarefa em um trabalho é executado em um nó.  O Blobfuse pode ser configurado para aproveitar um ramdisk e o SSD local de VMs para o cache de arquivos, que irá aumentar significativamente o desempenho se várias tarefas em um nó acessam alguns dos mesmos arquivos.

[Modelos de exemplo estão disponíveis](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) para execução autônoma para V-Ray renderizado usando um sistema de arquivos do blobfuse e podem ser usados como base para modelos para outros aplicativos.

### <a name="accessing-files"></a>Acessar arquivos

Tarefas de trabalho especificam caminhos para arquivos de entrada e saída usando o sistema de arquivos montado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool de lote

Como os arquivos são simplesmente blobs no armazenamento do Azure, APIs de BLObs padrão, ferramentas e interfaces do usuário podem ser usadas para copiar arquivos entre um local system e o armazenamento de BLOBs de arquivos; por exemplo, o azcopy, o Gerenciador de Armazenamento do Microsoft Azure, o Gerenciador do Lote, etc.

## <a name="using-azure-files-with-windows-vms"></a>Como usar os Arquivos do Azure com as VMs do Windows

Os [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do protocolo SMB.  Os arquivos que são baseados no armazenamento de Blobs do Azure são [econômicos](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configurados com replicação de dados para outra região, então são redundantes globalmente.  [Dimensionar destinos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) devem ser analisados para determinar se os arquivos do Azure devem ser usados devido ao tamanho de pool de previsão e o número de arquivos de ativo.

Há um [postagem de blog](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) e [documentação](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) abordando como montar um compartilhamento de arquivos do Azure.

### <a name="mounting-an-azure-files-share"></a>Montagem de um compartilhamento de arquivos do Azure

Para usar em lote, uma operação de montagem precisa ser executada sempre que uma tarefa em execução não for possível persistir a conexão entre tarefas.  A maneira mais fácil de fazer isso é usar cmdkey para manter as credenciais usando a tarefa inicial na configuração do pool e, em seguida, montar o compartilhamento antes de cada tarefa.

Exemplo de uso de cmdkey em um modelo de pool (escape para uso no arquivo JSON) – observe que ao separar a chamada cmdkey da chamada de uso de rede, o contexto do usuário para a tarefa inicial deve ser o mesmo usado para executar as tarefas:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Exemplo de Linha de comando de tarefa de trabalho:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Acessar arquivos

Tarefas de trabalho especificam caminhos para arquivos de entrada e saída usando o sistema de arquivos montado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool de lote

Os arquivos do Azure têm suporte para todas as principais APIs e ferramentas que têm suporte de Armazenamento do Microsoft Azure, por exemplo, o azcopy, CLI do Azure, o Gerenciador de armazenamento, Azure PowerShell, Gerenciador do lote, etc.

[Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) está disponível para sincronizar automaticamente os arquivos entre um sistema de arquivos local e um compartilhamento de arquivos do Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as opções de armazenamento, consulte a documentação detalhada:

* [Armazenamento de blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

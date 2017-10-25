---
title: Copiar ou mover dados para o Armazenamento do Azure com o AzCopy no Windows | Microsoft Docs
description: "Use o utilitário AzCopy no Windows para mover ou copiar dados para ou de conteúdo de blob, tabela e arquivo. Copie dados para o Armazenamento do Azure de arquivos locais ou copie dados dentro na mesma conta ou entre contas de armazenamento. Migre facilmente seus dados para o Armazenamento do Azure."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2017
ms.author: seguler
ms.openlocfilehash: 1a4c52babe76e59eacb30e8be91ed934cdbe305b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transferir dados com AzCopy no Windows
O AzCopy é um utilitário de linha de comando projetado para copiar dados entre o Armazenamento de Blobs, de Arquivos e de Tabelas do Microsoft Azure usando comandos simples com o desempenho ideal. Você pode copiar dados de um objeto para outro dentro de sua conta de armazenamento, ou entre contas de armazenamento.

Há duas versões do AzCopy que podem ser baixadas. O AzCopy no Windows se baseia no .NET Framework e oferece opções de linha de comando no estilo Windows. O [AzCopy no Linux](storage-use-azcopy-linux.md) se baseia no .NET Core Framework, que se destina a plataformas Linux que oferecem opções de linha de comando no estilo POSIX. Este artigo aborda o AzCopy no Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Baixar e instalar o AzCopy no Windows

Baixe a [versão mais recente do AzCopy no Windows](http://aka.ms/downloadazcopy).

Depois de instalar o AzCopy no Windows usando o instalador, abra uma janela de comando e navegue até o diretório de instalação do AzCopy em seu computador, onde o executável `AzCopy.exe` está localizado. Se quiser, você pode alterar o local da instalação do AzCopy para o caminho do sistema. Por padrão, o AzCopy é instalado em `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou em `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Como escrever seu primeiro comando do AzCopy

A sintaxe básica dos comandos do AzCopy é:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Os exemplos a seguir demonstram vários cenários para cópia de dados entre os Blobs, os Arquivos e as Tabelas do Microsoft Azure. Confira a seção [Parâmetros do AzCopy](#azcopy-parameters) para obter uma explicação detalhada dos parâmetros usados em cada exemplo.

## <a name="download-blobs-from-blob-storage"></a>Baixar blobs do Armazenamento de blobs

Vamos examinar várias maneiras de baixar blobs usando o AzCopy.

### <a name="download-a-single-blob"></a>Baixar um blob individual

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Observe que se a pasta `C:\myfolder` não existir, o AzCopy a criará e baixará `abc.txt ` na nova pasta.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Baixar um blob individual da região secundária

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Observe que você deve ter o armazenamento com redundância geográfica com acesso de leitura habilitado para acessar a região secundária.

### <a name="download-all-blobs-in-a-container"></a>Baixar todos os blobs em um contêiner

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Suponhamos que os seguintes blobs residam no contêiner especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de download, o diretório `C:\myfolder` inclui os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se você não especificar a opção `/S`, nenhum blob será baixado.

### <a name="download-blobs-with-a-specific-prefix"></a>Baixar blobs com um prefixo específico

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Suponhamos que os blobs a seguir residam no contêiner especificado. Todos os blobs que começam com o prefixo `a` são baixados:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de download, a pasta `C:\myfolder` inclui os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo se aplica ao diretório virtual, que forma a primeira parte do nome do blob. No exemplo mostrado acima, como não corresponde ao prefixo especificado, o diretório virtual não é baixado. Além disso, se a opção `\S` não for especificada, o AzCopy não baixará nenhum blob.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Defina a hora da última modificação dos arquivos exportados como sendo a mesma dos blobs de origem

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Você também pode excluir blobs da operação de download com base na hora da última modificação Por exemplo, se você quiser excluir blobs cuja hora da última modificação for a mesma ou mais recente do que o arquivo de destino, adicione a opção `/XN`:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Se desejar excluir blobs cuja hora da última modificação é a mesma ou mais antiga do que o arquivo de destino, adicione a opção `/XO`:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Carregar blobs para o Armazenamento de blobs

Vamos examinar várias maneiras de carregar blobs usando o AzCopy.

### <a name="upload-a-single-blob"></a>Carregar um blob individual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Carregar um blob individual em um diretório virtual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Se o diretório virtual especificado não existir, o AzCopy carregará o arquivo para incluir o diretório virtual em seu nome (*por exemplo*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-blobs-in-a-folder"></a>Carregar todos os blobs em uma pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

A especificação da opção `/S` carrega o conteúdo do diretório especificado no Armazenamento de blobs de maneira recursiva, o que significa que todas as subpastas e seus arquivos são carregados também. Por exemplo, suponhamos que os seguintes arquivos residam na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de upload, o contêiner incluirá os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se você não especificar a opção `/S`, o AzCopy não carregará de forma recursiva. Após a operação de upload, o contêiner incluirá os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Carregar blobs que correspondem a um padrão específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Vamos supor que a pasta `C:\myfolder`contenha os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de upload, o contêiner incluirá os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se você não especificar a opção `/S`, o AzCopy carregará somente os blobs que não residem em um diretório virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificar o tipo de conteúdo MIME de um blob de destino

Por padrão, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. A partir da versão 3.1.0, você pode especificar explicitamente o tipo de conteúdo por meio da opção `/SetContentType:[content-type]`. Essa sintaxe define o tipo de conteúdo para todos os blobs em uma operação de carregamento.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Se você especificar `/SetContentType` sem um valor, o AzCopy definirá o tipo de conteúdo de cada blob ou arquivo de acordo com sua extensão de arquivo.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copiar blobs no Armazenamento de blobs

Vamos examinar várias maneiras de copiar blobs de um local para outro usando o AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copiar um blob individual de um contêiner para outro na mesma conta de armazenamento 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Quando você copia um blob em uma conta de armazenamento, uma operação de [cópia no servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é realizada.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copiar um blob individual de uma conta de armazenamento para outra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Quando você copia um blob entre contas de armazenamento, uma operação de [cópia no servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é realizada.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copiar um blob individual da região secundária para a região primária

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Observe que você deve ter o armazenamento com redundância geográfica com acesso de leitura habilitado para acessar o armazenamento secundário.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copiar um blob individual e seus instantâneos de uma conta de armazenamento para outra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Após a operação de cópia, o contêiner de destino incluirá o blob e seus instantâneos. Supondo que o blob no exemplo acima tenha dois instantâneos, o contêiner incluirá os seguintes blob e instantâneos:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copiar todos os blobs em um contêiner para outra conta de armazenamento 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

A especificação da opção /S carrega o conteúdo do contêiner especificado de forma recursiva. Consulte [Carregar todos os blobs em uma pasta](#upload-all-blobs-in-a-folder) para obter mais informações e um exemplo.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Copiar blobs de forma síncrona de uma conta de armazenamento para outra

O AzCopy por padrão copia dados entre dois pontos de extremidade de armazenamento assincronamente. Portanto, a operação de cópia é executada em segundo plano usando a capacidade de largura de banda extra, sem nenhum SLA em termos da velocidade de como o blob é copiado, e o AzCopy verifica periodicamente o status da cópia até que a cópia esteja concluída ou tenha ocorrido uma falha.

A opção `/SyncCopy` garante que a operação de cópia obtém uma velocidade consistente. O AzCopy realiza a cópia síncrona baixando os blobs para copiar da fonte especificada para a memória local, e, em seguida, carregá-los para o destino de armazenamento de Blob.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

Talvez o `/SyncCopy` gere um custo de saída adicional comparando à cópia assíncrona. A abordagem recomendada é usar essa opção na VM do Azure que está na mesma região que a sua conta de armazenamento de origem, a fim de evitar o custo de saída.

## <a name="download-files-from-file-storage"></a>Baixar arquivos do Armazenamento de arquivos

Vamos examinar várias maneiras de baixar arquivos usando o AzCopy.

### <a name="download-a-single-file"></a>Baixar um arquivo individual

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Se a origem especificada for um Compartilhamento de arquivos do Azure, você deverá especificar o nome exato do arquivo, (*por exemplo*, `abc.txt`) para baixar um arquivo individual ou especificar a opção `/S` para baixar todos os arquivos do compartilhamento de maneira recursiva. A tentativa de especificar um padrão de arquivo e a opção `/S` simultaneamente resulta em um erro.

### <a name="download-all-files-in-a-directory"></a>Baixar todos os arquivos em um diretório

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Observe que as pastas vazias não são baixadas.

## <a name="upload-files-to-an-azure-file-share"></a>Carregar arquivos para um compartilhamento de Arquivo do Azure

Vamos examinar várias maneiras de carregar arquivos usando o AzCopy.

### <a name="upload-a-single-file"></a>Carregar um arquivo individual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Carregar todos os arquivos em uma pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Observe que as pastas vazias não são carregadas.

### <a name="upload-files-matching-a-specific-pattern"></a>Carregar arquivos que correspondem a um padrão específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copiar arquivos no Armazenamento de arquivos

Vamos examinar várias maneiras de copiar arquivos em um Compartilhamento de arquivos do Azure usando o AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copiar de um compartilhamento de arquivos para outro

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Quando você copia um arquivo entre compartilhamentos de arquivos, é executada uma operação [server-side copy](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copiar de um Compartilhamento de arquivos do Azure para o Armazenamento de blobs

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Quando você copia um arquivo do compartilhamento de arquivos no blob, é executada uma operação [server-side copy](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copiar um blob do Armazenamento de blobs para um Compartilhamento de arquivos do Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Quando você copia um arquivo de um blob para um compartilhamento de arquivos, uma operação [cópia do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é executada.

### <a name="synchronously-copy-files"></a>Copiar arquivos de forma síncrona

Você pode especificar a opção `/SyncCopy` para copiar dados do Armazenamento de Arquivos para o Armazenamento de Arquivos, do Armazenamento de Arquivos para o Armazenamento de Blobs e vice-versa de forma síncrona. O AzCopy faz isso baixando os dados de origem para a memória local e carregando-os novamente no destino. O custo de saída padrão se aplica.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Durante a cópia do Armazenamento de arquivos para o Armazenamento de blobs, o tipo de blob padrão é o blob de blocos; o usuário pode especificar a opção `/BlobType:page` para alterar o tipo de blob de destino.

Observe que `/SyncCopy` pode gerar custos de saída adicionais, comparado à cópia assíncrona. A abordagem recomendada é usar essa opção na VM do Azure que está na mesma região de sua conta de armazenamento de origem, para evitar o custo de saída.

## <a name="export-data-from-table-storage"></a>Exportar dados do Armazenamento de tabelas

Vamos dar uma olhada em como exportar dados do Armazenamento de tabelas do Azure usando o AzCopy.

### <a name="export-a-table"></a>Exportar uma tabela

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

O AzCopy grava um arquivo de manifesto na pasta de destino especificada. O arquivo do manifesto é usado pelo processo de importação para localizar os arquivos de dados necessários e executar a validação de dados. O arquivo de manifesto usa a seguinte convenção de nomenclatura por padrão:

    <account name>_<table name>_<timestamp>.manifest

O usuário também pode especificar a opção `/Manifest:<manifest file name>` para definir o nome do arquivo de manifesto.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Dividir uma exportação do Armazenamento de tabelas em vários arquivos

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

O AzCopy usa um *índice de volume* nos nomes dos arquivos de dados da divisão para diferenciar diversos arquivos. O índice do volume é composto em duas partes: um *índice de intervalo de chaves de partição* e um *índice de arquivos de divisão*. Os dois índices são de base zero.

O índice do intervalo de chaves de partição será 0 se o usuário não especificar a opção `/PKRS`.

Por exemplo, digamos que o AzCopy gere dois arquivos de dados depois que o usuário especifica a opção `/SplitSize`. Os nomes dos arquivos de dados resultantes podem ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observe que o menor valor possível para a opção `/SplitSize` é 32 MB. Se o destino especificado for um armazenamento de Blobs, o AzCopy dividirá o arquivo de dados quando alcançar o tamanho limite do blob (200 GB), sem levar em conta se o usuário especificou ou não a opção `/SplitSize` .

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportar uma tabela para o formato de arquivo de dados JSON ou CSV

Por padrão, o AzCopy exporta tabelas para arquivos de dados JSON. Você pode especificar a opção `/PayloadFormat:JSON|CSV` para exportar as tabelas como JSON ou CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Ao especificar o formato de carga CSV, o AzCopy também gera um arquivo de esquema com extensão `.schema.csv` para cada arquivo de dados.

### <a name="export-table-entities-concurrently"></a>Exportar entidades de tabela simultaneamente

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

O AzCopy inicia operações simultâneas para exportar entidades quando o usuário especifica a opção `/PKRS`. Cada operação exporta um intervalor de chaves de partição.

Observe que a opção `/NC`também controla a quantidade de operações simultâneas. O AzCopy usa a quantidade de processadores de núcleo como valor padrão de `/NC` ao copiar entidades de tabela, mesmo que `/NC` não tenha sido especificado. Quando o usuário especifica a opção `/PKRS`, o AzCopy usa o menor valor dos dois valores (intervalos de chaves de partição versus operações simultâneas especificadas implícita ou explicitamente) para determinar quantas operações simultâneas devem ser iniciadas. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

### <a name="export-a-table-to-blob-storage"></a>Exportar uma tabela para o Armazenamento de blobs

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

O AzCopy gerará um arquivo de dados JSON no contêiner do blob, seguindo esta convenção de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O arquivo de dados JSON gerado segue o formato de carga para metadados mínimos. Para obter detalhes sobre esse formado de carga, confira [Formato de carga para operações do serviço Tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Observe que ao exportar tabelas para blobs, o AzCopy baixará as entidades de tabela para arquivos de dados temporários locais e, em seguida, carregará essas entidades no blob. Esses arquivos de dados temporários são colocados na pasta de arquivos do diário com o caminho padrão "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>" e você pode especificar a opção /Z:[pasta-de-arquivo-do-diário] para alterar o local da pasta de arquivo do diário e, portanto, alterar o local dos arquivos de dados temporários. O tamanho dos arquivos de dados temporários é decidido pelo tamanho das entidades da tabela e pelo tamanho especificado com a opção /SplitSize, embora o arquivo de dados temporários no disco local seja excluído imediatamente após ser carregado no blob, verifique que você tem espaço em disco local suficiente para armazenar esses arquivos de dados temporários antes de serem excluídos.

## <a name="import-data-into-table-storage"></a>Importar dados para o Armazenamento de tabelas

Vamos dar uma olhada em como importar dados para o Armazenamento de tabelas do Azure usando o AzCopy.

### <a name="import-a-table"></a>Importar uma tabela

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

A opção `/EntityOperation` indica como inserir entidades na tabela. Os valores possíveis são:

* `InsertOrSkip`: ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
* `InsertOrMerge`: mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
* `InsertOrReplace`: substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.

Observe que não é possível especificar a opção `/PKRS` no cenário de importação. Diferente do cenário de exportação, no qual é necessário especificar a opção `/PKRS` para iniciar operações simultâneas, o AzCopy inicia as operações simultâneas por padrão quando você importar a tabela. A quantidade padrão de operações simultâneas iniciadas é igual à quantidade de processadores de núcleo. No entanto, você pode especificar uma quantidade diferente com a opção `/NC`. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

Observe que o AzCopy dá suporte apenas à importação para JSON, não para CSV. O AzCopy não dá suporte a importações de tabela de arquivos JSON e de manifesto criados pelo usuário. Ambos os arquivos devem vir de uma exportação de tabela AzCopy. Para evitar erros, não modifique o arquivo JSON ou de manifesto exportado.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importar entidades para uma tabela do Armazenamento de blobs

Vamos supor que um contêiner de Blob contenha o seguinte: um arquivo JSON que representa uma Tabela do Azure, e seu arquivo de manifesto.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Você pode executar o comando a seguir para importar entidades em uma tabela usando o arquivo de manifesto nesse mesmo contêiner de blob:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Outros recursos do AzCopy

Vamos dar uma olhada em alguns outros recursos do AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiar apenas os dados que não existem no destino

Os parâmetros `/XO` e `/XN` permitem a exclusão de recursos de origem mais antigos ou mais recentes da cópia, respectivamente. Se você quiser copiar apenas os recursos de origem que não existem no destino, especifique os dois parâmetros no comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Observe que isso não terá suporte quando a origem ou o destino for uma tabela.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Usar um arquivo de resposta para especificar parâmetros de linha de comando

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

É possível incluir qualquer parâmetro de linha de comando do AZCopy em um arquivo de resposta. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando, realizando uma substituição direta pelo conteúdo do arquivo.

Vamos supor que um arquivo de resposta chamado `copyoperation.txt`contenha as linhas a seguir. Cada parâmetro do AzCopy pode ser especificado em uma única linha

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou, em linhas separadas:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

O AzCopy falhará se você dividir o parâmetro em duas linhas, conforme mostrado aqui para o parâmetro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Usar vários arquivos de resposta para especificar parâmetros de linha de comando

Vamos supor um arquivo de resposta chamado `source.txt` que especifique um contêiner de origem:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um arquivo de resposta chamado `dest.txt` que especifique uma pasta de destino no sistema de arquivos:

    /Dest:C:\myfolder

E um arquivo de resposta chamado `options.txt` que especifique opções para o AzCopy:

    /S /Y

Para chamar o AzCopy usando esses arquivos de resposta, todos eles residindo em um diretório `C:\responsefiles`, use este comando:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Especificar uma SAS

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Você também pode especificar um SAS no URI do contêiner:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Pasta de arquivo de diário

Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.

Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual.

Se você quiser usar o local padrão para o arquivo de diário:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Se você omitir a opção `/Z` ou especificar a opção `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy criará o arquivo de diário no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, o AzCopy retomará a operação com base no arquivo de diário.

Se você quiser especificar um local personalizado para o arquivo de diário:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Este exemplo criará o arquivo de diário se ele ainda não existir. Se ele existir, o AzCopy retomará a operação com base no arquivo de diário.

Se você quiser retomar uma operação do AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Este exemplo retoma a última operação, cuja conclusão pode ter falhado.

### <a name="generate-a-log-file"></a>Gerar um arquivo de log

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Se você especificar a opção `/V` sem fornecer um caminho de arquivo para o log detalhado, o AzCopy criará o arquivo de log no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Caso contrário, você pode criar um arquivo de log em um local personalizado:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Observe que, se você especificar um caminho relativo depois da opção `/V`, como `/V:test/azcopy1.log`, o log detalhado será criado no diretório de trabalho atual dentro de uma subpasta chamada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificar o número de operações simultâneas para começar

A opção `/NC` especifica o número de operações de cópia simultâneas. Por padrão, o AzCopy inicia uma determinada quantidade de operações simultâneas para aumentar a taxa de transferência de dados. Para operações de tabela, o número de operações simultâneas é igual ao número de processadores que você tem. Para operações de blob e arquivo, o número de operações simultâneas é igual a oito vezes o número de processadores que você tem. Se estiver executando o AzCopy em uma rede de baixa largura de banda, você poderá especificar um número menor para essa /NC a fim de evitar uma falha causada pela concorrência de recursos.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Executar o AzCopy no Emulador de Armazenamento do Azure

Você pode executar o AzCopy no [Emulador de Armazenamento do Azure](storage-use-emulator.md) para blobs:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Também execute-o em tabelas:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>Parâmetros do AzCopy

Veja abaixo uma descrição dos parâmetros do AzCopy. Também é possível digitar um dos comandos a seguir na linha de comando para obter ajuda no uso do AzCopy:

* Para obter ajuda detalhada da linha de comando para o AzCopy: `AzCopy /?`
* Para obter ajuda detalhada para qualquer parâmetro do AzCopy: `AzCopy /?:SourceKey`
* Para obter exemplos da linha de comando: `AzCopy /?:Samples`

### <a name="sourcesource"></a>/Source:"origem"

Especifica os dados de origem para cópia. A origem pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="destdestination"></a>/Dest: "destino"

Especifica o destino da cópia. O destino pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="patternfile-pattern"></a>/Pattern:"padrão de arquivo"

Especifica um padrão de arquivo que indica quais arquivos devem ser copiados. O comportamento do parâmetro /Pattern é determinado pelo local dos dados de origem e pela presença da opção do modo recursivo. O modo recursivo é especificado pela opção /S.

Se a origem especificada for um diretório no sistema de arquivos, os curingas padrão estão em vigor, e o padrão de arquivo fornecido é comparado com os arquivos dentro do diretório. Se a opção /S for especificada, o AzCopy também compara o padrão especificado com todos os arquivos em todas as subpastas do diretório.

Se a origem especificada for um contêiner de blob ou um diretório virtual, os curingas não são aplicados. Se a opção /S for especificada, o AzCopy interpreta o padrão do arquivo especificado como um prefixo de blob. Se a opção /S não for especificada, o AzCopy compara o padrão do arquivo com os nomes de blob exatos.

Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome exato do arquivo (por exemplo, abc.txt) para copiar um único arquivo ou especificar a opção /S para copiar todos os arquivos recursivamente no compartilhamento. A tentativa de especificar um padrão de arquivo e a opção /S simultaneamente resulta em um erro.

O AzCopy diferencia maiúsculas de minúsculas quando /Source é um contêiner de blob ou diretório virtual de blob, e não diferencia maiúsculas de minúsculas em todos os outros casos.

O padrão de arquivo usado quando nenhum padrão de arquivo é especificado é *.* para uma localização do sistema de arquivos, ou um prefixo vazio para uma localização de armazenamento do Azure. Não é possível especificar diversos padrões para os arquivos.

**Aplicável a:** Blobs, Arquivos

### <a name="destkeystorage-key"></a>/DestKey:"chave de armazenamento"

Especifica a chave de conta de armazenamento do recurso de destino.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Especifica uma SAS (Assinatura de acesso compartilhado) com as permissões de LEITURA e GRAVAÇÃO para o destino (se for aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.

Se o recurso de destino for um contêiner de blob, compartilhamento de arquivo ou tabela, você poderá especificar essa opção seguida pelo token da SAS, ou a SAS como parte do URI do contêiner de blob, compartilhamento de arquivo ou tabela de destino, sem essa opção.

Se a origem e o destino forem blobs, ambos devem residir na mesma conta de armazenamento.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="sourcekeystorage-key"></a>/Sourcekey: "chave de armazenamento"

Especifica a chave de conta de armazenamento para o recurso de origem.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Especifica uma Assinatura de acesso compartilhado com as permissões de LEITURA e LISTAGEM para a origem (se for aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.

Se o recurso da fonte for um contêiner de blob, e nenhuma chave ou uma SAS for fornecida, o contêiner será lido por meio de acesso anônimo.

Se a origem for um compartilhamento de arquivo ou uma tabela, será necessário fornecer uma chave ou SAS.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="s"></a>/S

Especifica o modo recursivo para operações de cópia. No modo recursivo, o AzCopy copia todos os blobs ou arquivos correspondentes ao padrão do arquivo, inclusive os presentes nas subpastas.

**Aplicável a:** Blobs, Arquivos

### <a name="blobtypeblock--page--append"></a>/BlobType:"bloco" | "página" | "anexo"

Especifica se o blob de destino é um blob de blocos, um blob de páginas ou um blob anexo. Essa opção só será possível quando você estiver carregando um blob. Caso contrário, um erro é gerado. Se o destino é um blob e essa opção não está especificada, por padrão, o AzCopy cria um blob de blocos.

**Aplicável a:** Blobs

### <a name="checkmd5"></a>/CheckMD5

Calcula um hash MD5 para dados baixados e certifica que o hash MD5 armazenado no blob, ou a propriedade Content-MD5 do arquivo, corresponde ao hash calculado. Como, por padrão, a verificação MD5 permanece desativada, você deve especificar essa opção para realizar a verificação MD5 ao baixar os dados.

O Azure Storage não assegura que o hash MD5 armazenado para o blob ou o arquivo esteja atualizado. É de responsabilidade do cliente atualizar o MD5 sempre que o blob ou o arquivo é modificado.

O AzCopy sempre define a propriedade Content-MD5 para um blob ou um arquivo do Azure depois de carregá-lo no serviço.  

**Aplicável a:** Blobs, Arquivos

### <a name="snapshot"></a>/Snapshot

Indica se é necessário transferir ou não os instantâneos. Essa opção só é válida quando a origem é um blob.

Os instantâneos de blob transferidos são renomeados neste formato: nome-do-blob (hora-do-instantâneo).extensão

Por padrão, os instantâneos não são copiados.

**Aplicável a:** Blobs

### <a name="vverbose-log-file"></a>/V: [arquivo de log detalhado]

Produz mensagens de status detalhadas em um arquivo de log.

Por padrão, o arquivo de log detalhado é chamado de AzCopyVerbose.log no `%LocalAppData%\Microsoft\Azure\AzCopy`. Se você especificar um local de arquivo existente para essa opção, o log detalhado será acrescentado a esse arquivo.  

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Especifica uma pasta de arquivo de diário para retomar uma operação.

O AzCopy sempre dará suporte à retomada caso uma operação tenha sido interrompida.

Se essa opção não for especificada ou for especificada sem um caminho de pasta, o AzCopy criará o arquivo de diário no local padrão, que é %LocalAppData%\Microsoft\Azure\AzCopy.

Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.

Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual.

O arquivo de diário é excluído mediante a conclusão bem-sucedida da operação.

A retomada de uma operação de um arquivo de diário criado por uma versão anterior do AzCopy não é compatível.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="parameter-file"></a>/@: “arquivo de parâmetro”

Especifica um arquivo que contém parâmetros. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando.

Em um arquivo de resposta, é possível especificar vários parâmetros em um único arquivo ou especificar cada parâmetro na própria linha. Um parâmetro individual não pode abranger várias linhas.

Os arquivos de resposta podem incluir linhas de comentários iniciadas pelo símbolo #.

É possível especificar vários arquivos de resposta. No entanto, o AzCopy não permite arquivos de resposta aninhados.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="y"></a>/Y

Suprime todas as solicitações de confirmação do AzCopy.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="l"></a>/L

Especifica uma operação de listagem apenas. Nenhum dado é copiado.

O AzCopy interpreta o uso dessa opção como uma simulação para execução da linha de comando sem essa opção /L, e conta quantos objetos são copiados. Você pode especificar a opção /V ao mesmo tempo para verificar quais objetos são copiados no log detalhado.

O comportamento dessa opção também é determinado pelo local dos dados de origem e pela presença da opção no modo recursivo /S e pela opção de padrão de arquivo /Pattern.

O AzCopy exige a permissão de LISTAGEM e de LEITURA deste local de origem ao usar essa opção.

**Aplicável a:** Blobs, Arquivos

### <a name="mt"></a>/MT

Define a hora da última modificação do arquivo baixado como a mesma do blob de origem ou do arquivo.

**Aplicável a:** Blobs, Arquivos

### <a name="xn"></a>/XN

Exclui um recurso de origem mais novo. O recurso não é copiado se a hora da última modificação na origem for igual ou mais recente do que o destino.

**Aplicável a:** Blobs, Arquivos

### <a name="xo"></a>/XO
Exclui um recurso de origem mais antigo. O recurso não é copiado se a hora da última modificação na origem for igual ou mais recente do que o destino.

**Aplicável a:** Blobs, Arquivos

### <a name="a"></a>/A

Carrega apenas arquivos que tenham o atributo Archive definido.

**Aplicável a:** Blobs, Arquivos

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Carrega apenas arquivos que tenham algum dos atributos especificados definido.

Entre os atributos disponíveis estão:

* R = Arquivos somente leitura
* A = Arquivos prontos para arquivamento
* S = Arquivos de sistema
* H = Arquivos ocultos
* C = Arquivo compactado
* N = Arquivos normais
* E = Arquivos criptografados
* T = Arquivos temporários
* O = Arquivos offline
* I = Arquivos não indexados

**Aplicável a:** Blobs, Arquivos

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Exclui arquivos que tenham qualquer dos atributos especificados definido.

Entre os atributos disponíveis estão:

* R = Arquivos somente leitura
* A = Arquivos prontos para arquivamento
* S = Arquivos de sistema
* H = Arquivos ocultos
* C = Arquivo compactado
* N = Arquivos normais
* E = Arquivos criptografados
* T = Arquivos temporários
* O = Arquivos offline
* I = Arquivos não indexados

**Aplicável a:** Blobs, Arquivos

### <a name="delimiterdelimiter"></a>/Delimiter: "delimitador"

Indica o caractere delimitador usado para delimitar diretórios virtuais em um nome de blob.

Por padrão, o AzCopy usa / como o caractere delimitador. No entanto, o AzCopy dá suporte ao uso de qualquer caractere comum (como @, # ou %) como delimitador. Se precisar incluir um desses caracteres especiais na linha de comando, coloque o nome do arquivo entre aspas duplas.

Essa opção só é aplicável para o download de blobs.

**Aplicável a:** Blobs

### <a name="ncnumber-of-concurrent-operations"></a>/NC: "número-de-operações-simultâneas"

Especifica o número de operações simultâneas.

Por padrão, o AzCopy inicia uma determinada quantidade de operações simultâneas para aumentar a taxa de transferência dos dados. O grande número de operações simultâneas em um ambiente com baixa largura de banda pode sobrecarregar a conexão de rede, evitando que as operações sejam concluídas. Limite operações simultâneas com base na largura de banda real de rede disponível.

O limite máximo de operações simultâneas é 512.

**Aplicável a:** Blobs, Arquivos, Tabelas

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Especifica se o recurso `source` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.

**Aplicável a:** Blobs, Tabelas

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Especifica se o recurso `destination` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.

**Aplicável a:** Blobs, Tabelas

### <a name="pkrskey1key2key3"></a>/PKRS: "chave1#chave2#chave3#..."

Divide o intervalo de chaves de partição para possibilitar a exportação paralela dos dados, o que aumenta a velocidade dessa operação.

Se essa opção não for especificada, o AzCopy usa um único thread para exportar entidades de tabela. Por exemplo, se o usuário especifica /PKRS:"aa#bb", o AzCopy inicia três operações simultâneas.

Cada operação exporta um dos três intervalos de chaves de partição, como mostramos abaixo:

  [first-partition-key, aa)

  [aa, bb)

  [bb, last-partition-key]

**Aplicável a:** Tabelas

### <a name="splitsizefile-size"></a>/Splitsize: "tamanho do arquivo"

Especifica o tamanho da divisão do arquivo exportado em MB, o valor mínimo permitido é de 32.

Se essa opção não for especificada, o AzCopy exportará os dados da tabela para um único arquivo.

Se os dados da tabela forem exportados para um blob e o tamanho do arquivo exportado alcançar o limite de 200 GB, o AzCopy divide o arquivo exportado, mesmo que essa opção não seja especificada.

**Aplicável a:** Tabelas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica o comportamento da importação dos dados da tabela.

* InsertOrSkip — Ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
* InsertOrMerge — Mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
* InsertOrReplace — Substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.

**Aplicável a:** Tabelas

### <a name="manifestmanifest-file"></a>/Manifesto: "arquivo de manifesto"

Especifica o arquivo de manifesto para a operação de exportação e importação de tabela.

Essa opção é opcional durante a operação de exportação. O AzCopy gera um arquivo de manifesto com nome predefinido se essa opção não for especificada.

Essa opção é exigida durante a operação de importação para localização dos arquivos de dados.

**Aplicável a:** Tabelas

### <a name="synccopy"></a>/SyncCopy

Indica se devem ser copiados de forma síncrona blobs ou arquivos entre dois pontos de extremidade de armazenamento do Azure.

O AzCopy por padrão usa cópia assíncrona no servidor. Especifique essa opção para executar uma cópia síncrona, que baixa blobs ou arquivos para a memória local e, em seguida, carrega-as para o armazenamento do Azure.

Você pode usar essa opção ao copiar arquivos no armazenamento de Blob no armazenamento de arquivo ou do armazenamento de Blob para armazenamento de arquivos ou vice-versa.

**Aplicável a:** Blobs, Arquivos

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Especifica o tipo de conteúdo MIME para blobs ou arquivos de destino.

O AzCopy define o tipo de conteúdo para um blob ou arquivo application/octet-stream por padrão. Você pode definir o tipo de conteúdo para todos os blobs ou arquivos explicitamente especificando um valor para essa opção.

Se você especificar essa opção sem um valor, AzCopy definirá cada blob ou tipo de conteúdo do arquivo de acordo com a sua extensão de arquivo.

**Aplicável a:** Blobs, Arquivos

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Especifica o formato do arquivo de dados exportados da tabela.

Se essa opção não for especificada, por padrão, o AzCopy exportará o arquivo de dados da tabela no formato JSON.

**Aplicável a:** Tabelas

## <a name="known-issues-and-best-practices"></a>Problemas Conhecidos e Práticas Recomendadas

Vamos dar uma olhada em alguns dos problemas conhecidos e as melhores práticas.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite gravações simultâneas durante a cópia de dados

Ao copiar blobs ou arquivos usando o AZCopy, lembre-se de que outro aplicativo pode estar modificando os dados enquanto você os copia. Se possível, verifique se os dados que está copiando não estão sendo modificados durante a cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, verifique se nenhum outro aplicativo está gravando no VHD, no momento. Uma boa maneira de fazer isso é ceder o recurso para ser copiado. Também é possível criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não for possível evitar que outros aplicativos gravem em blobs ou arquivos enquanto são copiados, lembre-se que, quando o trabalho terminar, os recursos copiados não poderão mais ter paridade total com os recursos de origem.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Execute uma instância de AzCopy em um computador.

O AzCopy foi projetado para maximizar a utilização de seu recurso de máquina para acelerar a transferência de dados, é recomendável executar apenas uma instância de AzCopy em um único computador e especifique a opção `/NC` se precisar de mais operações em simultâneo. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Habilite algoritmos MD5 em conformidade com FIPS no AzCopy quando “Usar algoritmos em conformidade com FIPS para criptografia, hash e assinatura”.

Por padrão, o AzCopy usa a implementação MD5 do .NET para calcular o MD5 ao copiar objetos, mas há alguns requisitos de segurança que precisam que o AzCopy habilite a configuração MD5 em conformidade com FIPS.

Você pode criar um arquivo app.config `AzCopy.exe.config` com a propriedade `AzureStorageUseV1MD5` e reservá-lo com o AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Para a propriedade “AzureStorageUseV1MD5”:

* True – o valor padrão; o AzCopy usa a implementação MD5 do .NET.
* False – o AzCopy usa o algoritmo MD5 em conformidade com FIPS.

Os algoritmos em conformidade com FIPS são desabilitados por padrão no Windows. Altere essa configuração de política no computador. Na janela Executar (Windows+R), digite secpol.msc para abrir a janela **Política de Segurança Local**. Na janela **Configurações de Segurança**, navegue para **Configurações de Segurança** > **Políticas Locais** > **Opções de Segurança**. Localize a política **Criptografia do Sistema: Usar algoritmos em conformidade com FIPS para criptografia, hash e assinatura**. Clique duas vezes na política para ver o valor exibido na coluna **Configuração de Segurança**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, consulte os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do Armazenamento do Azure:
* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Como usar o Armazenamento de blob do .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Como usar o Armazenamento de Arquivos no .NET](../storage-dotnet-how-to-use-files.md)
* [Como usar o Armazenamento de Tabela do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account.md)
* [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Postagens de blog de armazenamento do Azure:
* [Apresentando a versão de visualização da biblioteca de movimentação de dados do armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducing synchronous copy and customized content type (AzCopy: introdução a cópia síncrona e tipo de conteúdo personalizado)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy: anúncio de disponibilidade geral do AzCopy 3.0 mais versão de teste do AzCopy 4.0 com suporte para Tabela e Arquivo)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimized for Large-Scale Copy Scenarios (AzCopy: otimizado para cenários de cópia em larga escala)](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Support for read-access geo-redundant storage (AzCopy: suporte para o armazenamento com redundância geográfica com acesso de leitura)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferir dados com o modo reiniciável e um token SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Using cross-account Copy Blob (AzCopy: usando blob de cópia em várias contas)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Uploading/downloading files for Azure Blobs (AzCopy: Upload/download de arquivos para Blobs do Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
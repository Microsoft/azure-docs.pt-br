---
title: Transferir dados de ou para arquivos do Azure usando AzCopy v10 | Microsoft Docs
description: Transferir dados com o AzCopy e o armazenamento de arquivos. AzCopy é uma ferramenta de linha de comando para copiar BLOBs ou arquivos de ou para uma conta de armazenamento. Use o AzCopy com os arquivos do Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 7bca683ea075710aa6fc677fcf457ceb2e0e5098
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881004"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com o AzCopy e o Armazenamento de Arquivos 

AzCopy é um utilitário de linha de comando que você pode usar para copiar arquivos de ou para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com os arquivos do Azure.

Antes de começar, consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e se familiarizar com a ferramenta.

> [!TIP]
> Os exemplos neste artigo incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

## <a name="create-file-shares"></a>Criar compartilhamentos de arquivo

Você pode usar o comando [azcopy Make](storage-ref-azcopy-make.md) para criar um compartilhamento de arquivos. O exemplo nesta seção cria um compartilhamento de arquivos chamado `myfileshare` .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obter documentos de referência detalhados, consulte [Make azcopy](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Carregar um diretório
> * Carregar o conteúdo de um diretório
> * Carregar um arquivo específico

> [!TIP]
> Você pode ajustar sua operação de carregamento usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copie listas de controle de acesso (ACLs) junto com os arquivos.|**--preserve-SMB-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB junto com os arquivos.|**--preserve-SMB-info** = \[ verdadeiro \| falso\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy não calcula e armazena automaticamente o código hash MD5 do arquivo. Se você quiser que o AzCopy faça isso, anexe o `--put-md5` sinalizador a cada comando de cópia. Dessa forma, quando o arquivo for baixado, AzCopy calculará um hash MD5 para dados baixados e verificará se o hash MD5 armazenado na Propriedade do arquivo `Content-md5` corresponde ao hash calculado.

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar no caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'` , ou `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Carregar um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um compartilhamento de arquivos. O resultado é um diretório no compartilhamento de arquivos com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar para um diretório dentro do compartilhamento de arquivos, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se você especificar o nome de um diretório que não existe no compartilhamento de arquivos, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregar o conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode carregar arquivos específicos usando nomes de arquivo completos, nomes parciais com caracteres curinga (*) ou usando datas e horas.

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de arquivo individuais usando um ponto-e-vírgula ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Neste exemplo, AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` arquivo. Você precisa incluir a `--recursive` opção para transferir todos os arquivos no `C:\myDirectory\photos` diretório.

Você também pode excluir arquivos usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um ponto-e-vírgula ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` `--exclude-pattern` Opções e aplicam-se somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Carregar arquivos que foram modificados após uma data e hora 

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-after` opção. Especifique uma data e hora no formato ISO 8601 (por exemplo: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Para referência detalhada, consulte os documentos de referência de [cópia do azcopy](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para baixar arquivos, diretórios e compartilhamentos de arquivos em seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Baixar um arquivo
> * Baixar um diretório
> * Baixar o conteúdo de um diretório
> * Baixar arquivos específicos

> [!TIP]
> Você pode ajustar sua operação de download usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copie listas de controle de acesso (ACLs) junto com os arquivos.|**--preserve-SMB-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB junto com os arquivos.|**--preserve-SMB-info** = \[ verdadeiro \| falso\]|
> |Descompacte arquivos automaticamente.|**--descompactar**|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um arquivo contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado na Propriedade do arquivo `Content-md5` corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você substitua esse comportamento acrescentando `--check-md5=NoCheck` ou `--check-md5=LogOnly` ao comando de cópia.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myFileShareDirectory` que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixar o conteúdo de um diretório

Você pode fazer download do conteúdo de um diretório sem copiar o próprio diretório usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

### <a name="download-specific-files"></a>Baixar arquivos específicos

Você pode baixar arquivos específicos usando nomes de arquivo completos, nomes parciais com caracteres curinga (*) ou usando datas e horas.

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de arquivo individuais usando um ponto-e-vírgula ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, AzCopy transfere o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` diretório e o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` arquivo. Inclua a `--recursive` opção para transferir todos os arquivos no `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` diretório.

Você também pode excluir arquivos usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um ponto-e-vírgula ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` `--exclude-pattern` Opções e aplicam-se somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Baixar arquivos que foram modificados após uma data e hora 

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-after` opção. Especifique uma data e hora no formato ISO-8601 (por exemplo: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Para referência detalhada, consulte os documentos de referência de [cópia do azcopy](storage-ref-azcopy-copy.md) .

#### <a name="download-from-a-share-snapshot"></a>Baixar de um instantâneo de compartilhamento

Você pode baixar uma versão específica de um arquivo ou diretório referenciando o valor **DateTime** de um instantâneo de compartilhamento. Para saber mais sobre instantâneos de compartilhamento, confira [visão geral de instantâneos de compartilhamento para arquivos do Azure](../files/storage-snapshots-files.md). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **Exemplo** (baixar um arquivo) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (baixar um diretório) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>Copiar arquivos entre as contas de armazenamento

Você pode usar o AzCopy para copiar arquivos para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

O AzCopy usa [APIs](/rest/api/storageservices/put-page-from-url)de [servidor para servidor](/rest/api/storageservices/put-block-from-url) , portanto, os dados são copiados diretamente entre os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador. Você pode aumentar a taxa de transferência dessas operações definindo o valor da `AZCOPY_CONCURRENCY_VALUE` variável de ambiente. Para saber mais, consulte [otimizar a taxa de transferência](storage-use-azcopy-configure.md#optimize-throughput).

Você também pode copiar versões específicas de um arquivo referenciando o valor **DateTime** de um instantâneo de compartilhamento. Para saber mais sobre instantâneos de compartilhamento, confira [visão geral de instantâneos de compartilhamento para arquivos do Azure](../files/storage-snapshots-files.md). 

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copiar um arquivo para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um compartilhamento de arquivos para outra conta de armazenamento
> * Copiar todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

> [!TIP]
> Você pode ajustar sua operação de cópia usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copie listas de controle de acesso (ACLs) junto com os arquivos.|**--preserve-SMB-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB junto com os arquivos.|**--preserve-SMB-info** = \[ verdadeiro \| falso\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Copiar um arquivo para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **Exemplo** (instantâneo de compartilhamento) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exemplo** (instantâneo de compartilhamento) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>Copiar um compartilhamento de arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exemplo** (instantâneo de compartilhamento) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copiar todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exemplo** (instantâneo de compartilhamento) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um compartilhamento de arquivos com outro compartilhamento de arquivos. Você também pode sincronizar o conteúdo de um diretório em um compartilhamento de arquivos com o conteúdo de um diretório localizado em outro compartilhamento de arquivos. A sincronização é de uma maneira. Em outras palavras, você escolhe quais desses dois pontos de extremidade são a origem e qual deles é o destino. A sincronização também usa APIs de servidor para servidor.

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico. A versão atual do AzCopy não é sincronizada entre os arquivos do Azure e o armazenamento de BLOBs.

O comando [Sync](storage-ref-azcopy-sync.md) compara os nomes de arquivo e os últimos carimbos de data/hora. Defina o `--delete-destination` sinalizador opcional como um valor de `true` ou `prompt` para excluir arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você definir o `--delete-destination` sinalizador como `true` , AzCopy excluirá os arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes de AzCopy excluir um arquivo, defina o `--delete-destination` sinalizador como `prompt` .

> [!TIP]
> Você pode ajustar a operação de sincronização usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copie listas de controle de acesso (ACLs) junto com os arquivos.|**--preserve-SMB-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB junto com os arquivos.|**--preserve-SMB-info** = \[ verdadeiro \| falso\]|
> |Excluir arquivos com base em um padrão.|**--Exclude-caminho**|
> |Especifique o quão detalhado você deseja que suas entradas de log relacionadas à sincronização sejam.|**--nível** = \[ de log informações de erro de aviso \| \| \| nenhuma\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Atualizar um compartilhamento de arquivos com alterações em outro compartilhamento de arquivos

O primeiro compartilhamento de arquivos que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações em um diretório em outro compartilhamento de arquivos

O primeiro diretório que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Atualizar um compartilhamento de arquivos para corresponder ao conteúdo de um instantâneo de compartilhamento

O primeiro compartilhamento de arquivos que aparece nesse comando é a origem. No final do URI, acrescente a cadeia de caracteres `&sharesnapshot=` seguida pelo valor **DateTime** do instantâneo. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Para saber mais sobre instantâneos de compartilhamento, consulte [visão geral de instantâneos de compartilhamento para arquivos do Azure](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados](storage-use-azcopy-v10.md#transfer-data)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
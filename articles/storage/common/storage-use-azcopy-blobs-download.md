---
title: Baixar BLOBs do armazenamento de BLOBs do Azure usando AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam você a baixar BLOBs do armazenamento de BLOBs do Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 20da05399eed4cb9c5a4b69a82b0b1e799997751
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880111"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Baixar BLOBs do armazenamento de BLOBs do Azure usando AzCopy v10

Você pode baixar BLOBs e diretórios do armazenamento de BLOBs usando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos de outros tipos de tarefas, como carregar arquivos, sincronizar com o armazenamento de BLOBs ou copiar BLOBs entre contas, consulte os links apresentados na seção [próximas etapas](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE] 
> Os exemplos neste artigo pressupõem que você forneceu credenciais de autorização usando Azure Active Directory (Azure AD).
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Baixar um blob

Baixe um BLOB usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um blob contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado na Propriedade do blob `Content-md5` corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você substitua esse comportamento acrescentando `--check-md5=NoCheck` ou `--check-md5=LogOnly` ao comando de cópia.

## <a name="download-a-directory"></a>Baixar um diretório

Baixe um diretório usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myBlobDirectory` que contém todos os BLOBs baixados.

## <a name="download-directory-contents"></a>Baixar conteúdo do diretório

Você pode fazer download do conteúdo de um diretório sem copiar o próprio diretório usando o símbolo curinga (*).

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Acrescente o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

## <a name="download-specific-blobs"></a>Baixar BLOBs específicos

Você pode baixar BLOBs específicos usando nomes de arquivo completos, nomes parciais com caracteres curinga (*) ou usando datas e horas. 

> [!TIP]
> Esses exemplos incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

#### <a name="specify-multiple-complete-blob-names"></a>Especificar vários nomes completos de BLOB

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de BLOBs individuais usando um semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, AzCopy transfere o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório e o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` arquivo. Inclua a `--recursive` opção para transferir todos os BLOBs no `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório.

Você também pode excluir BLOBs usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir BLOBs usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` `--exclude-pattern` Opções e aplicam-se somente a nomes de BLOB e não ao caminho.  Se você quiser copiar todos os arquivos de texto (BLOBs) que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Baixar BLOBs que foram modificados antes ou depois de uma data e hora 

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-before` `--include-after` opção ou. Especifique uma data e hora no formato ISO-8601 (por exemplo: `2020-08-19T15:04:00Z` ). 

Os exemplos a seguir baixam arquivos que foram modificados na data especificada ou após ela.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Para referência detalhada, consulte os documentos de referência de [cópia do azcopy](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Baixar versões anteriores de um blob

Se você tiver habilitado o [controle de versão de blob](../blobs/versioning-enable.md), poderá baixar uma ou mais versões anteriores de um blob. 

Primeiro, crie um arquivo de texto que contenha uma lista de [IDs de versão](../blobs/versioning-overview.md). Cada ID de versão deve aparecer em uma linha separada. Por exemplo: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Em seguida, use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--list-of-versions` opção. Especifique o local do arquivo de texto que contém a lista de versões (por exemplo: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Baixar um instantâneo de blob

Você pode baixar um [instantâneo de blob](../blobs/snapshots-overview.md) referenciando o valor **DateTime** de um instantâneo de BLOB. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se você estiver usando um token SAS para autorizar o acesso a dados de BLOB, anexe o **DateTime** de instantâneo após o token SAS. Por exemplo: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Baixar com sinalizadores opcionais

Você pode ajustar sua operação de download usando sinalizadores opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Descompacte arquivos automaticamente.|**--descompactar**|
|Especifique o quão detalhado você deseja que suas entradas de log relacionadas à cópia sejam.|**--nível** = \[ de log informações de erro de aviso \| \| \| nenhuma\]|
|Especifique se e como substituir os arquivos conflitantes e os BLOBs no destino.|**--substituir** = \[ \|prompt true false \| ifSourceNewer \|\]|

Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: copiar entre a conta](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: usar buckets do Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: arquivos do Azure](storage-use-azcopy-files.md)
- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
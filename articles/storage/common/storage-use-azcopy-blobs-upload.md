---
title: Carregar arquivos no armazenamento de BLOBs do Azure usando AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam você a carregar arquivos no armazenamento de BLOBs do Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec88a3c740ceda7ccf352f8f32f94e2cd52d0988
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358751"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Carregar arquivos no armazenamento de BLOBs do Azure usando AzCopy v10

Você pode carregar arquivos e diretórios no armazenamento de BLOBs usando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos de outros tipos de tarefas, como baixar BLOBs, sincronizar com o armazenamento de BLOBs ou copiar BLOBs entre contas, consulte os links apresentados na seção [próximas etapas](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE] 
> Os exemplos neste artigo pressupõem que você forneceu credenciais de autorização usando Azure Active Directory (Azure AD).
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contêiner

Você pode usar o comando [azcopy Make](storage-ref-azcopy-make.md) para criar um contêiner.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exemplo** (namespace hierárquico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Para obter documentos de referência detalhados, consulte [Make azcopy](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Carregar um arquivo

Carregue um arquivo usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar no caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'` , ou `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Carregar um diretório

Carregue um diretório usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) . 

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um contêiner de BLOB. O resultado é um diretório no contêiner com o mesmo nome.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Para copiar para um diretório dentro do contêiner, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se você especificar o nome de um diretório que não existe no contêiner, o AzCopy criará um novo diretório com esse nome.

## <a name="upload-directory-contents"></a>Carregar conteúdo do diretório

Carregue o conteúdo de um diretório usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) . Use o símbolo curinga (*) para carregar o conteúdo sem copiar o diretório recipiente.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Acrescente o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

## <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode carregar arquivos específicos usando nomes de arquivo completos, nomes parciais com caracteres curinga (*) ou usando datas e horas.

> [!TIP]
> Esses exemplos incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de arquivo individuais usando um ponto-e-vírgula ( `;` ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` arquivo. Inclua a `--recursive` opção para transferir todos os arquivos no `C:\myDirectory\photos` diretório.

Você também pode excluir arquivos usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` `--exclude-pattern` Opções e aplicam-se somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Carregar arquivos que foram modificados antes ou depois de uma data e hora 

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-before` `--include-after` opção ou. Especifique uma data e hora no formato ISO-8601 (por exemplo: `2020-08-19T15:04:00Z` ). 

Os exemplos a seguir carregam arquivos que foram modificados na data especificada ou após ela.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Para referência detalhada, consulte os documentos de referência de [cópia do azcopy](storage-ref-azcopy-copy.md) .

## <a name="upload-with-index-tags"></a>Carregar com marcas de índice

Você pode carregar um arquivo e adicionar [marcas de índice de BLOB (versão prévia)](../blobs/storage-manage-find-blobs.md) ao blob de destino.  

Se você estiver usando a autorização do Azure AD, sua entidade de segurança deverá receber a função de [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou deverá receber a permissão para a operação do provedor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure. Se você estiver usando um token SAS (assinatura de acesso compartilhado), esse token deverá fornecer acesso às marcas do blob por meio da `t` permissão SAS.

Para adicionar marcas, use a `--blob-tags` opção junto com um par chave-valor codificado por URL. Por exemplo, para adicionar a chave `my tag` e um valor `my tag value` , você adicionaria `--blob-tags='my%20tag=my%20tag%20value'` ao parâmetro de destino. 

Separe várias marcas de índice usando um e comercial ( `&` ).  Por exemplo, se você quiser adicionar uma chave `my second tag` e um valor `my second tag value` , a cadeia de caracteres de opção completa será `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Os exemplos a seguir mostram como usar a `--blob-tags` opção.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Carregar um arquivo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Carregar um diretório** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Carregar conteúdo do diretório** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Se você especificar um diretório para a origem, todos os BLOBs copiados para o destino terão as mesmas marcas que você especificar no comando.

## <a name="upload-with-optional-flags"></a>Carregar com sinalizadores opcionais

Você pode ajustar sua operação de carregamento usando sinalizadores opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Carregue arquivos como blobs de acréscimo ou blobs de páginas.|**--tipo** = \[ de BLOB BlockBlob \| PageBlob \| AppendBlob\]|
|Carregue para uma camada de acesso específica (como a camada de arquivo).|**--bloco-blob-camada** = \[ Nenhum \| \| arquivo frio \| quente\]|

Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos nestes artigos:

- [Exemplos: Baixar](storage-use-azcopy-blobs-download.md)
- [Exemplos: copiar entre contas](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: usar buckets do Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: arquivos do Azure](storage-use-azcopy-files.md)
- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
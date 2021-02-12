---
title: Sincronizar com o armazenamento de BLOBs do Azure usando AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudarão a sincronizar com o armazenamento de BLOBs do Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f8210428e772241134b57ac4fccb5b1549e04483
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617265"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Sincronizar com o armazenamento de BLOBs do Azure usando AzCopy v10

Você pode sincronizar o armazenamento local com o armazenamento de BLOBs do Azure usando o utilitário de linha de comando AzCopy v10. 

Você pode sincronizar o conteúdo de um sistema de arquivos local com um contêiner de BLOB. Você também pode sincronizar contêineres e diretórios virtuais entre si. A sincronização é de uma maneira. Em outras palavras, você escolhe quais desses dois pontos de extremidade são a origem e qual deles é o destino. A sincronização também usa APIs de servidor para servidor. Os exemplos apresentados nesta seção também funcionam com contas que têm um namespace hierárquico. 

> [!NOTE]
> A versão atual do AzCopy não sincroniza entre outras origens e destinos (por exemplo: armazenamento de arquivos Amazon Web Services ou AWS) S3 buckets).

Para ver exemplos de outros tipos de tarefas, como carregar arquivos, baixar BLOBs ou copiar BLOBs entre contas, consulte os links apresentados na seção [próximas etapas](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE] 
> Os exemplos neste artigo pressupõem que você forneceu credenciais de autorização usando Azure Active Directory (Azure AD).
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` . ken> ' '.

## <a name="guidelines"></a>Diretrizes

- O comando [Sync](storage-ref-azcopy-sync.md) compara os nomes de arquivo e os últimos carimbos de data/hora. Defina o `--delete-destination` sinalizador opcional como um valor de `true` ou `prompt` para excluir arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

- Se você definir o `--delete-destination` sinalizador como `true` , AzCopy excluirá os arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes de AzCopy excluir um arquivo, defina o `--delete-destination` sinalizador como `prompt` .

- Para evitar exclusões acidentais, certifique-se de habilitar o recurso de [exclusão reversível](../blobs/soft-delete-blob-overview.md) antes de usar o `--delete-destination=prompt|true` sinalizador.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um contêiner com alterações em um sistema de arquivos local

Nesse caso, o contêiner é o destino e o sistema de arquivos local é a origem. 

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de arquivos local com alterações em um contêiner

Nesse caso, o sistema de arquivos local é o destino e o contêiner é a origem.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um contêiner com alterações em outro contêiner

O primeiro contêiner que aparece nesse comando é a origem. O segundo é o destino.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Atualizar um diretório com alterações em um diretório em outro contêiner

O primeiro diretório que aparece nesse comando é a origem. O segundo é o destino.

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Sincronizar com sinalizadores opcionais

Você pode ajustar a operação de sincronização usando sinalizadores opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Especifique como os hashes MD5 estritamente devem ser validados durante o download.|**--verificação-MD5** = \[ NOCHECK \| FailIfDifferent logon \| \| FailIfDifferentOrMissing\]|
|Excluir arquivos com base em um padrão.|**--Exclude-caminho**|
|Especifique o quão detalhado você deseja que suas entradas de log relacionadas à sincronização sejam.|**--nível** = \[ de log informações de erro de aviso \| \| \| nenhuma\]|

Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Baixar](storage-use-azcopy-blobs-download.md)
- [Exemplos: copiar entre contas](storage-use-azcopy-blobs-copy.md)
- [Exemplos: usar buckets do Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: arquivos do Azure](storage-use-azcopy-files.md)
- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
---
title: Transferir dados de ou para o armazenamento de BLOBs do Azure usando AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam a criar contêineres, copiar arquivos e sincronizar diretórios entre sistemas de arquivos locais e contêineres.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d1bb0dc0e5c9cca0a9570e9074a294afdeb99455
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501369"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferir dados com o armazenamento de BLOBs e AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre as contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento de BLOBs.

## <a name="get-started"></a>Introdução

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos neste artigo pressupõem que você autenticou sua identidade usando o `AzCopy login` comando. Em seguida, o AzCopy usa sua conta do Azure AD para autorizar o acesso aos dados no armazenamento de BLOBs.
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy.
>
> Por exemplo: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Criar um contêiner

Você pode usar o comando `make` AzCopy para criar um contêiner. Os exemplos nesta seção criam um contêiner chamado `mycontainer`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exemplo** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exemplo** (namespace hierárquico) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando `copy` AzCopy para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Carregar um diretório
> * Carregar arquivos usando caracteres curinga

> [!NOTE]
> AzCopy não calcula e armazena automaticamente o código hash MD5 do arquivo. Se você quiser que o AzCopy faça isso, anexe o `--put-md5` sinalizador a cada comando de cópia. Dessa forma, quando o blob é baixado, o AzCopy calcula um hash MD5 para dados baixados e verifica se o hash MD5 armazenado na Propriedade do `Content-md5` blob corresponde ao hash calculado.

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exemplo** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> O AzCopy, por padrão, carrega os dados em blobs de bloco. Para carregar arquivos como BLOBs de acréscimo ou BLOBs de página, use `--blob-type=[BlockBlob|PageBlob|AppendBlob]`o sinalizador.

### <a name="upload-a-directory"></a>Carregar um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um contêiner de BLOB. O resultado é um diretório no contêiner com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Para copiar para um diretório dentro do contêiner, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Se você especificar o nome de um diretório que não existe no contêiner, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregar o conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Exemplo** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando `copy` AzCopy para baixar BLOBs, diretórios e contêineres para seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Baixar um arquivo
> * Baixar um diretório
> * Baixar arquivos usando caracteres curinga

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um blob contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado `Content-md5` na Propriedade do blob corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você `--check-md5=NoCheck` substitua `--check-md5=LogOnly` esse comportamento acrescentando ou ao comando de cópia.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myBlobDirectory` que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixar o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

## <a name="copy-blobs-between-storage-accounts"></a>Copiar BLOBs entre contas de armazenamento

Você pode usar AzCopy para copiar BLOBs para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico. 

O AzCopy usa [APIs](https://docs.microsoft.com/en-us/rest/api/storageservices/put-page-from-url)de [servidor para servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , portanto, os dados são copiados diretamente entre os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copiar um blob para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um contêiner para outra conta de armazenamento
> * Copiar todos os contêineres, diretórios e arquivos para outra conta de armazenamento

> [!NOTE]
> Na versão atual, você precisa acrescentar um token SAS a cada URL de origem. Se você fornecer credenciais de autorização usando o Azure Active Directory (AD), poderá omitir o token SAS somente da URL de destino. 

### <a name="copy-a-blob-to-another-storage-account"></a>Copiar um blob para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copiar um contêiner para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Copiar todos os contêineres, diretórios e arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um sistema de arquivos local com um contêiner de BLOB. A sincronização é unidirecional. Em outras palavras, você escolhe quais desses dois pontos de extremidade são a origem e qual deles é o destino.

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico. A versão atual do AzCopy não é sincronizada entre outras origens e destinos (por exemplo: Armazenamento de arquivos ou buckets de Amazon Web Services (AWS) S3).

O `sync` comando compara os nomes de arquivo e os carimbos de data/hora da última modificação. Defina o `--delete-destination` sinalizador opcional como um valor de `true` ou `prompt` para excluir arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você definir o `--delete-destination` sinalizador como `true` AzCopy exclui arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes de AzCopy excluir um arquivo, defina `--delete-destination` o sinalizador `prompt`como.

> [!NOTE]
> Para evitar exclusões acidentais, certifique-se de habilitar o recurso de [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) antes de usar `--delete-destination=prompt|true` o sinalizador.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um contêiner com alterações em um sistema de arquivos local

Nesse caso, o contêiner é o destino e o sistema de arquivos local é a origem.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de arquivos local com alterações em um contêiner

Nesse caso, o sistema de arquivos local é o destino e o contêiner é a origem.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Exemplo** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: Migrar dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

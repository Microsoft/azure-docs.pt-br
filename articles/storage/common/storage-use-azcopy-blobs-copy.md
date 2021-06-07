---
title: Copiar BLOBs entre contas de armazenamento do Azure com AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam a copiar BLOBs entre contas de armazenamento.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617282"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Copiar BLOBs entre contas de armazenamento do Azure usando AzCopy v10

Você pode copiar BLOBs, diretórios e contêineres entre contas de armazenamento usando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos de outros tipos de tarefas, como carregar arquivos, baixar BLOBs e sincronizar com o armazenamento de BLOBs, consulte os links apresentados na seção [próximas etapas](#next-steps) deste artigo.

O AzCopy usa [APIs](/rest/api/storageservices/put-page-from-url)de [servidor para servidor](/rest/api/storageservices/put-block-from-url) , portanto, os dados são copiados diretamente entre os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador.

Para baixar o AzCopy e saber mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento, consulte Introdução [ao AzCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Diretrizes

Aplique as seguintes diretrizes aos seus comandos do AzCopy. 

- O cliente deve ter acesso à rede para as contas de armazenamento de origem e de destino. Para saber como definir as configurações de rede para cada conta de armazenamento, consulte [Configurar firewalls de armazenamento do Azure e redes virtuais](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Acrescente um token SAS a cada URL de origem. 

  Se você fornecer credenciais de autorização usando Azure Active Directory (Azure AD), poderá omitir o token SAS somente da URL de destino. Verifique se você configurou as funções adequadas em sua conta de destino. Consulte a [opção 1: usar Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Os exemplos neste artigo pressupõem que você autenticou sua identidade usando o Azure AD para que os exemplos omitam os tokens SAS da URL de destino.

-  Se você copiar para uma conta de armazenamento de blob de blocos Premium, omita a camada de acesso de um blob da operação de cópia definindo o `s2s-preserve-access-tier` como `false` (por exemplo: `--s2s-preserve-access-tier=false` ). As contas de armazenamento de blob de blocos Premium não dão suporte a camadas de acesso. 

- Se você copiar de ou para uma conta que tenha um namespace hierárquico, use em `blob.core.windows.net` vez de `dfs.core.windows.net` na sintaxe da URL. O [acesso multiprotocolo no data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) permite que você use `blob.core.windows.net` o, e é a única sintaxe com suporte para cenários de cópia de conta para conta. 

- Você pode aumentar a taxa de transferência de operações de cópia definindo o valor da `AZCOPY_CONCURRENCY_VALUE` variável de ambiente. Para saber mais, consulte [otimizar a taxa de transferência](storage-use-azcopy-configure.md#optimize-throughput). 

- Se os blobs de origem tiverem marcas de índice e você quiser manter essas marcas, você precisará reaplicá-las aos blobs de destino. Para obter informações sobre como definir marcas de índice, consulte a seção [copiar BLOBs para outra conta de armazenamento com marcas de índice](#copy-between-accounts-and-add-index-tags) deste artigo.

## <a name="copy-a-blob"></a>Copiar um blob

Copie um blob para outra conta de armazenamento usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados. 

## <a name="copy-a-directory"></a>Copiar um diretório

Copie um diretório para outra conta de armazenamento usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

## <a name="copy-a-container"></a>Copiar um contêiner

Copie um contêiner para outra conta de armazenamento usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

## <a name="copy-containers-directories-and-blobs"></a>Copiar contêineres, diretórios e blobs

Copie todos os contêineres, diretórios e BLOBs para outra conta de armazenamento usando o comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Copiar BLOBs e adicionar marcas de índice

Copie blobs para outra conta de armazenamento e adicione [marcas de índice de BLOB (versão prévia)](../blobs/storage-manage-find-blobs.md) ao blob de destino.

Se você estiver usando a autorização do Azure AD, sua entidade de segurança deverá receber a função de [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou deverá receber a permissão para a operação do provedor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure. Se você estiver usando um token SAS (assinatura de acesso compartilhado), esse token deverá fornecer acesso às marcas do blob por meio da `t` permissão SAS.

Para adicionar marcas, use a `--blob-tags` opção junto com um par chave-valor codificado por URL. 

Por exemplo, para adicionar a chave `my tag` e um valor `my tag value` , você adicionaria `--blob-tags='my%20tag=my%20tag%20value'` ao parâmetro de destino. 

Separe várias marcas de índice usando um e comercial ( `&` ).  Por exemplo, se você quiser adicionar uma chave `my second tag` e um valor `my second tag value` , a cadeia de caracteres de opção completa será `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Os exemplos a seguir mostram como usar a `--blob-tags` opção.

> [!TIP]
> Esses exemplos incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Active** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Contêiner** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Conta** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

> [!NOTE]
> Se você especificar um diretório, um contêiner ou uma conta para a origem, todos os BLOBs copiados para o destino terão as mesmas marcas que você especificar no comando.

## <a name="copy-with-optional-flags"></a>Copiar com sinalizadores opcionais

Você pode ajustar sua operação de cópia usando sinalizadores opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Copie blobs como bloco, página ou BLOBs de acréscimo.|**--tipo** = \[ de BLOB BlockBlob \| PageBlob \| AppendBlob\]|
|Copie para uma camada de acesso específica (como a camada de arquivo morto).|**--bloco-blob-camada** = \[ Nenhum \| \| arquivo frio \| quente\]|
|Descompacte arquivos automaticamente.|**--descompactar** = \[ \|desinflar gzip\]|

Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Baixar](storage-use-azcopy-blobs-download.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: usar buckets do Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: arquivos do Azure](storage-use-azcopy-files.md)
- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
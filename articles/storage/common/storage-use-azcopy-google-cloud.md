---
title: Copiar dados do Google Cloud Storage para o armazenamento do Azure usando o AzCopy | Microsoft Docs
description: Use o AzCopy para copiar dados do Google Cloud Storage para o armazenamento do Azure. AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0f030683954ede013f769bf8584e6cf82bab69f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555236"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Copiar dados do Google Cloud Storage para o armazenamento do Azure usando AzCopy (versão prévia)

AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a copiar objetos, diretórios e buckets do armazenamento em nuvem do Google para o armazenamento de BLOBs do Azure usando o AzCopy.

> [!IMPORTANT]
> A cópia de dados do Google Cloud Storage para o armazenamento do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como você fornecerá credenciais de autorização

* Para autorizar com o armazenamento do Azure, use Azure Active Directory (AD) ou um token de SAS (assinatura de acesso compartilhado).

* Para autorizar com o Google Cloud Storage, use uma chave de conta de serviço.

### <a name="authorize-with-azure-storage"></a>Autorizar com o armazenamento do Azure

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE] 
> Os exemplos neste artigo pressupõem que você forneceu credenciais de autorização usando Azure Active Directory (Azure AD).
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorizar com o Google Cloud Storage

Para autorizar com o Google Cloud Storage, você usará uma chave de conta de serviço. Para obter informações sobre como criar uma chave de conta de serviço, consulte [criando e gerenciando chaves de conta de serviço](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Depois de obter uma chave de serviço, defina a `GOOGLE_APPLICATION_CREDENTIALS` variável de ambiente como caminho absoluto para o arquivo de chave de conta de serviço:

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Copiar objetos, diretórios e buckets

O AzCopy usa o [bloco Put da API de URL](/rest/api/storageservices/put-block-from-url) , para que os dados sejam copiados diretamente entre o armazenamento em nuvem do Google e os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador.

> [!TIP]
> Os exemplos nesta seção incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

 Esses exemplos também funcionam com contas que têm um namespace hierárquico. O [acesso de vários protocolos no data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) permite que você use a mesma sintaxe de URL ( `blob.core.windows.net` ) nessas contas. 

### <a name="copy-an-object"></a>Copiar um objeto

Use a mesma sintaxe de URL ( `blob.core.windows.net` ) para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Copiar um diretório

Use a mesma sintaxe de URL ( `blob.core.windows.net` ) para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Este exemplo acrescenta o `--recursive` sinalizador para copiar arquivos em todos os subdiretórios.

### <a name="copy-the-contents-of-a-directory"></a>Copiar o conteúdo de um diretório

Você pode copiar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Copiar um Bucket de armazenamento em nuvem

Use a mesma sintaxe de URL ( `blob.core.windows.net` ) para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Copiar todos os buckets em um projeto do Google Cloud 

Primeiro, defina `GOOGLE_CLOUD_PROJECT` como ID do projeto do Google Cloud.

Use a mesma sintaxe de URL ( `blob.core.windows.net` ) para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Copiar um subconjunto de buckets em um projeto do Google Cloud 

Primeiro, defina `GOOGLE_CLOUD_PROJECT` como ID do projeto do Google Cloud.

Copie um subconjunto de buckets usando um símbolo curinga (*) no nome do Bucket. Use a mesma sintaxe de URL ( `blob.core.windows.net` ) para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Tratar diferenças nas regras de nomenclatura de Bucket

O Google Cloud Storage tem um conjunto diferente de convenções de nomenclatura para nomes de Bucket em comparação com os contêineres de blob do Azure. Você pode ler sobre eles [aqui](https://cloud.google.com/storage/docs/naming-buckets). Se você optar por copiar um grupo de buckets para uma conta de armazenamento do Azure, a operação de cópia poderá falhar devido a diferenças de nomenclatura.

O AzCopy lida com três dos problemas mais comuns que podem surgir; buckets que contêm pontos, Buckets que contêm hifens consecutivos e buckets que contêm sublinhados. Os nomes de buckets do Google Cloud Storage podem conter pontos e hifens consecutivos, mas um contêiner no Azure não pode. AzCopy substitui os períodos por hifens e hifens consecutivos por um número que representa o número de hifens consecutivos (por exemplo: um Bucket chamado `my----bucket` se torna `my-4-bucket` . Se o nome do Bucket tiver um sublinhado ( `_` ), AzCopy substituirá o sublinhado por um hífen (por exemplo: um Bucket chamado `my_bucket` se tornará `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Tratar diferenças nas regras de nomenclatura de objeto

O Google Cloud Storage tem um conjunto diferente de convenções de nomenclatura para nomes de objeto em comparação com os BLOBs do Azure. Você pode ler sobre eles [aqui](https://cloud.google.com/storage/docs/naming-objects).

O armazenamento do Azure não permite que nomes de objetos (ou qualquer segmento no caminho do diretório virtual) terminem com pontos à direita (por exemplo `my-bucket...` ). Os pontos à direita são cortados quando a operação de cópia é executada. 

## <a name="handle-differences-in-object-metadata"></a>Tratar diferenças nos metadados do objeto

O Google Cloud Storage e o Azure permitem diferentes conjuntos de caracteres nos nomes das chaves de objeto. Você pode ler sobre metadados no Google Cloud Storage [aqui](https://cloud.google.com/storage/docs/metadata). No lado do Azure, as chaves de objeto de blob aderem às regras de nomenclatura para [identificadores C#](/dotnet/csharp/language-reference/).

Como parte de um `copy` comando AzCopy, você pode fornecer um valor para o `s2s-handle-invalid-metadata` sinalizador opcional que especifica como você gostaria de tratar os arquivos em que os metadados do arquivo contêm nomes de chave incompatíveis. A tabela a seguir descreve cada valor de sinalizador.

| Valor do sinalizador | Descrição  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opção padrão) Os metadados não estão incluídos no objeto transferido. AzCopy registra um aviso. |
| **FailIfInvalid** | Os objetos não são copiados. AzCopy registra um erro e inclui esse erro na contagem de falhas que aparece no resumo da transferência.  |
| **RenameIfInvalid**  | AzCopy resolve a chave de metadados inválida e copia o objeto para o Azure usando o par de valor de chave de metadados resolvido. Para saber exatamente quais etapas o AzCopy leva para renomear as chaves de objeto, consulte a seção [como o AzCopy renomeia as chaves de objeto](#rename-logic) abaixo. Se AzCopy não puder renomear a chave, o objeto não será copiado. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Como o AzCopy renomeia as chaves de objeto

AzCopy executa estas etapas:

1. Substitui caracteres inválidos por ' _ '.

2. Adiciona a cadeia de caracteres `rename_` ao início de uma nova chave válida.

   Essa chave será usada para salvar o **valor** de metadados original.

3. Adiciona a cadeia de caracteres `rename_key_` ao início de uma nova chave válida.
   Essa chave será usada para salvar a **chave** inválida de metadados originais.
   Você pode usar essa chave para tentar recuperar os metadados no lado do Azure, pois a chave de metadados é preservada como um valor no serviço de armazenamento de BLOBs.

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados](storage-use-azcopy-v10.md#transfer-data)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

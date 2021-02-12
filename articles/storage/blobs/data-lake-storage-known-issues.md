---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre as limitações e os problemas conhecidos do Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: db55a29e53c53e26fbb04d96ddf1864946bafbfd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581271"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo descreve as limitações e os problemas conhecidos do Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

Um número crescente de recursos de Armazenamento de Blobs agora funciona com contas que têm um namespace hierárquico. Para obter uma lista completa, confira [Recursos do Armazenamento de Blobs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviço do Azure com suporte

O Azure Data Lake Storage Gen2 é compatível com vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, confira [Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Confira [Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, confira [Plataformas de software livre que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Confira [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de Blob e APIs do Data Lake Storage Gen2 podem operar nos mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs de Blob e APIs do Data Lake Storage Gen2 para operar nos mesmos dados.

* Você não pode usar a API de BLOB e as APIs de Data Lake Storage para gravar na mesma instância de um arquivo. Se você gravar em um arquivo usando APIs Data Lake Storage Gen2, os blocos desse arquivo não estarão visíveis para chamadas para a API de Blob [Obter Lista de Blocos](/rest/api/storageservices/get-block-list). A única exceção é quando o uso de você está substituindo. Você pode substituir um arquivo/BLOB usando uma das APIs.

* Quando você usar a operação [Listar Blobs](/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão diretórios e blobs. Se você optar por usar um delimitador, use apenas uma barra (`/`). Esse é o único delimitador compatível.

* Se você usar a API [Excluir Blob](/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio. Isso significa que você não pode usar a API de Blob para excluir diretórios recursivamente.

Estas APIs REST de Blob não são compatíveis:

* [Colocar Blob (Página)](/rest/api/storageservices/put-blob)
* [Colocar Página](/rest/api/storageservices/put-page)
* [OBter Intervalos de Página](/rest/api/storageservices/get-page-ranges)
* [Blob de Cópia Incremental](/rest/api/storageservices/incremental-copy-blob)
* [Colocar Página da URL](/rest/api/storageservices/put-page-from-url)

Discos de VM não gerenciados não são compatíveis com contas que têm um namespace hierárquico. Se você desejar habilitar um namespace hierárquico em uma conta de armazenamento, coloque os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Suporte para configuração de listas de controle de acesso (ACLs) recursivamente

A capacidade de aplicar alterações de ACL recursivamente do diretório pai aos itens filho está disponível para o público geral. Na versão atual desse recurso, você pode aplicar alterações de ACL usando o PowerShell, CLI do Azure e o .NET, Java e o SDK do Python. O suporte ainda não está disponível para o portal do Azure, ou Gerenciador de Armazenamento do Azure.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Use apenas a versão mais recente do AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).  As versões anteriores do AzCopy, como AzCopy v8.1, não são compatíveis.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Use apenas versões `1.6.0` ou superiores.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Gerenciador de Armazenamento no portal do Azure

As ACLs ainda não são compatíveis.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Aplicativos de terceiros

Aplicativos de terceiros que usam APIs REST para funcionar continuarão a funcionar se você usá-los com o Data Lake Storage Gen2. Aplicativos que chamam APIs de Blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>As ACLs (listas de controle de acesso) e acesso de leitura anônimo

Se o [acesso de leitura anônimo](./anonymous-read-access-configure.md) tiver sido concedido a um contêiner, as ACLs não terão nenhum efeito nesse contêiner nem nos arquivos desse contêiner.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

A configuração para dias de retenção ainda não tem suporte, mas você pode excluir logs manualmente usando qualquer ferramenta com suporte, como Gerenciador de Armazenamento do Azure, REST ou um SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Políticas de gerenciamento de ciclo de vida com camada Premium para Azure Data Lake Storage

Não é possível mover dados armazenados na camada Premium entre as camadas quente, fria e arquivo morto. No entanto, você pode copiar dados da camada Premium para a camada de acesso quente em uma conta diferente.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Suporte Dremio com contas de armazenamento BlockBlobStorage de desempenho premium

O Dremio ainda não se conecta a uma conta de armazenamento de BlockBlobStorage que tem o recurso de namespace hierárquico habilitado nela. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Driver do WASB (Windows Azure Storage Blob) (não compatível com o Data Lake Storage Gen2)

Atualmente, o driver WASB, que foi projetado para funcionar apenas com a API de Blob, encontra problemas em alguns cenários comuns. Especificamente, quando ele é um cliente para uma conta de armazenamento habilitada para namespace hierárquico. O acesso de vários protocolos no Data Lake Storage não atenuará esses problemas. 

Por enquanto (e, provavelmente, no futuro próximo), não daremos suporte a clientes que usam o driver WASB como um cliente para uma conta de armazenamento habilitada para namespace hierárquico. Em vez disso, recomendamos que você opte por usar o driver do [ABFS (Azure Blob File System)](data-lake-storage-abfs-driver.md) em seu ambiente do Hadoop. Se você estiver tentando migrar de um ambiente local do Hadoop com uma versão anterior à Hadoop branch-3, abra um tíquete de suporte do Azure para que possamos entrar em contato com você e seguir o melhor rumo para você e sua organização.
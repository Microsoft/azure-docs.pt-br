---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre as limitações e problemas conhecidos com o Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: d56fb411eb032e5e6227d68cd8abe02c0e30850b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708457"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo contém problemas conhecidos e limitações temporárias com o Data Lake armazenamento Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Suporte a SDK para contas do Data Lake armazenamento Gen2

Não existem SDKs disponíveis que funcionarão com contas do Data Lake armazenamento Gen2.

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de armazenamento de blob ainda não estão disponíveis para contas do Data Lake armazenamento Gen2.

Essas APIs estão desabilitadas para evitar problemas de acesso acidental de dados que poderiam surgir porque as APIs de armazenamento de Blob ainda não estão interoperáveis com Azure Data Lake Gen2 APIs.

Se você usou essas APIs para carregar dados antes que fossem desabilitados, e você tem um requisito para acessar os dados de produção, em seguida, entre em contato com o Suporte da Microsoft com as seguintes informações:

* ID da assinatura (o GUID, não o nome)

* Nome da conta de armazenamento

* Se você ativamente é afetados em produção e nesse caso, para quais contas de armazenamento?

* Mesmo se você não será afetado ativamente em produção, conte-nos se você precisa dos dados a serem copiados para outra conta de armazenamento por algum motivo e nesse caso, por que?

Nessas circunstâncias, podemos restaurar o acesso à API do blob por um período de tempo limitado para que você possa copiar esses dados para uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

Discos de VM (máquina virtual) não gerenciados dependem das APIs de Armazenamento de Blobs desabilitadas; portanto, se você deseja habilitar um namespace hierárquico em uma conta de armazenamento, considere colocar os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

## <a name="api-interoperability"></a>Interoperabilidade da API

APIs de armazenamento de B’lob e APIs do Azure Data Lake Gen2 não são interoperáveis.

Se você tiver ferramentas, aplicativos, serviços ou scripts que usam APIs de Blob e desejar usá-los para trabalhar com todo o conteúdo carregado para sua conta, não habilite um namespace hierárquico em sua conta de Armazenamento de Blobs até que essas APIs de Blob se tornem interoperáveis com as APIs do Azure Data Lake Gen2. Usar uma conta de armazenamento sem um namespace hierárquico significa que, em seguida, você não tem acesso a recursos específicos do Data Lake armazenamento Gen2, como o diretório e arquivo listas de controle de acesso do sistema.

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Para exibir ou gerenciar contas do Azure Data Lake Storage Gen2 usando o Gerenciador de Armazenamento do Microsoft Azure, você deve ter pelo menos versão `1.6.0` da ferramenta que está disponível como [download gratuito](https://azure.microsoft.com/features/storage-explorer/).

Observe que a versão do Gerenciador de armazenamento que está incorporado no portal do Azure atualmente não dá suporte a exibir ou gerenciar contas do Data Lake armazenamento Gen2 com o recurso de namespace hierárquico habilitado.

## <a name="blob-viewing-tool"></a>Ferramenta de visualização de Blob

Ferramenta de visualização no portal do Azure de blob tem somente suporte limitado para Data Lake armazenamento Gen2.

## <a name="third-party-applications"></a>Aplicativo de terceiros

Aplicativos de terceiros podem não dar suporte a Data Lake armazenamento Gen2.

O suporte é o critério de cada provedor de aplicativo de terceiros. Atualmente, as APIs de armazenamento de Blob e APIs de Gen2 de armazenamento do Data Lake não pode ser usadas para gerenciar o mesmo conteúdo. Estamos trabalhando para habilitar essa interoperabilidade, é possível que muitas ferramentas de terceiros serão compatível com Data Lake armazenamento Gen2 automaticamente.

## <a name="azcopy-support"></a>Suporte do AzCopy

Versão 8 do AzCopy não dá suporte a Data Lake armazenamento Gen2.

Em vez disso, use a versão de visualização mais recente do AzCopy ( [v10 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) que ofereça suporte a pontos de extremidade do Data Lake armazenamento Gen2.

## <a name="azure-event-grid"></a>Grade de Eventos do Azure

[A Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) não recebe eventos de contas do Azure Data Lake Gen2, porque essas contas ainda não os geraram.  

## <a name="soft-delete-and-snapshots"></a>Instantâneos e exclusão reversível

Instantâneos e a exclusão reversível não estão disponíveis para contas do Data Lake armazenamento Gen2.

Todos os recursos de controle de versão, incluindo [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) ainda não estão disponíveis para contas de armazenamento que têm o recurso de namespace hierárquico habilitado.

## <a name="object-level-storage-tiers"></a>Camadas de armazenamento no nível de objeto

As camadas de armazenamento no nível de objeto (de acesso frequente, frio e de arquivos) ainda não estão disponíveis para contas do Azure Data Lake Storage Gen 2, mas elas estão disponíveis para contas de armazenamento que não têm o recurso de namespace hierárquico habilitado.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Políticas de gerenciamento de ciclo de vida do Armazenamento de Blobs do Azure

Políticas de gerenciamento do ciclo de vida de armazenamento de BLOBs do Azure ainda não estão disponíveis para contas do Data Lake armazenamento Gen2.

Essas políticas estão disponíveis para as contas de armazenamento que não têm o recurso de namespace hierárquico habilitado.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Os logs de diagnóstico não estão disponíveis para contas do Data Lake armazenamento Gen2.

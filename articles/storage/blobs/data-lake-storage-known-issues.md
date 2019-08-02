---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre as limitações e problemas conhecidos com o Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443226"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo lista os recursos e ferramentas que ainda não tem suporte ou apenas parcialmente com suporte com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake armazenamento Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de armazenamento de blob estão desabilitados para evitar problemas de capacidade de operação do recurso que poderiam surgir porque as APIs de armazenamento de Blob ainda não estão interoperáveis com APIs do Azure Data Lake Gen2.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>O que fazer com os serviços, aplicativos e ferramentas existentes

Se qualquer um desses usar APIs de Blob e você quiser usá-los para trabalhar com todo o conteúdo que você carrega à sua conta, em seguida, não habilite a um namespace hierárquico em sua conta de armazenamento de Blob até que o Blob APIs tornam-se interoperável com APIs do Azure Data Lake Gen2.

Usar uma conta de armazenamento sem um namespace hierárquico significa que, em seguida, você não tem acesso a recursos específicos do Data Lake armazenamento Gen2, como o diretório e arquivo listas de controle de acesso do sistema.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>O que fazer com discos não gerenciados de máquina Virtual (VM)

Eles dependem de APIs de armazenamento de BLOBs desabilitado, portanto, se você quiser habilitar um namespace hierárquico em uma conta de armazenamento, considere colocá-los em uma conta de armazenamento que não tem o recurso de namespace hierárquico habilitado.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>O que fazer se você usou APIs de Blob para carregar os dados antes de APIs de Blob foram desabilitados

Se você usou essas APIs para carregar dados antes que fossem desabilitados, e você tem um requisito para acessar os dados de produção, em seguida, entre em contato com o Suporte da Microsoft com as seguintes informações:

> [!div class="checklist"]
> * ID da assinatura (o GUID, não o nome).
> * Nomes de conta de armazenamento.
> * Se você ativamente é afetados em produção e nesse caso, para quais contas de armazenamento?.
> * Mesmo se você não será afetado ativamente em produção, conte-nos se você precisa dos dados a serem copiados para outra conta de armazenamento por algum motivo e nesse caso, por que?

Nessas circunstâncias, podemos restaurar o acesso à API do blob por um período de tempo limitado para que você possa copiar esses dados para uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

## <a name="all-other-features-and-tools"></a>Todos os outros recursos e ferramentas

A tabela a seguir lista todos os outros recursos e ferramentas que ainda não tem suporte ou apenas parcialmente com suporte com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake armazenamento Gen2).

| Recurso / ferramenta    | Mais informações    |
|--------|-----------|
| **APIs para contas de armazenamento do Data Lake armazenamento Gen2** | Com suporte parcial <br><br>Você pode usar o Data Lake armazenamento Gen2 **REST** APIs, mas as APIs em outros SDKs de Blob, como os SDKs do .NET, Java, Python ainda não estão disponíveis.|
| **AzCopy** | Suporte de versão específica <br><br>Usar somente a versão mais recente do AzCopy ([v10 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Não há suporte para versões anteriores do AzCopy como v8.1 AzCopy.|
| **Políticas de gerenciamento do ciclo de vida do Azure Blob storage** | Ainda não tem suporte |
| **Rede de distribuição de conteúdo (CDN)** | Ainda não tem suporte|
| **O Azure search** |Ainda não tem suporte|
| **Gerenciador de Armazenamento do Azure** | Suporte de versão específica <br><br>Use somente a versão `1.6.0` ou superior. <br>Versão `1.6.0` está disponível como um [download gratuito](https://azure.microsoft.com/features/storage-explorer/).|
| **As ACLs do contêiner de blob** |Ainda não tem suporte|
| **Blobfuse** |Ainda não tem suporte|
| **Domínios personalizados** |Ainda não tem suporte|
| **Logs de diagnóstico** |Ainda não tem suporte|
| **Gerenciador de sistema de arquivos** | Suporte limitado |
| **Armazenamento imutável** |Ainda não tem suporte <br><br>Armazenamento imutável oferece a capacidade de armazenar dados em um [WORM (Write Once, muitas leituras)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) estado.|
| **Camadas de nível de objeto** |Ainda não tem suporte <br><br>Por exemplo:  Premium, as camadas quente, frio e arquivo morto.|
| **Suporte do PowerShell e CLI** | Funcionalidade limitada <br><br>Você pode criar uma conta usando o Powershell ou a CLI. Você não pode executar operações ou definir listas de controle de acesso nos sistemas de arquivos, diretórios e arquivos.|
| **Sites estáticos** |Ainda não tem suporte <br><br>Especificamente, a capacidade de servir arquivos a serem [sites estáticos](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Os aplicativos de terceiros** | Suporte limitado <br><br>Os aplicativos de terceiros que usam APIs REST para trabalhar continuarão a funcionar se você usá-los com o Data Lake armazenamento Gen2. <br>Se você tiver um aplicativo que usa APIs de Blob, esse aplicativo provavelmente terá problemas se você usar esse aplicativo com o Data Lake armazenamento Gen2. Para obter mais informações, consulte o [APIs estão desabilitadas para as contas de armazenamento do Data Lake armazenamento Gen2 do armazenamento de BLOBs](#blob-apis-disabled) seção deste artigo.|
| **Recursos de controle de versão** |Ainda não tem suporte <br><br>Isso inclui [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|


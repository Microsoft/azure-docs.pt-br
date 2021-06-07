---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612893"
---
O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O Armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários.

## <a name="about-blob-storage"></a>Sobre Armazenamento de Blobs

O Armazenamento de Blobs foi projetado para:

* Fornecimento de imagens ou de documentos diretamente a um navegador.
* Armazenamento de arquivos para acesso distribuído.
* Transmissão por streaming de áudio e vídeo.
* Gravando nos arquivo de log.
* Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
* Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

Usuários ou aplicativos cliente podem acessar objetos no Armazenamento de Blobs via HTTP/HTTPS de qualquer lugar no mundo. Objetos no Armazenamento de Blobs estão acessíveis por meio da [API REST do Armazenamento do Azure](/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](/powershell/module/az.storage), da [CLI do Azure](/cli/azure/storage) ou em uma biblioteca de clientes do Armazenamento do Azure. As bibliotecas de cliente estão disponíveis para diferentes idiomas, incluindo:

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Azure Data Lake Storage Gen2

O Armazenamento de Blobs dá suporte ao Azure Data Lake Storage Gen2, a solução de análise de Big Data corporativa da Microsoft para a nuvem. O Azure Data Lake Storage Gen2 oferece um sistema de arquivos hierárquico, bem como as vantagens do armazenamento de Blobs, incluindo:

* Armazenamento em camadas de baixo custo
* Alta disponibilidade
* Coerência forte
* Recursos de recuperação de desastre

Para obter mais informações sobre o Data Lake Storage Gen2, veja a [Introdução ao Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).
---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8d1f9ca5a964787f0ee68e6d99fe870cd7b5994b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673222"
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

Usuários ou aplicativos cliente podem acessar objetos no Armazenamento de Blobs via HTTP/HTTPS de qualquer lugar no mundo. Objetos no Armazenamento de Blobs estão acessíveis por meio da [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), da [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou em uma biblioteca de clientes do Armazenamento do Azure. Bibliotecas de clientes estão disponíveis para uma variedade de linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go](https://github.com/azure/azure-storage-blob-go/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Azure Data Lake Storage Gen2

O Armazenamento de Blobs dá suporte ao Azure Data Lake Storage Gen2, a solução de análise de Big Data corporativa da Microsoft para a nuvem. O Azure Data Lake Storage Gen2 oferece um sistema de arquivos hierárquico, bem como as vantagens do Armazenamento de Blobs, incluindo armazenamento em camadas de baixo custo; alta disponibilidade; coerência forte; e funcionalidades de recuperação de desastres.

Para obter mais informações sobre o Data Lake Storage Gen2, veja a [Introdução ao Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).

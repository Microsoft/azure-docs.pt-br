---
title: incluir arquivo
description: incluir arquivo
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509231"
---
O **Azure Data Lake Storage Gen2** não é um serviço dedicado ou um tipo de conta de armazenamento. Ele é a versão mais recente de funcionalidades dedicadas à análise de Big Data.  Essas funcionalidades estão disponíveis em uma conta de armazenamento de uso geral v2 ou de BlockBlobStorage e você pode obtê-las habilitando o recurso **Namespace hierárquico** da conta. Para destinos de escala, confira estes artigos. 

- [Destinos de escala para armazenamento de blobs](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage).
- [Destinos de escala para contas de armazenamento padrão](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts).

O **Azure Data Lake Storage Gen1** é um serviço dedicado. Ele é um repositório em hiper-escala corporativo para cargas de trabalho de análise de Big Data. Você pode usar o Data Lake Storage Gen1 para capturar dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias. Não há limite de quantidade de dados que podem ser armazenados em uma conta do Data Lake Storage Gen1.

| **Recurso** | **Limite** | **Comentários** |
| --- | --- | --- |
| Número máximo de contas do Data Lake Storage Gen1, por assinatura e por região |10 | Para solicitar um aumento desse limite, entre em contato com o suporte. |
| Número máximo de ACLs de acesso, por arquivo ou por pasta |32 | Este é um limite rígido. Usar grupos para gerenciar o acesso com menos entradas. |
| Número máximo de ACLs padrões, por arquivo ou por pasta |32 | Este é um limite rígido. Usar grupos para gerenciar o acesso com menos entradas. |
---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011675"
---
Os compartilhamentos de arquivo do Azure são implantados em *contas de armazenamento*, que são objetos de nível superior que representam um pool de armazenamento compartilhado. Esse pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivo, bem como outros recursos de armazenamento, como contêineres de blob, filas ou tabelas. Todos os recursos de armazenamento implantados em uma conta de armazenamento compartilham os limites aplicáveis a essa conta de armazenamento. Para ver os limites atuais de uma conta de armazenamento, confira [Metas de desempenho e escalabilidade dos Arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

Há dois tipos principais de contas de armazenamento que serão usadas para implantações dos Arquivos do Azure: 
- **Contas de armazenamento GPv2 (uso geral versão 2)** : as contas de armazenamento GPv2 permitem que você implante compartilhamentos de arquivo do Azure em hardware padrão/baseado em HD (disco rígido). Além de armazenar compartilhamentos de arquivo do Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como contêineres de blob, filas ou tabelas. 
- **Contas de armazenamento FileStorage**: as contas de armazenamento FileStorage permitem que você implante compartilhamentos de arquivo do Azure em hardware premium/baseado em SSD (disco de estado sólido). As contas FileStorage só podem ser usadas para armazenar compartilhamentos de arquivo do Azure; nenhum outro recurso de armazenamento (contêineres de blob, filas, tabelas etc.) pode ser implantado em uma conta FileStorage. Somente contas FileStorage podem implantar compartilhamentos de arquivo SMB e NFS.

Há vários outros tipos de contas de armazenamento que podem ser incluídos no portal do Azure, no PowerShell ou na CLI. Dois tipos de conta de armazenamento, contas de armazenamento BlockBlobStorage e BlobStorage, não podem conter compartilhamentos de arquivo do Azure. Os outros dois tipos de conta de armazenamento que você pode ver são GPv1 (uso geral versão 1) e contas de armazenamento clássicas; ambos podem conter compartilhamentos de arquivo do Azure. Embora as contas de armazenamento GPv1 e clássicas possam conter compartilhamentos de arquivo do Azure, a maioria dos novos recursos dos Arquivos do Azure está disponível apenas nas contas de armazenamento GPv2 e FileStorage. Portanto, recomendamos que você use apenas contas de armazenamento GPv2 e FileStorage para novas implantações e atualize as contas de armazenamento GPv1 e clássicas se elas já existirem em seu ambiente.  
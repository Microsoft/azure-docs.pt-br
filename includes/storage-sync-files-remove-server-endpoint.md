---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96309941"
---
Não: a remoção de um ponto de extremidade do servidor não é como reinicializar um servidor! Remover e recriar o ponto de extremidade do servidor quase nunca é uma solução apropriada para corrigir problemas com sincronização, camadas de nuvem ou outros aspectos de Sincronização de Arquivos do Azure. A remoção de um ponto de extremidade do servidor é uma operação destrutiva. Isso pode resultar em perda de dados, caso os arquivos em camadas existam fora do namespace do ponto de extremidade do servidor. Veja [por que arquivos em camadas existem fora do namespace do ponto de extremidade do servidor](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) para obter mais informações. Ou pode resultar em arquivos inacessíveis para arquivos em camadas que existem dentro do namespace do ponto de extremidade do servidor. Esses problemas não serão resolvidos quando o ponto de extremidade do servidor for recriado. Arquivos em camadas podem existir em seu ponto de extremidade do servidor, mesmo se você nunca tiver habilitado nuvem em camadas. É por isso que recomendamos que você não remova o ponto de extremidade do servidor, a menos que você queira parar de usar Sincronização de Arquivos do Azure com essa pasta específica ou tenha sido instruído explicitamente a fazer isso por um engenheiro da Microsoft. Para obter mais informações sobre remover pontos de extremidade do servidor, consulte [remover um ponto de extremidade do servidor](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    

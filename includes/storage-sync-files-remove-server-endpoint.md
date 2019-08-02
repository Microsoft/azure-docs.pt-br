---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171994"
---
Não: remover um ponto de extremidade do servidor não é como reiniciar um servidor! Remover e recriar o ponto de extremidade do servidor quase nunca é uma solução apropriada para corrigir problemas com a sincronização, camadas de nuvem ou outros aspectos da Sincronização de Arquivos do Azure. Remover um ponto de extremidade do servidor é uma operação destrutiva e pode resultar em perda de dados no caso de arquivos em camadas existirem fora do namespace do ponto de extremidade do servidor (consulte [Por que há arquivos em camada fora do namespace do ponto de extremidade do servidor](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) para obter mais informações) ou em arquivos inacessíveis para arquivos em camadas existentes dentro do namespace de ponto de extremidade do servidor. Esses problemas não serão resolvidos quando o ponto de extremidade do servidor for recriado. Arquivos em camadas podem existir em seu ponto de extremidade do servidor, mesmo se você nunca tiver habilitado nuvem em camadas. Portanto, recomendamos que você não remova o ponto de extremidade do servidor, a menos que deseje parar de usar a Sincronização de Arquivos do Azure com essa pasta específica ou tenha sido explicitamente instruído a fazer isso por um engenheiro da Microsoft. Para obter mais informações sobre remover pontos de extremidade do servidor, consulte [remover um ponto de extremidade do servidor](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
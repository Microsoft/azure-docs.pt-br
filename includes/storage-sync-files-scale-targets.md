---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172129"
---
| Resource | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 20 serviços de sincronização de armazenamento | Sim |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 100 grupos de sincronização | Sim |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | Sim |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | Sim |
| Pontos de extremidade do servidor por grupo de sincronização | 50 pontos de extremidade de servidor | Não |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | Sim |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 25 milhões de objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 1 milhão de objetos | Sim |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 64 KiB | Sim |
| Tamanho do arquivo | 100 GiB | Não |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | 64 KiB | Sim |
| Sessões de sincronização simultâneas | Agente V4 e posterior: O limite varia com base nos recursos do sistema disponíveis. <BR> Agente V3: Duas sessões de sincronização do Active Directory por processador ou um máximo de oito sessões de sincronização do Active Directory por servidor. | Sim

> [!Note]  
> Um ponto de extremidade de sincronização de arquivos do Azure pode escalar verticalmente para o tamanho de um compartilhamento de arquivos do Azure. Se o limite de tamanho de compartilhamento de arquivos do Azure for atingido, sincronização não será capaz de operar.
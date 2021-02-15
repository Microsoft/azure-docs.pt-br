---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516980"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um cluster?

Para um cluster de hubs de eventos, quanto você pode ingerir e transmitir depende de vários fatores, como seus produtores, consumidores, a taxa na qual você está ingerindo e processando e muito mais. 

A tabela a seguir mostra os resultados de benchmark que obtivemos durante nosso teste:

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400 mil mensagens/s | 800 MB/s | 800 mil mensagens/s | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | 66,6 mil mensagens/s | 1,33 GB/s | 133 mil mensagens/s | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | 34 mil mensagens/s | 1,05 GB/s | 34 mil mensagens/s | 1000 TUs | 250 TUs |

No teste, usamos os critérios a seguir:

- Um cluster de hubs de eventos de camada dedicada com quatro unidades de capacidade (CUs) foi usado. 
- O hub de eventos usado para ingestão tinha 200 partições. 
- Os dados ingeridos foram recebidos por dois aplicativos destinatários que recebem de todas as partições.

### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar/reduzir verticalmente o meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de uso. Na versão de visualização da experiência de autoatendimento, você pode enviar uma [solicitação de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) à equipe de hubs de eventos sob solicitação de cota **técnica**  >  **Quota**  >  **para escalar ou reduzir verticalmente o cluster dedicado** para dimensionar ou reduzir verticalmente o cluster. Pode levar até 7 dias para concluir a solicitação para reduzir verticalmente o cluster. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Como o geo-DR funciona com o meu cluster?

Você pode emparelhar geograficamente um namespace em um cluster de camada dedicada com outro namespace em um cluster de camada dedicada. Não incentivamos o emparelhamento de um namespace de camada dedicada com um namespace em nossa oferta padrão, pois o limite de taxa de transferência será incompatível e resultará em erros. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar meus namespaces padrão para pertencer a um cluster de camada dedicada?
Atualmente, não damos suporte a um processo de migração automatizado para migrar seus dados de hubs de eventos de um namespace padrão para um dedicado. 

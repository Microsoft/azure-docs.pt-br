---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563225"
---
| Modelo de uso | Modo de cache | Verificação de back-end | Atraso máximo de write-back |
|--|--|--|--|
| Leia gravações pesadas e frequentes | Ler | Nunca | Nenhum |
| Mais de 15% de gravações | Leitura/gravação | 8 horas | 20 minutos |
| Clientes ignoram o cache | Ler | 30 segundos | Nenhum |
| Mais de 15% de gravações, verificação de back-end frequente (30 segundos) | Leitura/gravação | 30 segundos | 20 minutos |
| Mais de 15% de gravações, verificação de back-end frequente (60 segundos) | Leitura/gravação | 60 segundos | 20 minutos |
| Mais de 15% de gravações, write-back frequente | Leitura/gravação | 30 segundos | 30 segundos |
| Leia pesado, verificando o servidor de backup a cada 3 horas | Ler | 3 horas | Nenhum |

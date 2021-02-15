---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999716"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurar a sessão de âncora espacial de nuvem

Cuidaremos da configuração da sessão de âncora espacial de nuvem em seguida. Na primeira linha, definimos o provedor de sensor na sessão. De agora em diante, qualquer âncora que criamos durante a sessão será associada a um conjunto de leituras de sensor. Em seguida, criamos uma instância de critérios de localização de dispositivo próximo e o inicializamos para corresponder aos requisitos do aplicativo. Por fim, instruímos a sessão a usar dados de sensor ao localizar âncoras criando um observador de nossos critérios de quase dispositivo.
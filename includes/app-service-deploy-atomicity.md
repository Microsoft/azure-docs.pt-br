---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836817"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação com suporte oficialmente fazem alterações aos arquivos no `/home/site/wwwroot` pasta do seu aplicativo. Esses arquivos são as mesmas que são executados em produção. Portanto, a implantação pode falhar devido a arquivos bloqueados. O aplicativo em produção pode também se comportam de forma imprevisível durante a implantação, porque nem todos os arquivos atualizados ao mesmo tempo. Há algumas maneiras diferentes para evitar esses problemas:

- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) habilitada. 
- Use [execução do pacote](https://github.com/Azure/app-service-announcements/issues/84) em vez da implantação contínua.

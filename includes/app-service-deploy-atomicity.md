---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004323"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação oficialmente suportados fazem alterações nos arquivos na `/home/site/wwwroot` pasta do seu aplicativo. Esses arquivos são usados para executar seu aplicativo. Portanto, a implantação pode falhar devido a arquivos bloqueados. O aplicativo também pode se comportar de modo não previsível durante a implantação, porque nem todos os arquivos são atualizados ao mesmo tempo. Isso não é desejável para um aplicativo voltado para o cliente. Há algumas maneiras diferentes para evitar esses problemas:

- [Execute seu aplicativo do pacote zip diretamente](../articles/app-service/deploy-run-package.md) sem desempacotá-lo.
- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) habilitada. 

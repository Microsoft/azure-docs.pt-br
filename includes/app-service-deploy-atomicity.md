---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765531"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação com suporte oficialmente têm algo em comum: fazem alterações para os arquivos da pasta `/home/site/wwwroot` do seu aplicativo. Esses são os mesmos arquivos que são executados em produção. Portanto, a implantação pode falhar devido a arquivos bloqueados, ou o aplicativo em produção pode ter um comportamento inesperado durante a implantação porque nem todos os arquivos são atualizados ao mesmo tempo. Há algumas maneiras diferentes para evitar esses problemas:

- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [Lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) habilitada. 
- Use [Executar do pacote](https://github.com/Azure/app-service-announcements/issues/84).

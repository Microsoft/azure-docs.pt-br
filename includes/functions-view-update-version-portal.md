---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83343347"
---
Use o procedimento a seguir para exibir e atualizar a versão de runtime usada no momento por um aplicativo de funções.

1. No [portal do Azure](https://portal.azure.com), navegue até o aplicativo de funções.

1. Em **configurações**, escolha **configuração**. Na guia **configurações de tempo de execução de função** , localize a **versão de tempo de execução**. Observe a versão de tempo de execução específica. No exemplo a seguir, a versão é definida como `~3`.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Exiba a versão de tempo de execução." border="true":::

1. Para fixar o aplicativo de funções no runtime de versão 1.x, escolha **~1** em **Versão de runtime**. Essa opção estará desabilitada quando houver funções no aplicativo.

1. Ao alterar a versão de runtime, retorne à guia **Visão geral** e escolha **Reiniciar** para reiniciar o aplicativo.  O aplicativo de funções reinicia a execução no runtime de versão 1.x e os modelos de versão 1.x são usados quando você cria funções.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Reinicie seu aplicativo de funções." border="true":::

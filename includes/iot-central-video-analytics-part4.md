---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832055"
---
### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para adicionar um dispositivo ao aplicativo, publique o modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA v2**, selecione **Publicar**.

1. Na página **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

O **Gateway de Borda de LVA v2** agora está disponível como o tipo de dispositivo a ser usado na página **Dispositivos** no aplicativo.

## <a name="migrate-the-gateway-device"></a>Migrar o dispositivo de gateway

O dispositivo **gateway-001** existente usa o modelo de dispositivo **Gateway de LVA do Edge**. Para usar o novo manifesto de implantação, migre o dispositivo para o novo modelo de dispositivo:

Para migrar o dispositivo **gateway-001**:

1. Procure a página **Dispositivos** e selecione o dispositivo **gateway-001** para destacá-lo na lista.

1. Selecione **Migrar**. Se o ícone **Migrar** não estiver visível, selecione **...** para ver mais opções.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrar o dispositivo de gateway para uma nova versão":::

1. Na lista na caixa de diálogo **Migrar**, selecione **Gateway de LVA do Edge v2** e, em seguida, selecione **Migrar**.

Após alguns segundos, a migração é concluída. Seu dispositivo agora está usando o modelo de dispositivo **LVA Edge Gateway v2** com o manifesto de implantação personalizado.

Agora, não há dispositivos usando o modelo de dispositivo **LVA Edge Gateway** original. Exclua este modelo de dispositivo:

1. Navegue até a página **Modelos de dispositivo** e selecione o modelo de dispositivo **LVA Edge Gateway**.

1. Selecione **Excluir** para excluir o modelo de dispositivo.

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Você precisará das credenciais que permitem que o dispositivo se conecte ao seu aplicativo do IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivos**, selecione o dispositivo **gateway-001**.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote no arquivo *scratchpad.txt* o **Escopo da ID**, a **Identificação do Dispositivo** e a **Chave Primária** do dispositivo. Esses valores serão usados depois.

1. Verifique se o método de conexão está definido como **Assinatura de Acesso Compartilhado**.

1. Selecione **Fechar**.


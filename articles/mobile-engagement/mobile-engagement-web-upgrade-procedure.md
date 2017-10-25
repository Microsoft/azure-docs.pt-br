---
title: "Procedimentos de atualização do SDK para Web do Azure Mobile Engagement | Microsoft Docs"
description: "As atualizações e procedimentos mais recentes do SDK para Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimentos de atualização do SDK para Web do Azure Mobile Engagement
Se você já tiver integrado uma versão anterior do SDK para Web do Azure Mobile Engagement em seu aplicativo Web, precisará considerar os seguintes pontos ao atualizar o SDK.

Se você tiver ignorado várias versões do SDK para Web do Mobile Engagement, talvez seja necessário concluir vários procedimentos durante o processo de atualização. Por exemplo, se você migrar da 1.4.0 para a 1.6.0, primeiro siga os procedimentos para atualizar da 1.4.0 para a 1.5.0. Em seguida, siga os procedimentos para atualizar da 1.5.0 para a 1.6.0.

Seja qual for a versão da qual você está realizando a atualização, substitua qualquer versão anterior do arquivo azure-engagement.js pela versão mais recente dele.

## <a name="upgrade-from-121-to-200"></a>Atualizar da 1.2.1 para a 2.0.0
Esta seção descreve como migrar uma integração do SDK para Web do Mobile Engagement do serviço Capptain, oferecido pelo Capptain SAS, para um aplicativo do Azure Mobile Engagement. Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para 1.2.1 e depois aplicar os procedimentos a seguir.

Essa versão do SDK para Web do Mobile Engagement não dá suporte para Samsung Smart TV, Opera TV, webOS ou com o recurso Reach.

> [!IMPORTANT]
> O Capptain e o Azure Mobile Engagement não são o mesmo serviço. O procedimento a seguir destaca apenas como migrar o aplicativo cliente. Migrar o SDK para Web do Mobile Engagement no aplicativo não migrará os dados de um servidor do Capptain para um servidor do Mobile Engagement.
> 
> 

### <a name="javascript-files"></a>Arquivos JavaScript
Substitua o arquivo capptain-sdk.js pelo arquivo azure-engagement.js e atualize suas importações de script adequadamente.

### <a name="remove-capptain-reach"></a>Remover o Capptain Reach
Essa versão do SDK para Web do Mobile Engagement não dá suporte ao recurso Reach. Se você tiver integrado o Capptain Reach ao aplicativo, será necessário removê-lo.

Remova a importação de CSS do Reach da sua página e exclua o arquivo .css relacionado (capptain-reach.css, por padrão).

Exclua os seguintes recursos do Reach: a imagem de fechamento (capptain-close.png, por padrão) e o ícone da marca (capptain-notification-icon, por padrão).

Remova a interface do usuário do Reach para notificações no aplicativo. O layout padrão tem esta aparência:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Remova a interface do usuário do Reach para anúncios da Web e pesquisas. O layout padrão tem esta aparência:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Remova o objeto `reach` da sua configuração, se ele existir. Ele tem esta aparência:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Remova qualquer outra personalização do Reach, como categorias.

### <a name="remove-deprecated-apis"></a>Remover APIs substituídas
Algumas das APIs do Capptain foram preteridas na versão do SDK para Web do Mobile Engagement.

Remova todas as chamadas para as seguintes APIs: `agent.connect`, `agent.disconnect`, `agent.pause` e `agent.sendMessageToDevice`.

Remova as instâncias dos seguintes retornos de chamada da sua configuração do Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` e `onPushMessageReceived`.

### <a name="configuration"></a>Configuração
O Mobile Engagement usa uma cadeia de conexão para configurar os identificadores do SDK, por exemplo, o identificador do aplicativo.

Substitua a ID do aplicativo pela cadeia de conexão. Observe que o objeto global para a configuração do SDK muda de `capptain` para `azureEngagement`.

Antes da migração:

    window.capptain = {
      appId: ...,
      [...]
    };

Após a migração:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### <a name="javascript-apis"></a>APIs do JavaScript
O objeto `window.capptain` global do JavaScript foi renomeado para `window.azureEngagement`, mas você pode usar o alias `window.engagement` para chamadas à API. Você não pode usar o alias para definir a configuração do SDK.

Por exemplo: `capptain.deviceId` se torna `engagement.deviceId`, `capptain.agent.startActivity` se torna `engagement.agent.startActivity` e assim por diante.


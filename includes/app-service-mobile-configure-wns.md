---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172421"
---
1. No [Portal do Azure](https://portal.azure.com/), selecione **Procurar todos os** > **Serviços de Aplicativos**. Em seguida, selecione o back-end dos Aplicativos Móveis. Em **Configurações**, selecione **Push do Serviço de Aplicativo**. Em seguida, selecione o nome do hub de notificação.
2. Vá para **Windows (WNS)** . Em seguida, insira a **Chave de segurança** (segredo do cliente) e o **SID do Pacote** que você obteve do site dos Live Services. Em seguida, selecione **Salvar**.

    ![Definir a chave WNS no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

O back-end agora está configurado para usar o WNS a fim de enviar notificações por push.

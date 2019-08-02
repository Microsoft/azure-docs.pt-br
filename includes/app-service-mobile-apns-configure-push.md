---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172420"
---
1. Em seu Mac, inicie **Acesso ao Conjunto de Chaves**. Na barra de navegação à esquerda, em **Categoria**, abra **Meus Certificados**. Localize o certificado SSL que você baixou na seção anterior e, em seguida, divulgue seus conteúdos. Selecione apenas o certificado (não selecione a chave privada). Em seguida, [exporte-o](https://support.apple.com/kb/PH20122?locale=en_US).
2. No [Portal do Azure](https://portal.azure.com/), selecione **Procurar todos os** > **Serviços de Aplicativos**. Em seguida, selecione o back-end dos Aplicativos Móveis. 
3. Em **Configurações**, selecione **Push do Serviço de Aplicativo**. Em seguida, selecione o nome do hub de notificação. 
4. Vá para **Serviços de Notificação por Push da Apple** > **Carregar Certificado**. Carregue o arquivo .p12 selecionando o **Modo** correto (dependendo do certificado de cliente SSL anterior ser de produção ou de área restrita). Salve as alterações.

Agora, seu serviço móvel está configurado para funcionar com notificações por push no iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

---
title: Integração dos Hubs de Notificação aos Aplicativos Móveis do Serviço de Aplicativo
description: Saiba como os Hubs de Notificação do Azure funcionam com os Aplicativos Móveis do Serviço de Aplicativo do Azure.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004052"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com Aplicativos Móveis do Serviço de Aplicativo

Para facilitar uma experiência integrada e unificada nos serviços do Azure, os [Aplicativos Móveis do Serviço de Aplicativo](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) tem suporte interno para notificações usando Hubs de Notificação do Azure. [Aplicativos Móveis do Serviço de Aplicativo](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) oferecem uma plataforma de desenvolvimento de aplicativos móveis altamente escalonável, disponível globalmente para os desenvolvedores corporativos e integradores de sistema e que traz um conjunto rico de recursos para desenvolvedores de aplicativos móveis.

Os desenvolvedores de aplicativos móveis podem usar Hubs de Notificação com oseguinte fluxo de trabalho:

1. Recuperar o identificador de PNS do dispositivo.
2. Registrar o dispositivo com Hubs de Notificação usando APIs de registro do SDK do cliente de Aplicativos Móveis.

    > [!NOTE]
    > Observe que os aplicativos móveis removem imediatamente todas as marcas nos registros para fins de segurança. Trabalhar com Hubs de notificação do seu back-end diretamente para associar marcas a dispositivos.

3. Enviar notificações do seu back-end de aplicativo com Hubs de Notificação.

Algumas vantagens que essa integração oferece são:

- **SDKs de cliente de Aplicativos Móveis**: Esses SDKs de várias plataformas fornecem APIs para registro e se comunicam com o Hub de Notificação vinculado ao aplicativo móvel. Você não precisa de credenciais de Hubs de Notificação nem trabalhar com um serviço adicional.
  - *Enviar por push para um usuário*: Os SDKs marcam automaticamente o dispositivo especificado com uma ID de usuário autenticada de Aplicativos Móveis para habilitar o cenário de "envio por push para o usuário".
  - *Enviar por push para o dispositivo*: Os SDKs usam automaticamente a ID de instalação dos aplicativos móveis como um GUID para se registrarem nos Hubs de Notificação, portanto, não há necessidade de manter vários GUIDs de serviço.
- **Modelo de instalação**: Os Aplicativos Móveis funcionam com o modelo de push mais recente dos Hubs de Notificação para declarar todas as propriedades de push associadas a um dispositivo em uma instalação de JSON que se alinha com Serviços de Notificação por Push e é fácil de usar.
- **Flexibilidade**: os desenvolvedores sempre podem optar por trabalhar com os Hubs de Notificação diretamente até mesmo com a integração em vigor.
- **Experiência integrada no [portal do Azure](https://portal.azure.com)** : O push como uma funcionalidade é representado visualmente em aplicativos móveis e os desenvolvedores podem trabalhar facilmente com o hub de notificação associado por meio de aplicativos móveis.

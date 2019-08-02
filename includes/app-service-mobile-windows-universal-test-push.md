---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172430"
---
1. Clique com o botão direito do mouse no projeto do Windows Store, clique em **Definir como Projeto de Inicialização**e pressione a tecla F5 para executar o aplicativo do Windows Store.

    Depois que o aplicativo é iniciado, o dispositivo está registrado para notificações por push.
2. Pare o aplicativo da Windows Store e repita a etapa anterior para o aplicativo Loja do Windows Phone.

    Nesse ponto, ambos os dispositivos são registrados para receber as notificações por push.

3. Execute o aplicativo do Windows Store novamente, digite o texto em **Inserir um TodoItem** e clique em **Salvar**.

    Observe que, após a inserção, tanto os aplicativos do Windows Store como do Windows Phone recebem uma notificação por push dos WNS. A notificação é exibida no Windows Phone, mesmo quando o aplicativo não está em execução.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

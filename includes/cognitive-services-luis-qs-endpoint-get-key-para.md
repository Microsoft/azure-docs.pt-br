---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: ef8dae8219eaf1a85a5c112705517b992e25a50f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172233"
---
O acesso ao ponto de extremidade de previsão é fornecido com uma chave de ponto de extremidade. Para os fins deste início rápido, use a chave de início gratuita associada à sua conta do LUIS. 
 
1. Conecte-se usando sua conta do LUIS. 

    [![Lista de aplicativos de captura de tela do LUIS (Serviço Inteligente de Reconhecimento Vocal)](media/cognitive-services-luis/app-list.png "Lista de aplicativos de captura de tela do LUIS (Serviço Inteligente de Reconhecimento Vocal)")](media/cognitive-services-luis/app-list.png)

2. Selecione o nome no menu superior direito e selecione **Configurações**.

    ![Acesso ao menu de configurações de usuário do LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Copie o valor da **chave de criação**. Você a usará posteriormente no início rápido. 

    [![Configurações do usuário da captura de tela do LUIS (Serviço Inteligente de Reconhecimento Vocal)](media/cognitive-services-luis/get-user-authoring-key.png "Configurações do usuário da captura de tela do LUIS (Serviço Inteligente de Reconhecimento Vocal)")](media/cognitive-services-luis/get-user-authoring-key.png)

    A chave de criação permite solicitações ilimitadas gratuitas para a API de criação e até 1.000 consultas para a API do ponto de extremidade de previsão por mês para todos os seus aplicativos de LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->

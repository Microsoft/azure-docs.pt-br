---
title: incluir arquivo
description: incluir arquivo
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376226"
---
1. Acesse o [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager)e entre com suas credenciais de conta do Google. 
2. Selecione **Criar projeto** na barra de ferramentas. 
   
    ![Criar um novo projeto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Para **Nome do projeto**, insira um nome para seu projeto e clique em **Criar**.
4. Selecione o botão **Alertas** na barra de ferramentas e, em seguida, selecione o projeto na lista. Você verá o painel do seu projeto. Também é possível acessar diretamente o painel usando a URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Selecione seu projeto nos alertas](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Anote o **Número do projeto** no bloco **Informações do projeto** do painel. 

    ![Project ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. No painel, no bloco **APIs**, selecione **Acessar visão geral das APIs**. 

    ![Link Visão geral da API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Na página da **API**, selecione **HABILITAR APIS E SERVIÇOS**. 

    ![Botão Habilitar APIs e Serviços](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Procure e selecione **Google Cloud Messaging**. 

    ![Procurar e selecionar Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Para habilitar o Google Cloud Messaging no projeto, selecione **HABILITAR**.

    ![Habilitar o sistema de mensagens em nuvem do Google](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Selecione **Criar credenciais** na barra de ferramentas. 

    ![Botão Criar credenciais](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Na página **Adicionar credenciais ao seu projeto**, selecione o link **Chave de API**. 

    ![Adicionar credenciais](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Na página **Chave de API**, selecione **Criar/Salvar**. No exemplo a seguir, a opção **endereços IP** está selecionada, e **0.0.0.0/0** é inserido nos endereços IP permitidos. Você deve restringir adequadamente sua chave de API. 

    ![Chave de API - botão Criar](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Copie a **chave de API** para a área de transferência e salve-a em outro lugar. 

    ![Copiar chave de API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo. Para voltar ao painel do projeto, use a URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`


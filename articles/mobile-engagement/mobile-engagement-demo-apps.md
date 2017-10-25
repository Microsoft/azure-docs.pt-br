---
title: "Aplicativo de demonstração do Azure Mobile Engagement | Microsoft Docs"
description: "Descreve o local em que é possível baixar, como usar e os benefícios de usar o aplicativo de demonstração do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Aplicativo de demonstração do Azure Mobile Engagement
Publicamos um aplicativo de demonstração do Azure Mobile Engagement para as plataformas **iOS**, **Android** e **Windows** para ajudá-lo encontrar recursos úteis e saber mais sobre o Mobile Engagement.

O aplicativo ajuda você a:

* Encontrar com facilidade links úteis para recursos do Mobile Engagement, como vídeos, documentação, fórum de suporte e o local para gerar solicitações de recursos.
* Experimente as notificações de exemplo com suporte do Mobile Engagement para ter ideias para seus próprios aplicativos móveis.
* Use uma implementação de referência para estudar como implementar o Mobile Engagement em seu próprio aplicativo. Você pode aprender a:
  
  * Coletar dados de análise.
  * Implementar cenários de notificação avançada de tipos, como *Tela inteira intersticial* ou *Pop-up*.
  * Implementar pesquisas e sondagens.
  * Implementar cenários de push e dados por push silencioso.   

## <a name="app-installation"></a>Instalação do aplicativo
Este aplicativo está disponível nas seguintes lojas de aplicativos:

* **Aplicativo de demonstração Universal do Windows**:
  
  * Baixe o aplicativo da [Windows Store](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * O aplicativo foi desenvolvido como um aplicativo Universal do Windows 10. O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **Aplicativo de demonstração do iOS**:
  
  * Baixe o aplicativo na [Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * O aplicativo foi desenvolvido no iOS Swift. O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Aplicativo de demonstração do Android**:
  
  * Baixe o aplicativo na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicativo de demonstração Universal do Windows][1]

![Aplicativo de demonstração do iOS][2]
![Aplicativo de demonstração Android][3]

## <a name="usage"></a>Uso
Você pode usar esses aplicativos das seguintes formas:

**Baixe o aplicativo em seu dispositivo por meio dos links da loja de aplicativos (fornecidos anteriormente):**

> [!IMPORTANT]
> Você não precisa de uma conta do Azure ou de conectar o aplicativo a um back-end. O aplicativo funciona de forma independente.
> 
> 

* Assim que o aplicativo estiver em seu dispositivo, você poderá percorrer os links no menu do lado esquerdo para encontrar os recursos úteis sobre o Mobile Engagement.
* Adicionamos o [feed RSS do serviço](https://aka.ms/azmerssfeed) neste aplicativo para que você esteja sempre atualizado sobre as últimas atualizações do produto.
* Também é possível percorrer os cenários de notificação de exemplo para experimentar os tipos de notificações que têm suporte do Mobile Engagement para cada plataforma. Essas notificações podem ser experimentadas localmente, ou seja, você pode clicar nos botões nas telas para mostrar a experiência de notificações, que é idêntica a quando você envia as notificações por meio da plataforma Mobile Engagement.

![Menu do aplicativo para Windows][4]

![Menu de aplicativo para iOS][5]
![Menu de aplicativo para Android][6]

**Baixe o código-fonte dos links do GitHub (fornecidos anteriormente):**

* Depois de baixar o código-fonte, abra-o no respectivo ambiente de desenvolvimento: XCode para iOS, Android Studio para Android e Visual Studio para Windows.
* Em seguida, siga nossas [etapas básicas de integração do SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que você possa se conectar a esse aplicativo em sua própria instância de back-end do Mobile Engagement.
  * Você precisa configurar uma cadeia de conexão no aplicativo.
  * Também é necessário configurar a plataforma de notificação por push do aplicativo.
* Você observará que o aplicativo em si é instrumentado com o Mobile Engagement. Portanto, ao abrir o aplicativo após sua conexão ao back-end, você poderá ver a sessão do usuário, as atividades, os eventos e, assim por diante, na guia **Monitorar** .
* Você também poderá enviar notificações para este aplicativo de sua própria instância do Mobile Engagement, em vez de usar as notificações locais.
  
  * Aqui é possível adicionar seu dispositivo como um dispositivo de teste usando o item de menu **Obter a Identificação do Dispositivo** no aplicativo. Isso fornece uma identificação do dispositivo que você registra em seguida como um dispositivo de teste com sua instância de back-end da plataforma.
    
    ![Identificação do dispositivo no Windows][7]
    
    ![ID do dispositivo no iOS][8]
    ![ID do dispositivo no Android][9]

## <a name="key-features-of-the-demo-app"></a>Principais recursos do aplicativo de demonstração
* Como mencionamos anteriormente, com este aplicativo, você tem todos os principais recursos do Mobile Engagement ao seu alcance. Você pode percorrer os links no menu à esquerda.
* Você pode experimentar as notificações fora do aplicativo de cada plataforma. Essas notificações podem ser entregues como **Somente notificação**, nas quais um clique na notificação fará com que uma tela nativa do aplicativo seja aberta (usando a **vinculação profunda**) ou como um **comunicado da Web**, no qual você pode entregar o conteúdo HTML adicional do back-end do Mobile Engagement a ser exibido quando a notificação é clicada.
  
    ![Notificações fora do aplicativo][29]
* No iOS, você terá de fechar o aplicativo para ver as notificações por push fora do aplicativo ou do sistema. É possível examinar a implementação aqui para adicionar os **Botões de ação**, como aqueles adicionados nesta notificação fora do aplicativo para *Comentários* e *Compartilhamento* (de forma que o usuário possa tomar uma ação diretamente na própria notificação).
  
    ![Notificações fora do aplicativo no iOS][11] ![Tela de notificação fora do aplicativo no iOS][14]
* No Android, as opções com suporte são a adição de texto multilinha (**Texto grande**) ou de uma imagem de notificação (**Visão geral**) à notificação, juntamente com os **Botões de ação** (quando tiverem suporte do iOS).
  
    ![Notificações fora do aplicativo no Android][12] ![Tela de notificação fora do aplicativo no Android][15]
* No Windows 10, é possível ver a aparência das notificações no PC. Essa notificação também será exibida na **Central de Notificações**do Windows 10. Não há suporte para a adição de **Botões de ação** com o SDK do Windows no momento.
  
    ![Notificações fora do aplicativo no Windows][10] ![Tela fora do aplicativo no Windows][13]
* Você pode experimentar as notificações padrão “no aplicativo” de cada plataforma. Essa é uma experiência de duas etapas em que uma janela **Notificação** é exibida primeiro. Quando você clica nela, ela abre um **Comunicado**de tela inteira, conforme exibido na captura de tela a seguir. O conteúdo deste comunicado é recebido de sua instância de back-end do Mobile Engagement. O SDK tem os modelos para notificações e comunicados. Você pode personalizá-los com facilidade, conforme mostrado neste aplicativo de demonstração com a adição de nosso logotipo e cores.  
  
    ![Notificações no aplicativo no Windows][16]
  
    ![Notificações no aplicativo no iOS][17]  ![Notificações no aplicativo no Android][18]
  
    **iOS**, **Android**
* Você também pode usar o recurso **Categoria** do Mobile Engagement para personalizar essa experiência padrão. No aplicativo de demonstração, demonstramos duas maneiras de alterar a experiência das notificações. Observe que ainda não há suporte para o recurso Categoria no SDK do Windows.
  
    **Tela inteira intersticial:**
  
    ![Notificação no aplicativo - Categoria intersticial][30]
  
    ![Categoria intersticial no iOS][21]     ![Categoria intersticial no Android][22]
  
    **Notificação de pop-up:**
  
    ![Notificação no aplicativo - Categoria pop-up][31]
  
    ![Notificação de pop-up no iOS][19]    ![Notificação de pop-up no Android][20]

**iOS**, **Android**

* O Mobile Engagement também dá suporte a um tipo especializado de notificação no aplicativo chamado **Sondagens**. Isso permite que você envie pesquisas rápidas para usuários segmentados de seu aplicativo. É possível adicionar perguntas e opções para cada pergunta, como mostrado na captura de tela a seguir. Em seguida, isso será exibido como uma notificação no aplicativo para o usuário do aplicativo.   
  
    ![Notificações de sondagem][32]
  
    ![Pesquisa no Windows][26]
  
    ![Pesquisa no iOS][27]   ![Pesquisa no Android][28]

**iOS**, **Android**

* O Mobile Engagement também dá suporte à notificações de **Push de Dados** silenciosas. Com essas notificações, é possível enviar dados de seu serviço (como os dados JSON no exemplo a seguir), que você pode manipular em seu aplicativo e executar alguma ação. Um exemplo é como estamos alterando o preço de um item de forma seletiva, usando a notificação por push de dados.
  
    ![Notificação por push de dados][33]
  
    ![Notificação por push de dados no Windows][23]
  
    ![Notificação por push de dados no iOS][24]  ![Notificação por push de dados no Android][25]

**iOS**, **Android**

> [!NOTE]
> Você pode exibir instruções passo a passo detalhadas de qualquer uma dessas notificações clicando em **Clique aqui para obter instruções sobre como enviar essas notificações da plataforma Mobile Engagement** em qualquer tela de notificação de exemplo.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

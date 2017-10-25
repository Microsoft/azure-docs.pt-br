---
title: "Introdução aos Aplicativos Móveis usando o Xamarin.Forms"
description: "Siga este tutorial para começar a usar os Aplicativos Móveis para desenvolvimento do Xamarin.Forms"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-xamarinforms-app"></a>Criar um aplicativo Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel do Xamarin.Forms usando o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure como back-end. Você cria um novo back-end do Aplicativo Móvel e um aplicativo de lista de tarefas pendentes Xamarin.Forms que armazena dados do aplicativo no Azure.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para o Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para saber mais, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Para saber mais, confira a página [Configurar e instalar o Visual Studio e o Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Um Mac com Xcode v7.0 ou posterior e o Xamarin Studio Community instalados. Para saber mais, confira [Configurar e instalar o Visual Studio e o Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configurar, instalar e verificar para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicativos Móveis

Para criar um novo back-end de Aplicativos Móveis, faça o seguinte:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você já configurou um back-end de Aplicativos Móveis que os aplicativos clientes móveis podem usar. Em seguida, você baixa um projeto do servidor para um back-end simples da "lista de tarefas" e o publica no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor

Para configurar o projeto de servidor para usar o back-end Node.js ou .NET, faça o seguinte:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Baixar e executar a solução de Xamarin.Forms

Você pode baixar a solução de duas maneiras. Baixe-a em um Mac e abra-a no Xamarin Studio, ou baixe-a em um computador com Windows e abra-a no Visual Studio usando um Mac em rede a fim de compilar o aplicativo para iOS. Para saber mais, confira a página [Configurar e instalar o Visual Studio e o Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

Em um computador Mac ou Windows, faça o seguinte:

1. Vá para o [Portal do Azure].

2. Na folha **Configurações** do seu aplicativo móvel, em **Móvel**, selecione **Introdução** > **Xamarin.Forms**. Na **etapa 3**, selecione **Criar um novo aplicativo**e selecione **Baixar**.

   Essa ação baixa um projeto que contém um aplicativo cliente conectado ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

3. Extraia o projeto que você baixou e abra-o no Xamarin Studio (Mac) ou no Visual Studio (Windows).

   ![Projeto extraído no Xamarin Studio][9]

   ![Projeto extraído no Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto do iOS
Nesta seção, você executará o projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

#### <a name="in-xamarin-studio"></a>No Xamarin Studio
1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir Como Projeto de Inicialização**.

2. No menu **Executar**, selecione **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

#### <a name="in-visual-studio"></a>No Visual Studio
1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto do iOS.

4. Para compilar o projeto e iniciar o aplicativo no emulador do iPhone, selecione a tecla **F5**.

   > [!NOTE]
   > Se você está com dificuldades na criação do projeto, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Os projetos de início rápido podem ser lentos na atualização para as versões mais recentes.    
   >
   >

5. No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

    ![][10]

    Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

    > [!NOTE]
    > Você encontrará o código que acessa o back-end dos Aplicativos Móveis no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.
    >
    >

## <a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto do Android
Nesta seção, você executará o projeto droid Xamarin para Android. Você poderá ignorá-la se não estiver trabalhando com dispositivos Android.

#### <a name="in-xamarin-studio"></a>No Xamarin Studio

1. Clique com o botão direito do mouse no projeto do Android e selecione **Definir Como Projeto de Inicialização**.

2. Para compilar o projeto e iniciar o aplicativo em um emulador do Android,no menu **Executar**, selecione **Iniciar Depuração**.

#### <a name="in-visual-studio"></a>No Visual Studio

1. Clique com o botão direito do mouse no projeto do Android (Droid) e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto do Android.

4. Para compilar o projeto e iniciar o aplicativo no emulador do Android, selecione a tecla **F5**.

   > [!NOTE]
   > Se você está com dificuldades na criação do projeto, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Os projetos de início rápido podem ser lentos na atualização para as versões mais recentes.    
   >
   >

5. No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

    ![][11]
    
    Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.
    
    > [!NOTE]
    > Você encontrará o código que acessa o back-end dos Aplicativos Móveis no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto do Windows

Nesta seção, você executará o projeto WinApp Xamarin para dispositivos Windows. Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

#### <a name="in-visual-studio"></a>No Visual Studio

1. Clique com o botão direito do mouse em qualquer um dos projetos do Windows e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto do Windows que você escolheu.

4. Para compilar o projeto e iniciar o aplicativo no emulador do Windows, selecione a tecla **F5**.

   > [!NOTE]
   > Se você está com dificuldades na criação do projeto, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Os projetos de início rápido podem ser lentos na atualização para as versões mais recentes.    
   >
   >

5. No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

    Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.
    
    ![][12]
    
    > [!NOTE]
    > Você encontrará o código que acessa o back-end dos Aplicativos Móveis no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.
    >
    >

## <a name="next-steps"></a>Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao aplicativo](app-service-mobile-xamarin-forms-get-started-push.md)  
  Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline para o aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, você pode exibir, adicionar ou modificar os dados do aplicativo móvel mesmo quando não há nenhuma conexão de rede.

* [Usar o cliente gerenciado para Aplicativos Móveis](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Saiba como trabalhar com o SDK do cliente gerenciado em seu aplicativo Xamarin.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/

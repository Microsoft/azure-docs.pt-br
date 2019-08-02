---
title: Introdução aos aplicativos móveis do Azure para aplicativos Xamarin Android
description: Siga este tutorial para começar a usar os Aplicativos Móveis do Azure para desenvolvimento Android Xamarin
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: eed900ee54f62056eceeb35a43a4ba6526b049ca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447083"
---
# <a name="create-a-xamarinandroid-app"></a>Criar um Aplicativo Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started) hoje mesmo.
>

## <a name="overview"></a>Visão Geral
Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin Android. Para saber mais, confira [O que são Aplicativos Móveis](app-service-mobile-value-prop.md).

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para aplicativos Xamarin.Android.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se você não tiver uma conta, inscreva-se para uma avaliação do Azure e obtenha até 10 aplicativos móveis gratuitos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio com Xamarin. Veja [Setup and Install for Visual Studio and Xamarin](/visualstudio/cross-platform/setup-and-install) (Configuração e instalação para Visual Studio e Xamarin).

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end de aplicativo móvel do Azure
Siga estas etapas para criar um back-end de aplicativo móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, baixe um projeto do servidor para um back-end simples da "lista de tarefas" e publique-o no Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Execute o aplicativo xamarin. Android
1. Abra o projeto xamarin. Android.

2. Vá para o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Sobre o `Overview` folha, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o nome do site para o meu nome de aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `ToDoActivity.cs` nesta pasta - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na `ToDoActivity` classe, substitua `ZUMOAPPURL` variável com o ponto de extremidade público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    torna-se
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Pressione a tecla F5 para implantar e executar o aplicativo.

6. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e depois clique no botão **Adicionar**.

    ![][10]

    Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados aparecem na lista.

   > [!NOTE]
   > Você pode examinar o código que acessa o back-end do aplicativo móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.
   
## <a name="troubleshooting"></a>solução de problemas
Se você tiver problemas para compilar a solução, execute o gerenciador de pacotes NuGet e atualize os pacotes de suporte do `Xamarin.Android`. Os projetos de Início Rápido nem sempre incluem as últimas versões.

Observe que todos os pacotes de suporte referenciados em seu projeto devem ter a mesma versão. O [pacote NuGet de Aplicativos Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) depende de `Xamarin.Android.Support.CustomTabs` para a plataforma Android e, portanto, se seu projeto usar pacotes de suporte mais recentes, será necessário instalar diretamente esse pacote com a versão necessária para evitar conflitos.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

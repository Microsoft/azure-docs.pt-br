---
title: Criar um aplicativo de registro para Android com reagir
titleSuffix: Azure Cognitive Services
description: Saiba como configurar seu ambiente de desenvolvimento e implantar um aplicativo de registro facial para obter consentimento dos clientes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 085dd18214f795566669fb862bba63b67eb9115d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350357"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Criar um aplicativo de registro para Android com reagir

Este guia mostrará a você como começar a usar o aplicativo de registro de face de exemplo. O aplicativo demonstra as práticas recomendadas para obter um consentimento significativo para registrar usuários em um serviço de reconhecimento facial e adquirir dados de face de alta precisão. Um sistema integrado pode usar um aplicativo de registro como este para fornecer controle de acesso sem toque, verificação de identidade, acompanhamento de presença, quiosque de personalização ou verificação de identidade, com base em seus dados de face.

Quando iniciado, o aplicativo mostra aos usuários uma tela de consentimento detalhada. Se o usuário der consentimento, o aplicativo solicitará um nome de usuário e uma senha e, em seguida, capturará uma imagem de face de alta qualidade usando a câmera do dispositivo.

O aplicativo de registro de exemplo é escrito usando JavaScript e a estrutura nativa reagir. No momento, ele pode ser implantado em dispositivos Android; mais opções de implantação estão chegando no futuro.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/).  
* Depois de ter sua assinatura do Azure, [crie um recurso de face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) no portal do Azure para obter sua chave e ponto de extremidade. Após a implantação, selecione **Ir para recurso**.  
  * Você precisará da chave e do ponto de extremidade do recurso que você criou para conectar seu aplicativo ao API de Detecção Facial.  
  * Para desenvolvimento e teste locais, você pode colar a chave de API e o ponto de extremidade no arquivo de configuração. Para a implantação final, armazene a chave de API em um local seguro e nunca no código.  

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API do Serviço Cognitivo. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Recomendamos a regeneração regular dessas chaves. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Clone o repositório Git para o [aplicativo de registro de exemplo](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Para configurar o ambiente de desenvolvimento, siga a documentação nativa reagir da <a href="https://reactnative.dev/docs/environment-setup"  title=" documentação nativa "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Selecione **reagir o início rápido da CLI nativa** como seu sistema operacional de desenvolvimento e selecione **Android** como o so de destino. Conclua as seções **instalando dependências** e **ambiente de desenvolvimento do Android**.
1. Abra o env.jsno arquivo em seu editor de texto preferido, como [Visual Studio Code](https://code.visualstudio.com/)e adicione o ponto de extremidade e a chave. Você pode obter o ponto de extremidade e a chave na portal do Azure na guia **visão geral** do recurso. Esta etapa é apenas para fins de teste locais &mdash; não verifique sua chave de API de detecção facial em seu repositório remoto.
1. Execute o aplicativo usando o emulador de dispositivo virtual Android do Android Studio ou seu próprio dispositivo Android. Para testar seu aplicativo em um dispositivo físico, siga a <a href="https://reactnative.dev/docs/running-on-device"  title=" documentação nativa reagir da documentação nativa "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>Criar uma experiência de registro  

Agora que você configurou o aplicativo de registro de exemplo, você pode adaptá-lo às suas próprias necessidades de experiência de registro.

Por exemplo, talvez você queira adicionar informações específicas da situação em sua página de consentimento:

> [!div class="mx-imgBorder"]
> ![página de consentimento do aplicativo](./media/enrollment-app/1-consent-1.jpg)

O serviço fornece verificações de qualidade de imagem para ajudá-lo a escolher se a imagem é de qualidade suficiente para registrar o cliente ou tentar o reconhecimento facial. Este aplicativo demonstra como acessar quadros da câmera do dispositivo, selecionar os quadros de qualidade mais alta e registrar a face detectada no serviço de API de Detecção Facial. 

Muitos problemas de reconhecimento facial são causados por imagens de referência de baixa qualidade. Alguns fatores que podem prejudicar o desempenho do modelo são:
* Tamanho da face (rostos distantes da câmera)
* Orientação da face (faces viradas ou inclinadas para longe da câmera)
* Condições de iluminação insuficiente (pouca luz ou baixa) em que a imagem pode ser exposta insatisfatoriamente ou ter muito ruído
* Oclusão (faces parcialmente ocultas ou obstruídas), incluindo acessórios como chapéus ou óculos de semirimm)
* Desfoque (por exemplo, por movimento rápido de face quando a fotografia foi tirada). 

> [!div class="mx-imgBorder"]
> ![página de instrução de captura de imagem de aplicativo](./media/enrollment-app/4-instruction.jpg)

Observe que o aplicativo também oferece funcionalidade para excluir o registro do usuário e a opção de registrar novamente.

> [!div class="mx-imgBorder"]
> ![página de gerenciamento de perfil](./media/enrollment-app/10-manage-2.jpg)

Para estender a funcionalidade do aplicativo para cobrir a experiência completa de registro, leia a [visão geral](enrollment-overview.md) de recursos adicionais para implementar e práticas recomendadas.

## <a name="deploy-the-enrollment-app"></a>Implantar o aplicativo de registro

### <a name="android"></a>Android

Primeiro, verifique se seu aplicativo está pronto para implantação de produção: Remova as chaves ou segredos do código do aplicativo e verifique se você seguiu as [práticas recomendadas de segurança](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Quando estiver pronto para liberar seu aplicativo para produção, você gerará um arquivo APK pronto para liberação, que é o formato de arquivo de pacote para aplicativos Android. Esse arquivo APK deve ser assinado com uma chave privada. Com esse Build de versão, você pode começar a distribuir o aplicativo diretamente para seus dispositivos. 

Siga a <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" documentação preparar para a versão "  target="_blank"> preparar para <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a versão para saber como gerar uma chave privada, assinar seu aplicativo e gerar uma versão apk.  

Depois de criar um APK assinado, consulte a <a href="https://developer.android.com/studio/publish"  title=" documentação publicar seu aplicativo "  target="_blank"> publicar seu aplicativo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> para saber mais sobre como liberar seu aplicativo.

## <a name="next-steps"></a>Próximas etapas  

Neste guia, você aprendeu a configurar seu ambiente de desenvolvimento e a começar a usar o aplicativo de registro de exemplo. Se você for novo para reagir nativamente, poderá ler seus [documentos de introdução](https://reactnative.dev/docs/getting-started) para saber mais sobre informações básicas. Também pode ser útil se familiarizar com [API de detecção facial](Overview.md). Leia as outras seções na documentação do aplicativo de registro antes de começar o desenvolvimento.
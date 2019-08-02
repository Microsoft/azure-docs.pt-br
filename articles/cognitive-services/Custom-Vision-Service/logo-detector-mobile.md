---
title: 'Tutorial: Usar o detector de logotipo personalizado para reconhecer serviços do Azure – Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você acompanhará as etapas de um aplicativo de exemplo que usa a Visão Personalizada do Azure como parte de um cenário de detecção de logotipo. Saiba como a Visão Personalizada é usada com outros componentes para entregar um aplicativo completo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b0a5b110951d7b13110fab935d5ca1333f7f8c1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564202"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconhecer logotipos de serviços do Azure em imagens da câmera

Neste tutorial, você explorará um aplicativo de exemplo que usa a Visão Personalizada do Azure como parte de um cenário maior. O aplicativo de Provisionamento Visual e IA, um aplicativo Xamarin.Forms para plataformas móveis, analisa imagens da câmera de logotipos de serviços do Azure e, em seguida, implanta os serviços reais na conta do Azure do usuário. Aqui você aprenderá como ele usa a Visão Personalizada em coordenação com outros componentes para entregar um aplicativo útil de ponta a ponta. Você pode executar todo o cenário do aplicativo por conta própria ou apenas concluir a parte da Visão Personalizada da instalação e explorar como o aplicativo a utiliza.

Este tutorial mostrará como:

> [!div class="checklist"]
> - Criar um detector de objetos personalizado para reconhecer logotipos de serviços do Azure.
> - Conectar seu aplicativo à Pesquisa Visual Computacional e à Visão Personalizada do Azure.
> - Criar uma conta de entidade de serviço do Azure para implantar serviços do Azure por meio do aplicativo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/)
- A carga de trabalho do Xamarin para Visual Studio (confira [Instalando o Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Um emulador do iOS ou o Android Emulator para Visual Studio
- A [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Caso queira usar o aplicativo Web fornecido, clone ou baixe o código-fonte do aplicativo no repositório [Provisionamento Visual de IA](https://github.com/Microsoft/AIVisualProvision) no GitHub. Abra o arquivo *Source/VisualProvision.sln* no Visual Studio. Mais tarde você editará alguns dos arquivos de projeto para poder executar o aplicativo.

## <a name="create-an-object-detector"></a>Criar um detector de objeto

Entre no [site da Visão Personalizada](https://customvision.ai/) e crie um projeto. Especifique um projeto de Detecção de Objetos e use o domínio do Logotipo; isso permitirá que o serviço use um algoritmo otimizado para a detecção de logotipo. 

![Janela Novo projeto no site da Visão Personalizada no navegador Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Em seguida, treine o algoritmo de detecção de logotipo carregando imagens de logotipos de serviços do Azure e marcando-os manualmente. O repositório de AIVisualProvision inclui um conjunto de imagens de treinamento que pode ser usado. No site, selecione o botão **Adicionar imagens** na guia **Imagens de Treinamento**. Em seguida, acesse a pasta **Documents/Images/Training_DataSet** do repositório. Deve-se marcar manualmente os logotipos em cada imagem. Portanto, se estiver apenas testando este projeto, carregue apenas um subconjunto das imagens. Carregue pelo menos 15 instâncias de cada tag que você planeja usar.

Depois de carregar as imagens de treinamento, selecione a primeira na exibição. A janela de marcação será exibida. Desenhe caixas e atribua marcas a cada logotipo em cada imagem. 

![Marcação de logotipo no site da Visão Personalizada](media/azure-logo-tutorial/tag-logos.png)

O aplicativo está configurado para funcionar com cadeias de caracteres de tag específicas. Você encontrará as definições no arquivo *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Depois de marcar uma imagem, vá para a direita para marcar a próxima. Feche a janela de marcação quando terminar.

## <a name="train-the-object-detector"></a>Treinar o detector de objeto

No painel esquerdo, defina a opção **Marcações** como **Marcadas** para exibir suas imagens. Em seguida, selecione o botão verde na parte superior da página para treinar o modelo. O algoritmo fará o treinamento para reconhecer as mesmas marcas em novas imagens. Também testará o modelo em algumas das imagens existentes para gerar pontuações de precisão.

![O site da Visão Personalizada, na guia Imagens de Treinamento. Nesta captura de tela, o botão Treinar está contornado](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obter a URL de previsão

Depois que o modelo for treinado, você estará pronto para integrá-lo ao aplicativo. Você precisará obter a URL do ponto de extremidade (o endereço do modelo que será consultado pelo aplicativo) e a chave de previsão (para permitir acesso ao aplicativo às solicitações de previsão). Na guia **Desempenho**, selecione o botão **URL de Previsão** na parte superior da página.

![O site da Visão Personalizada, mostrando uma janela de API de Previsão que exibe um endereço de URL e uma chave de API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie a URL do arquivo de imagem e o valor **Prediction-Key** para os campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Examinar o uso da Visão Personalizada

Abra o arquivo *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* para ver como o aplicativo usa a URL do ponto de extremidade e a chave da Visão Personalizada. O método **PredictImageContentsAsync** usa um fluxo de bytes de um arquivo de imagem, juntamente com um token de cancelamento (para o gerenciamento de tarefas assíncronas), chama a API de previsão da Visão Personalizada e retorna o resultado da previsão. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Esse resultado assume a forma de uma instância de **PredictionResult**, que por si só contém uma lista de instâncias de **Prediction**. Uma **Previsão** contém uma tag detectada e sua localização de caixa delimitadora na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Para saber mais sobre como o aplicativo manipula esses dados, comece com o método **GetResourcesAsync**. Esse método é definido no arquivo *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Adicionar a Pesquisa Visual Computacional

A parte da Visão Personalizada do tutorial foi concluída. Se você quiser executar o aplicativo, será necessário integrar o serviço da Pesquisa Visual Computacional também. O aplicativo usa o recurso de reconhecimento de texto da Pesquisa Visual Computacional para complementar o processo de detecção de logotipos. Um logotipo do Azure pode ser reconhecido por sua aparência *ou* pelo texto impresso próximo a ele. Ao contrário dos modelos da Visão Personalizada, a Pesquisa Visual Computacional é pré-treinada para executar determinadas operações em imagens ou vídeos.

Assine o serviço de Pesquisa Visual Computacional para obter uma chave e uma URL do ponto de extremidade. Para obter ajuda nesta etapa, consulte [Como obter chaves de assinatura](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![O serviço de Pesquisa Visual Computacional no portal do Azure, com o menu Início Rápido selecionado. Um link para as chaves está contornado, bem como a URL de ponto de extremidade da API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra o arquivo *Source\VisualProvision\AppSettings.cs* e preencha as variáveis `ComputerVisionEndpoint` e `ComputerVisionKey` com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

O aplicativo exige uma conta de entidade de serviço do Azure para implantar serviços em sua assinatura do Azure. Uma entidade de serviço permite que você delegue permissões específicas a um aplicativo usando o controle de acesso baseado em função. Para obter mais informações, consulte o [guia de entidades de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Crie uma entidade de serviço usando o Azure Cloud Shell ou a CLI do Azure, conforme mostrado aqui. Para começar, entre e selecione a assinatura que você deseja usar.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie sua entidade de serviço. (Esse processo pode levar algum tempo para ser concluído).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão bem-sucedida, você deverá ver a saída JSON a seguir, incluindo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Anote os valores `clientId` e `tenantId`. Adicione-os aos campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Execute o aplicativo

Nesta altura, você permitiu acesso ao aplicativo ao seguinte:

- Um modelo treinado da Visão Personalizada
- O serviço de Pesquisa Visual Computacional
- Uma conta de entidade de serviço

Siga estas etapas para executar o aplicativo:

1. No Gerenciador de Soluções do Visual Studio, selecione o projeto **VisualProvision.Android** ou o **VisualProvision.iOS**. Escolha um emulador correspondente ou dispositivo móvel conectado no menu suspenso na barra de ferramentas principal. Em seguida, execute o arquivo.

    > [!NOTE]
    > Você precisará de um dispositivo MacOS para executar um emulador de iOS.

1. Na primeira tela, insira a ID do cliente da entidade de serviço, a ID de locatário e a senha. Selecione o botão **Logon**.

    > [!NOTE]
    > Em alguns emuladores, o botão **Logon** pode não estar ativado nesta etapa. Se isso acontecer, pare o aplicativo, abra o arquivo *Source/VisualProvision/Pages/LoginPage.xaml*, localize o elemento `Button` rotulado como **LOGIN BUTTON**, remova a seguinte linha e, em seguida, execute novamente o aplicativo.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![A tela do aplicativo mostrando campos para as credenciais da entidade de serviço](media/azure-logo-tutorial/app-credentials.png)

1. Na próxima tela, selecione sua assinatura do Azure no menu suspenso. (Esse menu deve conter todas as assinaturas às quais a entidade de serviço tem acesso). Selecione o botão **Continuar**. Nesta altura, o aplicativo poderá solicitar que você permita acesso à câmera e ao armazenamento de fotos do dispositivo. Conceda as permissões de acesso.

    ![A tela do aplicativo, mostrando um campo suspenso para a assinatura de destino do Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. A câmera do dispositivo será ativada. Tire uma foto de um dos logotipos de serviços do Azure que você treinou. Uma janela de implantação solicitará que você selecione uma região e um grupo de recursos para os novos serviços (como você faria se os estivesse implantando no portal do Azure). 

    ![Uma tela da câmera do smartphone focada em dois recortes de papel de logotipos do Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Uma tela do aplicativo mostrando campos para a região de implantação e o grupo de recursos](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você seguiu todas as etapas deste cenário e usou o aplicativo para implantar serviços do Azure em sua conta, acesse o [portal do Azure](https://ms.portal.azure.com/). No portal, cancele os serviços que você não deseja usar.

Se você pretende criar seu próprio projeto de detecção de objetos com a Visão Personalizada, talvez queira excluir o projeto de detecção de logotipos criado neste tutorial. Uma avaliação gratuita da Visão Personalizada permite apenas dois projetos. Para excluir o projeto de detecção de logotipos, no [site da Visão Personalizada](https://customvision.ai), abra **Projetos** e, em seguida, selecione o ícone de Lixeira em **Meu Novo Projeto**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e explorou um aplicativo Xamarin.Forms completo que usa o serviço de Visão Personalizada para detectar logotipos em imagens de câmeras de dispositivos móveis. Em seguida, conheça as melhores práticas para a criação de um modelo da Visão Personalizada, de modo que quando você criar um para seu próprio aplicativo, você possa torná-lo eficiente e preciso.

> [!div class="nextstepaction"]
> [Como melhorar o classificador](getting-started-improving-your-classifier.md)

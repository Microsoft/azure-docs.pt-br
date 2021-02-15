---
title: 'Tutorial: exemplo de Alertas Visuais da IoT'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, a Visão Personalizada será usada com um dispositivo IoT para reconhecer e relatar estados visuais a partir do feed de vídeo de uma câmera.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020168"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Tutorial: usar a Visão Personalizada com um dispositivo IoT para relatar estados visuais

Este exemplo de aplicativo ilustra como usar a Visão Personalizada para treinar um dispositivo com uma câmera para detectar estados visuais. Você pode executar esse cenário de detecção em um dispositivo IoT usando um modelo ONNX exportado.

Um estado visual descreve o conteúdo de uma imagem: uma sala vazia ou uma sala com pessoas, uma garagem vazia ou uma garagem com um caminhão, e assim por diante. Na imagem abaixo, você pode ver que o aplicativo detecta quando um banana ou uma maçã é colocada na frente da câmera.

![Animação de uma interface do usuário rotulando as frutas na frente da câmera](./media/iot-visual-alerts-tutorial/scoring.gif)

Este tutorial mostrará como:
> [!div class="checklist"]
> * Configurar o aplicativo de exemplo para usar seus próprios recursos de Visão Personalizada e Hub IoT.
> * Usar o aplicativo para treinar seu projeto de Visão Personalizada.
> * Usar o aplicativo para pontuar novas imagens em tempo real e enviar os resultados para o Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Este precisa ser um projeto de classificação de imagem **Compacto**, pois vamos exportar o modelo para o ONNX mais tarde.
* Você também precisará [criar um recurso do Hub IoT](https://ms.portal.azure.com/#create/Microsoft.IotHub) no Azure.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com/downloads/)
* Se desejar, um dispositivo IoT que execute o Windows 10 IoT Core versão 17763 ou superior. Você também pode executar o aplicativo diretamente do seu PC.
   * Para o Raspberry Pi 2 e 3, é possível configurar o Windows 10 diretamente do aplicativo Painel de IoT. Para outros dispositivos, como o DrangonBoard, você precisará instalar a imagem usando o [método eMMC](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Se precisar de ajuda para configurar um novo dispositivo, confira [Configurando seu dispositivo](/windows/iot-core/tutorials/quickstarter/devicesetup) na documentação do Windows IoT.

## <a name="about-the-visual-alerts-app"></a>Sobre o aplicativo Alertas Visuais

O aplicativo Alertas Visuais da IoT é executado em loop contínuo, alternando entre quatro estados diferentes, conforme apropriado:

* **Nenhum Modelo**: um estado não operacional. O aplicativo será suspenso continuamente por um segundo e verificará a câmera.
* **Capturando imagens de treinamento**: nesse estado, o aplicativo captura uma imagem e a carrega como uma imagem de treinamento para o projeto Visão Personalizada de destino. O aplicativo é suspenso por 500 ms e repete a operação até que a quantidade de imagens desejada do conjunto seja capturada. Em seguida, aciona o treinamento do modelo Visão Personalizada.
* **Aguardando o modelo treinado**: nesse estado, o aplicativo chama a API da Visão Personalizada a cada segundo para verificar se o projeto de destino contém uma iteração treinada. Ao encontrar uma, baixa o modelo do ONNX correspondente em um arquivo local e alterna para o estado **Pontuação**.
* **Pontuação**: nesse estado o aplicativo usa o Azure Machine Learning para avaliar um único quadro da câmera em relação ao modelo local do ONNX. A classificação da imagem resultante é exibida na tela e enviada como uma mensagem ao Hub IoT. O aplicativo é suspenso por um segundo antes de pontuar uma nova imagem.

## <a name="examine-the-code-structure"></a>Examinar a estrutura de código

Os arquivos a seguir tratam da principal funcionalidade do aplicativo.

| Arquivo | Descrição |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Esse arquivo define a interface do usuário XAML. Ele hospeda o controle da câmera Web e contém os rótulos usados para atualizações de status.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Esse código controla o comportamento da interface do usuário XAML. Ele contém o código de processamento da máquina de estado.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Essa classe é um wrapper que trata da integração ao Serviço de Visão Personalizada.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Essa classe é um wrapper que trata da integração ao Azure Machine Learning para carregamento do modelo do ONNX e da pontuação das imagens relacionadas a ele.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Essa classe é um wrapper que trata da integração ao Hub IoT para carregamento dos resultados da pontuação no Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Configurar o aplicativo Alertas Visuais

Siga estas etapas para instalar o aplicativo Alertas Visuais da IoT no seu PC ou dispositivo IoT.

1. Clone ou baixe o exemplo [IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) no GitHub.
1. Abra a solução _IoTVisualAlerts.sln_ no Visual Studio
1. Integre seu projeto de Visão Personalizada:
    1. No script _CustomVision\CustomVisionServiceWrapper.cs_, atualize a variável `ApiKey` com sua chave de treinamento.
    1. Em seguida, atualize a variável `Endpoint` com a URL do ponto de extremidade associada à sua chave.
    1. Atualize a variável `targetCVSProjectGuid` com a ID correspondente do projeto de Visão Personalizada que você deseja usar. 
1. Configure o recurso Hub IoT:
    1. No script _IoTHub\IotHubWrapper.cs_, atualize a variável `s_connectionString` com a cadeia de conexão apropriada para o seu dispositivo. 
    1. No portal do Azure, carregue a instância do Hub IoT, clique em **Dispositivos IoT** em **Exploradores**, selecione no dispositivo de destino (ou crie um, se necessário), e localize a cadeia de conexão em **Cadeia de Conexão Primária**. A cadeia de caracteres conterá o nome do Hub IoT, a identificação do dispositivo e a chave de acesso compartilhada; ela tem o seguinte formato: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Executar o aplicativo

Se você está executando o aplicativo em seu computador, selecione **Computador Local** para o dispositivo de destino no Visual Studio e selecione **x64** ou **x86** para a plataforma de destino. Em seguida, pressione F5 para executar o programa. O aplicativo deve iniciar e exibir o feed ao vivo da câmera, bem como uma mensagem de status.

Se estiver implantando em um dispositivo IoT com um processador ARM, você precisará selecionar **ARM** como a plataforma de destino e **Computador Remoto** como o dispositivo de destino. Forneça o endereço IP do seu dispositivo quando solicitado (ele deve estar na mesma rede que seu PC). Você pode obter o endereço IP do aplicativo Windows IoT padrão após iniciar o dispositivo e conectá-lo à rede. Pressione F5 para executar o programa.

Ao executar o aplicativo pela primeira vez, ele não terá nenhum conhecimento dos estados visuais. E exibirá uma mensagem de status informando que nenhum modelo está disponível. 

## <a name="capture-training-images"></a>Capturar imagens de treinamento

Para configurar um modelo, é preciso colocar o aplicativo no estado **Capturando imagens de treinamento**. Siga uma destas etapas:
* Se você está executando o aplicativo no PC, use o botão no canto superior direito da interface do usuário.
* Se está executando o aplicativo em um dispositivo IoT, chame o método `EnterLearningMode` no dispositivo por meio do Hub IoT. Você pode chamá-lo por meio da entrada de dispositivo no menu do Hub IoT no portal do Azure ou com uma ferramenta, como o [Gerenciador de Dispositivos do Hub IoT](https://github.com/Azure/azure-iot-sdk-csharp).
 
Quando o aplicativo entra no estado **Capturando imagens de treinamento**, vai capturar cerca de duas imagens a cada segundo, até que tenha atingido a quantidade alvo de imagens. Por padrão, a meta são 30 imagens, mas você pode definir esse parâmetro passando o número desejado como um argumento para o método `EnterLearningMode` do Hub IoT. 

Enquanto o aplicativo estiver capturando imagens, você deverá expor a câmera aos tipos de estado visual que deseja detectar (por exemplo, uma sala vazia, uma sala com pessoas, uma mesa vazia, uma mesa com um carrinho de brinquedo e assim por diante).

## <a name="train-the-custom-vision-model"></a>Treinar o modelo de Visão Personalizada

Depois que o aplicativo terminar de capturar as imagens, ele as carregará e, em seguida, alternará para o estado **Aguardando o modelo treinado**. Nesse momento, será necessário acessar o [Site Visão Personalizada](https://www.customvision.ai/) e criar um modelo baseado nas novas imagens de treinamento. A animação a seguir mostra um exemplo desse processo.

![Animação: marca de várias imagens de bananas](./media/iot-visual-alerts-tutorial/labeling.gif)

Para repetir esse processo com seu próprio cenário:

1. Entre no [Site Visão Personalizada](http://customvision.ai).
1. Localize seu projeto de destino, que agora deve ter todas as imagens de treinamento carregadas pelo aplicativo.
1. Para cada estado visual que você deseja identificar, selecione as imagens apropriadas e aplique a marca manualmente.
    * Por exemplo, se sua meta é distinguir entre uma sala vazia e uma sala com pessoas, é recomendável marcar cinco ou mais imagens com pessoas como uma nova classe, **Pessoas**, e marcar cinco ou mais imagens sem pessoas como a marca **Negativa**. Isso ajudará o modelo a diferenciar entre os dois estados.
    * Como outro exemplo, se sua meta é identificar quão cheia está uma prateleira, você pode usar as marcas **EmptyShelf**, **PartiallyFullShelf** e **FullShelf**.
1. Quando terminar, selecione o botão **Treinar**.
1. Quando o treinamento for concluído, o aplicativo detectará que uma iteração treinada está disponível. E iniciará o processo de exportar o modelo treinado para o ONNX e baixá-lo no dispositivo.

## <a name="use-the-trained-model"></a>Usar o modelo treinado

Depois que o aplicativo baixar o modelo treinado, ele mudará para o estado **Pontuação** e começará a pontuar imagens da câmera em um loop contínuo.

Para cada imagem capturada, o aplicativo exibirá a marca principal na tela. Se ele não reconhecer o estado visual, você verá o aviso de **Nenhuma correspondência**. O aplicativo também envia essas mensagens para o Hub IoT e, se houver uma classe sendo detectada, a mensagem incluirá o rótulo, a pontuação de confiança e uma propriedade chamada `detectedClassAlert`, que pode ser usada por clientes do Hub IoT interessados em fazer o direcionamento rápido de mensagens com base nas propriedades.

Além disso, o exemplo usa uma [biblioteca Sense HAT](https://github.com/emmellsoft/RPi.SenseHat) para detectar quando ele está em execução em um Raspberry Pi com uma unidade do Sense HAT, de modo que seja possível usá-la como um monitor de saída, definindo todas as luzes de exibição como vermelhas sempre que uma classe for detectada e brancas quando nada for detectado.

## <a name="reuse-the-app"></a>Reutilizar o aplicativo

Se quiser redefinir o aplicativo de volta para seu estado original, é possível fazer isso clicando no botão no canto superior direito da interface do usuário ou chamando o método `DeleteCurrentModel` por meio do Hub IoT.

Você pode repetir a etapa de carregamento das imagens de treinamento sempre que quiser, bastando para isso clicar no botão da interface do usuário no canto superior direito ou chamar o método `EnterLearningMode` novamente.

Se estiver executando o aplicativo em um dispositivo e precisar recuperar o endereço IP novamente (para estabelecer uma conexão remota por meio do [Cliente Remoto do Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), por exemplo), você poderá chamar o método `GetIpAddress` por meio do Hub IoT.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua seu projeto de Visão Personalizada se não quiser mais mantê-lo. No [Site Visão Personalizada](https://customvision.ai), navegue até **Projetos** e selecione a lata de lixo sob o novo projeto.

![Captura de tela de um painel rotulado Meu novo projeto com um ícone de lixeira](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e executou um aplicativo que detecta informações de estado visual em um dispositivo IoT e envia os resultados para o Hub IoT. Na próxima etapa, explore mais o código-fonte ou faça uma das alterações sugeridas abaixo.

> [!div class="nextstepaction"]
> [Exemplo do IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Adicione um método do Hub IoT para alternar o aplicativo diretamente para o estado **Aguardando o modelo treinado**. Dessa forma, você pode treinar o modelo com imagens que não são capturadas pelo próprio dispositivo e, em seguida, efetuar push do novo modelo para o dispositivo sob demanda.
* Siga o tutorial [Visualizar dados do sensor em tempo real](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) para criar um Painel do Power BI, a fim de visualizar os alertas do Hub IoT enviados pelo exemplo.
* Siga o tutorial [Monitoramento remoto de IoT](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) para criar um Aplicativo Lógico que responda aos alertas do Hub IoT quando forem detectados estados visuais.
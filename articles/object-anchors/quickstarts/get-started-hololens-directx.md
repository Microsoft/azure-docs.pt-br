---
title: 'Início Rápido: Criar um aplicativo do HoloLens com DirectX'
description: Neste início rápido, você aprenderá a criar um aplicativo do HoloLens usando as Âncoras de Objeto.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047602"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Início rápido: criar um aplicativo do HoloLens com as Âncoras de Objeto do Azure no C++/WinRT e no DirectX

Este início rápido aborda como criar um aplicativo do HoloLens usando as [Âncoras de Objeto do Azure](../overview.md) no C++/WinRT e no DirectX. As Âncoras de Objeto do Azure são um serviço de nuvem gerenciado que converte ativos 3D em modelos de IA que habilitam experiências de realidade misturada com reconhecimento de objeto para o HoloLens. Quando tiver terminado, você terá um aplicativo do HoloLens que pode detectar um objeto e sua pose em um aplicativo Holographic DirectX 11 (Universal do Windows).

Você aprenderá a:

> [!div class="checklist"]
> * Criar e carregar paralelamente um aplicativo do HoloLens
> * Detectar um objeto e visualizar o respectivo modelo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

* Um objeto físico no ambiente e o respectivo modelo 3D (do CAD ou digitalizado).
* Um computador Windows com o seguinte instalado:
  * <a href="https://git-scm.com" target="_blank">Git para Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento da Plataforma Universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)**
* Um dispositivo do HoloLens 2 que está atualizado e tem o [modo de desenvolvedor](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) habilitado.
  * Para atualizar para a versão mais recente do HoloLens, abra o aplicativo de **Configurações**, acesse **Atualização e Segurança** e, em seguida, selecione **Verificar atualizações**.

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Abra o `quickstarts/apps/directx/DirectXAoaSampleApp.sln` no Visual Studio.

Altere a **Configuração da Solução** para **Versão**, altere **Plataforma da Solução** para **ARM64** e selecione **Dispositivo** nas opções de destino da implantação. Em seguida, compile o projeto **AoaSampleApp** clicando com o botão direito do mouse sobre o projeto e selecionando **Compilar**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Configurar o projeto do Visual Studio para implantação":::

## <a name="deploy-the-app-to-hololens"></a>Implantar o aplicativo no HoloLens

Depois de compilar o projeto de exemplo com sucesso, você poderá implantar o aplicativo no HoloLens.

Ligue o dispositivo do HoloLens, entre nele e conecte-o ao computador usando um cabo USB. Verifique se o destino de implantação escolhido é **Device** (confira acima).

Clique com o botão direito do mouse em projeto **AoaSampleApp** e clique em **Implantar**, no menu pop-up, para instalar o aplicativo. Se nenhum erro for exibido na **Janela de Saída** do Visual Studio, o aplicativo será instalado no HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Implantar o aplicativo no HoloLens":::

Antes de iniciar o aplicativo, você precisará carregar um modelo de objeto. Siga as instruções da seção **Ingerir o modelo de objeto e detectar a respectiva instância**, abaixo.

Para inicializar e depurar o aplicativo, selecione **Depurar > Iniciar Depuração**. Para interromper o aplicativo, selecione **Parar Depuração** ou pressione **Shift + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Ingerir o modelo de objeto e detectar a respectiva instância

Será necessário criar um modelo de objeto para executar o aplicativo de exemplo. Suponha que você já tem um modelo de malha 3D do CAD ou digitalizado de um objeto em seu espaço. Confira o [Início rápido: ingestão de um modelo 3D](./get-started-model-conversion.md) sobre como criar um modelo.

Baixe esse modelo – **chair.ou**, em nosso caso – para o seu computador. Em seguida, no portal de dispositivo do HoloLens, selecione **Sistema > Explorador de Arquivos > LocalAppData > AoaSampleApp > LocalState** e depois **Procurar...** . Então, selecione o arquivo de modelo (**chair.ou**, por exemplo) e selecione **Upload**. Em seguida, você deve ver o arquivo do modelo no cache local.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Modelo de carregamento do portal":::

No HoloLens, inicie o aplicativo **AoaSampleApp** (se já estiver aberto, feche-o e abra-o novamente). Aproxime-se (a uma distância de dois metros) do objeto de destino (cadeira) e examine-o analisando-o de várias perspectivas. Você deverá ver uma caixa delimitadora rosa em volta do objeto com alguns pontos amarelos renderizados próximos à superfície dele, o que indica que ele foi detectado.

:::image type="content" source="./media/chair-detection.png" alt-text="Detecção da cadeira":::

Figura: cadeira detectada renderizada com a caixa delimitadora (rosa), nuvem de pontos (amarela) e uma área de pesquisa (caixa amarela grande).

Você pode definir o espaço de pesquisa do objeto no aplicativo clicando com os dedos no ar com a mão direita ou esquerda. O espaço de pesquisa será definido por meio de uma esfera com raio de dois metros, uma caixa delimitadora com 4 m³ e um tronco de exibição. Para objetos maiores, como carros, a melhor opção normalmente é usar a seleção do tronco de exibição enquanto estiver posicionado voltado para um canto do objeto, a uma distância de dois metros.
Cada vez que a área de pesquisa for alterada, o aplicativo removerá as instâncias que estão sendo rastreadas no momento e tentará encontrá-las novamente na nova área de pesquisa.

Esse aplicativo pode rastrear vários objetos ao mesmo tempo. Para fazer isso, carregue vários modelos na pasta **LocalState** e defina uma área de pesquisa que abranja todos os objetos de destino. Pode levar mais tempo para detectar e rastrear vários objetos.

O aplicativo alinha um modelo 3D à sua contraparte física com proximidade. O usuário pode tocar no ar usando a mão esquerda para ativar o modo de rastreamento de alta precisão, que computa poses mais precisas. Esse recurso ainda é experimental, o que consome mais recursos do sistema e pode resultar em maior tremulação na pose estimada. Toque de ar novamente com a mão esquerda para voltar ao modo de rastreamento normal.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início rápido: ingestão de um modelo 3D](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Conceitos: visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [perguntas frequentes](../faq.md)
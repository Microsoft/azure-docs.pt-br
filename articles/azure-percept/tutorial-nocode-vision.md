---
title: Criar uma solução de pesquisa visual sem código no Azure Percept Studio
description: Saiba como criar uma solução de pesquisa visual sem código no Azure Percept Studio e implantá-la em seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023122"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Criar uma solução de pesquisa visual sem código no Azure Percept Studio

O Azure Percept Studio permite que você crie e implante soluções personalizadas de pesquisa visual computacional, sem necessidade de codificação. Neste artigo, você irá:

- Criar um projeto de pesquisa visual no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Coletar imagens de treinamento com seu devkit
- Rotular as imagens de treinamento na [Visão Personalizada](https://www.customvision.ai/)
- Treinar o modelo personalizado de detecção ou classificação de objetos
- Implantar o modelo no devkit
- Configure o retreinamento para aprimorar seu modelo

Este tutorial é adequado para desenvolvedores com pouca ou nenhuma experiência de IA e aqueles que estão apenas começando a usar o Azure Percept.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- Experiência de instalação do Azure Percept DK: você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT

## <a name="create-a-vision-prototype"></a>Criar um protótipo de pesquisa visual

1. Inicie o navegador e vá para o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na página de visão geral, clique na guia **Demonstrações e tutoriais**. :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Tela de visão geral do Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Em **Tutoriais e demonstrações de pesquisa visual**, clique em **Criar um protótipo de pesquisa**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Tela de demonstrações e tutoriais do Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Na página **Novo protótipo de Visão Personalizada do Azure Percept**, faça o seguinte:

    1. Na caixa **Nome do projeto**, insira um nome para o protótipo de pesquisa visual.

    1. Insira uma descrição do protótipo de pesquisa visual na caixa **Descrição do projeto**.

    1. Selecione **Azure Percept DK** no menu suspenso **Tipo de dispositivo**.

    1. Selecione um recurso no menu suspenso **Recurso** ou clique em **Criar um recurso**. Se você optar por criar um recurso, faça o seguinte na janela **Criar**:
        1. Insira um nome para o novo recurso.
        1. Selecione sua assinatura do Azure.
        1. Selecione um grupo de recursos ou crie um novo.
        1. Selecione sua região preferida.
        1. Selecione o tipo de preço (recomendamos o S0).
        1. Na parte inferior da janela, clique em **Criar**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Janela Criar recurso.":::

    1. Em **Tipo de projeto**, escolha se o projeto de pesquisa visual executará a detecção de objetos ou a classificação de imagem. Para obter mais informações sobre os tipos de projeto, clique em **Ajude-me a escolher**.

    1. Para **Otimização**, selecione se você deseja otimizar seu projeto para precisão, latência de rede baixa ou um equilíbrio entre ambos.

    1. Selecione o botão **Criar**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Tela Criar protótipo de Visão Personalizada.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Conectar um dispositivo ao projeto e capturar imagens

Depois de criar uma solução de pesquisa visual, você precisa adicionar o devkit e o Hub IoT correspondente a ela.

1. Ligue o devkit.

1. No menu suspenso **Hub IoT**, selecione o Hub IoT ao qual o devkit foi conectado durante o OOBE.

1. No menu suspenso **Dispositivos**, selecione o devkit.

Em seguida, para treinar seu modelo de IA, você precisa carregar ou capturar imagens. Recomendamos que sejam carregadas pelo menos 30 imagens por tipo de marca. Por exemplo, se você quiser criar um detector de cachorros e gatos, carregue pelo menos 30 imagens de cachorros e 30 imagens de gatos. Para capturar imagens com o SoM de Pesquisa Visual do devkit, faça o seguinte:

1. Na janela **Captura de imagem**, selecione **Exibir fluxo de dispositivos** para exibir o fluxo de vídeo de SoM da pesquisa visual.

1. Verifique o fluxo de vídeo para garantir que sua câmera de SoM de pesquisa visual esteja alinhada corretamente para tirar as fotos de treinamento. Faça ajustes conforme necessário.

1. Na janela **Captura de imagem**, clique em **Tirar foto**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Tela de captura de imagem.":::

1. Como alternativa, configure uma captura de imagem automatizada para coletar uma grande quantidade de imagens por vez, marcando a caixa **Captura de imagem automática**. Selecione a taxa de geração de imagens de sua preferência em **Taxa de captura** e o número total de imagens que você gostaria de coletar em **Destino**. Clique em **Definir captura automática** para iniciar o processo de captura automática de imagens.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Menu suspenso de captura automática de imagens.":::

Quando você tiver fotos suficientes, clique em **Avançar: marcar imagens e treinamento de modelo** na parte inferior da tela. Todas as imagens serão salvas em [Visão Personalizada](https://www.customvision.ai/).

> [!NOTE]
> Se você optar por carregar imagens de treinamento diretamente na Visão Personalizada, observe que o tamanho do arquivo de imagem não pode exceder 6 MB.

## <a name="tag-images-and-train-your-model"></a>Marcar imagens e treinar seu modelo

Antes de treinar o modelo, adicione rótulos às imagens.

1. Na página **Marcar imagens e treinamento de modelo**, clique em **Abrir projeto na Visão Personalizada**.

1. No lado esquerdo da página **Visão Personalizada**, clique em **Sem Marcas** em **Marcas** para exibir as imagens recém-coletadas na etapa anterior. Selecione uma ou mais das imagens sem marcas.

1. Na janela **Detalhes da imagem**, clique na imagem para começar a marcação. Se você tiver selecionado detecção de objetos como o tipo de projeto, precisará também desenhar uma [caixa delimitadora](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) nos objetos específicos que quiser marcar. Ajuste a caixa delimitadora conforme necessário. Digite a marca de objeto e clique **+** para aplicar a marca. Por exemplo, se você estiver criando uma solução de pesquisa visual que notifique você quando uma prateleira de loja precisar ser reabastecida, adicione a marca "Prateleira vazia" a imagens de prateleiras vazias e adicione a marca "Prateleira cheia" a imagens de prateleiras totalmente abastecidas. Repita para todas as imagens sem marcas.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Tela de marcação de imagem na Visão Personalizada.":::

1. Depois de marcar as imagens, clique no ícone **X** no canto superior direito da janela. Clique em **Com Marcas** em **Marcas** para exibir todas as imagens marcadas recentemente.

1. Depois que as imagens forem rotuladas, estará tudo pronto para treinar seu modelo de IA. Para fazer isso, clique em **Treinar** próximo à parte superior da página. Você precisa ter pelo menos 15 imagens por tipo de marca para treinar o modelo (é recomendável usar pelo menos 30). O treinamento normalmente leva cerca de 30 minutos, mas pode levar mais tempo se o conjunto de imagens for muito grande.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Seleção de imagem de treinamento com o botão treinar realçado.":::

1. Quando o treinamento for concluído, sua tela mostrará o desempenho do modelo. Para obter mais informações sobre como avaliar esses resultados, confira a [documentação de avaliação do modelo](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). Após o treinamento, convém que você [teste o modelo](../cognitive-services/custom-vision-service/test-your-model.md) com imagens adicionais e treine-o novamente conforme necessário. Cada vez que você treinar o modelo, ele será salvo como uma nova iteração. Confira a [documentação da Visão Personalizada](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) para obter informações adicionais sobre como aprimorar o desempenho do modelo.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Resultados do treinamento de modelo.":::

    > [!NOTE]
    > Se você optar por testar seu modelo em imagens adicionais na Visão Personalizada, observe que o tamanho do arquivo de cada imagem de teste não pode exceder 4 MB.

Quando estiver satisfeito com o desempenho do seu modelo, feche a Visão Personalizada fechando a guia correspondente do navegador.

## <a name="deploy-your-ai-model"></a>Implantar o modelo de IA

1. Volte para a guia Azure Percept Studio e clique em **Avançar: avaliar e implantar** na parte inferior da tela.

1. A janela **Avaliar e implantar** mostrará o desempenho da iteração do modelo selecionada. No menu suspenso **Iteração de modelo**, selecione a iteração que você deseja implantar no devkit e clique em **Implantar modelo** na parte inferior da tela.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Tela de implantação de modelo." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Depois de implantar o modelo, exiba o fluxo de vídeo do dispositivo para ver a inferência de modelo em ação.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Fluxo de dispositivos mostrando o detector de fone de ouvido em ação.":::

Depois de fechar esta janela, você pode voltar e editar seu projeto de pesquisa visual a qualquer momento clicando em **Pesquisa visual** em **Projetos de IA** na página inicial do Azure Percept Studio e selecionando o nome do projeto de pesquisa visual.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Página do projeto de pesquisa visual." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Configure o retreinamento para aprimorar seu modelo

Depois de treinar e implantar seu modelo no dispositivo, você poderá melhorar o desempenho dele por meio da configuração dos parâmetros de readaptação para capturar mais dados de treinamento. Esse recurso é usado para melhorar o desempenho de um modelo treinado, dando a você a capacidade de capturar imagens com base em um intervalo de probabilidade. Por exemplo, você pode definir seu dispositivo para capturar imagens de treinamento apenas quando a probabilidade for baixa. Eis algumas [diretrizes adicionais](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) sobre como adicionar mais imagens e equilibrar dados de treinamento.

1. Para configurar o retreinamento, volte ao seu **Projeto** e, depois, vá para **Resumo do Projeto**
1. Na guia **Captura de imagem**, selecione **Captura automática de imagem** e **Configure o retreinamento**.
1. Configure a captura de imagem automatizada para coletar uma grande quantidade de imagens por vez, marcando a caixa **Captura de imagem automática**.
1. Selecione a taxa de geração de imagens de sua preferência em **Taxa de captura** e o número total de imagens que você gostaria de coletar em **Destino**.
1. Na seção **Configurar o retreinamento**, selecione a iteração para a qual você gostaria de capturar mais dados de treinamento e selecione o intervalo de probabilidade. Somente imagens que atendam à taxa de probabilidade serão carregadas em seu projeto.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="captura de imagem.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou um recurso do Azure para este tutorial e não deseja mais desenvolver nem usar sua solução de pesquisa visual, execute as seguintes etapas para excluir o recurso:

1. Acesse o [portal do Azure](https://ms.portal.azure.com/).
1. Clique em **Todos os recursos**.
1. Clique na caixa de seleção ao lado do recurso criado durante este tutorial. O tipo de recurso será listado como **Serviços Cognitivos**.
1. Clique no ícone **Excluir** próximo à parte superior da tela.

## <a name="video-walkthrough"></a>Passo a passo em vídeo

Para obter um passo a passo visual das etapas descritas acima, assista ao seguinte vídeo:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Próximas etapas

Em seguida, confira os artigos de instruções da pesquisa visual para obter informações sobre os recursos adicionais da solução de pesquisa visual no Azure Percept Studio.

<!--
Add links to how-to articles and oobe article.
-->
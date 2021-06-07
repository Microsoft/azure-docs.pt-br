---
title: 'Início Rápido: criar um detector de objeto com o site da Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a usar o site da Visão Personalizada para criar, treinar e testar um modelo de detector de objetos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento de imagem, aplicativo de reconhecimento de imagem, visão personalizada
ms.openlocfilehash: 5ecd5fee565a8d31e0ff05f3b234771446242d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99221288"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Início Rápido: criar um detector de objeto com o site da Visão Personalizada

Neste guia início rápido, você aprenderá a usar o site da Visão Personalizada para criar um modelo de detector de objetos. Depois de criar um modelo, você poderá testá-lo com novas imagens e, eventualmente, integrá-lo ao próprio aplicativo de reconhecimento de imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens com o qual o modelo de detector será treinado. Você pode usar o conjunto de [imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) no GitHub. Ou pode escolher suas próprias imagens usando as dicas abaixo.

## <a name="create-custom-vision-resources"></a>Criar recursos de Visão Personalizada

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No navegador da Web, navegue até o [site Visão Personalizada](https://customvision.ai) e selecione __Entrar__. Entre com a mesma conta usada para entrar no portal do Azure.

![Imagem da página de entrada](./media/browser-home.png)


1. Para criar seu primeiro projeto, selecione **Novo Projeto**. A caixa de diálogo **Criar novo projeto** será exibida.

    ![A caixa de diálogo do novo projeto tem campos para nome, descrição e domínios.](./media/get-started-build-detector/new-project.png)

1. Insira um nome e uma descrição para o projeto. Selecione um Grupo de Recursos. Se a sua conta conectada estiver associada a uma conta do Azure, a lista suspensa Grupo de Recursos exibirá todos os seus Grupos de Recursos do Azure que contêm um Recurso do Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se nenhum grupo de recursos estiver disponível, confirme se você fez logon em [customvision.ai](https://customvision.ai) com a mesma conta que foi usada para fazer logon no [portal do Azure](https://portal.azure.com/). Além disso, confirme se você selecionou o mesmo "Diretório" no site da Visão Personalizada como o diretório no portal do Azure em que os recursos da Visão Personalizada estão localizados. Em ambos os sites, é possível selecionar seu diretório no menu suspenso de conta no canto superior direito da tela. 

1. Selecione __Detecção de Objetos__ em __Tipos de Projeto__.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o detector para tipos específicos de imagens, conforme descrito na tabela a seguir. Você pode alterar o domínio posteriormente, se desejar.

    |Domínio|Finalidade|
    |---|---|
    |__Geral__| Otimizado para uma ampla variedade de tarefas de detecção de objetos. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Logotipo__|Otimizado para localizar logotipos de marcas em imagens.|
    |__Produtos em prateleiras__|Otimizado para detectar e classificar produtos em prateleiras.|
    |__Domínios compactos__| Otimizados para as restrições da detecção de objetos em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente.|

1. Por fim, selecione __Criar projeto__.

## <a name="choose-training-images"></a>Escolher imagens de treinamento

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Nesta seção, você carregará e marcará manualmente imagens para ajudar a treinar o detector. 

1. Para adicionar imagens, selecione __Adicionar imagens__ e selecione __Procurar arquivos locais__. Selecione __Abrir__ para carregar as imagens.

    ![O controle de adição de imagens é mostrado no canto superior esquerdo e como um botão na parte inferior central.](./media/get-started-build-detector/add-images.png)

1. Você verá as imagens carregadas na seção **Sem marcas** da interface do usuário. A próxima etapa é marcar manualmente os objetos que você deseja que o detector aprenda a reconhecer. Clique na primeira imagem para abrir a janela da caixa de diálogo de marcação. 

    ![Imagens carregadas, na seção Sem marcas](./media/get-started-build-detector/images-untagged.png)

1. Clique e arraste um retângulo em torno do objeto na imagem. Em seguida, insira um novo nome de marca com o botão **+** ou selecione uma marca existente na lista suspensa. É importante marcar todas as instâncias dos objetos que você deseja detectar, pois o detector usa a área da tela de fundo sem marcas como um exemplo negativo no treinamento. Quando concluir a marcação, clique na seta à direita para salvar as marcas e passar para a próxima imagem.

    ![Como marcar um objeto com uma seleção retangular](./media/get-started-build-detector/image-tagging.png)

Para carregar outro conjunto de imagens, retorne ao topo desta seção e repita as etapas.

## <a name="train-the-detector"></a>Treinar o detector

Para treinar o modelo de detector, selecione o botão **Treinar**. O detector usa todas as imagens atuais e suas marcas para criar um modelo que identifica cada objeto marcado.

![Botão Treinar no canto superior direito na barra de ferramentas do cabeçalho da página da Web](./media/getting-started-build-a-classifier/train01.png)

O processo de treinamento deve levar apenas alguns minutos. Durante esse tempo, informações sobre o processo de treinamento são exibidas na barra **Desempenho**.

![A janela do navegador com uma caixa de diálogo de treinamento na seção principal](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Avaliar o detector

Depois que o treinamento for concluído, o desempenho do modelo será calculado e exibido. O serviço de Visão Personalizada usa as imagens que você enviou para treinamento a fim de calcular a precisão, o recall e a precisão média. A precisão e o recall são duas medidas diferentes da eficácia de um detector:

- A **precisão** indica a fração de classificações identificadas que estão corretas. Por exemplo, se o modelo identificou 100 imagens como cachorros e 99 delas são realmente de cachorros, a precisão é de 99%.
- A **recuperação** indica a fração de classificações reais que foram corretamente identificadas. Por exemplo, se há de fato 100 imagens de maçãs e o modelo identifica 80 como maçãs, a recuperação é de 80%.
- **A média da precisão média** é o valor médio da AP (precisão média). AP é a área sob a curva de precisão/recall (a precisão é plotada em relação à recuperação para cada previsão feita).

![Os resultados do treinamento mostram a precisão e o recall gerais, bem como a precisão média.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Limite de probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Limite de sobreposição

O controle deslizante do **Limite de Sobreposição** aborda o quão correta deve ser uma previsão de objeto para que seja considerada "correta" no treinamento. Ele define a sobreposição mínima permitida entre a caixa delimitadora do objeto previsto e a caixa delimitadora real inserida pelo usuário. Se as caixas delimitadoras não se sobrepuserem a esse grau, a previsão não será considerada correta.

## <a name="manage-training-iterations"></a>Gerenciar iterações de treinamento

Sempre que treinar o detector, você criará uma _iteração_ com suas próprias métricas de desempenho atualizadas. É possível exibir todas as iterações no painel à esquerda da guia **Desempenho**. No painel à esquerda, também é possível encontrar o botão **Excluir**, que pode ser usado para excluir uma iteração se ela estiver obsoleta. Ao excluir uma iteração, você exclui qualquer imagem que esteja exclusivamente associada a ela.

Confira [Usar o modelo com a API de previsão](./use-prediction-api.md) para saber como acessar seus modelos treinados de maneira programática.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar e treinar um modelo de detector de objeto usando o site da Visão Personalizada. A seguir, saiba mais sobre o processo iterativo para melhorar o seu modelo.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)

* [O que é Visão Personalizada?](./overview.md)

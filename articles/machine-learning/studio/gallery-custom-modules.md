---
title: "Módulos personalizados na Galeria do Cortana Intelligence | Microsoft Docs"
description: "Descubra os módulos de aprendizado de máquina personalizados na Galeria do Cortana Intelligence."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.openlocfilehash: 4bab94c04f09261eaa88b9e6a225c05f57992ab0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>Descubra os módulos de aprendizado de máquina personalizados na Galeria do Cortana Intelligence
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Módulos personalizados para o Machine Learning Studio
A Galeria do Cortana Intelligence oferece diversos [módulos personalizados](https://gallery.cortanaintelligence.com/customModules) que ampliam os recursos do Azure Machine Learning Studio. Você pode importar esses módulos para usá-los em seus experimentos para que você possa desenvolver soluções ainda mais avançadas de análise preditiva.

Atualmente, a Galeria oferece módulos em *análise de série temporal*, *regras de associação*, *algoritmos de clustering* (além do k-means), *visualizações* e outros módulos do utilitário de força de trabalho.


## <a name="discover"></a>Descobrir
Para procurar módulos personalizados [na Galeria](http://gallery.cortanaintelligence.com), em **Mais**, selecione **Módulos Personalizados**.

![Selecione Módulos Personalizados na home page da Galeria](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

A página **[Módulos Personalizados](https://gallery.cortanaintelligence.com/customModules)** exibe uma lista dos módulos mais populares e adicionados recentemente. Para exibir todos os módulos personalizados, selecione o botão **Ver todos**. Para procurar um módulo personalizado específico, selecione **Ver todos** e selecione os critérios de filtro. Também é possível inserir os termos de pesquisa na caixa **Pesquisar** na parte superior da página da Galeria.

![Selecione “Ver todos” para procurar todos os módulos personalizados](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Noções básicas

Para entender como funciona um módulo personalizado publicado, selecione o módulo personalizado para abrir a página de detalhes do módulo. A página de detalhes oferece uma experiência de aprendizado consistente e informativa. Por exemplo, a página de detalhes destaca a finalidade do módulo e lista os parâmetros, entradas e saídas esperados. A página de detalhes também tem um link para o código-fonte subjacente, que você pode examinar e personalizar.

### <a name="comment-and-share"></a>Comentário e compartilhamento
Na página de detalhes de um módulo personalizado, na seção **Comentários**, é possível comentar ou fazer perguntas sobre o módulo. Você pode até mesmo compartilhar o módulo com amigos ou colegas no Twitter ou LinkedIn. Você também pode enviar um link por email para a página de detalhes do módulo a fim de convidar outros usuários para ver a página.

![Compartilhar este item com amigos](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Adicionar seus próprios comentários](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importar
Você pode importar qualquer módulo personalizado da Galeria para seus próprios experimentos.

A Galeria do Cortana Intelligence oferece duas maneiras de importar uma cópia do módulo:

* **Da Galeria**. Ao importar um módulo personalizado da Galeria, você também obtém um experimento de exemplo que mostra como usar o módulo.
* **Do Machine Learning Studio**. Você pode importar qualquer módulo personalizado enquanto estiver trabalhando no Machine Learning Studio (neste caso, você não obtém o experimento de exemplo).

### <a name="from-the-gallery"></a>Da Galeria

1. Na Galeria, abra a página de detalhes do módulo. 
2. Selecione **Abrir no Studio**.
   
    ![Abrir o módulo personalizado da Galeria](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Cada módulo personalizado inclui um experimento de amostra que demonstra como usar o módulo. Quando você seleciona **Abrir no Studio**, o experimento de exemplo é aberto em seu espaço de trabalho do Machine Learning Studio. (Se você ainda não estiver conectado ao Studio, será necessário primeiro entrar usando sua conta da Microsoft.)

Além do experimento de exemplo, o módulo personalizado é copiado para seu espaço de trabalho. Ele também é colocado na sua paleta de módulo, junto com todos os seus módulos internos ou personalizados do Machine Learning Studio. Agora você pode usá-lo em seus experimentos como qualquer outro módulo no espaço de trabalho.

### <a name="from-within-machine-learning-studio"></a>Do Machine Learning Studio

1. No Machine Learning Studio, selecione **NOVO**.
2. Selecione **Módulo**. Você pode escolher de uma lista de módulos da Galeria ou encontrar um módulo específico usando a caixa **Pesquisar**.
3. Aponte o mouse para um módulo e selecione **Importar Módulo**. (Para ver informações sobre o módulo, selecione **Exibir na Galeria**. Isso levará você para a página de detalhes do módulo na Galeria.)
   
    ![Importar o módulo personalizado no Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

O módulo personalizado é copiado para seu espaço de trabalho e colocado em sua paleta de módulos, junto com seus módulos internos ou personalizados do Machine Learning Studio. Agora você pode usá-lo em seus experimentos como qualquer outro módulo no espaço de trabalho.

## <a name="use"></a>Uso

Independentemente do método escolhido para importar um módulo personalizado, quando você importa o módulo, ele é colocado na sua paleta de módulos no Machine Learning Studio. Na paleta de módulos, você pode usar o módulo personalizado em qualquer experimento em seu espaço de trabalho, assim como qualquer outro módulo.

Para usar um módulo importado:

1. Crie um novo experimento ou abra um existente.
2. Para expandir a lista de módulos personalizados no espaço de trabalho, selecione **Personalizado** na paleta de módulos. A paleta do módulo fica à esquerda da tela do experimento.
   
    ![Lista de módulo personalizado na paleta do Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Selecione o módulo que você importou e arraste-o para o experimento.


**[Vá para a Galeria](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


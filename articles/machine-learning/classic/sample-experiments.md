---
title: 'ML Studio (clássico): iniciar experimentos de exemplos – Azure'
description: Saiba como usar os experimentos de exemplo do machine learning para criar experimentos com a Galeria de IA do Azure e o Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 40306973dde989862f8dfe9df5c9d873ac18fdd0
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517494"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Criar experiências do Azure Machine Learning Studio (clássico) dos exemplos de trabalho na Galeria de IA do Azure

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)




Saiba como usar os experimentos de exemplo na [Galeria de IA do Azure](https://gallery.azure.ai/), em vez de criar experimentos de machine learning a partir do zero. Use os exemplos para compilar sua própria solução de machine learning.

A galeria contém experimentos de exemplo da equipe do Microsoft Azure Machine Learning Studio (clássico), bem como exemplos compartilhados pela comunidade do Machine Learning. Você pode fazer perguntas ou postar comentários sobre os experimentos

Para saber como usar a galeria, assista ao vídeo de 3 minutos [Copiar o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) da série [Ciência de Dados para Iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Localizar uma experiência para copiar na Galeria de IA do Azure
Para ver quais experimentos estão disponíveis, vá para a [Galeria](https://gallery.azure.ai/) e clique em **Experimentos** na parte superior da página.

### <a name="find-the-newest-or-most-popular-experiments"></a>Localizar os experimentos mais recentes ou mais populares
Nessa página, você pode ver os experimentos **Adicionados recentemente**, descer para examinar **O que é popular** ou os **Testes populares da Microsoft** mais recentes.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Procurar um experimento que atenda aos requisitos específicos
Para procurar todos os experimentos:

1. Clique em **Procurar tudo** na parte superior da página.
2. No lado esquerdo, em **Refinar por**, na seção **Categorias**, selecione **Experimento** para ver todos os experimentos na Galeria.
3. Você pode encontrar os experimentos que atendem aos seus requisitos de duas maneiras diferentes:
   * **Selecione os filtros à esquerda.** Por exemplo, para procurar experimentos que usem um algoritmo de detecção de anomalias baseado em PCA: Em **Categorias**, clique em **Experimento**. Em seguida, em **Algoritmos Usados**, clique em **Mostrar tudo** e, na caixa de diálogo, escolha **Detecção de Anomalias Baseada em PCA**. Talvez seja necessário rolar para vê-lo.<br></br>
     ![Selecionar filtros](./media/sample-experiments/choose-an-algorithm.png)
   * **Use a caixa de pesquisa.** Por exemplo, para encontrar os experimentos fornecidos pela Microsoft relacionados ao reconhecimento de dígito e que usam um algoritmo de máquina do vetor com suporte de duas classes, digite "reconhecimento de dígito" na caixa de pesquisa. Em seguida, selecione os filtros **Experimento**, **Apenas o conteúdo da Microsoft** e **Máquina do Vetor com Suporte de Duas Classes**:<br></br>
     ![Usar a caixa de pesquisa](./media/sample-experiments/search-for-experiments.png)
4. Clique em um experimento para saber mais sobre ele.
5. Para executar e/ou modificar o experimento, clique em **Abrir no Estúdio** na página do experimento. <br></br>

    ![Teste de exemplo](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Criar um novo experimento usando um exemplo como modelo
Você também pode criar um experimento no Machine Learning Studio (clássico) usando um exemplo da galeria como modelo.

1. Entre com suas credenciais de conta da Microsoft no [Estúdio](https://studio.azureml.net) e clique em **Novo** para criar um experimento.
2. Navegue pelo conteúdo de exemplo e clique em um.

Um experimento é criado em seu workspace do Machine Learning Studio (clássico) usando o experimento de exemplo como modelo.

## <a name="next-steps"></a>Próximas etapas
* [Importar dados de várias fontes](import-data.md)
* [Implantar um serviço Web do Machine Learning](deploy-a-machine-learning-web-service.md)
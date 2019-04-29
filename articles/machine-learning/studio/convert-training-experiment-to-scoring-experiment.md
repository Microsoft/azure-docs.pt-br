---
title: Preparar o modelo para implantação
titleSuffix: Azure Machine Learning Studio
description: Como preparar seu modelo treinado para implantação como um serviço Web convertendo o teste de treinamento do Machine Learning Studio para um teste preditivo.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 2a318edada5cdc4124e221fdc8c441ab323a9289
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751902"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Como preparar seu modelo para implantação no Azure Machine Learning Studio

O Azure Machine Learning Studio fornece as ferramentas necessárias para desenvolver um modelo de análise preditiva e operacionalizá-lo implantando-o como um serviço Web do Azure.

Para fazer isso, use o Studio para criar um teste – chamado de *teste de treinamento* – em que você treina, pontua e edita o modelo. Quando estiver satisfeito, você terá o modelo pronto para implantação convertendo o teste de treinamento em um *teste preditivo* que é configurado para pontuar dados do usuário.

Você pode ver um exemplo desse processo no [Tutorial 1: Prever risco de crédito](tutorial-part1-credit-risk.md).

Este artigo aprofunda-se nos detalhes de como um teste de treinamento é convertido em um teste preditivo e como ele é implantado. Ao compreender esses detalhes, você pode aprender a configurar o modelo implantado a fim de torná-lo mais eficaz.



## <a name="overview"></a>Visão geral 

O processo de conversão de um teste de treinamento em um teste preditivo envolve três etapas:

1. Substitua os módulos de algoritmo do Machine Learning pelo modelo treinado.
2. Restrinja o experimento a somente os módulos necessários para pontuação. Um teste de treinamento inclui vários módulos que são necessárias para treinamento, mas não são necessários depois que o modelo está treinado.
3. Defina como seu modelo aceitará dados do usuário do serviço Web e quais dados será retornados.

> [!TIP]
> No teste de treinamento, você estará concentrado em treinar e pontuar o modelo usando seus próprios dados. Porém, depois de implantado, os usuários enviarão novos dados ao modelo e este retornará resultados de previsão. Desse modo, à medida que você converte o teste de treinamento em um teste preditivo para prepará-lo para implantação, lembre-se de como o modelo será usado por outras pessoas.
> 
> 

## <a name="set-up-web-service-button"></a>Botão Configurar Serviço Web
Depois de executar o teste (clique em **EXECUTAR** na parte inferior da tela do teste), clique no botão **Configurar Serviço Web** (selecione a opção **Serviço Web Preditivo**). **Configurar Serviço Web** executa para você as três etapas de conversão do teste de treinamento em um teste preditivo:

1. Ele salva o modelo treinado na seção **Modelos Treinados** da paleta de módulos (à esquerda da tela do teste). Ele substitui o algoritmo do Machine Learning e os módulos [Treinar Modelo][train-model] pelo modelo treinado salvo.
2. Ele analisa o seu teste e remove os módulos que foram claramente usados apenas para treinamento e não são mais necessários.
3. Ele insere módulos de _entrada_ e _saída de serviço Web_ em locais padrão no seu teste (esses módulos aceitam e retornam dados do usuário).

Por exemplo, o seguinte experimento treina um modelo de árvore de decisão de duas classes aumentada usando dados de censo de exemplo:

![experimento de treinamento](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Os módulos nesse experimento executam basicamente quatro funções diferentes:

![Funções de módulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Ao converter esse teste de treinamento em um teste preditivo, alguns desses módulos não serão mais necessários ou agora terão outra finalidade:

* **Dados** – os dados neste conjunto de dados de exemplo não são usados durante a pontuação. O usuário do serviço Web fornecerá os dados a serem pontuados. No entanto, os metadados desse conjunto de dados, tais como tipos de dados, são usados pelo modelo treinado. Portanto, é necessário manter o conjunto de dados no teste preditivo para que ele possa fornecer esses metadados.

* **Preparação** – dependendo dos dados de usuário que serão enviados para pontuação, esses módulos podem ou não ser necessários para processar os dados de entrada. O botão **Configurar Serviço Web** não interfere neles – você precisa decidir como deseja tratá-los.
  
    Por exemplo, nesse exemplo, o conjunto de dados de exemplo pode ter valores ausentes, portanto, um módulo [Limpar Dados Ausentes][clean-missing-data] foi incluído para lidar com eles. Além disso, o conjunto de dados de exemplo inclui colunas que não são necessárias para treinar o modelo. Desse modo, o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] foi incluído para excluir essas colunas extras do fluxo de dados. Se souber que os dados que serão enviados para pontuação por meio do serviço Web não terão valores ausentes, você poderá remover o módulo [Limpar Dados Ausentes][clean-missing-data]. No entanto, uma vez que o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] ajuda a definir as colunas de dados esperadas pelo modelo treinado, esse módulo precisa permanecer.

* **Treinar** – esses módulos são usados para treinar o modelo. Quando você clica em **Configurar Serviço Web**, esses módulos são substituídos por um único módulo que contém o modelo treinado. Esse novo módulo é salvo na seção **Modelos Treinados** da paleta do módulo.

* **Pontuação** – nesse exemplo, o módulo [Dividir Dados][split] é usado para dividir o fluxo de dados em dados de teste e de treinamento. No teste preditivo, não estamos mais treinando, de modo que [Dividir Dados][split] pode ser removido. Da mesma forma, o segundo módulo [Pontuar Modelo][score-model] e o módulo [Avaliar Modelo][evaluate-model] são usados para comparar os resultados dos dados de teste; portanto, esses módulos não são necessários no teste preditivo. O módulo [Modelo de Pontuação][score-model] restante, contudo, é necessário para retornar um resultado de pontuação por meio do serviço Web.

Veja como fica nosso exemplo depois do clique em **Configurar Serviço Web**:

![Teste preditivo convertido](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

O trabalho feito pelo botão **Configurar Serviço Web** pode ser suficiente para preparar o teste para implantação como um serviço Web. No entanto, convém trabalhar nos detalhes específicos do seu experimento.

### <a name="adjust-input-and-output-modules"></a>Ajustar os módulos de entrada e saída
No seu experimento de treinamento, você usou um conjunto de dados de treinamento, que foram processados para deixar os dados em um formato que o algoritmo de aprendizado de máquina necessitava. Se os dados que você espera receber por meio do serviço Web não precisarem desse processamento, será possível ignorá-lo: conecte a saída do **módulo de entrada do serviço Web** a um módulo diferente no teste. Os dados do usuário agora chegarão no modelo nesse local.

Por exemplo, por padrão, **Configurar Serviço Web** coloca o **módulo de entrada do serviço Web** na parte superior do fluxo de dados, como mostrado na figura acima. Porém, podemos posicionar manualmente a **Entrada do serviço Web** depois dos módulos de processamento de dados:

![Movendo a entrada do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos pelo serviço Web agora passarão diretamente para o módulo do Modelo de Pontuação sem qualquer pré-processamento.

Da mesma forma, por padrão, **Configurar Serviço Web** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Nesse exemplo, o serviço Web retornará ao usuário a saída do módulo do [Pontuar Modelo][score-model], que inclui o vetor de dados de entrada completo, além dos resultados de pontuação.
No entanto, se preferir retornar algo diferente, você poderá adicionar outros módulos antes do módulo **Saída do serviço Web**. 

Por exemplo, para retornar apenas os resultados da pontuação, e não todo o vetor de dados de entrada, adicione um módulo [Selecionar Colunas do Conjunto de Dados][select-columns] para excluir todas as colunas, exceto os resultados da pontuação. Em seguida, mova o módulo **Saída do serviço Web** para a saída do módulo [Selecionar Colunas do Conjunto de Dados][select-columns]. O teste tem esta aparência:

![Movendo a saída do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se houver mais módulos no seu experimento que você sabe que não será necessário durante a pontuação, eles podem ser removidos. Por exemplo, como movemos o módulo **Entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo [Limpar Dados Ausentes][clean-missing-data] do teste preditivo.

Nosso teste preditivo ficou assim:

![Removendo o módulo adicional](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros de serviço Web opcionais
Em alguns casos, talvez você queira permitir que o usuário do serviço Web altere o comportamento dos módulos quando o serviço é acessado. *Parâmetros de serviço Web* permitem que você faça isso.

Um exemplo comum é a configuração de um módulo [Importar Dados][import-data] para que o usuário do serviço Web implantado possa especificar outra fonte de dados quando o serviço Web for acessado. Ou então, configurar o módulo [Exportar Dados][export-data] para que um destino diferente possa ser especificado.

Você pode definir os Parâmetros de Serviço Web e associá-los a um ou mais parâmetros de módulo, podendo também especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web fornece valores para esses parâmetros quando o serviço é acessado e as ações de módulo são modificadas de acordo.

Para saber mais sobre Parâmetros de Serviço Web e como usá-los, confira [Usar os parâmetros do Serviço Web do Azure Machine Learning ][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implantar o teste preditivo como um serviço Web
Agora que o teste preditivo foi devidamente preparado, você pode implantá-lo como um serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para saber mais sobre o processo completo de implantação, consulte [Implantar um Serviço Web do Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

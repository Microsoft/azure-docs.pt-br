---
title: 'ML Studio (clássico): Início Rápido: Criar um experimento de ciência de dados – Azure'
description: Este início rápido de aprendizado de máquina percorre um teste de ciência de dados. Vamos prever o preço de um carro usando um algoritmo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 843845e6099bdf43f7cd37b0f3e6f82d3ed50b4c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520605"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Início Rápido: Crie seu primeiro experimento de ciência de dados no Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Essa é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste início rápido, você cria um teste de machine learning no [Azure Machine Learning Studio (clássico)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) que prevê o preço de um carro com base em variáveis diferentes, tais como marca e especificações técnicas.

Se você nunca teve contato com o aprendizado de máquina, a série de vídeo [Ciência de dados para iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md) é uma excelente introdução ao aprendizado de máquina usando linguagem e conceitos do dia a dia.

Este início rápido segue o fluxo de trabalho padrão para um experimento:

1. **Criar um modelo**
    - [Obter os dados]
    - [Preparar os dados]
    - [Definir recursos]
1. **Treinar o modelo**
    - [Escolher e aplicar um algoritmo]
1. **Pontuar e testar o modelo**
    - [Prever novos preços de automóveis]

[Obter os dados]: #get-the-data
[Preparar os dados]: #prepare-the-data
[Definir recursos]: #define-features
[Escolher e aplicar um algoritmo]: #choose-and-apply-an-algorithm
[Prever novos preços de automóveis]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Obter os dados

A primeira coisa que você precisa para executar o aprendizado de máquina são dados.
Há uma série de conjuntos de dados de exemplo incluídos no Studio (clássico) que você pode usar ou você pode importar dados de várias fontes. Neste exemplo, usaremos o conjunto de dados de exemplo **Dados de preço de automóvel (Brutos)** , que está incluído no seu workspace.
Esse conjunto de dados inclui entradas para vários automóveis individuais, incluindo informações como marca, modelo, especificações técnicas e preço.

> [!TIP]
> Você pode encontrar uma cópia funcional do seguinte experimento na [Galeria de IA do Azure](https://gallery.azure.ai). Vá para **[Seu primeiro experimento de ciência de dados – previsão de preço de automóvel](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** e clique em **Abrir no Studio** para baixar uma cópia do experimento no seu espaço de trabalho do Machine Learning Studio (clássico).

Aqui está a explicação de como colocar o conjunto de dados no seu experimento.

1. Crie um novo experimento clicando em **+NOVO** na parte inferior da janela do Machine Learning Studio (clássico). Selecione **EXPERIMENTO** >  **Experimento em Branco**.

1. O teste recebe um nome padrão que você pode ver na parte superior da tela. Selecione este texto e renomeie para algo que tenha sentido, por exemplo, **Previsão de preço de automóvel**. O nome não precisa ser exclusivo.

    ![Renomear o experimento](./media/create-experiment/rename-experiment.png)

1. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa Pesquisar na parte superior desta paleta para localizar o conjunto de dados rotulado como **Dados de preço de automóvel (brutos)** . Arraste este conjunto de dados até a tela do experimento.

    ![Localize o conjunto de dados de automóvel e arraste-o para a tela do experimento](./media/create-experiment/type-automobile.png)

Para ver a aparência dos dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**.

![Clique na porta de saída e selecione "Visualizar"](./media/create-experiment/select-visualize.png)

> [!TIP]
> Os módulos e conjuntos de dados têm portas de entrada e saída representadas por círculos pequenos – portas de entrada na parte superior, portas de saída na parte inferior.
Para criar um fluxo de dados por meio do experimento, você conectará uma porta de um módulo de saída a uma porta de entrada do outro.
A qualquer momento, você pode clicar na porta de saída de um conjunto de dados ou de um módulo para ver aparência dos dados naquele ponto do fluxo de dados.

No conjunto de dados de exemplo, cada linha representa uma instância de um automóvel e as variáveis associadas a cada automóvel aparecem como colunas. Vamos prever o preço na coluna à direita (coluna 26, intitulada "preço") usando as variáveis de um automóvel específico.

![Exibir os dados de automóveis na janela de visualização de dados](./media/create-experiment/visualize-auto-data.png)

Feche a janela de visualização clicando no "**x**" no canto superior direito.

## <a name="prepare-the-data"></a>Preparar os dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado. Você deve ter observado os valores ausentes presentes nas colunas de várias linhas. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente. Vamos remover quaisquer linhas que tenham valores ausentes. Além disso, a coluna **normalized-losses** tem uma grande proporção de valores ausentes, portanto excluiremos totalmente essa coluna do modelo.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos.

Primeiro, vamos adicionar um módulo que remova a coluna **normalized-losses** completamente. Em seguida, adicionamos outro módulo que remove qualquer linha que tenha dados ausentes.

1. Digite **selecionar colunas** na caixa de Pesquisa na parte superior da paleta do módulo para encontrar o módulo [Selecionar Colunas no Conjunto de Dados][select-columns]. Em seguida, arraste-o para a tela do experimento. Esse módulo permite selecionar quais colunas de dados desejamos incluir ou excluir no modelo.

1. Conecte a porta de saída do conjunto de dados **Dados de preço de automóveis (Brutos)** à porta de entrada de Selecionar Colunas no Conjunto de Dados.

    ![Adicionar o módulo "Selecionar Colunas no Conjunto de Dados" à tela do experimento e conectá-lo](./media/create-experiment/type-select-columns.png)

1. Selecione o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e clique em **Iniciar seletor de coluna** no painel **Propriedades**.

   - À esquerda, clique em **Com regras**
   - Em **Começa com**, clique em **Todas as colunas**. Essas regras instruem o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] a passar por todas as colunas (exceto aquelas que estamos prestes a excluir).
   - Nos menus suspensos, selecione **Excluir** e **nomes da coluna** e clique dentro da caixa de texto. Uma lista de colunas é exibida. Selecione **normalized-losses** e ela será adicionada à caixa de texto.
   - Clique no botão de marca de seleção (OK) para fechar o seletor de coluna (no canto inferior direito).

     ![Inicie o seletor de coluna e exclua a coluna "normalized-losses"](./media/create-experiment/launch-column-selector.png)

     Agora, o painel de propriedades de **Selecionar Colunas no Conjunto de Dados** indica que ele passará por todas as colunas do conjunto de dados exceto por **normalized-losses**.

     ![O painel de propriedades mostra que a coluna "normalized-losses" foi excluída](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. Neste caso, clique duas vezes no módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e digite o comentário "Excluir perdas normalizadas".

     ![Clique duas vezes em um módulo para adicionar um comentário](./media/create-experiment/add-comment.png)

1. Arraste o módulo [Limpar Dados Ausentes][clean-missing-data] para a tela do teste e conecte-o ao módulo [Selecionar Colunas no Conjunto de Dados][select-columns]. No painel **Propriedades** selecione **Remover linha inteira** em **Modo de limpeza**. Essas opções instruem o módulo [Limpar Dados Ausentes][clean-missing-data] a limpar os dados removendo as linhas que têm valores ausentes. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".

    ![Defina o modo de limpeza para "Remover linha inteira" no módulo "Limpar Dados Ausentes"](./media/create-experiment/set-remove-entire-row.png)

1. Execute o experimento clicando em **EXECUTAR** na parte inferior da página.

    Quando o experimento terminar a execução, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso. Observe também o status **Execução concluída** no canto superior direito.

    ![Depois da execução, o experimento deve ser semelhante a](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Por que executamos o experimento agora? Ao executar o experimento, as definições de coluna dos dados passam do conjunto de dados, através do módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e através do módulo [Limpar Dados Ausentes][clean-missing-data]. Isso significa que todos os módulos que conectarmos ao [Limpar Dados Ausentes][clean-missing-data] também terão essas mesmas informações.

Agora temos dados limpos. Se desejar exibir o conjunto de dados limpo, clique na porta de saída à esquerda do módulo [Limpar Dados Ausentes][clean-missing-data] e selecione **Visualizar**. Observe que a coluna **normalized-losses** não está mais incluída e não há valores ausentes.

Agora que os dados estão limpos, estamos prontos para especificar quais recursos usaremos no modelo preditivo.

## <a name="define-features"></a>definir recursos

No aprendizado de máquina, *recursos* são propriedades individuais mensuráveis de algo em que você está interessado. Em nosso conjunto de dados, cada linha representa um automóvel e cada coluna é um recurso desse automóvel.

Localizar um bom conjunto de recursos para criar um modelo de previsão requer experimentação e conhecimento sobre o problema que você deseja resolver. Alguns recursos são melhores para prever o destino do que outros. Alguns recursos têm uma forte correlação com outros recursos e podem ser removidos. Por exemplo, city-mpg e highway-mpg estão intimamente relacionados, por isso podemos manter um e remover o outro sem afetar a previsão de forma significativa.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso conjunto de dados. É possível voltar depois e selecionar diferentes recursos, executar o experimento novamente e ver se você obtém melhores resultados. Mas, para começar, vamos testar os seguintes recursos:

> marca, estilo de carroceria, distância entre os eixos, tamanho do motor, potência, pico de RPM, quilometragem em estrada, preço

1. Arraste outro módulo [Selecionar Colunas no Conjunto de Dados][select-columns] à tela do experimento. Conecte a porta de saída à esquerda do módulo [Limpar Dados Ausentes][clean-missing-data] à entrada do módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

    ![Conectar o módulo "Selecionar Colunas no Conjunto de Dados" no módulo "Limpar Dados Ausentes"](./media/create-experiment/connect-clean-to-select.png)

1. Clique duas vezes no módulo e digite “Selecionar recursos de previsão".

1. Clique em **Iniciar seletor de coluna** no painel de **Propriedades**.

1. Clique em **Com regras**.

1. Em **Começa com**, clique em **Nenhuma coluna**. Na linha do filtro, selecione **Incluir** e **nomes de coluna** e selecione a lista de nomes de coluna na caixa de texto. Esse filtro instrui o módulo a passar somente pelas colunas (recursos) que especificamos.

1. Clique no botão de marca de seleção (OK).

    ![Selecione as colunas (recursos) a incluir na previsão](./media/create-experiment/select-columns-to-include.png)

Esse módulo produz um conjunto de dados filtrado que contém somente os recursos que desejamos passar para o algoritmo de aprendizado que usaremos na próxima etapa. Posteriormente, é possível retornar e tentar novamente com uma seleção diferente de recursos.

## <a name="choose-and-apply-an-algorithm"></a>Escolher e aplicar um algoritmo

Agora que os dados estão prontos, construir um modelo preditivo consiste em treinamento e teste. Vamos usar nossos dados para treinar o modelo e depois testar o modelo para ver com que proximidade ele é capaz de prever os preços.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classificação* e *regressão* são dois tipos de técnicas de algoritmo de machine learning supervisionado. A classificação prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelha, azul ou verde). A regressão é usada para prever um número.

Como desejamos prever o preço, que é um número, usaremos um algoritmo de regressão. Neste exemplo, usaremos um modelo de *regressão linear*.


Treinamos o modelo, dando a ele um conjunto de dados que inclui o preço. O modelo examina os dados e procura correlações entre os recursos de um automóvel e seu preço. Em seguida, testaremos o modelo – vamos dar a ele um conjunto de recursos para automóveis, com os quais estamos familiarizados e veremos qual a proximidade da previsão feita pelo modelo com o preço conhecido.

Vamos usar nossos dados para treinar o modelo e para testá-lo, dividindo-os em conjuntos de dados separados de treinamento e de teste.

1. Selecione e arraste o módulo [Dividir Dados][split] até a tela do teste e conecte-o à porta de saída do último módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

1. Clique no módulo [Dividir Dados][split] para selecioná-lo. Encontre o painel **Fração de linhas no primeiro conjunto de dados de saída** (no painel **Propriedades** à direita da tela) e defina-o para 0,75. Desta forma, usaremos 75% dos dados para treinar o modelo e manteremos 25% para teste.

    ![Defina a fração de divisão do módulo "Dividir Dados" para 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Alterando o parâmetro **Semente aleatória** , é possível produzir amostras aleatórias diferentes para treinamento e teste. Esse parâmetro controla a alimentação do gerador de número pseudo-aleatório.

1. Execute o experimento. Quando o experimento é executado os módulos [Selecionar Colunas no Conjunto de Dados][select-columns] e [Dividir Dados][split] passam definições de coluna para os módulos que incluiremos em seguida.  

1. Para selecionar o algoritmo de aprendizado, expanda a categoria **Machine Learning** na paleta do módulo à esquerda da tela e expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina. Para este experimento, selecione módulo [Regressão Linear][linear-regression] na categoria **Regressão** e arraste-o para a tela do experimento. (Você também pode localizar o módulo digitando "regressão linear" na caixa de pesquisa da paleta.)

1. Encontre e arraste o módulo [Treinar Modelo][train-model] para a tela do teste. Conecte a saída do módulo [Regressão Linear][linear-regression] à entrada esquerda do módulo [Treinar Modelo][train-model] e conecte a saída de dados de treinamento (porta esquerda) do módulo [Dividir Dados][split] à entrada direita do módulo [Treinar Modelo][train-model].

    ![Conecte o módulo "Modelo de treinamento" aos módulos "Regressão Linear" e "Dividir Dados"](./media/create-experiment/connect-train-model.png)

1. Selecione o módulo [Modelo de Treinamento][train-model], clique em **Iniciar seletor de coluna** no painel **Propriedades** e selecione a coluna **preço**. O **Preço** é o valor que nosso modelo vai prever.

    Selecione a coluna **preço** movendo-a da lista de **Colunas disponíveis** para a lista de **Colunas selecionadas**.

    ![Selecione a coluna de preço para o módulo "Modelo de treinamento"](./media/create-experiment/select-price-column.png)

1. Execute o experimento.

Agora temos um modelo de regressão treinado que pode ser usado para pontuar novos dados de automóveis para fazer previsões de preço.

![Depois da execução, o experimento agora deve ser semelhante a isto](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>prever novos preços de automóveis

Agora que treinamos o modelo usando 75% de nossos dados, podemos usá-lo para classificar os outros 25% dos dados e ver se nosso modelo funciona bem.

1. Localize e arraste o módulo [Modelo de Pontuação][score-model] até a tela do experimento. Conecte a saída do módulo [Treinar Modelo][train-model] à porta de entrada esquerda do módulo [Pontuar Modelo][score-model]. Conecte a saída de dados de teste (porta direita) do módulo [Dividir Dados][split] à porta de entrada direita do módulo [Pontuar Modelo][score-model].

    ![Conecte o módulo "Modelo de Pontuação" com os módulos "Modelo de Treinamento" e "Dividir Dados"](./media/create-experiment/connect-score-model.png)

1. Execute o experimento e exiba a saída do módulo [Modelo de Pontuação][score-model] clicando duas vezes na porta de saída do [Modelo de Pontuação][score-model] e selecionando **Visualizar**. A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.  

    ![Saída do módulo "Modelo de Pontuação"](./media/create-experiment/score-model-output.png)

1. Por fim, podemos testar a qualidade dos resultados. Selecione e arraste o módulo [Modelo de Avaliação][evaluate-model] para a tela do experimento e conecte a saída do módulo [Modelo de Pontuação][score-model] com a entrada à direita do [Modelo de Avaliação][evaluate-model]. O experimento final deve se parecer como o seguinte:

    ![O experimento final](./media/create-experiment/complete-linear-regression-experiment.png)

1. Execute o experimento.

Execute o experimento e exiba a saída do módulo [Modelo de Avaliação][evaluate-model], clique duas vezes na porta de saída e selecione **Visualizar**.

![Resultados de avaliação para o experimento](./media/create-experiment/evaluation-results.png)

As estatísticas a seguir são mostradas para nosso modelo:

- **MAE** (Média de erros absolutos): a média de erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **RMSE** (Raiz quadrada dos erros ao quadrado): a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
- **Erro absoluto relativo**: a média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
- **Erro ao quadrado relativo**: a média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
- **Coeficiente de determinação**: também conhecido como o **Valor quadrado R**, esta é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para **Coeficiente de Determinação**, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um teste simples usando um conjunto de dados de exemplo. Para explorar o processo de criação e implantação de um modelo mais detalhadamente, continue o tutorial de solução preditiva.

> [!div class="nextstepaction"]
> [Tutorial: Desenvolver uma solução preditiva no Studio (clássico)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
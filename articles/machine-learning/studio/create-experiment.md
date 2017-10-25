---
title: Um teste simples no Machine Learning Studio | Microsoft Docs
description: "Este tutorial de aprendizado de máquina percorre um teste de ciência de dados. Vamos prever o preço de um carro usando um algoritmo de regressão."
keywords: "teste,regressão linear,algoritmos de aprendizado de máquina,tutorial de aprendizado de máquina,técnicas de modelos de previsão, teste de ciência de dados"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: 4cc8e78e3ce22d70546d8a25da17b56f4b7cc166
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Tutorial de aprendizado de máquina: Crie sua primeira experiência no Azure Machine Learning Studio

Se você nunca usou o **Azure Machine Learning Studio** antes, este tutorial é para você.

Neste tutorial, vamos examinar como usar o Studio pela primeira vez para criar um experimento de machine learning. A experiência testará um modelo analítico que prevê o preço de um automóvel com base em variáveis diferentes, como marca e especificações técnicas.

> [!NOTE]
> Este tutorial mostra os conceitos básicos de como arrastar e soltar módulos no seu teste, conectá-los, executar o experimento e examinar os resultados. Não vamos abordar o tópico geral de machine learning ou como selecionar e usar os mais de 100 algoritmos internos e módulos de manipulação de dados incluídos no Estúdio.
>
>Se você é novo no machine learning, a série de vídeos [Ciência de dados para iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md) pode ser um bom lugar para começar. Esta série de vídeos é uma excelente introdução ao machine learning usando linguagem e conceitos cotidianos.
>
>Se você estiver familiarizado com o aprendizado de máquina, mas estiver procurando informações mais gerais sobre o Machine Learning Studio e os algoritmos de machine learning que ele contém, aqui estão alguns bons recursos:
>
- [O que é o Machine Learning Studio?](what-is-ml-studio.md) – Essa é uma visão geral do Estúdio.
- [Conceitos básicos de machine learning com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md) – Este infográfico é útil se você quiser saber mais sobre os diferentes tipos de algoritmos de machine learning incluídos no Machine Learning Studio.
- [Guia do Machine Learning](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) – Este guia aborda informações semelhantes às do infográfico acima, mas em um formato interativo.
- [Roteiro do algoritmo de machine learning](algorithm-cheat-sheet.md) e [Como escolher algoritmos para o Microsoft Azure Machine Learning](algorithm-choice.md) – Este pôster para download e o artigo que o acompanha abordam os algoritmos do Studio detalhadamente.
- [Machine Learning Studio: ajuda de algoritmo e módulo](https://msdn.microsoft.com/library/azure/dn905974.aspx) – Essa é a referência completa para todos os módulos do Studio, incluindo algoritmos de machine learning

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Como o Machine Learning Studio ajuda?

O Machine Learning Studio facilita a configuração de um teste usando módulos de arrastar e soltar pré-programados com técnicas de modelagem de previsão.

Usando um espaço de trabalho visual, interativo, você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em telas interativas. Você os conecta para formar um ***experimento*** executado no Machine Learning Studio.
***Cria um modelo***, ***treina o modelo*** e ***pontua e testa o modelo***.

Você pode iterar seu design de modelo, editando o teste e executando-o até que ele ofereça os resultados que você está procurando. Quando o modelo estiver pronto, você poderá publicá-lo como um ***serviço Web*** para que outras pessoas possam enviar novos dados e obter previsões em troca.

## <a name="open-machine-learning-studio"></a>Abrir o Machine Learning Studio

Para começar com o Estúdio, vá para [https://studio.azureml.net](https://studio.azureml.net). Se você já entrou no Machine Learning Studio antes, clique em **Entrar**. Caso contrário, clique em **Inscrever-se aqui** e escolha entre as opções gratuitas e pagas.

![Entrar no Machine Learning Studio][sign-in-to-studio]
<br/>
***Entrar no Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Cinco etapas para criar um experimento

Neste tutorial de machine learning, você seguirá cinco etapas básicas para criar um experimento no Machine Learning Studio para criar, treinar e pontuar o seu modelo:

- **Criar um modelo**
    - [Etapa 1: Obter dados]
    - [Etapa 2: Preparar os dados]
    - [Etapa 3: Definir recursos]
- **Treinar o modelo**
    - [Etapa 4: Escolher e aplicar um algoritmo de aprendizado]
- **Pontuar e testar o modelo**
    - [Etapa 5: Prever novos preços de automóveis]

[Etapa 1: Obter dados]: #step-1-get-data
[Etapa 2: Preparar os dados]: #step-2-prepare-the-data
[Etapa 3: Definir recursos]: #step-3-define-features
[Etapa 4: Escolher e aplicar um algoritmo de aprendizado]: #step-4-choose-and-apply-a-learning-algorithm
[Etapa 5: Prever novos preços de automóveis]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Você pode encontrar uma cópia funcional do seguinte experimento na [Galeria do Cortana Intelligence](https://gallery.cortanaintelligence.com). Vá para **[Seu primeiro experimento de ciência de dados – previsão de preço de automóvel](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** e clique em **Abrir no Studio** para baixar uma cópia do experimento no seu espaço de trabalho do Machine Learning Studio.


## <a name="step-1-get-data"></a>Etapa 1: Obter dados

A primeira coisa que você precisa para executar o machine learning são dados.
Há uma série de conjuntos de dados de exemplo incluídos no Machine Learning Studio que você pode usar ou você pode importar dados de várias fontes. Neste exemplo, usaremos o conjunto de dados de exemplo **Dados de preço de automóvel (Brutos)**, que está incluído no seu espaço de trabalho.
Esse conjunto de dados inclui entradas para vários automóveis individuais, incluindo informações como marca, modelo, especificações técnicas e preço.

Aqui está a explicação de como colocar o conjunto de dados no seu experimento.

1. Crie um novo experimento clicando em **+NOVO** na parte inferior da janela do Machine Learning Studio, selecione **EXPERIMENTO** e selecione **Experimento em Branco**.

2. O teste recebe um nome padrão que você pode ver na parte superior da tela. Selecione este texto e renomeie para algo que tenha sentido, por exemplo, **Previsão de preço de automóvel**. O nome não precisa ser exclusivo.

    ![Renomear o experimento][rename-experiment]

2. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa Pesquisar na parte superior desta paleta para localizar o conjunto de dados rotulado como **Dados de preço de automóvel (brutos)**. Arraste este conjunto de dados até a tela do experimento.

    ![Localize o conjunto de dados de automóvel e arraste-o para as telas do experimento][type-automobile]
    <br/>
    ***Localize o conjunto de dados de automóvel e arraste-o para a tela do experimento***

Para ver a aparência dos dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**.

![Clique na porta de saída e selecione "Visualizar"][select-visualize]
<br/>
***Clique na porta de saída e selecione "Visualizar"***

> [!TIP]
> Os módulos e conjuntos de dados têm portas de entrada e saída representadas por círculos pequenos – portas de entrada na parte superior, portas de saída na parte inferior.
Para criar um fluxo de dados por meio do experimento, você conectará uma porta de um módulo de saída a uma porta de entrada do outro.
A qualquer momento, você pode clicar na porta de saída de um conjunto de dados ou de um módulo para ver aparência dos dados naquele ponto do fluxo de dados.

No conjunto de dados de exemplo, cada instância de um automóvel aparece como uma linha e as variáveis associadas a cada automóvel aparecem como colunas. Considerando as variáveis de um automóvel específico, vamos tentar prever o preço na coluna à direita (coluna 26, intitulada "preço").

![Exibir os dados de automóveis na janela de visualização de dados][visualize-auto-data]
<br/>
***Exibir os dados de automóveis na janela de visualização de dados***

Feche a janela de visualização clicando no "**x**" no canto superior direito.

## <a name="step-2-prepare-the-data"></a>Etapa 2: Preparar os dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado. Por exemplo, você deve ter observado os valores ausentes nas colunas de várias linhas. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente. Em nosso caso, vamos remover quaisquer linhas que contém valores ausentes. Além disso, a coluna **normalized-losses** tem uma grande proporção de valores ausentes, portanto excluiremos totalmente essa coluna do modelo.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos.

Primeiro, adicionamos um módulo que remove completamente a coluna **normalized-losses** e, em seguida, adicionamos outro módulo que remove qualquer linha com os dados ausentes.

1. Digite **selecionar colunas** na caixa de Pesquisa na parte superior da paleta do módulo para encontrar o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e, em seguida, arraste-o para a tela do experimento. Esse módulo permite selecionar quais colunas de dados desejamos incluir ou excluir no modelo.

2. Conecte a porta de saída do conjunto de dados **Dados de preço de automóvel (Brutos)** na porta de entrada do módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

    ![Adicionar o módulo "Selecionar Colunas no Conjunto de Dados" à tela do experimento e conectá-lo][type-select-columns]
    <br/>
    ***Adicione o módulo "Selecionar Colunas no Conjunto de Dados" à tela do experimento e conectá-lo***

3. Selecione o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e clique em **Iniciar seletor de coluna** no painel **Propriedades**.

    - À esquerda, clique em **Com regras**
    - Em **Começa com**, clique em **Todas as colunas**. Isso instrui [Selecionar Colunas no Conjunto de Dados][select-columns] a passar por todas as colunas (exceto por aquelas que estamos prestes a excluir).
    - Nos menus suspensos, selecione **Excluir** e **nomes da coluna** e clique dentro da caixa de texto. Uma lista de colunas é exibida. Selecione **normalized-losses**e ela será adicionada à caixa de texto.
    - Clique no botão de marca de seleção (OK) para fechar o seletor de coluna (no canto inferior direito).

    ![Inicie o seletor de coluna e exclua a coluna "normalized-losses"][launch-column-selector]
    <br/>
    ***Inicie o seletor de coluna e exclua a coluna "normalized-losses"***

    Agora, o painel de propriedades de **Selecionar Colunas no Conjunto de Dados** indica que ele passará por todas as colunas do conjunto de dados exceto por **normalized-losses**.

    ![O painel de propriedades mostra que a coluna "normalized-losses" foi excluída][showing-excluded-column]
    <br/>
    ***O painel de propriedades mostra que a coluna "normalized-losses" foi excluída***

    > [!TIP]
    É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. Neste caso, clique duas vezes no módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e digite o comentário “Excluir perdas normalizadas”.
    >
    >


    ![Clique duas vezes em um módulo para adicionar um comentário][add-comment]
    <br/>
    ***Clique duas vezes em um módulo para adicionar um comentário***

3. Arraste o módulo [Limpar valores ausentes][clean-missing-data] até a tela do teste e conecte-o ao módulo [Selecionar Colunas no Conjunto de Dados][select-columns]. No painel **Propriedades** selecione **Remover linha inteira** em **Modo de limpeza**. Isso direciona o [Limpar Dados Ausentes][clean-missing-data] a limpar os dados removendo as linhas que têm valores ausentes. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".

    ![Definir o modo de limpeza para "Remover linha inteira" no módulo "Limpar Dados Ausentes"][set-remove-entire-row]
    <br/>
    ***Defina o modo de limpeza para "Remover linha inteira" no módulo "Limpar Dados Ausentes"***

4. Execute o experimento clicando em **EXECUTAR** na parte inferior da página.

    Quando o experimento terminar a execução, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso. Observe também o status **Execução concluída** no canto superior direito.

![Depois da execução, o experimento deve ser semelhante a][early-experiment-run]
<br/>
***Depois da execução, o experimento deve ser semelhante a***

> [!TIP]
> Por que executamos o experimento agora? Ao executar o experimento, as definições de coluna dos dados passam do conjunto de dados, através do módulo [Selecionar Colunas no Conjunto de Dados][select-columns] e através do módulo [Limpar Dados Ausentes][clean-missing-data]. Isso significa que todos os módulos que conectarmos ao [Limpar Dados Ausentes][clean-missing-data] também terão essas mesmas informações.

Tudo o que fizemos no experimento até esse ponto é limpar os dados. Se desejar exibir o conjunto de dados limpo, clique na porta de saída à esquerda do módulo [Limpar Dados Ausentes][clean-missing-data] e selecione **Visualizar**. Observe que a coluna **normalized-losses** não está mais incluída e não há valores ausentes.

Agora que os dados estão limpos, estamos prontos para especificar quais recursos usaremos no modelo preditivo.

## <a name="step-3-define-features"></a>Etapa 3: Definir recursos

No aprendizado de máquina, *recursos* são propriedades individuais mensuráveis de algo em que você está interessado. Em nosso conjunto de dados, cada linha representa um automóvel e cada coluna é um recurso desse automóvel.

Localizar um bom conjunto de recursos para criar um modelo de previsão requer experimentação e conhecimento sobre o problema que você deseja resolver. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos têm uma forte correlação com outros recursos e podem ser removidos. Por exemplo, city-mpg e highway-mpg estão intimamente relacionados, por isso podemos manter um e remover o outro sem afetar a previsão de forma significativa.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso conjunto de dados. É possível voltar depois e selecionar diferentes recursos, executar o experimento novamente e ver se você obtém melhores resultados. Mas, para começar, vamos testar os seguintes recursos:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arraste outro módulo [Selecionar Colunas no Conjunto de Dados][select-columns] à tela do experimento. Conecte a porta de saída à esquerda do módulo [Limpar Dados Ausentes][clean-missing-data] à entrada do módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

    ![Conectar o módulo "Selecionar Colunas no Conjunto de Dados" no módulo "Limpar Dados Ausentes"][connect-clean-to-select]
    <br/>
    ***Conectar o módulo "Selecionar Colunas no Conjunto de Dados" no módulo "Limpar Dados Ausentes"***

2. Clique duas vezes no módulo e digite “Selecionar recursos de previsão".

2. Clique em **Iniciar seletor de coluna** no painel de **Propriedades**.

3. Clique em **Com regras**.

4. Em **Começa com**, clique em **Nenhuma coluna**. Na linha do filtro, selecione **Incluir** e **nomes de coluna** e selecione a lista de nomes de coluna na caixa de texto. Isso instrui o módulo a passar somente pelas colunas (recursos) que especificamos.

5. Clique no botão de marca de seleção (OK).

    ![Selecione as colunas (recursos) para incluir na previsão][select-columns-to-include]
    <br/>
    ***Selecione as colunas (recursos) a incluir na previsão***

Isso produz um conjunto de dados filtrado que contém somente os recursos que desejamos passar para o algoritmo de aprendizado que usaremos na próxima etapa. Posteriormente, é possível retornar e tentar novamente com uma seleção diferente de recursos.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Etapa 4: Escolher e aplicar um algoritmo de aprendizado

Agora que os dados estão prontos, construir um modelo preditivo consiste em treinamento e teste. Vamos usar nossos dados para treinar o modelo e depois testar o modelo para ver com que proximidade ele é capaz de prever os preços.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classificação* e *regressão* são dois tipos de técnicas de algoritmo de machine learning supervisionado. A classificação prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelha, azul ou verde). A regressão é usada para prever um número.

Como desejamos prever o preço, que é um número, usaremos um algoritmo de regressão. Neste exemplo, usaremos um modelo simples de *regressão linear*.

> [!TIP]
> Se quiser saber mais sobre os diferentes tipos de algoritmos de machine learning e quando usá-los, você pode assistir o primeiro vídeo da série Ciência de Dados para Iniciantes: [As cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md). Você também pode examinar o infográfico [Conceitos básicos de machine learning com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md) ou confira o [Roteiro do algoritmo de machine learning](algorithm-cheat-sheet.md).

Treinamos o modelo, dando a ele um conjunto de dados que inclui o preço. O modelo examina os dados e procura correlações entre os recursos de um automóvel e seu preço. Em seguida, testaremos o modelo – vamos dar a ele um conjunto de recursos para automóveis, com os quais estamos familiarizados e veremos qual a proximidade da previsão feita pelo modelo com o preço conhecido.

Vamos usar nossos dados para treinar o modelo e para testá-lo, dividindo-os em conjuntos de dados separados de treinamento e de teste.

1. Selecione e arraste o módulo [Dividir Dados][split] até a tela do teste e conecte-o à porta de saída do último módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

2. Clique no módulo [Dividir Dados][split] para selecioná-lo. Encontre o painel **Fração de linhas no primeiro conjunto de dados de saída** (no painel **Propriedades** à direita da tela) e defina-o para 0,75. Desta forma, usaremos 75% dos dados para treinar o modelo e manteremos 25% para teste.(posteriormente, você pode experimentar usando porcentagens diferentes).

    ![Configure a fração de divisão do módulo "Dividir dados" para 0,75][set-split-data-percentage]
    <br/>
    ***Defina a fração de divisão do módulo "Dividir Dados" para 0,75***

    > [!TIP]
    > Alterando o parâmetro **Semente aleatória** , é possível produzir amostras aleatórias diferentes para treinamento e teste. Esse parâmetro controla a alimentação do gerador de número pseudo-aleatório.

2. Execute o experimento. Quando o experimento é executado os módulos [Selecionar Colunas no Conjunto de Dados][select-columns] e [Dividir Dados][split] passam definições de coluna para os módulos que incluiremos em seguida.  

3. Para selecionar o algoritmo de aprendizado, expanda a categoria **Machine Learning** na paleta do módulo à esquerda da tela e expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina. Para este experimento, selecione módulo [Regressão Linear][linear-regression] na categoria **Regressão** e arraste-o para a tela do experimento.
(Você também pode localizar o módulo digitando "regressão linear" na caixa de pesquisa da paleta.)

4. Localize e arraste o módulo [Modelo de Treinamento][train-model] até a tela do experimento. Conecte a saída do módulo [Regressão Linear][linear-regression] com a entrada à esquerda do módulo [Modelo de treinamento][train-model] e conecte a saída de dados de treinamento (porta à esquerda) do módulo [Dividir Dados][split] com a entrada à direita do módulo [Modelo de treinamento][train-model].

    ![Conectar o módulo "Modelo de treinamento" com os módulos "Regressão Linear" e "Dividir Dados"][connect-train-model]
    <br/>
    ***Conecte o módulo "Modelo de treinamento" aos módulos "Regressão Linear" e "Dividir Dados"***

5. Selecione o módulo [Modelo de Treinamento][train-model], clique em **Iniciar seletor de coluna** no painel **Propriedades** e selecione a coluna **preço**. Este é o valor que nosso modelo vai prever.

    Selecione a coluna **preço** movendo-a da lista de **Colunas disponíveis** para a lista de **Colunas selecionadas**.

    ![Selecione a coluna de preço para o módulo "Modelo de treinamento"][select-price-column]
    <br/>
    ***Selecione a coluna de preço para o módulo "Modelo de treinamento"***

6. Execute o experimento.

Agora temos um modelo de regressão treinado que pode ser usado para pontuar novos dados de automóveis para fazer previsões de preço.

![Depois da execução, o experimento agora deve ser semelhante a][second-experiment-run]
<br/>
***Depois da execução, o experimento agora deve ser semelhante a***

## <a name="step-5-predict-new-automobile-prices"></a>Etapa 5: Prever novos preços de automóveis

Agora que treinamos o modelo usando 75% de nossos dados, podemos usá-lo para classificar os outros 25% dos dados e ver se nosso modelo funciona bem.

1. Localize e arraste o módulo [Modelo de Pontuação][score-model] até a tela do experimento. Conecte a saída do módulo [Modelo de Treinamento][train-model] com a porta de entrada à esquerda do [Modelo de Pontuação][score-model]. Conecte a porta de entrada direita à saída de dados de teste (porta à direita) do módulo [Dividir Dados][split] à porta de entrada direita do [Modelo de Pontuação][score-model].

    ![Conectar o módulo "Modelo de Pontuação" com os módulos "Modelo de Treinamento" e "Dividir Dados"][connect-score-model]
    <br/>
    ***Conecte o módulo "Modelo de Pontuação" com os módulos "Modelo de Treinamento" e "Dividir Dados"***

2. Execute o experimento e veja a saída do módulo [Modelo de Pontuação][score-model] (clique duas vezes na porta de saída do [Modelo de Pontuação][score-model] e selecione **Visualizar**). A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.  

    ![Saída do módulo "Modelo de Pontuação"][score-model-output]
    <br/>
    ***Saída do módulo "Modelo de Pontuação"***

3. Por fim, podemos testar a qualidade dos resultados. Selecione e arraste o módulo [Modelo de Avaliação][evaluate-model] para a tela do experimento e conecte a saída do módulo [Modelo de Pontuação][score-model] com a entrada à direita do [Modelo de Avaliação][evaluate-model].

    > [!TIP]
    > Existem duas portas de entrada no módulo [Modelo de Avaliação][evaluate-model] porque ele pode ser usado para comparar dois modelos lado a lado. Posteriormente, você pode adicionar outro algoritmo para o experimento e usar o [Modelo de Avaliação][evaluate-model] para ver qual deles oferece melhores resultados.

4. Execute o experimento.

Execute o experimento e exiba a saída do módulo [Modelo de Avaliação][evaluate-model], clique duas vezes na porta de saída e selecione **Visualizar**.

![Resultados de avaliação para o experimento][evaluation-results]
<br/>
***Resultados de avaliação para o experimento***

As estatísticas a seguir são mostradas para nosso modelo:

- **Média de erros absolutos** (MAE) - A média de erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Raiz quadrada dos erros ao quadrado** (RMSE) - A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
- **Erro absoluto relativo**- A média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de valores reais.
- **Erro ao quadrado relativo**- A média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
- **Coeficiente de Determinação** – Também conhecido como o **Valor quadrado R**, esta é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para **Coeficiente de Determinação**, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

## <a name="final-experiment"></a>Experimento final

O experimento final deve se parecer como o seguinte:

![O experimento final][complete-linear-regression-experiment]
<br/>
***O experimento final***

## <a name="next-steps"></a>Próximas etapas

Agora que concluiu o primeiro tutorial de machine learning e seu experimento foi configurado, você pode continuar a melhorar o modelo e, em seguida, implantá-lo como um serviço Web de previsão.

- **Iterar para tentar melhorar o modelo** – Por exemplo, você pode alterar os recursos usados em sua previsão. Ou é possível modificar as propriedades do algoritmo [Regressão Linear][linear-regression] ou tentar um algoritmo diferente. Ainda é possível adicionar vários algoritmos de Machine Learning à sua experiência de aprendizagem e comparar dois usando o módulo [Avaliar modelo][evaluate-model].
Para obter um exemplo de como comparar vários modelos em um único experimento, consulte [Comparar Regressores](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) na [Galeria do Cortana Intelligence](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Para copiar qualquer iteração do experimento, use o botão **SALVAR COMO** na parte inferior da página. Você pode ver todas as iterações do experimento clicando em **EXIBIR HISTÓRICO DE EXECUÇÃO** na parte inferior da página. Para obter mais detalhes, consulte [Gerenciar iterações do experimento no Azure Machine Learning Studio][runhistory].

[runhistory]: manage-experiment-iterations.md

- **Implantar o modelo como um serviço Web de previsão** – Quando estiver satisfeito com seu modelo, você poderá implantá-lo como um serviço Web a ser usado para prever preços de automóveis usando novos dados. Consulte [Implantar um serviço Web do Azure Machine Learning][publish] para obter mais detalhes.

[publish]: publish-a-machine-learning-web-service.md

Quer saber mais? Para obter um passo a passo maior e mais detalhado do processo de criação, treinamento, pontuação e implantação de um modelo, veja [Desenvolver uma solução preditiva usando o Azure Machine Learning][walkthrough].

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

---
title: 'Tutorial 3 do ML Studio (clássico): Implantar modelos de risco de crédito – Azure'
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a terceira parte de uma série com três partes. Ele mostra como implantar um modelo como um serviço Web.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos, implantar, serviço Web
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 68a5f6773543b922d024336556e4de24dd96fd33
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517375"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: Implantar modelo de risco de crédito – Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Essa é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Neste tutorial, você analisará de maneira aprofundada o processo de desenvolvimento de uma solução de análise preditiva. Você desenvolverá um modelo simples no Machine Learning Studio (clássico).  Em seguida, você implantará o modelo como um serviço Web do Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é a **terceira parte de uma série com três partes**.

Suponha que você precisa prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial simplificará um pouco esse tópico. Você usará isso como exemplo da forma de criar uma solução de análise preditiva usando o Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço Web do Machine Learning para esta solução. 

Neste tutorial de três partes, você começará com os dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolverá e treinará um modelo preditivo.  Por fim, você implantará o modelo como um serviço Web.

Na [primeira parte do tutorial](tutorial-part1-credit-risk.md), você criou um workspace do Machine Learning Studio (clássico), carregou dados e criou um experimento.

Na [segunda parte do tutorial](tutorial-part2-credit-risk-train.md), você treinou e avaliou modelos.

Nesta parte do tutorial, você vai:

> [!div class="checklist"]
> * Preparar para a implantação
> * Implantar o serviço Web
> * Testar o serviço Web
> * Gerenciar o serviço Web
> * Acessar o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

Concluir a [segunda parte do tutorial](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação
Para que outras pessoas possam usar o modelo preditivo que você desenvolveu neste tutorial, implante-o como um serviço Web no Azure.

Até este ponto, você esteve testando nosso modelo de treinamento. Mas o serviço implantado não fará mais o treinamento – ele gerará previsões pontuando a entrada do usuário com base em nosso modelo. Portanto, vamos fazer algumas preparações para converter esse experimento de ***treinamento** _ em um experimento _ *_preditivo_**. 

A preparação para a implantação é um processo de três etapas:  

1. Remova um dos modelos
1. Converta o *teste de treinamento* criado em um *teste preditivo*
1. Implantar o teste preditivo como um serviço Web

### <a name="remove-one-of-the-models"></a>Remova um dos modelos

Primeiro, é necessário reduzir esse experimento um pouco. no momento, você tem dois modelos diferentes no experimento, mas deseja usar apenas um modelo ao implantar isso como um serviço Web.  

Digamos que você decidiu que o modelo de árvore aumentado apresentou melhor desempenho do que o modelo SVM. Portanto, a primeira coisa a fazer é remover o módulo [Computador de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treiná-lo. Talvez você queira fazer uma cópia do teste clicando primeiro em **Salvar como** na parte inferior da tela do teste.

é necessário excluir os seguintes módulos:  

* [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine]
* Módulos [Treinar Modelo][train-model] e [ Modelo de Pontuação][score-model] que foram conectados a ele
* [Normalizar Dados][normalize-data] (ambos)
* [Avaliar Modelo][evaluate-model] (pois terminamos de avaliar os modelos)

Selecione cada módulo e pressione a tecla Excluir, ou clique com botão direito do mouse no módulo e selecione **Excluir**. 

![Realça quais módulos para excluir para remover o modelo de máquina de vetor de suporte](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Nosso modelo agora deve ser semelhante ao seguinte:

![Experimento resultante quando o modelo de máquina de vetor de suporte é excluído](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora, estamos prontos para implantar esse modelo usando a [Árvore de Decisão Aumentada em Duas Classes][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo

Para obter esse modelo pronto para implantação, é necessário converter esse teste de treinamento em um teste preditivo. Isso envolve três etapas:

1. Salvar o modelo que você treinou e substituir nossos módulos de treinamento
1. Cortar o teste para remover os módulos necessários somente para treinamento
1. Definir onde o serviço Web aceitará a entrada e onde ele gera a saída

você poderia fazer isso manualmente, mas felizmente todas as três etapas podem ser realizadas apenas clicando em **Configurar Serviço Web** na parte inferior da tela de teste (e selecionando a opção **Serviço Web Preditivo**).

> [!TIP]
> Se você quiser obter mais detalhes sobre o que acontece quando você converte um teste de treinamento em um teste de previsão, confira [Como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).

Quando você clica em **Configurar Serviço Web**, várias coisas acontecem:

* O modelo treinado é convertido em um único módulo **Modelo Treinado** e armazenado na paleta de módulos à esquerda da tela de teste (você o encontrará em **Modelos Treinados**)
* Os módulos que foram usados para o treinamento são removidos; especificamente:
  * [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree]
  * [Treinar Modelo][train-model]
  * [Dividir Dados][split]
  * o segundo módulo [Executar Script R][execute-r-script] que foi usado para testar os dados
* O modelo treinado salvo é adicionado de volta ao teste
* Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados (eles identificam onde os dados do usuário entrarão no modelo e quais dados retornam quando o serviço Web é acessado)

> [!NOTE]
> Você pode ver que o teste é salvo em duas partes em guias que foram adicionadas na parte superior da tela do teste. O teste de treinamento original está na guia **Teste de treinamento**, e o teste de previsão recém-criado está em **Teste de previsão**. O teste preditivo é o que será implantado como um serviço Web.

é necessário executar uma etapa adicional com esse experimento específico.
você adicionou dois módulos [Executar Script R][execute-r-script] para fornecer uma função de ponderação aos dados. Isso era apenas um truque de que você precisava para treinamento e teste, a fim de levar esses módulos para o modelo final.
O Machine Learning Studio (clássico) removeu um módulo [Executar Script R][execute-r-script] quando removeu o módulo [Divisão][split]. Agora é possível remover o outro e conectar o [Editor de metadados][metadata-editor] diretamente ao [Modelo de pontuação][score-model].    

Agora o teste deve se parecer como isto:  

![Pontuando o modelo treinado](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Você pode estar se perguntando por que deixou o conjunto de dados de Dados de cartão de crédito alemão UCI no teste preditivo. O serviço pontuará os dados do usuário, não o conjunto de dados original, portanto, porque deixar o conjunto de dados original no modelo?
> 
> É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. você deixou esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.  
> 
>Uma coisa importante a observar é que, se o conjunto de dados original continha o rótulo, o esquema esperado da entrada da Web também esperaria uma coluna com o rótulo! Uma maneira de contornar isso é remover o rótulo e quaisquer outros dados que estavam no conjunto de dados de treinamento, mas não estarão nas entradas da Web, antes de conectar a entrada da Web e o conjunto de dados de treinamento em um módulo comum. 
> 

Execute o teste uma última vez (clique em **Executar**). Se você deseja verificar se o modelo ainda está funcionando, clique na saída do módulo [Modelo de Pontuação][score-model] e selecione **Exibir Resultados**. Você pode ver que os dados originais são exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidade de pontuação ("Probabilidades de pontuação"). 

## <a name="deploy-the-web-service"></a>Implantar o serviço Web
Você pode implantar o teste como um serviço Web clássico ou um novo serviço Web com base no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implantar como serviço Web clássico
Para implantar um serviço Web Clássico derivado do teste, clique em **Implantar Serviço Web** abaixo da tela e selecione **Implantar Serviço Web [Clássico]** . O Machine Learning Studio (clássico) implanta o teste como um serviço Web e leva você até o painel para o serviço Web. Dessa página, é possível retornar ao experimento (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (confira **Testar o serviço Web** abaixo). Também há informações aqui para criar aplicativos que possam acessar o serviço Web (mais sobre isso na próxima etapa deste tutorial).

![Painel de serviço Web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO** . Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simplificados aos dados de entrada e saída.  

![Configurar o serviço Web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implantar como um novo serviço Web

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implantar um Novo serviço Web derivado de nosso teste:

1. Clique em **Implantar Serviço Web** abaixo da tela e selecione **Implantar Serviço Web [Novo]** . O Machine Learning Studio (clássico) o transfere para a página de **Teste de implantação** de serviços Web do Azure Machine Learning.

1. Insira um nome para o serviço Web. 

1. Para **Plano de Preços**, você pode selecionar um plano de preços existente, ou selecionar "Criar novo", dar um nome para o novo plano e selecionar a opção de plano mensal. As camadas do plano usam como padrão os planos da região padrão e o serviço Web é implantado nessa região.

1. Clique em **Implantar**.

Após alguns minutos, a página **Início Rápido** do serviço Web é aberta.

Você pode configurar o serviço clicando na guia **Configurar**. Aqui você pode modificar o título do serviço e fornecer uma descrição. 

Para testar o serviço Web, clique na guia **Testar** (veja **Testar o serviço Web** abaixo). Para saber mais sobre a criação de aplicativos que podem acessar o serviço Web, clique na guia **Consumir** (a próxima etapa neste tutorial fornecerá mais detalhes).

> [!TIP]
> Você pode atualizar o serviço Web depois de implantá-lo. Por exemplo, se você quiser alterar o modelo, edite o teste de treinamento, ajuste os parâmetros de modelo e clique em **Implantar Serviço Web**, selecione **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]** . Quando você implantar o teste novamente, ele substituirá o serviço Web, agora usando o modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço Web

Quando o serviço Web é acessado, os dados do usuário entram por meio do módulo **Entrada do serviço Web** e são passados para o módulo [Modelo de Pontuação][score-model], nos quais são pontuados. Da forma como você configurou o teste de previsão, o modelo espera dados no mesmo formato do conjunto de dados de risco de crédito original.
Os resultados retornam para o usuário do serviço Web, passando pelo módulo **Saída do serviço Web**.

> [!TIP]
> Da forma como você configurou o teste preditivo, todos os resultados do módulo [Modelo de Pontuação][score-model] são retornados. Isso inclui todos os dados de entrada, o valor do risco de crédito e a probabilidade de pontuação. Mas você pode retornar algo diferente se desejar – por exemplo, você pode retornar apenas o valor do risco de crédito. Para fazer isso, insira um módulo [Selecionar Colunas][select-columns] entre [Modelo de Pontuação][score-model] e **Saída do serviço Web** para eliminar colunas que você não deseja que o serviço Web retorne. 
> 
> 

Você pode testar o serviço Web Clássico no **Machine Learning Studio (clássico)** ou no portal de **Serviços Web do Azure Machine Learning**.
Você pode testar um novo serviço Web no portal dos **Serviços Web do Azure Machine Learning**.

> [!TIP]
> Durante o teste no portal de serviços Web do Azure Machine Learning, você pode fazer com que o portal crie dados de exemplo e use-os para testar o serviço de solicitação-resposta. Na página **Configurar**, selecione "Sim" para **Dados de Exemplo Habilitados?** . Quando você abre o guia de Solicitação-Resposta na página **Teste**, o portal preenche os dados de exemplo obtidos do conjunto de dados de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço Web Clássico

Você pode testar o serviço Web Clássico no Machine Learning Studio (clássico) ou no portal de Serviços Web Machine Learning. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testar no Machine Learning Studio (clássico)

1. Na página **PAINEL** do serviço Web, clique no botão **Testar** em **Ponto de Extremidade Padrão**. Uma caixa de diálogo é exibida e solicita os dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito original.  

1. Insira um conjunto de dados e clique em **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testar no portal de Serviços Web do Azure Machine Learning

1. Na página **PAINEL** do serviço Web, clique no link **Visualização do teste** em **Ponto de Extremidade Padrão**. A página de teste no portal de serviços Web do Azure Machine Learning para o ponto de extremidade de serviço Web é aberta e solicita dados para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito original.

2. Clique em **Solicitação-Resposta**. 

### <a name="test-a-new-web-service"></a>Testar um novo serviço Web

Você pode testar um novo serviço Web no portal dos Serviços Web do Azure Machine Learning.

1. No portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net/quickstart), clique em **Testar** na parte superior da página. A página de **Teste** é aberta e você pode inserir dados para o serviço. Os campos de entrada exibidos correspondem às mesmas colunas que apareciam no conjunto de dados de risco de crédito original. 

1. Insira um conjunto de dados e clique em **Testar solicitação-resposta**.

Os resultados do teste são exibidos no lado direito da página na coluna de saída. 


## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

Assim que você implantar o serviço Web, clássico ou novo, poderá gerenciá-lo no [portal de serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Para monitorar o desempenho do seu serviço Web:

1. Entre no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
1. Clique em **Serviços Web**
1. Clique no seu serviço Web
1. Clique em **Painel**

## <a name="access-the-web-service"></a>Acessar o serviço Web

Na etapa anterior neste tutorial, você implantou um serviço Web que usa o nosso modelo de previsão de risco de crédito. Agora, os usuários podem enviar dados a ele e receber os resultados. 

O serviço Web é um serviço Web do Azure que pode receber e retornar dados usando as APIs REST de uma das duas maneiras:  

* **Solicitação/Resposta** – O usuário envia uma ou mais linhas de dados de crédito para o serviço usando um protocolo HTTP, e o serviço responde com um ou mais conjunto de resultados.
* **Execução em lote** - o usuário armazena uma ou mais linhas de dados de crédito em um blob do Azure e envia o local do blob ao serviço. O serviço pontua todas as linhas de dados no blob de entrada, armazena os resultados em outro blob e retorna a URL desse contêiner. 

> [!NOTE]
> Os nomes de coluna de recurso no Studio (clássico) **diferenciam maiúsculas e minúsculas**. Verifique se os dados de entrada para invocar o serviço Web têm os mesmos nomes de coluna do conjunto de dados de treinamento.

Para obter mais informações sobre como acessar e consumir o serviço Web, confira [Consumir um serviço Web do Azure Machine Learning com um modelo de aplicativo Web](./consume-web-services.md).



## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu estas etapas:

> [!div class="checklist"]
> * Preparar para a implantação
> * Implantar o serviço Web
> * Testar o serviço Web
> * Gerenciar o serviço Web
> * Acessar o serviço Web

Você também pode desenvolver um aplicativo personalizado para acessar o serviço Web usando código inicial fornecido nas linguagens de programação R, C# e Python.

> [!div class="nextstepaction"]
> [Consumir um serviço Web do Azure Machine Learning](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
---
title: 'ML Studio (clássico): como um modelo se torna um serviço Web-Azure'
description: Uma visão geral da mecânica de como seu modelo do Azure Machine Learning Studio (clássico) evolui de um teste de desenvolvimento para um serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: c92f8c74da76b2ac938892e27f3d6be9c70c3238
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507251"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Como um modelo do Machine Learning Studio (clássico) evolui de uma experiência para um serviço Web

**aplica-se a:** ![ Essa é uma marca de seleção, o que significa que este artigo se aplica a Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ é um X, o que significa que este artigo se aplica a Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (clássico) fornece uma tela interativa que permite desenvolver, executar, testar e iterar um **_experimento_* _ que representa um modelo de análise preditiva. Há uma grande variedade de módulos disponíveis que podem:

_ Dados de entrada em seu experimento
* Manipular os dados
* Treinar um modelo usando algoritmos de aprendizado de máquina
* Pontuar o modelo
* Avaliar os resultados
* Exibir os valores finais

Quando estiver satisfeito com o experimento, você poderá implantá-lo como um serviço Web do ***clássico Azure Machine Learning** _ ou um _*_novo Azure Machine Learning Web Service_*_ para que os usuários possam enviar novos dados e receber resultados.

Neste artigo, ofereceremos uma visão geral sobre a mecânica de como seu modelo de Machine Learning evolui de um experimento de desenvolvimento para um serviço Web operacional.

> [!NOTE]
> Há outras maneiras de desenvolver e implantar modelos de aprendizado de máquina, mas este artigo foca em como usar o Machine Learning Studio (clássico). Por exemplo, para ler uma descrição de como criar um serviço Web preditivo clássico com R, confira a postagem no blog [Build & Deploy Predictive Web Apps Using RStudio and Azure Machine Learning studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml) (Compilar e implantar aplicativos Web preditivos usando o RStudio e o Azure Machine Learning Studio).
>
>

Embora Azure Machine Learning Studio (clássico) seja projetado para ajudá-lo a desenvolver e implantar um modelo de análise de _predictive *, é possível usar o estúdio (clássico) para desenvolver um experimento que não inclua um modelo de análise preditiva. Por exemplo, um experimento pode simplesmente inserir dados, manipulá-los e gerar os resultados. Assim como um teste de análise preditiva, você pode implantar esse teste não preditivo como um serviço Web, mas esse é um processo mais simples porque o experimento não está treinando ou pontuando um modelo de aprendizado de máquina. Embora esse não seja o uso usual do Studio (clássico), o incluiremos na discussão para que possamos dar uma explicação completa de como funciona o Studio (clássico).

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvendo e implantando um serviço Web preditivo
Estes são os estágios pelos quais passa uma solução típica quando você a desenvolve e implanta usando o Machine Learning Studio (clássico):

![Fluxo de implantação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Estágios de um modelo típico de análise preditiva*

### <a name="the-training-experiment"></a>O teste de treinamento
O ***experimento de treinamento** _ é a fase inicial de desenvolvimento de seu serviço Web no Machine Learning Studio (clássico). A finalidade do teste de treinamento é fornecer a você um lugar para desenvolver, testar, iterar e, eventualmente, treinar um modelo de aprendizado de máquina. Você pode até mesmo treinar vários modelos simultaneamente já que procura pela melhor solução, mas assim que terminar de testar, selecionará um único modelo treinado e eliminará o restante do teste. Para obter um exemplo de como desenvolver um teste de análise preditiva, consulte [Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>O teste preditivo
Depois de ter um modelo treinado em seu experimento de treinamento, clique em _*configurar serviço Web** e selecione **serviço web de previsão** no Machine Learning Studio (clássico) para iniciar o processo de conversão do teste de treinamento em um **_experimento de previsão_*_. A finalidade do experimento preditivo é usar o modelo treinado para pontuar novos dados, com o objetivo de se tornar posteriormente operacionalizado como um serviço Web do Azure.

Essa conversão é feita para você pelas seguintes etapas:

_ Converta o conjunto de módulos usados para treinamento em um único módulo e salve-o como um modelo treinado
* Eliminar quaisquer módulos externos não relacionados à pontuação
* Adicionar portas de entrada e saída que serão usadas pelo possível serviço Web

Pode haver mais alterações que você queira fazer para ter seu experimento preditivo pronto para ser implantado como um serviço Web. Por exemplo, se você quiser que o serviço Web gere apenas um subconjunto dos resultados, você pode adicionar um módulo de filtragem antes da porta de saída.

O teste de treinamento não é descartado neste processo de conversão. Quando o processo for concluído, você terá duas guias no Studio (clássico): uma para o teste de treinamento e outra para o preditivo. Dessa forma, você poderá alterar o teste de treinamento antes de implantar o serviço Web e recompilar o teste preditivo. Ou você pode salvar uma cópia do teste de treinamento para iniciar outra linha de teste.

> [!NOTE]
> Quando você clica em **Serviço Web Preditivo**, um processo automático é iniciado para converter seu teste de treinamento em um teste preditivo, e isso funciona bem na maioria dos casos. Se o seu teste de treinamento for complexo (por exemplo, se você tiver vários caminhos para treinamento reunidos), convém fazer essa conversão manualmente. Para obter mais informações, consulte [Como preparar o modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>O serviço Web
Quando estiver satisfeito que o teste preditivo está pronto, implante o serviço como um serviço Web clássico ou um novo serviço Web com base no Azure Resource Manager. Para colocar em operação seu modelo implantando-o como um *Serviço Web clássico do Machine Learning*, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [clássico]** . Para implantar como *Novo serviço Web de Machine Learning*, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [Novo]** . Os usuários podem agora enviar dados para seu modelo usando a API REST do serviço Web e receber os resultados de volta. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O caso não típico: criando um serviço Web não preditivo
Se o teste não treinar um modelo de análise preditivo, você não precisará criar um teste de treinamento e um teste de pontuação. Há apenas um teste, e você pode implantá-lo como um serviço Web. O Machine Learning Studio (clássico) detecta se o teste contém um modelo preditivo analisando os módulos usados.

Depois que tiver iterado no teste e estiver satisfeito com ele:

1. Clique em **Configurar Serviço Web** e selecione **Novo Treinamento de Serviço Web** – nós de entrada e saída são adicionados automaticamente
2. Clique em **Executar**
3. Clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [clássico]** ou **Implantar Serviço Web [Novo]** dependendo do ambiente no qual você deseja implantar.

Seu serviço Web está implantado, e você pode acessá-lo e gerenciá-lo como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>Atualização de seu serviço Web
Agora que implantou o teste como um serviço Web, o que acontecerá se precisar atualizá-lo?

Depende do que você precisa atualizar:

**Para alterar a entrada ou saída, ou para modificar como o serviço Web manipula dados**

Se não estiver alterando o modelo, mas estiver apenas alterando como o serviço Web manipula os dados, poderá editar o teste preditivo e clicar em **Implantar serviço Web** e selecionar **Implantar serviço Web [Clássico]** ou **Implantar serviço Web [Novo]** novamente. O serviço Web é parado, o teste preditivo atualizado é implantado e o serviço Web é reiniciado.

Aqui está um exemplo: suponha que seu experimento preditivo retorne toda a linha de dados de entrada com o resultado previsto. Talvez você queira que o serviço Web retorne apenas o resultado. Então adicione um módulo de **Colunas de Projeto** no experimento preditivo, logo antes da porta de saída, para excluir colunas que não sejam o resultado. Quando você clica em **Implantar Serviço Web** e seleciona **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]** novamente, o serviço Web é atualizado.

**Você quer treinar novamente o modelo com novos dados**

Se você desejar manter a modelo de aprendizado de máquina, mas se quiser treiná-lo novamente com novos dados, terá duas opções:

1. **Treinar novamente o modelo enquanto o serviço Web estiver em execução** -se você quiser treinar novamente seu modelo enquanto o serviço Web de previsão estiver em execução, faça isso fazendo algumas modificações no teste de treinamento para torná-lo um experimento de novo **_treinamento_*, então você pode implantá-lo como um* serviço Web de novo _treinamento_**. Para obter instruções sobre como fazer isso, veja [Treinar novamente os modelos do Machine Learning de forma programática](./retrain-machine-learning-model.md).
2. **Volte para o teste de treinamento original e use dados de treinamento diferentes para desenvolver seu modelo** – seu teste preditivo está vinculado ao serviço Web, mas o teste de treinamento não é diretamente vinculado dessa maneira. Se você modificar o teste de treinamento original e clicar em **Configurar Serviço Web**, será criado um *novo* teste preditivo que, quando implantado, criará um *novo* serviço Web. Isso não apenas atualiza o serviço Web original.

   Se você precisar modificar o teste de treinamento, abra-o e clique em **Salvar como** para fazer uma cópia. Isso deixará intacto o teste de treinamento original, o teste preditivo e o serviço Web. Agora você pode criar um novo serviço Web com suas alterações. Depois de implantar o novo serviço Web, você poderá decidir se deseja interromper o serviço Web anterior ou mantê-lo em execução junto com o novo.

**Você deseja treinar um modelo diferente**

Se você deseja fazer alterações em seu teste preditivo original, como selecionar um algoritmo de aprendizado de máquina diferente, tentar um método diferente de treinamento etc., siga o segundo procedimento descrito acima para treinar novamente seu modelo: abra o teste de treinamento, clique em **Salvar como** para fazer uma cópia e inicie o novo caminho de desenvolvimento de modelo, criando o teste preditivo e implantando o serviço Web. Isso criará um novo serviço Web não relacionado ao original – você pode decidir qual deles, ou ambos, continuará em execução.

## <a name="next-steps"></a>Próximas etapas
Para obter mais detalhes sobre o processo de desenvolvimento e teste, veja os seguintes artigos:

* converter o experimento – [Como preparar o modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md)
* implantando o serviço Web – [Implantar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* treinando novamente o modelo - [Treinar novamente os modelos do Machine Learning de forma programática](./retrain-machine-learning-model.md)

Para obter exemplos do processo inteiro, consulte:

* [Tutorial de aprendizado de máquina: Crie seu primeiro teste no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Passo a passo: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning](tutorial-part1-credit-risk.md)
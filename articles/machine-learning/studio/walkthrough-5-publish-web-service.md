---
title: "Etapa 5: implantar o serviço Web do Machine Learning | Microsoft Docs"
description: "Etapa 5 do passo-a-passo Desenvolver uma solução preditiva: implantar um teste preditivo no Machine Learning Studio como um serviço Web."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 1bbc8ce31fc8e5ffb048a1fb9553a82975680a3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Etapa 5 do passo-a-passo: Implantar o serviço Web de Azure Machine Learning
Esta é a quinta etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar um espaço de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](walkthrough-2-upload-data.md)
3. [Criar um novo teste](walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. **Implantar o serviço Web**
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

- - -
Para que outras pessoas possam usar o modelo de previsão que desenvolvemos neste passo a passo, podemos implantá-lo como um serviço Web no Azure.

Até este ponto, estávamos testando nosso modelo de treinamento. Mas o serviço implantado não fará mais o treinamento – ele gerará previsões pontuando a entrada do usuário com base em nosso modelo. Então, vamos fazer algumas preparações para converter esse teste de um teste de ***treinamento*** em um teste ***preditivo***. 

Esse é um processo de três etapas:  

1. Remova um dos modelos
2. Converter o *teste de treinamento* que criamos em um *teste preditivo*
3. Implantar o teste preditivo como um serviço Web

## <a name="remove-one-of-the-models"></a>Remova um dos modelos

Primeiro, precisamos reduzir esse teste um pouco. No momento, temos dois modelos diferentes no teste, mas queremos usar apenas um modelo ao implantar isso como um serviço Web.  

Vamos supor que decidimos que o modelo de árvore aumentada apresentou um desempenho melhor do que modelo SVM. Portanto, a primeira coisa a fazer é remover o módulo [Computador de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treiná-lo. Talvez você queira fazer uma cópia do teste clicando primeiro em **Salvar como** na parte inferior da tela do teste.

É preciso excluir os seguintes módulos:  

* [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine]
* Módulos [Treinar Modelo][train-model] e [Modelo de Pontuação][score-model] que foram conectados a ele
* [Normalizar Dados][normalize-data] (ambos)
* [Avaliar Modelo] [ evaluate-model] (pois terminamos de avaliar os modelos)

Selecione cada módulo e pressione a tecla Excluir, ou clique com botão direito do mouse no módulo e selecione **Excluir**. 

![Removido o modelo SVM][3a]

Nosso modelo agora deve ser semelhante ao seguinte:

![Removido o modelo SVM][3]

Agora, estamos prontos para implantar esse modelo usando a [Árvore de Decisão Aumentada em Duas Classes][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo

Para obter esse modelo pronto para implantação, precisamos converter esse teste de treinamento em um teste preditivo. Isso envolve três etapas:

1. Salvar o modelo com que treinamos e substituir nossos módulos de treinamento
2. Cortar o teste para remover os módulos necessários somente para treinamento
3. Definir onde o serviço Web aceitará a entrada e onde ele gera a saída

Poderíamos fazer isso manualmente, mas felizmente as três etapas podem ser realizadas apenas clicando em **Configurar Serviço Web** na parte inferior da tela de teste (e selecionando a opção **Serviço Web Preditivo**).

> [!TIP]
> Se você quiser obter mais detalhes sobre o que acontece quando você converte um teste de treinamento em um teste de previsão, confira [Como preparar seu modelo para implantação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Quando você clica em **Configurar Serviço Web**, várias coisas acontecem:

* O modelo treinado é convertido em um único módulo **Modelo Treinado** e armazenado na paleta de módulos à esquerda da tela de teste (você o encontrará em **Modelos Treinados**)
* Os módulos que foram usados para o treinamento são removidos; especificamente:
  * [Árvore de Decisão Aumentada em Duas Classes][two-class-boosted-decision-tree]
  * [Modelo de Treinamento][train-model]
  * [Dados Divididos][split]
  * o segundo módulo [Executar Script R][execute-r-script] que foi usado para testar os dados
* O modelo treinado salvo é adicionado de volta ao teste
* Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados (eles identificam onde os dados do usuário entrarão no modelo e quais dados retornam quando o serviço Web é acessado)

> [!NOTE]
> Você pode ver que o teste é salvo em duas partes em guias que foram adicionadas na parte superior da tela do teste. O teste de treinamento original está na guia **Teste de treinamento**, e o teste de previsão recém-criado está em **Teste de previsão**. Implantaremos o teste preditivo como um serviço Web.

É necessário executar uma etapa adicional com esse teste específico.
Adicionamos dois módulos [Executar Script R][execute-r-script] para fornecer uma função de ponderação aos dados. Isso era apenas um truque que precisávamos para treinamento e teste, a fim de levarmos esses módulos para o modelo final.
O Machine Learning Studio removeu um módulo [Executar Script R][execute-r-script] quando removeu o módulo [Divisão][split]. Podemos remover o outro e conectar o [Editor de metadados][metadata-editor] diretamente ao [Modelo de pontuação][score-model].    

Agora o teste deve se parecer como isto:  

![Pontuando o modelo treinado][4]  

> [!NOTE]
> Você pode estar se perguntando por que deixamos o conjunto de dados de Dados de cartão de crédito alemão UCI no teste preditivo. O serviço pontuará os dados do usuário, não o conjunto de dados original, portanto, porque deixar o conjunto de dados original no modelo?
> 
> É verdade que o serviço não precisa dos dados de cartão de crédito originais. Mas ele precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixamos esses componentes conectados para que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço estiver sendo executado. Os dados não são usados, somente o esquema.  
> 
> 

Execute o teste uma última vez (clique em **Executar**). Se você deseja verificar se o modelo ainda está funcionando, clique na saída do módulo [Modelo de Pontuação][score-model] e selecione **Exibir Resultados**. Você pode ver que os dados originais são exibidos, juntamente com o valor de risco de crédito ("Rótulos de pontuação") e o valor de probabilidade de pontuação ("Probabilidades de pontuação"). 

## <a name="deploy-the-web-service"></a>Implantar o serviço Web
Você pode implantar o teste como um serviço Web clássico ou um novo serviço Web com base no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implantar como serviço Web clássico
Para implantar um serviço Web Clássico derivado do teste, clique em **Implantar Serviço Web** abaixo da tela e selecione **Implantar Serviço Web [Clássico]**. O Machine Learning Studio implanta o teste como um serviço Web e leva você até o painel para o serviço Web. Dessa página, você pode retornar ao teste (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (veja **Testar o serviço Web** abaixo). Há também informações para a criação de aplicativos que podem acessar o serviço Web (mais sobre isso na próxima etapa deste passo a passo).

![Painel de serviço Web][6]

Você pode configurar o serviço clicando na guia **CONFIGURAÇÃO** . Assim, você pode modificar o nome do serviço (é dado o nome do teste por padrão) e fornecer uma descrição para ele. Também pode adicionar rótulos mais simplificados aos dados de entrada e saída.  

![Configurar o serviço Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implantar como um novo serviço Web

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implantar um Novo serviço Web derivado de nosso teste:

1. Clique em **Implantar Serviço Web** abaixo da tela e selecione **Implantar Serviço Web [Novo]**. O Machine Learning Studio transfere você para a página **Implantar Teste** dos serviços Web do Azure Machine Learning.

2. Insira um nome para o serviço Web. 

3. Para **Plano de Preços**, você pode selecionar um plano de preços existente, ou selecionar "Criar novo", dar um nome para o novo plano e selecionar a opção de plano mensal. As camadas do plano usam como padrão os planos da região padrão e o serviço Web é implantado nessa região.

4. Clique em **Implantar**.

Após alguns minutos, a página **Início Rápido** do serviço Web é aberta.

Você pode configurar o serviço clicando na guia **Configurar**. Aqui você pode modificar o título do serviço e fornecer uma descrição. 

Para testar o serviço Web, clique na guia **Testar** (veja **Testar o serviço Web** abaixo). Para obter informações sobre a criação de aplicativos que podem acessar o serviço Web, clique na guia **Consumir** (a próxima etapa deste passo a passo fornecerá mais detalhes).

> [!TIP]
> Você pode atualizar o serviço Web depois de implantá-lo. Por exemplo, se você quiser alterar o modelo, edite o teste de treinamento, ajuste os parâmetros de modelo e clique em **Implantar Serviço Web**, selecione **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]**. Quando você implantar o teste novamente, ele substituirá o serviço Web, agora usando o modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço Web

Quando o serviço Web é acessado, os dados do usuário entram por meio do módulo **Entrada do serviço Web** e são passados para o módulo [Modelo de Pontuação][score-model], nos quais são pontuados. Na maneira que configuramos o teste de previsão, o modelo espera dados no mesmo formato do conjunto de dados de risco de crédito original.
Os resultados retornam para o usuário do serviço Web, passando pelo módulo **Saída do serviço Web**.

> [!TIP]
> Na maneira que configuramos o teste de previsão, todos os resultados do módulo [Modelo de Pontuação][score-model] retornam. Isso inclui todos os dados de entrada, o valor do risco de crédito e a probabilidade de pontuação. Mas você pode retornar algo diferente se desejar – por exemplo, você pode retornar apenas o valor do risco de crédito. Para fazer isso, insira um módulo [Colunas do Projeto][project-columns] entre [Modelo de Pontuação][score-model] e **Saída do serviço Web** para eliminar colunas que você não deseja que o serviço Web retorne. 
> 
> 

Você pode testar o serviço Web Clássico no **Machine Learning Studio** ou no portal de **Serviços Web do Azure Machine Learning**.
Você pode testar um novo serviço Web no portal dos **Serviços Web do Azure Machine Learning**.

> [!TIP]
> Durante o teste no portal de serviços Web do Azure Machine Learning, você pode fazer com que o portal crie dados de exemplo e use-os para testar o serviço de solicitação-resposta. Na página **Configurar**, selecione "Sim" para **Dados de Exemplo Habilitados?**. Quando você abre o guia de Solicitação-Resposta na página **Teste**, o portal preenche os dados de exemplo obtidos do conjunto de dados de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço Web Clássico

Você pode testar um serviço Web Clássico no Machine Learning Studio ou no portal de Serviços Web do Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testar no Machine Learning Studio

1. Na página **PAINEL** do serviço Web, clique no botão **Testar** em **Ponto de Extremidade Padrão**. Uma caixa de diálogo é exibida e solicita os dados de entrada para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito original.  

2. Insira um conjunto de dados e clique em **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testar no portal de Serviços Web do Azure Machine Learning

1. Na página **PAINEL** do serviço Web, clique no link **Visualização do teste** em **Ponto de Extremidade Padrão**. A página de teste no portal de serviços Web do Azure Machine Learning para o ponto de extremidade de serviço Web é aberta e solicita dados para o serviço. Existem as mesmas colunas que apareciam no conjunto de dados de risco de crédito original.

2. Clique em **Solicitação-Resposta**. 

### <a name="test-a-new-web-service"></a>Testar um novo serviço Web

Você pode testar um novo serviço Web no portal dos Serviços Web do Azure Machine Learning.

1. No portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net/quickstart), clique em **Testar** na parte superior da página. A página de **Teste** é aberta e você pode inserir dados para o serviço. Os campos de entrada exibidos correspondem às mesmas colunas que apareciam no conjunto de dados de risco de crédito original. 

2. Insira um conjunto de dados e clique em **Testar solicitação-resposta**.

Os resultados do teste são exibidos no lado direito da página na coluna de saída. 


## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

### <a name="manage-a-classic-web-service-in-the-azure-classic-portal"></a>Gerenciar um serviço Web Clássico no Portal Clássico do Azure

Assim que implantar o serviço Web clássico, você poderá gerenciá-lo no [Portal Clássico do Azure](https://manage.windowsazure.com).

1. Entre no [Portal clássico do Azure](https://manage.windowsazure.com)
2. No painel de serviços do Microsoft Azure, clique em **Machine Learning**
3. Clique no espaço de trabalho
4. Clique na guia **Serviços Web**
5. Clique no serviço Web que criamos
6. Clique no ponto de extremidade "padrão"

Aqui, você pode fazer coisas como monitorar como o serviço Web está se saindo e fazer ajustes de desempenho alterando quantas chamadas simultâneas o serviço pode processar.

Para obter mais informações, consulte:

* [Criando pontos de extremidade](create-endpoint.md)
* [Dimensionando serviço Web](scaling-webservice.md)

### <a name="manage-a-classic-or-new-web-service-in-the-azure-machine-learning-web-services-portal"></a>Gerenciar um serviço Web Novo ou Clássico no portal de serviços Web do Azure Machine Learning

Assim que você implantar o serviço Web, clássico ou novo, poderá gerenciá-lo no [portal de serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Para monitorar o desempenho do seu serviço Web:

1. Entre no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
2. Clique em **Serviços Web**
3. Clique no seu serviço Web
4. Clique em **Painel**

- - -
**Em seguida: [Acessar o serviço Web](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

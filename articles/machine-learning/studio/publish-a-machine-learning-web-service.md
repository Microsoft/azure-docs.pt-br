---
title: "Implantar um serviço Web do Machine Learning | Microsoft Docs"
description: "Como converter um teste de treinamento em um teste preditivo, prepará-lo para implantação e implantá-lo como um serviço Web de Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: garye
ms.openlocfilehash: bdf0bd54130521a7178af3a28731f4c0e21e3e0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Implantar um serviço Web de Azure Machine Learning
O Azure Machine Learning permite compilar, testar e implantar soluções de análise preditiva.

Em um ponto de exibição de alto nível, isso é feito em três etapas:

* **[Criar um teste de treinamento]** - o Azure Machine Learning Studio é um ambiente de desenvolvimento visual colaborativo usado para treinar e testar um modelo de análise de previsão usando os dados de treinamento que você fornecer.
* **[Convertê-lo em um teste preditivo]**: depois que o modelo tiver sido treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu teste de previsões.
* **[Implantá-lo como um serviço Web]**: você pode implantar um teste preditivo como um serviço Web do Azure [novo] ou [clássico]. Os usuários podem enviar dados ao seu modelo e receber as previsões do modelo.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Criar um teste de treinamento
Para treinar um modelo de análise de previsão, você usa o Azure Machine Learning Studio para criar uma experiência de treinamento na qual você pode incluir vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de aprendizagem de máquina e avaliar os resultados. Você pode iterar em um teste e testar algoritmos de aprendizado de máquina diferentes para comparar e avaliar os resultados.

O processo de criar e gerenciar testes de treinamento é abordado mais detalhadamente em outro ponto. Para obter mais informações, consulte estes artigos:

* [Criar um experimento simples no Azure Machine Learning Studio](create-experiment.md)
* [Desenvolver uma solução preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importar seus dados de treinamento para o Azure Machine Learning Studio](import-data.md)
* [Gerenciar iterações de teste no Machine Learning Studio do Microsoft Azure](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo
Depois que você treinou seu modelo, estará pronto para converter seu teste de treinamento em um experimento de previsão para pontuar novos dados.

Ao converter em um teste preditivo, você estará preparando seu modelo para ser implantado como um serviço Web de pontuação. Os usuários do serviço Web podem enviar dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. À medida que converte em um teste preditivo, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

Para converter o teste de treinamento em um teste preditivo, clique em **Executar** na parte inferior da tela do teste, clique em **Configurar Serviço Web** e selecione **Serviço Web Preditivo**.

![Converter para o teste de pontuação](./media/publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como realizar essa conversão, consulte [Como preparar seu modelo para implantação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

As etapas a seguir descrevem a implantação de um teste preditivo como um novo serviço Web. Você também pode implantar o teste como um serviço Web clássico.

## <a name="deploy-it-as-a-web-service"></a>Implantá-lo como um serviço Web

Você pode implantar o teste preditivo como um serviço Web Novo ou Clássico.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implantar o teste preditivo como um novo serviço Web
Agora que o teste preditivo foi preparado, você pode implantá-lo como um novo serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar o teste preditivo, clique em **Executar** na parte inferior da tela do teste. Após a conclusão do teste, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [novo]**.  A página de implantação do portal do serviço Web do Machine Learning será aberta.

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Página de teste de implantação do portal de Serviços Web do Machine Learning
Na página de teste de implantação, insira um nome para o serviço Web.
Selecione um plano de preços. Se você tiver um plano de preços existente, selecione-o, caso contrário, você deverá criar um novo plano de preços para o serviço.

1. Na lista suspensa **Plano de Preços**, selecione um plano existente ou selecione a opção **Selecione novo plano**.
2. Em **Nome do Plano**, digite um nome que identificará o plano na sua conta.
3. Selecione uma dos **Níveis de Planos Mensais**. As camadas do plano usam como padrão os planos da região padrão e o serviço Web é implantado nessa região.

Clique em **Implantar** e na página **Início Rápido** para abrir seu serviço Web.

A página de início rápido do serviço Web fornece acesso e orientações sobre as tarefas mais comuns que você executará depois de criar um serviço Web. A partir daqui, você poderá acessar facilmente a página Teste e a página Consumo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Gerenciar seu Novo serviço Web
Para testar seu novo serviço Web, clique em **Testar serviço Web** em tarefas comuns. Na página de teste, você poderá testar o serviço Web como um Serviço de solicitação-resposta (RRS) ou Serviço de execução em lote (BES).

A página de teste RRS exibe as entradas, as saídas e todos os parâmetros globais que você definiu para o teste. Para testar o serviço Web, você pode manualmente inserir os valores apropriados para as entradas ou fornecer um arquivo CSV (valores separados por vírgulas) formatado que contém os valores de teste.

Para testar usando RRS, do modo de exibição de lista, insira os valores apropriados para as entradas e clique em **Teste de solicitação-resposta**. Os resultados de previsão serão exibidos na coluna de saída à esquerda.

![Implantar o serviço Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar o BES, clique em **Lote**. Na página de teste em lote, clique em Pesquisar na sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu teste preditivo usando o Machine Learning Studio, você pode baixar o conjunto de dados para o seu teste preditivo e usá-lo.

Para baixar o conjunto de dados, abra Machine Learning Studio. Abra seu teste preditivo e clique com o botão direito na entrada para o seu teste. No menu de contexto, selecione **conjunto de dados** e, em seguida, selecione **Baixar**.

![Implantar o serviço Web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **Testar**. O status de seu trabalho de execução de lote é exibido à direita em **Trabalhos em lote de teste**.

![Implantar o serviço Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na página **CONFIGURAÇÃO**, você pode alterar a descrição, o título, atualizar a chave da conta de armazenamento e habilitar dados de exemplo para o serviço Web.

![Configurar o serviço Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Depois de implantar o serviço Web, é possível:

* **Acessá-lo** por meio da API do serviço Web.
* **Gerenciá-lo** por meio do portal de serviços Web de Azure Machine Learning ou Portal Clássico do Azure.
* **Atualizá-lo** se o seu modelo for alterado.

#### <a name="access-your-new-web-service"></a>Acessar seu Novo serviço Web
Depois de implantar o serviço Web por meio do Machine Learning Studio, você pode enviar dados para o serviço e receber respostas de forma programática.

A página **Consumo** fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço.

Para obter mais informações sobre como acessar um serviço Web do Machine Learning, consulte [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Gerenciar seu Novo serviço Web
É possível gerenciar o Novo serviço Web no portal dos Serviços Web do Machine Learning. Na [página principal do portal](https://services.azureml-test.net/) clique em **serviços Web**. Na página de serviços Web, você pode excluir ou copiar um serviço. Para monitorar um serviço específico, clique no serviço e, em seguida, clique em **Painel**. Para monitorar trabalhos em lotes associados ao serviço Web, clique em **Log de solicitações em lote**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implantar o teste preditivo como um serviço Web clássico

Agora que o teste preditivo foi devidamente preparado, você pode implantá-lo como um serviço Web Clássico do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar o teste preditivo, clique em **Executar** na parte inferior da tela do teste e clique em **Implantar Serviço Web**. O serviço Web é configurado e colocado no painel de serviço Web.

![Implantar o serviço Web](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testar seu serviço Web Clássico

Você pode testar o serviço Web no portal de Serviços Web do Machine Learning ou no Machine Learning Studio.

Para testar o serviço Web Resposta a Solicitação, clique no botão **Teste** no painel de serviço Web. Uma caixa de diálogo é exibida para solicitar os dados de entrada para o serviço. Essas são as colunas esperadas pelo teste de pontuação. Insira um conjunto de dados e clique em **OK**. Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel.

Você pode clicar no link de visualização **Teste** para testar seu serviço no portal de Serviços Web do Azure Machine Learning, conforme mostrado anteriormente na seção novo Serviço Web.

Para testar o serviço de execução em lote, clique no link de visualização do **teste**. Na página de teste em lote, clique em Pesquisar na sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu teste preditivo usando o Machine Learning Studio, você pode baixar o conjunto de dados para o seu teste preditivo e usá-lo.

![Testar o serviço Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na página **CONFIGURAÇÃO** , você pode alterar o nome de exibição do serviço e fornecer uma descrição. O nome e a descrição são exibidos no [Portal Clássico do Azure](http://manage.windowsazure.com/) , no qual você gerencia seus serviços Web.

Você pode fornecer uma descrição dos dados de entrada, dados de saída e parâmetros de serviço Web inserindo uma cadeia de caracteres para cada coluna em **INPUT SCHEMA**, **OUTPUT SCHEMA**, e **Web SERVICE PARAMETER**. Essas descrições são usadas na documentação do código de exemplo fornecida para o serviço Web.

Você pode habilitar o log para diagnosticar quaisquer falhas que esteja vendo quando o serviço Web for acessado. Para obter mais informações, consulte [Habilitar o log de serviços Web de Machine Learning](web-services-logging.md).

![Configurar o serviço Web](./media/publish-a-machine-learning-web-service/figure-4.png)

Você também pode configurar os pontos de extremidade do serviço Web no portal de serviços Web do Azure Machine Learning da mesma forma que o procedimento mostrado anteriormente na seção Novo serviço Web. As opções são diferentes, você pode adicionar ou alterar a descrição do serviço, habilitar o registro em log e habilitar dados de exemplo para teste.

#### <a name="access-your-classic-web-service"></a>Acessar seu serviço Web Clássico
Depois de implantar o serviço Web por meio do Machine Learning Studio, você pode enviar dados para o serviço e receber respostas de forma programática.

O painel fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço e as páginas de ajuda de API são fornecidas para ajudar você a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço Web do Machine Learning, consulte [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Gerenciar seu serviço Web Clássico
Há várias ações que podem ser executadas para monitorar um serviço Web. Você pode atualizá-lo e excluí-lo. Você também pode adicionar pontos de extremidade adicionais a um serviço Web clássico além do ponto de extremidade padrão que é criado durante sua implantação.

Para obter mais informações, consulte [Gerenciar um espaço de trabalho do Azure Machine Learning](manage-workspace.md) e [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Atualizar o serviço Web
Você pode fazer alterações ao serviço Web, como atualizar o modelo com dados de treinamento adicionais e implantá-lo novamente, substituindo o serviço Web original.

Para atualizar o serviço Web, abra o teste preditivo original que você usou para implantar o serviço Web original e faça uma cópia editável clicando em **SALVAR COMO**. Faça as alterações e clique em **Implantar Serviço Web**.

Como você já implantou esse teste antes, será solicitado se deseja substituir (Serviço Web clássico) ou atualizar (Novo serviço Web) o serviço existente. Se você clicar em **SIM** ou **Atualizar**, o serviço Web existente será interrompido e o novo teste preditivo será implantado em seu lugar.

> [!NOTE]
> Se você fez alterações na configuração do serviço Web original, por exemplo, inserindo um novo nome de exibição ou uma descrição, você precisará inserir esses valores novamente.
> 
> 

Uma opção para atualizar seu serviço Web é treinar novamente o modelo de forma programática. Para obter mais informações, consulte [Treinar novamente os modelos de Machine Learning de forma programática](retrain-models-programmatically.md).

<!-- internal links -->
[Criar um teste de treinamento]: #create-a-training-experiment
[Convertê-lo em um teste preditivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Implantá-lo como um serviço Web]: #deploy-it-as-a-web-service
[novo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clássico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

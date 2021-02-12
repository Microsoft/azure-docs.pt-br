---
title: 'ML Studio (clássico): implantar um serviço Web-Azure'
description: Como converter um teste de treinamento em um experimento de previsão, prepará-lo para implantação e implantá-lo como um serviço Web Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: f77c0fb09dddd884335d31d630904ea6aeafeaf5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495195"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Implantar um serviço Web Azure Machine Learning Studio (clássico)

**aplica-se a:** ![ Essa é uma marca de seleção, o que significa que este artigo se aplica a Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ é um X, o que significa que este artigo se aplica a Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (clássico) permite que você crie e teste uma solução analítica preditiva. Em seguida, é possível implantar a solução como um serviço Web.

Machine Learning Studio serviços Web (clássicos) fornecem uma interface entre um aplicativo e um modelo de Pontuação de fluxo de trabalho Machine Learning Studio (clássico). Um aplicativo externo pode se comunicar com um modelo de Pontuação de fluxo de trabalho Machine Learning Studio (clássico) em tempo real. Uma chamada para um serviço Web Machine Learning Studio (clássico) retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada a um serviço Web, você passa uma chave de API que foi criada quando você implantou o serviço Web. Um serviço Web Machine Learning Studio (clássico) é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

Azure Machine Learning Studio (clássico) tem dois tipos de serviços Web:

* RRS (serviço de Request-Response): um serviço de baixa latência e altamente escalonável que classifica um único registro de dados.
* BES (serviço de execução em lote): um serviço assíncrono que classifica um lote de registros de dados.

A entrada para BES é como os dados de entrada que RRS usa. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como o armazenamento de Blobs do Azure, o armazenamento de Tabelas do Azure, um banco de dados SQL do Azure, o HDInsight (consulta hive) e fontes HTTP.

Em um ponto de exibição de alto nível, implante seu modelo em três etapas:

* **[Criar um teste de treinamento]** -no estúdio (clássico), você pode treinar e testar um modelo de análise preditiva usando dados de treinamento fornecidos por você, usando um grande conjunto de algoritmos de aprendizado de máquina internos.
* **[Convertê-lo em um teste preditivo]**: depois que o modelo tiver sido treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu teste de previsões.
* **Implante-o** como um **[novo serviço Web]** ou um **[serviço Web clássico]** – Quando você implanta seu teste preditivo como um serviço Web do Azure, os usuários podem enviar dados para seu modelo e receber previsões dele.

## <a name="create-a-training-experiment"></a>Criar um teste de treinamento

Para treinar um modelo de análise preditiva, você usa Azure Machine Learning Studio (clássico) para criar um teste de treinamento no qual você inclui vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de aprendizado de máquina e avaliar os resultados. Você pode iterar em um teste e testar algoritmos de aprendizado de máquina diferentes para comparar e avaliar os resultados.

O processo de criar e gerenciar testes de treinamento é abordado mais detalhadamente em outro ponto. Para obter mais informações, confira estes tópicos:

* [Criar um experimento simples no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Desenvolver uma solução preditiva com Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md)
* [Importar seus dados de treinamento para Azure Machine Learning Studio (clássico)](import-data.md)
* [Gerenciar iterações de experimento no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo

Depois que você treinou seu modelo, estará pronto para converter seu teste de treinamento em um experimento de previsão para pontuar novos dados.

Ao converter em um teste preditivo, você estará preparando seu modelo para ser implantado como um serviço Web de pontuação. Os usuários do serviço Web podem enviar dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. À medida que converte em um teste preditivo, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

O processo de conversão de um teste de treinamento em um teste preditivo envolve três etapas:

1. Substitua os módulos de algoritmo do Machine Learning pelo modelo treinado.
2. Restrinja o experimento a somente os módulos necessários para pontuação. Um teste de treinamento inclui vários módulos que são necessárias para treinamento, mas não são necessários depois que o modelo está treinado.
3. Defina como seu modelo aceitará dados do usuário do serviço Web e quais dados será retornados.

> [!TIP]
> No teste de treinamento, você estará concentrado em treinar e pontuar o modelo usando seus próprios dados. Porém, depois de implantado, os usuários enviarão novos dados ao modelo e este retornará resultados de previsão. Desse modo, à medida que você converte o teste de treinamento em um teste preditivo para prepará-lo para implantação, lembre-se de como o modelo será usado por outras pessoas.

![Converter para o teste de pontuação](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Botão Configurar Serviço Web
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

#### <a name="adjust-input-and-output-modules"></a>Ajustar os módulos de entrada e saída
No seu experimento de treinamento, você usou um conjunto de dados de treinamento, que foram processados para deixar os dados em um formato que o algoritmo de aprendizado de máquina necessitava. Se os dados que você espera receber por meio do serviço Web não precisarem desse processamento, será possível ignorá-lo: conecte a saída do **módulo de entrada do serviço Web** a um módulo diferente no teste. Os dados do usuário agora chegarão no modelo nesse local.

Por exemplo, por padrão, **Configurar Serviço Web** coloca o **módulo de entrada do serviço Web** na parte superior do fluxo de dados, como mostrado na figura acima. Porém, podemos posicionar manualmente a **Entrada do serviço Web** depois dos módulos de processamento de dados:

![Movendo a entrada do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos pelo serviço Web agora passarão diretamente para o módulo do Modelo de Pontuação sem qualquer pré-processamento.

Da mesma forma, por padrão, **Configurar Serviço Web** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço Web retornará ao usuário a saída do módulo [modelo de Pontuação][score-model] , que inclui o vetor de dados de entrada completo, além dos resultados da pontuação.
No entanto, se preferir retornar algo diferente, você poderá adicionar outros módulos antes do módulo **Saída do serviço Web**. 

Por exemplo, para retornar apenas os resultados da pontuação, e não todo o vetor de dados de entrada, adicione um módulo [Selecionar Colunas do Conjunto de Dados][select-columns] para excluir todas as colunas, exceto os resultados da pontuação. Em seguida, mova o módulo **Saída do serviço Web** para a saída do módulo [Selecionar Colunas do Conjunto de Dados][select-columns]. O teste tem esta aparência:

![Movendo a saída do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se houver mais módulos no seu experimento que você sabe que não será necessário durante a pontuação, eles podem ser removidos. Por exemplo, como movemos o módulo **Entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo [Limpar Dados Ausentes][clean-missing-data] do teste preditivo.

Nosso teste preditivo ficou assim:

![Removendo o módulo adicional](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros de serviço Web opcionais
Em alguns casos, talvez você queira permitir que o usuário do serviço Web altere o comportamento dos módulos quando o serviço é acessado. *Parâmetros de serviço Web* permitem que você faça isso.

Um exemplo comum é a configuração de um módulo [Importar Dados][import-data] para que o usuário do serviço Web implantado possa especificar outra fonte de dados quando o serviço Web for acessado. Ou então, configurar o módulo [Exportar Dados][export-data] para que um destino diferente possa ser especificado.

Você pode definir os Parâmetros de Serviço Web e associá-los a um ou mais parâmetros de módulo, podendo também especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web fornece valores para esses parâmetros quando o serviço é acessado e as ações de módulo são modificadas de acordo.

Para saber mais sobre Parâmetros de Serviço Web e como usá-los, confira [Usar os parâmetros do Serviço Web do Azure Machine Learning ][webserviceparameters].

As etapas a seguir descrevem a implantação de um teste preditivo como um novo serviço Web. Você também pode implantar o teste como um serviço Web clássico.

## <a name="deploy-it-as-a-new-web-service"></a>Implantá-lo como um novo serviço Web

Agora que o teste preditivo foi preparado, é possível implantá-lo como um novo serviço Web do Azure (baseado no Resource Manager). Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar o teste preditivo, clique em **Executar** na parte inferior da tela do teste. Após a conclusão do teste, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [novo]**.  A página implantação do portal do serviço Web Machine Learning Studio (clássico) é aberta.

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [gerenciar um serviço Web usando o Azure Machine Learning Portal de serviços Web](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Página de experimento de implantação do portal de serviço Web

Na página de teste de implantação, insira um nome para o serviço Web.
Selecione um plano de preços. Se você tiver um plano de preços existente, selecione-o, caso contrário, você deverá criar um novo plano de preços para o serviço.

1. Na lista suspensa **Plano de Preços**, selecione um plano existente ou selecione a opção **Selecione novo plano**.
2. Em **Nome do Plano**, digite um nome que identificará o plano na sua conta.
3. Selecione uma dos **Níveis de Planos Mensais**. As camadas do plano usam como padrão os planos da região padrão e o serviço Web é implantado nessa região.

Clique em **Implantar** e na página **Início Rápido** para abrir seu serviço Web.

A página de início rápido do serviço Web fornece acesso e orientações sobre as tarefas mais comuns que você executará depois de criar um serviço Web. A partir daqui, você poderá acessar facilmente a página Teste e a página Consumo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Gerenciar seu Novo serviço Web

Para testar o novo serviço Web, clique em **testar serviço Web** em tarefas comuns. Na página de teste, você poderá testar o serviço Web como um Serviço de solicitação-resposta (RRS) ou Serviço de execução em lote (BES).

A página de teste RRS exibe as entradas, as saídas e todos os parâmetros globais que você definiu para o teste. Para testar o serviço Web, você pode manualmente inserir os valores apropriados para as entradas ou fornecer um arquivo CSV (valores separados por vírgulas) formatado que contém os valores de teste.

Para testar usando RRS, do modo de exibição de lista, insira os valores apropriados para as entradas e clique em **Teste de solicitação-resposta**. Os resultados de previsão serão exibidos na coluna de saída à esquerda.

![Insira os valores apropriados para testar seu serviço Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar o BES, clique em **Lote**. Na página de teste em lote, clique em Pesquisar na sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu experimento de previsão usando Machine Learning Studio (clássico), poderá baixar o conjunto de dados para seu experimento de previsão e usá-lo.

Para baixar o conjunto de dados, abra Machine Learning Studio (clássico). Abra seu teste preditivo e clique com o botão direito na entrada para o seu teste. No menu de contexto, selecione **conjunto de dados** e, em seguida, selecione **Baixar**.

![Baixar o conjunto de dados da tela do estúdio (clássico)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **Testar**. O status de seu trabalho de execução de lote é exibido à direita em **Trabalhos em lote de teste**.

![Testar seu trabalho de execução em lote com o portal de serviço Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na página **CONFIGURAÇÃO**, você pode alterar a descrição, o título, atualizar a chave da conta de armazenamento e habilitar dados de exemplo para o serviço Web.

![Configurar seu serviço Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Acessar seu Novo serviço Web

Depois de implantar o serviço Web do Machine Learning Studio (clássico), você pode enviar dados para o serviço e receber respostas programaticamente.

A página **Consumo** fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço.

Para obter mais informações sobre como acessar um serviço Web Machine Learning Studio (clássico), consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gerenciar seu Novo serviço Web

Você pode gerenciar seus novos serviços Web usando o portal de serviços da Web Machine Learning Studio (clássico). Na [página principal do portal](https://services.azureml.net/), clique em **Serviços Web**. Na página de serviços Web, você pode excluir ou copiar um serviço. Para monitorar um serviço específico, clique no serviço e, em seguida, clique em **Painel**. Para monitorar trabalhos em lotes associados ao serviço Web, clique em **Log de solicitações em lote**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Implantar o novo serviço Web em várias regiões

É possível implantar facilmente um novo serviço Web em várias regiões sem a necessidade de várias assinaturas ou workspaces.

O preço é específico de cada região, então é necessário definir um plano de cobrança para cada região na qual você implantará o serviço Web.

#### <a name="create-a-plan-in-another-region"></a>Criar um plano em outra região

1. Entre nos [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Clique na opção de menu **Planos** .
3. Na página de visão geral de Planos, clique em **Novo**.
4. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo plano residirá.
5. No menu suspenso **Região** , selecione uma região para o novo plano. As opções de plano para a região selecionada serão exibidas na seção **Opções de Plano** da página.
6. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o plano. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. Em **Nome do Plano** , digite o nome do plano.
8. Em **Opções do Plano**, clique no nível de cobrança para o novo plano.
9. Clique em **Criar**.

#### <a name="deploy-the-web-service-to-another-region"></a>Implantar o serviço Web em outra região

1. Na página de Serviços Web do Microsoft Azure Machine Learning, clique na opção de menu **Serviços Web**.
2. Selecione o serviço Web que você está implantando em uma nova região.
3. Clique em **Copiar**.
4. Em **Nome do Serviço Web**, digite um novo nome para o serviço Web.
5. Em **Descrição do serviço Web**, digite uma descrição para o serviço Web.
6. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo serviço Web residirá.
7. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o serviço Web. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. No menu suspenso **Região** , selecione a região na qual implantar o serviço Web.
9. No menu suspenso **Conta de armazenamento** , selecione uma conta de armazenamento na qual armazenar o serviço Web.
10. Na lista suspensa **Plano de preços** , selecione um plano na região que você selecionou na etapa 8.
11. Clique em **Copiar**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implantá-lo como um serviço Web clássico

Agora que o teste preditivo foi devidamente preparado, você pode implantá-lo como um serviço Web Clássico do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar o teste preditivo, clique em **Executar** na parte inferior da tela do teste e clique em **Implantar Serviço Web**. O serviço Web é configurado e colocado no painel de serviço Web.

![Implantar o serviço Web do estúdio (clássico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testar seu serviço Web Clássico

Você pode testar o serviço Web no portal de serviços Web Machine Learning Studio (clássico) ou Machine Learning Studio (clássico).

Para testar o serviço Web Resposta a Solicitação, clique no botão **Teste** no painel de serviço Web. Uma caixa de diálogo é exibida para solicitar os dados de entrada para o serviço. Essas são as colunas esperadas pelo teste de pontuação. Insira um conjunto de dados e clique em **OK**. Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel.

Você pode clicar no link visualização de **teste** para testar seu serviço no portal de serviços Web Azure Machine Learning Studio (clássico), conforme mostrado anteriormente na seção novo serviço Web.

Para testar o serviço de execução em lote, clique no link de visualização do **teste**. Na página de teste em lote, clique em Pesquisar na sua entrada e selecione um arquivo CSV que contém os valores de exemplo apropriados. Se você não tiver um arquivo CSV e tiver criado seu experimento de previsão usando Machine Learning Studio (clássico), poderá baixar o conjunto de dados para seu experimento de previsão e usá-lo.

![Testar o serviço Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na página **CONFIGURAÇÃO** , você pode alterar o nome de exibição do serviço e fornecer uma descrição. O nome e a descrição são exibidos no [Portal do Azure](https://portal.azure.com/), no qual você gerencia seus serviços Web.

Você pode fornecer uma descrição dos dados de entrada, dados de saída e parâmetros de serviço Web inserindo uma cadeia de caracteres para cada coluna em **INPUT SCHEMA**, **OUTPUT SCHEMA**, e **Web SERVICE PARAMETER**. Essas descrições são usadas na documentação do código de exemplo fornecida para o serviço Web.

Você pode habilitar o log para diagnosticar quaisquer falhas que esteja vendo quando o serviço Web for acessado. Para obter mais informações, consulte [habilitar o registro em log para serviços web Machine Learning Studio (clássico)](web-services-logging.md).

![Habilitar o registro em log no portal de serviços Web](./media/publish-a-machine-learning-web-service/figure-4.png)

Você também pode configurar os pontos de extremidade do serviço Web no portal de serviços Web do Azure Machine Learning da mesma forma que o procedimento mostrado anteriormente na seção Novo serviço Web. As opções são diferentes, você pode adicionar ou alterar a descrição do serviço, habilitar o registro em log e habilitar dados de exemplo para teste.

### <a name="access-your-classic-web-service"></a>Acessar seu serviço Web Clássico

Depois de implantar o serviço Web do Azure Machine Learning Studio (clássico), você pode enviar dados para o serviço e receber respostas programaticamente.

O painel fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço e as páginas de ajuda de API são fornecidas para ajudar você a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço Web Machine Learning Studio (clássico), consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gerenciar seu serviço Web Clássico

Há várias ações que podem ser executadas para monitorar um serviço Web. Você pode atualizá-lo e excluí-lo. Você também pode adicionar pontos de extremidade adicionais a um serviço Web clássico além do ponto de extremidade padrão que é criado durante sua implantação.

Para obter mais informações, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md) e [gerenciar um serviço Web usando o portal de serviços Web Azure Machine Learning Studio (clássico)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Atualizar o serviço Web
Você pode fazer alterações ao serviço Web, como atualizar o modelo com dados de treinamento adicionais e implantá-lo novamente, substituindo o serviço Web original.

Para atualizar o serviço Web, abra o teste preditivo original que você usou para implantar o serviço Web original e faça uma cópia editável clicando em **SALVAR COMO**. Faça as alterações e clique em **Implantar Serviço Web**.

Como você já implantou esse teste antes, será solicitado se deseja substituir (Serviço Web clássico) ou atualizar (Novo serviço Web) o serviço existente. Se você clicar em **SIM** ou **Atualizar**, o serviço Web existente será interrompido e o novo teste preditivo será implantado em seu lugar.

> [!NOTE]
> Se você fez alterações na configuração do serviço Web original, por exemplo, inserindo um novo nome de exibição ou uma descrição, você precisará inserir esses valores novamente.

Uma opção para atualizar seu serviço Web é treinar novamente o modelo de forma programática. Para obter mais informações, consulte [readaptação de modelos de Machine Learning Studio (clássico) programaticamente](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais detalhes técnicos sobre como funciona a implantação, consulte [como um modelo de Machine Learning Studio (clássico) progride de um experimento para um serviço Web operacional](model-progression-experiment-to-web-service.md).

* Para obter detalhes sobre como preparar seu modelo para implantação, consulte [como Prepare seu modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).

* Há várias maneiras para consumir a API REST e acessar o serviço Web. Consulte [como consumir um serviço web Azure Machine Learning Studio (clássico)](consume-web-services.md).

<!-- internal links -->
[Criar um teste de treinamento]: #create-a-training-experiment
[Convertê-lo em um experimento de previsão]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo serviço Web]: #deploy-it-as-a-new-web-service
[Serviço Web clássico]: #deploy-it-as-a-classic-web-service
[Novo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data
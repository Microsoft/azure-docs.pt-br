---
title: Usar Visual Studio Code para aprendizado de máquina
titleSuffix: Azure Machine Learning service
description: Saiba como instalar o Azure Machine Learning para Visual Studio Code e criar um experimento simples no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3b49def1af7f13e38c3a9daea32d56bf3c633261
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871757"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, você aprenderá a usar a extensão para Azure Machine Learning para Visual Studio Code para treinar e implantar o aprendizado de máquina e modelos de aprendizado profundo.

O [serviço de Azure Machine Learning](overview-what-is-azure-ml.md) fornece suporte para experimentos que você executa localmente e em destinos de computação remota. Em cada experimento, é possível acompanhar várias execuções, já que muitas vezes é preciso testar iterativamente diferentes técnicas, hiperparâmetros e muito mais. Você pode usar o Azure Machine Learning para acompanhar métricas personalizadas e execuções de experimento, possibilitando a capacidade de reprodução e auditoria da ciência de dados.

Você também pode implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree).

+ O Visual Studio Code deve ser instalado. Visual Studio Code é um editor de código-fonte leve, mas poderoso, que é executado em sua área de trabalho. Ele vem com suporte interno para Python e outras linguagens de programação. Se você ainda não instalou o Visual Studio Code, [Descubra como](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instale o Python 3,5 ou posterior](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Instalar a extensão para Azure Machine Learning para Visual Studio Code

Quando você instala a extensão de Azure Machine Learning, mais duas extensões são instaladas automaticamente (se você tiver acesso à Internet). Eles são a [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e a [extensão Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Para trabalhar com Azure Machine Learning, você precisa transformar Visual Studio Code em um IDE (ambiente de desenvolvimento integrado) do Python. Você precisa da extensão Microsoft Python para usar o [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Essa extensão é instalada automaticamente com a extensão Azure Machine Learning. A extensão torna Visual Studio Code um IDE excelente e funciona em qualquer sistema operacional com uma variedade de intérpretes de Python. A extensão do Microsoft Python usa toda a potência do Visual Studio Code para fornecer preenchimento automático, IntelliSense, refiapo, depuração e teste de unidade. A extensão também permite que você alterne facilmente entre ambientes Python, incluindo ambientes virtuais e Conda. Para obter mais informações sobre como editar, executar e depurar o código Python, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).

Para instalar a extensão de Azure Machine Learning:

1. Abra o Visual Studio Code.

1. Em um navegador da Web, acesse [Azure Machine Learning para Visual Studio Code extensão (versão prévia)](https://aka.ms/vscodetoolsforai).

1. Nessa página da Web, selecione **instalar**. 

1. Na guia extensão, selecione **instalar**.

1. Uma guia de boas-vindas para a extensão é aberta no Visual Studio Code e o símbolo do Azure (descrito em vermelho na captura de tela a seguir) é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividades do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, selecione **entrar** e siga os prompts para autenticar com o Azure. 
   
   A extensão de conta do Azure, que foi instalada junto com a Azure Machine Learning para Visual Studio Code extensão, ajuda você a se autenticar com sua conta do Azure. Para obter uma lista de comandos, consulte a página da [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Confira a [extensão IntelliCode para Visual Studio Code (versão prévia)](https://go.microsoft.com/fwlink/?linkid=2006060). O IntelliCode fornece um conjunto de recursos assistidos por ia para o IntelliSense no Python, como inferir as conclusões automáticas mais relevantes com base no contexto de código atual.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o SDK do Azure Machine Learning

1. Verifique se o Python 3,5 ou posterior está instalado e é reconhecido pelo Visual Studio Code. Se você instalá-lo agora, reinicie Visual Studio Code e [Selecione um intérprete Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Na janela do terminal integrado, especifique o intérprete Python a ser usado. Ou selecione Enter para usar o intérprete Python padrão.

   ![Escolha o interpretador](./media/vscode-tools-for-ai/python.png)

1. No canto inferior direito da janela, uma notificação é exibida, indicando que o SDK do [Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) está sendo instalado automaticamente. O ambiente do Python recém-criado é local e privado, e tem os pré-requisitos de Visual Studio Code para trabalhar com o serviço Azure Machine Learning.

   ![Instalar o SDK do Azure Machine Learning para Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de começar a treinar e implantar modelos de aprendizado de máquina no Visual Studio Code, você precisa criar um [espaço de trabalho de serviço do Azure Machine Learning](concept-workspace.md) na nuvem. Este espaço de trabalho conterá seus modelos e recursos. 

Para criar um espaço de trabalho e adicionar seu primeiro experimento:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

   [![Criar um espaço de trabalho](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **Criar Espaço de Trabalho**. É exibida uma lista. Na imagem animada de exemplo, o nome da assinatura é **avaliação gratuita**e o espaço de trabalho é **TeamWorkspace**. 

1. Selecione um grupo de recursos na lista ou crie um novo usando o assistente na paleta de comandos.

1. No campo, digite um nome exclusivo e claro para o novo espaço de trabalho. Na imagem de exemplo, o espaço de trabalho é denominado **TeamWorkspace**.

1. Selecione Enter para criar o novo espaço de trabalho. Ele aparece na árvore, abaixo do nome da assinatura.

1. Clique com o botão direito do mouse no nó **experimento** e escolha **criar experimento** no menu de contexto.  Os experimentos acompanham suas execuções usando o Azure Machine Learning.

1. No campo, insira um nome para o experimento. Nas capturas de tela de exemplo, o experimento é denominado **MNIST**.
 
1. Selecione Enter para criar o novo experimento. O experimento aparece na árvore, abaixo do nome do espaço de trabalho.

1. Em um espaço de trabalho, você pode clicar com o botão direito do mouse em um experimento para defini-lo como o experimento **ativo** . O experimento **ativo** é o experimento atual. A pasta aberta no Visual Studio Code será vinculada a este experimento na nuvem. Essa pasta deve conter seus scripts do Python locais.

Agora, suas métricas-chave serão armazenadas no histórico do experimento. Da mesma forma, os modelos que você treinar serão automaticamente carregados para Azure Machine Learning e armazenados junto com suas métricas de teste e logs. 

[![Anexar uma pasta no Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Criar e gerenciar destinos de computação

Com Azure Machine Learning para Visual Studio Code, você pode preparar seus dados, treinar modelos e implantá-los localmente e em destinos de computação remota.

A extensão dá suporte a vários destinos de computação remota para Azure Machine Learning. Para obter mais informações, consulte a lista completa de [destinos de computação com suporte para Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Criar destinos de computação para Azure Machine Learning no Visual Studio Code

Para criar um destino de computação:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem de exemplo a seguir, o nome da assinatura é **avaliação gratuita**e o espaço de trabalho é **TeamWorkspace**. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. Na paleta de comandos, selecione um tamanho de máquina virtual.

6. Na paleta de comandos, no campo, insira um nome para o destino de computação. 

7. No arquivo de configuração JSON que é aberto em uma nova guia, especifique as propriedades avançadas. Você pode especificar propriedades como uma contagem máxima de nós.

8. Quando terminar de configurar o destino de computação, no canto inferior direito da janela, selecione **Enviar**.

Aqui está um exemplo de como criar um AMLCompute (computação de Azure Machine Learning):

[![Criar computação AML no Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O arquivo de configuração de execução

A extensão de Visual Studio Code cria automaticamente um destino de computação local e executa configurações para seus ambientes local e Docker no computador local. Você pode encontrar os arquivos de configuração de execução no nó de destino de computação associado. 

## <a name="train-and-tune-models"></a>Treinar e ajustar modelos

Use Azure Machine Learning para Visual Studio Code (visualização) para iterar rapidamente em seu código, percorrer e depurar e usar sua solução para controle do código-fonte. 

Para executar seu experimento localmente usando Azure Machine Learning:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. No nó do espaço de trabalho, expanda o nó **computação** e clique com o botão direito do mouse na **configuração de execução** da computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. No explorador de arquivos, selecione o script que você deseja executar. 

1. Selecione **Exibir execução do experimento** para ver o portal de Azure Machine Learning integrado para monitorar suas execuções e ver seus modelos treinados.

Aqui está um exemplo de como executar um experimento localmente:

[![Executar um experimento localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Usar computações remotas para experimentos em Visual Studio Code

Para usar um destino de computação remota para treinamento, você precisa criar um arquivo de configuração de execução. Esse arquivo informa ao Azure Machine Learning não apenas onde executar o experimento, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O arquivo de dependências conda

Por padrão, um novo ambiente Conda é criado para você e suas dependências de instalação são gerenciadas. No entanto, você deve especificar suas dependências e suas versões no arquivo *aml_config/conda_dependencies. yml* . 

O trecho a seguir do *aml_config/conda_dependencies. yml* padrão especifica `tensorflow=1.12.0`. Se você não especificar a versão da dependência, a versão mais recente será usada. É possível adicionar outras dependências no arquivo de configuração.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

Para executar seu experimento com Azure Machine Learning em um destino de computação remota:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. Na janela do editor, clique com o botão direito do mouse em seu **script Python e selecione AML: executar como experimento no Azure**. 

1. Na paleta de comandos, selecione o destino de computação. 

1. Na paleta de comandos, no campo, insira o nome da configuração de execução. 

1. Edite o arquivo *conda_dependencies. yml* para especificar as dependências de tempo de execução do experimento. Em seguida, no canto inferior direito da janela, selecione **Enviar**. 

1. Selecione **Exibir execução do experimento** para ver o portal de Azure Machine Learning integrado para monitorar suas execuções e ver seus modelos treinados.

Aqui está um exemplo de como executar um experimento em um destino de computação remota:

[![Executar um experimento em um destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implantar e gerenciar modelos
No Azure Machine Learning, você pode implantar e gerenciar seus modelos de aprendizado de máquina na nuvem e na borda. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrar seu modelo para Azure Machine Learning de Visual Studio Code

Agora que você treinou seu modelo, você pode registrá-lo em seu espaço de trabalho. Você pode acompanhar e implantar modelos registrados.

Para registrar seu modelo:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, clique com o botão direito do mouse em **Modelos** e escolha **Registrar Modelo**.

1. Na paleta de comandos, no campo, insira um nome de modelo. 

1. Na lista, escolha se deseja carregar um **arquivo de modelo** (para modelos únicos) ou uma **pasta de modelo** (para modelos com vários arquivos, como TensorFlow). 

1. Selecione sua pasta ou arquivo.

1. Quando terminar de configurar as propriedades do modelo, no canto inferior direito da janela, selecione **Enviar**. 

Aqui está um exemplo de como registrar seu modelo para Azure Machine Learning:

[![Registrar um modelo para AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Implantar seu serviço de Visual Studio Code

No Visual Studio Code, você pode implantar seu serviço Web para:
+ ACI (instâncias de contêiner do Azure) para teste.
+ AKS (serviço kubernetes do Azure) para produção.

Você não precisa criar um contêiner ACI para testar com antecedência, pois os contêineres ACI são criados em tempo real. No entanto, você precisa configurar clusters AKS com antecedência. Para obter mais informações, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md).

Para implantar um serviço Web:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, expanda o nó **Modelos**.

1. Clique com o botão direito do mouse no modelo que você deseja implantar e escolha **implantar serviço do modelo registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação no qual você deseja implantar. 

1. Na paleta de comandos, no campo, insira um nome para esse serviço.  

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de script.

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de dependência Conda.

1. Quando terminar de configurar suas propriedades de serviço, no canto inferior direito da janela, selecione **Enviar** para implantar. No arquivo de propriedades do serviço, você pode especificar um arquivo do Docker local ou um arquivo Schema. JSON.

O serviço Web está implantado.

Veja um exemplo de como implantar um serviço Web:

[![Implantar um serviço Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usar atalhos do teclado

Você pode usar o teclado para acessar Azure Machine Learning recursos no Visual Studio Code. O atalho de teclado mais importante a ser informado é Ctrl + Shift + P, que exibe a paleta de comandos. Na paleta de comandos, você tem acesso a todas as funcionalidades de Visual Studio Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para Azure Machine Learning Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma explicação de como treinar com Azure Machine Learning fora do Visual Studio Code, consulte [o tutorial: Treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter uma explicação de como editar, executar e depurar código localmente, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).

---
title: Usar o Visual Studio Code com
titleSuffix: Azure Machine Learning service
description: Saiba como instalar o Azure Machine Learning para Visual Studio Code e criar um experimento simples no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818613"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, você aprenderá a usar a extensão **Azure Machine Learning para Visual Studio Code** para treinar e implantar modelos de aprendizado profundo e aprendizado de máquina com o serviço do Azure Machine Learning no VS Code (Visual Studio Code).

Serviço de Machine Learning do Azure fornece suporte para executar testes localmente e destinos de computação remota. Em cada experimento, é possível acompanhar várias execuções, já que muitas vezes é preciso testar iterativamente diferentes técnicas, hiperparâmetros e muito mais. Você pode usar o Azure Machine Learning para acompanhar métricas personalizadas e execuções de experimento, possibilitando a capacidade de reprodução e auditoria da ciência de dados.

Além disso, é possível implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

+ O Visual Studio Code deve ser instalado. O VS Code é um editor de código-fonte leve, mas poderoso que é executado na área de trabalho. Ele vem com suporte interno para o Python e muito mais.  [Saiba como instalar o VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instale o Python 3.5 ou superior](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalar a extensão Azure Machine Learning para VS Code

Quando você instala a extensão **Azure Machine Learning**, mais duas outras extensões são instaladas automaticamente (se você tiver acesso à Internet). Elas são a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e a extensão [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Para trabalhar com o Azure Machine Learning, precisamos transformar o VS Code em um IDE Python. Trabalhar com [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python) requer a extensão Microsoft Python, que é instalada automaticamente com a extensão do Azure Machine Learning. A extensão transforma o VS Code em um excelente IDE e funciona em qualquer sistema operacional, com uma variedade de interpretadores do Python. Ela aproveita toda a capacidade do VS Code para fornecer preenchimento automático e IntelliSense, linting, depuração e testes de unidade, além da capacidade de alternar facilmente entre ambientes Python, incluindo ambientes virtuais e conda. Confira este passo a passo de edição, execução e depuração de código Python. Confira o [Tutorial Olá Mundo do Python](https://code.visualstudio.com/docs/python/python-tutorial)

**Para instalar a extensão do Azure Machine Learning:**

1. Inicie o VS Code.

1. Em um navegador, acesse: Extensão [Azure Machine Learning para Visual Studio Code (Versão Prévia)](https://aka.ms/vscodetoolsforai)

1. Na página da Web, clique em **Instalar**. 

1. Na guia de extensão, clique em **Instalar**.

1. Uma guia de boas-vinda é aberta no VS Code para a extensão e o símbolo do Azure (descrito na caixa vermelha na figura abaixo) é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividade do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, clique em **Entrar** e siga o prompt na tela para se autenticar no Azure. 
   
   A extensão da Conta do Azure, que foi instalada junto com o Azure Machine Learning para a extensão do VS Code, ajuda você a se autenticar com sua conta do Azure. Veja a lista de comandos na página [Extensão Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Confira a [Extensão do IntelliCode para VS Code (versão prévia)](https://go.microsoft.com/fwlink/?linkid=2006060). O IntelliCode fornece um conjunto de recursos assistidos por IA para o IntelliSense no Python, como inferir os preenchimentos automáticos mais relevantes com base no contexto de código atual.

## <a name="azure-ml-sdk-installation"></a>Instalação do SDK do Azure ML

1. Verifique se o Python 3.5 ou posterior está instalado e é reconhecido pelo VS Code. Se você instalá-lo agora, reinicie o VS Code e selecione um interpretador do Python seguindo as instruções em https://code.visualstudio.com/docs/python/python-tutorial.

1. Na janela de terminal integrada, especifique o interpretador do Python a ser usado ou pressione **Enter** para usar seu interpretador do Python padrão.

   ![Escolha o interpretador](./media/vscode-tools-for-ai/python.png)

1. No canto inferior direito da janela, será exibida uma notificação indicando que o SDK do Azure ML está sendo instalado automaticamente.    Um ambiente de Python local privado é criado que tem os pré-requisitos do Visual Studio Code para trabalhar com o serviço de Azure Machine Learning.

   ![instalar o SDK do Azure Machine Learning para Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de iniciar o treinamento e implantar modelos de Machine Learning usando o VS Code, você precisará criar um [workspace de serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) na nuvem para conter os modelos e recursos. Saiba como criá-lo e criar seu primeiro experimento no espaço de trabalho.

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

   [![instalar](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **Criar Espaço de Trabalho**. É exibida uma lista. Na imagem animada, o nome da assinatura é 'Avaliação gratuita' e o workspace é 'TeamWorkspace'. 

1. Selecione um grupo de recursos existente na lista ou crie um novo usando o assistente na Paleta de Comandos.

1. No campo, digite um nome exclusivo e claro para o novo espaço de trabalho. Nas capturas de tela, o workspace é denominado 'TeamWorkspace'.

1. Pressione enter, e o novo espaço de trabalho será criado. Ele aparece na árvore abaixo do nome da assinatura.

1. Clique com o botão direito do mouse no nó Experimento e escolha **Criar Experimento** no menu de contexto.  Os experimentos acompanham suas execuções usando o Azure Machine Learning.

1. No campo, insira um nome para o experimento. Nas capturas de tela, o experimento é denominado 'MNIST'.
 
1. Pressione enter, e o novo experimento será criado. Ele aparece na árvore abaixo do nome do espaço de trabalho.

1. Você pode clicar com o botão direito em um Experimento em um Workspace e selecionar “Definir como Experimento Ativo”. O experimento “**Ativo**” é o que você está usando no momento e sua pasta aberta no VS Code será vinculada a esse experimento na nuvem. Essa pasta deve conter seus scripts do Python locais.

   Agora, cada um de seus experimentos é executado com o experimento, assim, todas as métricas essenciais são armazenadas no histórico de experimentos e os modelos que você treinar são automaticamente carregados no Azure Machine Learning e armazenados com seus logs e métricas de experimentos.

   [![Anexar uma pasta ao VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Criar e gerenciar destinos de computação

Com o Azure Machine Learning para VS Code, você pode preparar seus dados, treinar modelos e implantá-los tanto localmente quanto em destinos de computação remota.

Essa extensão é compatível com vários destinos de computação remota diferentes para o Azure Machine Learning. Consulte a [lista completa de destinos de computação compatíveis](how-to-set-up-training-targets.md) do Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Criar destinos de computação para Azure Machine Learning no VS Code

**Para criar um destino de computação:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem animada, o nome da assinatura é 'Avaliação gratuita' e o workspace é 'TeamWorkspace'. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. Na Paleta de Comandos, selecione um tamanho de Máquina Virtual.

6. Na Paleta de Comandos, insira um nome para o destino de computação no campo. 

7. Especifique as propriedades avançadas no arquivo de configuração JSON que é aberto em uma nova guia. Você pode especificar as propriedades, como uma contagem máxima do nó.

8. Ao terminar a configuração do seu destino de computação, clique em **Enviar** no canto inferior direito da tela.

Este é um exemplo para criar uma Computação do Microsoft Azure Machine Learning (AMLCompute): [![Criar a Computação do AML no VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O arquivo de “configuração de execução”

A extensão do VS Code criará automaticamente um destino de computação local e executará configurações para seus ambientes **local** e **docker** em seu computador local. Os arquivos de configuração de execução podem ser encontrados sob o nó de destino de computação associados. 

## <a name="train-and-tune-models"></a>Treinar e ajustar modelos

Use o Azure Machine Learning para VS Code (Versão Prévia) para fazer iteração rapidamente no seu código, percorrê-lo, depurá-lo e usar sua solução preferencial de controle do código-fonte. 

**Para executar seu experimento localmente com o Azure Machine Learning:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. No nó do espaço de trabalho, expanda o nó **Computação** e clique com o botão direito do mouse em **Executar Configuração** para a computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. Selecione o script para executar no Explorador de Arquivos. 

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

Este é um exemplo de como executar um experimento localmente: [![Executar um experimento localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Usar serviços de computação remotos para experimentos no VS Code

Para usar um destino de computação remota durante o treinamento, crie um arquivo de configuração de execução. Esse arquivo informa ao Azure Machine Learning não apenas onde executar o experimento, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O arquivo de dependências conda

Por padrão, um novo ambiente conda é criado, e suas dependências de instalação são gerenciadas. No entanto, você precisa especificar suas dependências e as versões no arquivo `aml_config/conda_dependencies.yml`. 

Trata-se de um trecho de código do padrão “aml_config/conda_dependencies.yml”. Por exemplo, você pode especificar 'tensorflow=1.12.0' conforme mostrado a seguir. Se você não especificar a versão da dependência, a versão mais recente será usada.  
É possível adicionar outras dependências no arquivo de configuração.

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

**Para executar seu experimento com o Azure Machine Learning em um destino de computação remoto:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. Clique com o botão direito em seu script Python na janela do editor e selecione **AML: executar como experimento no Azure**. 

1. Na Paleta de Comandos, selecione o destino de computação. 

1. Na Paleta de Comandos, insira o nome da configuração de execução no campo. 

1. Edite o arquivo conda_dependencies.yml para especificar as dependências de tempo de execução do experimento e, em seguida, clique em **Enviar** no canto inferior direito da tela. 

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

Este é um exemplo de como executar um experimento em um destino de computação remoto: [![Executar um experimento em um destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implantar e gerenciar modelos
O Azure Machine Learning permite implantar e gerenciar modelos de aprendizado de máquina na nuvem e na borda. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrar seu modelo no Azure Machine Learning pelo VS Code

Agora que você já treinou seu modelo, pode registrá-lo em seu espaço de trabalho.
Os modelos registrados podem ser controlados e implantados.

**Para registrar seu modelo:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, clique com o botão direito do mouse em **Modelos** e escolha **Registrar Modelo**.

1. Na Paleta de Comandos, insira um nome de modelo no campo. 

1. Na lista, escolha se você deseja carregar um **arquivo de modelo** (para modelos únicos), uma **pasta de modelo** (para modelos com vários arquivos, como o Tensorflow). 

1. Selecione sua pasta ou arquivo.

1. Ao terminar a configuração das propriedades do modelo, clique em **Enviar** no canto inferior direito da tela. 

Este é um exemplo de como registrar seu modelo para AML: [![Registrar um modelo para AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Implantar o serviço pelo VS Code

Usando o VS Code, você pode implantar seu serviço Web nos seguintes locais:
+ ACI (Instância do Contêiner do Azure): para teste
+ AKS (Serviço do Kubernetes do Azure): para produção 

Não será preciso criar um contêiner ACI para testar com antecedência, pois eles são criados em tempo real. No entanto, clusters AKS precisam ser configurados com antecedência. 

Saiba mais sobre [como implantar com Azure Machine Learning](how-to-deploy-and-where.md) em geral.

**Para implantar um serviço Web:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, expanda o nó **Modelos**.

1. Clique com o botão direito do mouse no modelo que você deseja implantar e escolha o comando **Implantar Serviço do Modelo Registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação para o qual você deseja implantar. 

1. Na Paleta de Comandos, digite um nome para este serviço no campo.  

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecionar o arquivo de script.

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecione o arquivo de dependência Conda.

1. Ao terminar a configuração das propriedades do serviço, clique em **Enviar** no canto inferior direito da tela para implantar. Nesse arquivo de propriedades do serviço, você pode especificar um arquivo do Docker local ou um arquivo schema.json que talvez queira usar.

O serviço Web está implantado.

Este é um exemplo de como implantar um serviço da Web: [![Implantar um serviço da Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usar atalhos do teclado

Como a maior parte do VS Code, os recursos do Azure Machine Learning no VS Code são acessíveis por meio do teclado. A combinação de teclas mais importante a ser aprendida é Ctrl+Shift+P, que abrirá a Paleta de Comandos. Da paleta, você tem acesso a toda a funcionalidade do VS Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para o Azure Machine Learning para VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação passo a passo do treinamento com Machine Learning fora do VS Code, leia o [Tutorial: Treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo da edição, execução e depuração de código localmente, confira o [Tutorial “Olá, Mundo” do Python](https://code.visualstudio.com/docs/python/python-tutorial)

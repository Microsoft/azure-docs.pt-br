---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning service
description: Saiba como configurar um ambiente de desenvolvimento ao trabalhar com o serviço do Azure Machine Learning. Neste artigo, você aprenderá como usar os ambientes do Conda, criar arquivos de configuração e configurar Jupyter Notebooks, Azure Notebooks, Azure Databricks, IDEs, editores de códigos e Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 720f984feb5675281510962d4ebee63f638d696d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819974"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Neste artigo, você aprenderá como configurar um ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning. O serviço do Machine Learning é independente de plataforma.

Os únicos requisitos para o seu ambiente de desenvolvimento são Python 3, Anaconda (para ambientes isolados) e um arquivo de configuração que contém as informações de espaço de trabalho do Azure Machine Learning.

Este artigo concentra-se nos ambientes e ferramentas a seguir:

* Azure Notebooks: Um serviço Jupyter Notebooks hospedado na nuvem do Azure. Essa é a maneira mais fácil de começar, porque o SDK do Azure Machine Learning já está instalado.

* [DSVM (Máquina Virtual de Ciência de Dados)](#dsvm): Um ambiente de desenvolvimento ou experimentação pré-configurado na nuvem do Azure, projetado para o trabalho de ciência de dados e que pode ser implantado em instâncias de VM somente da CPU ou em instâncias baseadas em GPU. O Python 3, o Conda, o Jupyter Notebooks e o SDK do Azure Machine Learning já estão instalados. A VM é fornecida com estruturas de aprendizado profundo e aprendizado de máquina populares, ferramentas e editores para o desenvolvimento de soluções de aprendizado de máquina. É provavelmente o ambiente de desenvolvimento mais completo para aprendizado de máquina na plataforma do Azure.

* [Jupyter Notebook](#jupyter): Se você já estiver usando o Jupyter Notebook, o SDK terá alguns adicionais que deverão ser instalados.

* [Visual Studio Code](#vscode): Se você usar o Visual Studio Code, ele terá algumas extensões úteis que você poderá instalar.

* [Azure Databricks](#aml-databricks): Uma plataforma de análise de dados popular baseada no Apache Spark. Saiba como obter o SDK do Azure Machine Learning do cluster para que você possa implantar modelos.

* [Azure Notebooks](#aznotebooks): Um serviço Jupyter Notebooks hospedado na nuvem do Azure. Também é uma maneira fácil de começar, porque o SDK de aprendizado de máquina do Azure já está instalado.  

Se você já tiver um ambiente de Python 3 ou desejar ver apenas as etapas básicas para instalar o SDK, confira a seção [Computador local](#local).

## <a name="prerequisites"></a>Pré-requisitos

- Um workspace de serviço do Azure Machine Learning. Para criar o espaço de trabalho, consulte [criar um espaço de trabalho do serviço de Azure Machine Learning](setup-create-workspace.md).

Um espaço de trabalho é tudo o que você precisa para começar a usar sua [Azure Notebooks](#aznotebooks), um [DSVM](#dsvm), ou [Azure Databricks](#aml-databricks).

Para instalar o ambiente do SDK para seu [computador local](#local), [servidor de Jupyter Notebook](#jupyter) ou [Visual Studio Code](#vscode) também é necessário:

- Ambos os [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) Gerenciador de pacotes.

- No Linux ou MacOS, será necessário o shell do bash.

    > [!TIP]
    > Se você estiver no Linux ou no macOS e usar um shell diferente do bash (por exemplo, zsh), poderá receber erros ao executar alguns comandos. Para contornar esse problema, use o comando `bash` para iniciar um novo shell do Bash e executar os comandos nele.

- No Windows, você precisa do prompt de comando ou do prompt Anaconda (instalado pelo Anaconda e pelo Miniconda).

## <a id="aznotebooks"></a>Azure Notebooks

O [Azure Notebooks](https://notebooks.azure.com) (versão prévia) é um ambiente de desenvolvimento interativo na nuvem do Azure. É uma maneira fácil de Introdução ao desenvolvimento do Azure Machine Learning.

* O SDK do Azure Machine Learning já está instalado.
* Após criar um workspace de serviço do Azure Machine Learning no portal do Azure, você poderá clicar em um botão para configurar automaticamente o ambiente do Azure Notebook para trabalhar com o workspace.

Use o [portal do Azure](https://portal.azure.com) começar com os blocos de anotações do Azure.  Abra seu espaço de trabalho e para o **visão geral** seção, selecione **Introdução ao Azure Notebooks**.

Por padrão, o Azure Notebooks usa uma camada de serviço gratuita que está limitada a 4 GB de memória e 1 GB de dados. No entanto, é possível remover esses limites anexando uma instância de Máquina Virtual de Ciência de Dados ao projeto do Azure Notebooks. Para saber mais, confira [Gerenciar e configurar projetos do Azure Notebooks: camada de computação](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).    


## <a id="dsvm"></a>Máquina Virtual de Ciência de Dados

A DSVM é uma imagem de VM (máquina virtual) personalizada. Ela foi projetada para trabalhos de ciência de dados pré-configurados com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e o SDK do Azure Machine Learning
  - Ferramentas de ciência de dados populares como o Spark Standalone e Drill
  - Ferramentas do Azure como a CLI do Azure, o AzCopy e o Gerenciador de Armazenamento
  - IDEs (Ambientes de Desenvolvimento Integrados) como o Visual Studio Code e o PyCharm
  - Servidor do Jupyter Notebook

O SDK do Azure Machine Learning funciona na versão da DSVM para Ubuntu ou Windows. Porém, se você também planeja usar a DSVM como um destino de computação, somente o Ubuntu tem suporte.

Para usar a DSVM como um ambiente de desenvolvimento, faça o seguinte:

1. Crie uma DSVM em um dos seguintes ambientes:

    * Portal do Azure:

        * [Criar uma Máquina Virtual de Ciência de Dados do Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma máquina virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * CLI do Azure:

        > [!IMPORTANT]
        > * Ao usar a CLI do Azure, primeiro você deverá entrar na assinatura do Azure usando o comando `az login`.
        >
        > * Ao usar os comandos nessa etapa, será necessários fornecer um nome do grupo de recursos, um nome para a VM, um nome de usuário e uma senha.

        * Para criar uma Máquina Virtual de Ciência de Dados do Ubuntu, use o comando a seguir:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma Máquina Virtual de Ciência de Dados do Windows, use o comando a seguir:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O SDK do Azure Machine Learning já está instalado na DSVM. Para usar o ambiente do Conda que contém o SDK, use um dos seguintes comandos:

    * Para DSVM do Ubuntu:

        ```shell
        conda activate py36
        ```

    * Para DSVM do Windows:

        ```shell
        conda activate AzureML
        ```

1. Para confirmar que você pode acessar o SDK e verificar a versão, use o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar a DSVM para usar o workspace de serviço do Azure Machine Learning, consulte a seção [Criar um arquivo de configuração do workspace](#workspace).

Para obter mais informações, consulte [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computador local

Quando você estiver usando um computador local (que também pode ser uma máquina virtual remota), criar um ambiente do Anaconda e instale o SDK da seguinte maneira:

1. Baixe e instale [Anaconda](https://www.anaconda.com/distribution/#download-section) (versão 3.7 Python) se ainda não tiver.

1. Abra um prompt do Anaconda e criar um ambiente com os seguintes comandos:

    Execute o seguinte comando para criar o ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Em seguida, ative o ambiente.

    ```shell
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando o python 3.6.5, mas qualquer subversions específicos podem ser escolhidos. Compatibilidade do SDK não pode ser garantida com determinadas versões principais (3.5 + é recomendado), e é recomendável para experimentar um versão/subversion diferente em seu ambiente do Anaconda se você encontrar erros. Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados.

1. Execute os seguintes comandos em seu novo ambiente para habilitar os kernels do ipython específicas do ambiente. Isso garantirá que o pacote e o kernel esperado importar comportamento ao trabalhar com Notebooks Jupyter em ambientes de Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, execute o seguinte comando para criar o kernel:

    ```shell
    ipython kernel install --user
    ```

1. Use os seguintes comandos para instalar os pacotes:

    Este comando instala o SDK base de aprendizado de máquina do Azure com o bloco de anotações e automl extras. O `automl` extra é uma instalação grande e pode ser removido de colchetes se você não pretende executar experimentos de aprendizado de máquina automatizado. O `automl` extra também inclui o SDK do Azure Machine Learning Data Prep por padrão como uma dependência.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Use este comando para instalar o SDK do Azure Machine Learning Data Prep por conta própria:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Se você receber uma mensagem informando que o PyYAML não pode ser desinstalado, use o seguinte comando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Levará vários minutos para instalar o SDK. Consulte a [guia de instalação](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre as opções de instalação.

1. Instale outros pacotes para experimentação de aprendizado de máquina.

    Use um dos comandos a seguir e substitua  *\<novo pacote >* com o pacote que você deseja instalar. Instalação de pacotes por meio de `conda install` requer que o pacote é parte de canais atuais (novos canais podem ser adicionados na nuvem do Anaconda).

    ```shell
    conda install <new package>
    ```

    Como alternativa, você pode instalar pacotes por meio de `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação, em que você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. O Jupyter Notebook também é uma ótima maneira de compartilhar seus resultados com outras pessoas, já que é possível salvar a saída das seções de código no documento. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

O procedimento a [computador Local](#local) seção instala componentes necessários para a execução de blocos de anotações do Jupyter em um ambiente do Anaconda. Para habilitar esses componentes no ambiente do Jupyter Notebook, faça o seguinte:

1. Abra um prompt do Anaconda e ativar o seu ambiente.

    ```shell
    conda activate myenv
    ```

1. Inicie o servidor do Notebook do Jupyter com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para verificar que o bloco de anotações do Jupyter pode usar o SDK, crie uma **New** notebook, selecione **Python 3** como o kernel e em seguida, execute o comando a seguir em uma célula do bloco de anotações:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se você tiver problemas para importar os módulos e receber um `ModuleNotFoundError`, verifique se o kernel de Jupyter está conectado para o caminho correto para o seu ambiente, executando o código a seguir em uma célula de Notebook.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o Jupyter Notebook para usar o workspace de serviço do Azure Machine Learning, vá para a seção [Criar um arquivo de configuração do workspace](#workspace).

### <a id="vscode"></a>Visual Studio Code

O Visual Studio Code é um editor de código multiplataforma. Ele se baseia em uma instalação local do Python 3 e do Conda para suporte de Python, mas fornece ferramentas adicionais para trabalhar com Inteligência Artificial. Ele também dá suporte para selecionar o ambiente do Conda de dentro do editor de código.

Para usar o Visual Studio Code para desenvolvimento, faça o seguinte:

1. Para aprender a usar o Visual Studio Code para desenvolvimento em Python, consulte [Introdução ao Python no VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Para selecionar o ambiente Conda, abra o VS Code e, em seguida, selecione Ctrl+Shift+P (Linux e Windows) ou Command+Shift+P (Mac).
    A __Paleta de Comandos__ abre.

1. Insira __Python: Selecione Interpretador__e, em seguida, selecione o ambiente Conda.

1. Para validar que é possível usar o SDK, crie e execute um novo arquivo Python (.py) contendo o seguinte código:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para instalar a extensão do Azure Machine Learning para Visual Studio Code, consulte [Ferramentas para IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, consulte [Usar o Azure Machine Learning para Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
O Azure Databricks é um ambiente com base no Apache Spark na nuvem do Azure. Ele fornece um ambiente de Notebook com base em colaboração com o cluster de computação com base de CPU ou GPU.

Como o Databricks do Azure funciona com o serviço de Azure Machine Learning:
+ Você pode treinar um modelo usando o MLlib Spark e implantar o modelo em ACI/AKS do Azure databricks. 
+ Você também pode usar [automatizada de aprendizado de máquina](concept-automated-ml.md) recursos em um SDK do ML Azure especial com o Azure Databricks.
+ Você pode usar o Azure Databricks como um destino de computação de uma [pipeline do Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configurar o cluster do Databricks

Criar uma [cluster do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas configurações se aplicam apenas se você instalar o SDK automatizado para aprendizado de máquina no Databricks.
**Levará alguns minutos para criar o cluster.**

Use estas configurações:

| Configuração |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |sempre| nomedoseucluster |
| Databricks Runtime |sempre| Qualquer tempo de execução não ML (não ML 4.x, 5.x) |
| Versão do Python |sempre| 3 |
| Trabalhos |sempre| 2 ou superior |
| Tipos de VM do nó de trabalho <br>(determina o número máximo de iterações simultâneas) |ML automatizado<br>Apenas| Uma VM otimizada para memória é preferível |
| Habilitar o dimensionamento automático |ML automatizado<br>Apenas| Desmarcar |

Aguarde até que o cluster está em execução antes de continuar.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalar o SDK correto em uma biblioteca do Databricks
Quando o cluster estiver em execução, [criar uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote apropriado do SDK do Azure Machine Learning ao seu cluster. 

1. Escolher **apenas uma** opção (não há suporte para nenhuma outra instalação do SDK)

   |SDK&nbsp;pacote&nbsp;extras|Fonte|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para o Databricks| Carregar Python Egg ou PyPI | azureml-sdk[databricks]|
   |Para o Databricks - with-<br> recursos de ML automatizado| Carregar Python Egg ou PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Não há outros extras do SDK podem ser instalados. Escolha apenas uma das opções anteriores [databricks] ou [automl_databricks].

   * Não marque **anexar automaticamente a todos os clusters**.
   * Selecione **Attach** ao lado do nome do seu cluster.

1. Monitorar para detectar erros até que o status é alterado para **anexados**, que pode levar vários minutos.  Se isso falhar, verifique o seguinte: 

   Tente reiniciar o cluster por:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do cluster.
   1. Na guia **Bibliotecas**, selecione **Reiniciar**.
      
   Considere também:
   + Na configuração Automl, ao usar o Azure Databricks adicione os seguintes parâmetros:
        1. ```max_concurrent_iterations``` é baseada no número de nós de trabalho em seu cluster. 
        2. ```spark_context=sc``` baseia-se no contexto do spark padrão. 
   + Ou, se você tiver uma versão antiga do SDK, desmarcá-la de bibliotecas de instalados do cluster e mover para Lixeira. Instale a nova versão do SDK e reinicie o cluster. Se houver um problema depois de fazer isso, desanexe e anexe novamente o cluster.

Se a instalação foi bem-sucedida, a biblioteca importada deve ser semelhante uma destas opções:
   
SDK para o Databricks **_sem_** automatizada de aprendizado de máquina ![SDK de aprendizado de máquina do Azure para o Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para o Databricks **WITH** automatizada de aprendizado de máquina ![automatizada de SDK com o aprendizado de máquina instalado no Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Comece a explorar

Experimente:
+ Baixe o [arquivo morto de bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) do SDK do Azure Databricks e do Azure Machine Learning e [importar o arquivo morto](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) em seu cluster do Databricks.  
  Enquanto muitos notebooks de exemplo estão disponíveis, **só [esses notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com o Azure Databricks.**
  
+ Saiba como [criar um pipeline com o Databricks como a computação de treinamento](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Criar um arquivo de configuração do workspace

O arquivo de configuração do workspace é um arquivo JSON que informa ao SDK como comunicar-se com o workspace de serviço do Azure Machine Learning. O arquivo é nomeado *config.json* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo JSON deve estar na estrutura de diretório que contém os scripts Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório nomeado *.azureml*, ou em um diretório pai.

Para usar esse arquivo de seu código, use `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e se conecta ao seu workspace.

É possível criar o arquivo de configuração de três maneiras:

* **Siga as etapas em [criar um espaço de trabalho do serviço de Azure Machine Learning](setup-create-workspace.md#sdk)**: Um arquivo *config.json* é criado na biblioteca do Azure Notebooks. O arquivo contém as informações de configuração do workspace. É possível baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Crie o arquivo manualmente**: Com esse método, você usa um editor de texto. É possível localizar os valores que entram no arquivo de configuração, visitando o workspace no [portal do Azure](https://portal.azure.com). Copie os valores de nome do workspace, grupo de recursos e ID de assinatura e use-os no arquivo de configuração.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o arquivo programaticamente**: No snippet de código a seguir, você conecta um workspace, fornecendo a ID de assinatura, grupo de recursos e nome do workspace. Em seguida, salva a configuração do workspace no arquivo:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Esse código grava o arquivo de configuração para o *.azureml/config.json* arquivo.


## <a name="next-steps"></a>Próximos passos

- [Treinar um modelo](tutorial-train-models-with-aml.md) no Azure Machine Learning com o conjunto de dados MNIST
- Veja a referência do [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk)
- Saiba mais sobre o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

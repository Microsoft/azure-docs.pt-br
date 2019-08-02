---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning service
description: Saiba como configurar um ambiente de desenvolvimento ao trabalhar com o serviço do Azure Machine Learning. Neste artigo, você aprenderá a usar ambientes Conda, criar arquivos de configuração e configurar seu próprio servidor de notebook baseado em nuvem, notebooks Jupyter, Azure Databricks, Azure Notebooks, IDEs, editores de código e o Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3738ffe8b3faedc328bde01173400289403652f4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297928"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Neste artigo, você aprenderá como configurar um ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning. O serviço do Machine Learning é independente de plataforma.

Os únicos requisitos para seu ambiente de desenvolvimento são Python 3, Anaconda (para ambientes isolados) e um arquivo de configuração que contém suas informações de Azure Machine Learning espaço de trabalho.

Este artigo concentra-se nos ambientes e ferramentas a seguir:

* Sua própria [VM de bloco de anotações baseado em nuvem](#notebookvm): Use um recurso de computação em sua estação de trabalho para executar blocos de anotações do Jupyter. Essa é a maneira mais fácil de começar, porque o SDK do Azure Machine Learning já está instalado.

* [DSVM (Máquina Virtual de Ciência de Dados)](#dsvm): Um ambiente de desenvolvimento ou experimentação pré-configurado na nuvem do Azure, projetado para o trabalho de ciência de dados e que pode ser implantado em instâncias de VM somente da CPU ou em instâncias baseadas em GPU. O Python 3, o Conda, o Jupyter Notebooks e o SDK do Azure Machine Learning já estão instalados. A VM é fornecida com estruturas de aprendizado profundo e aprendizado de máquina populares, ferramentas e editores para o desenvolvimento de soluções de aprendizado de máquina. É provavelmente o ambiente de desenvolvimento mais completo para aprendizado de máquina na plataforma do Azure.

* [Jupyter Notebooks](#jupyter): Se você já estiver usando o Jupyter Notebook, o SDK terá alguns adicionais que deverão ser instalados.

* [Visual Studio Code](#vscode): Se você usar o Visual Studio Code, ele terá algumas extensões úteis que você poderá instalar.

* [Azure Databricks](#aml-databricks): Uma plataforma de análise de dados popular baseada no Apache Spark. Saiba como obter o SDK do Azure Machine Learning do cluster para que você possa implantar modelos.

* [Azure Notebooks](#aznotebooks): Um serviço Jupyter Notebooks hospedado na nuvem do Azure. Também é uma maneira fácil de começar, pois o SDK do Azure Machine Learning já está instalado.  

Se você já tiver um ambiente de Python 3 ou desejar ver apenas as etapas básicas para instalar o SDK, confira a seção [Computador local](#local).

## <a name="prerequisites"></a>Pré-requisitos

Um workspace de serviço do Azure Machine Learning. Para criar o espaço de trabalho, consulte [criar um Azure Machine Learning espaço de trabalho de serviço](setup-create-workspace.md). Um espaço de trabalho é tudo o que você precisa para começar com seu próprio [servidor de bloco de anotações baseado em nuvem](#notebookvm), um [DSVM](#dsvm), um [Azure Databricks](#aml-databricks)ou um [Azure notebooks](#aznotebooks).

Para instalar o ambiente do SDK do [computador local](#local), [Jupyter Notebook Server](#jupyter) ou [Visual Studio Code](#vscode) você também precisa:

- O Gerenciador de pacotes [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) .

- No Linux ou MacOS, será necessário o shell do bash.

    > [!TIP]
    > Se você estiver no Linux ou no macOS e usar um shell diferente do bash (por exemplo, zsh), poderá receber erros ao executar alguns comandos. Para contornar esse problema, use o comando `bash` para iniciar um novo shell do Bash e executar os comandos nele.

- No Windows, você precisa do prompt de comando ou do prompt Anaconda (instalado pelo Anaconda e pelo Miniconda).

## <a id="notebookvm"></a>Sua própria VM de notebook baseada em nuvem

A máquina virtual do notebook (versão prévia) é uma estação de trabalho do Azure segura e baseada em nuvem que fornece cientistas de dados com um servidor de notebook Jupyter, JupyterLab e um ambiente de ML totalmente preparado. 

A VM do notebook é: 

+ **Seguro**. Como a VM e o acesso ao notebook são protegidos com HTTPS e Azure Active Directory por padrão, os profissionais de ti podem facilmente impor o logon único e outros recursos de segurança, como a autenticação multifator.

+ **Pré-configurado**. Esse ambiente Python ML totalmente preparado desenha seu pedigree da popular VM de Ciência de Dados de IaaS e inclui:
  + SDK do Python do Azure ML (mais recente)
  + Configuração automática para trabalhar com seu espaço de trabalho
  + Um servidor do Jupyter Notebook
  + IDE do notebook JupyterLab
  + Drivers de GPU pré-configurados 
  + Uma seleção de estruturas de aprendizado profundo
 

  Se você estiver no código, a VM incluirá tutoriais e exemplos para ajudá-lo a explorar e aprender a usar Azure Machine Learning serviço. Os blocos de anotações de exemplo são armazenados na conta de armazenamento de BLOBs do Azure do espaço de trabalho, tornando-os compartilháveis entre as VMs. Quando executado, eles também têm acesso aos armazenamentos de dados e aos recursos de computação do seu espaço de trabalho. 

+ **Configuração simples**: Crie uma a qualquer momento em seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo de VM do Azure. Experimente agora com este [guia de início rápido: Usar um servidor do notebook baseado em nuvem para começar a usar o Azure Machine Learning](quickstart-run-cloud-notebook.md).

+ **Personalizável**. Enquanto uma oferta de VM gerenciada e segura, você mantém o acesso completo aos recursos de hardware e o personaliza para o desejo do coração. Por exemplo, crie rapidamente a VM mais recente NVidia V100 para executar a depuração passo a passo da arquitetura de rede neural de romance.

Para parar de incorrer em encargos [de VM do bloco de notas, pare a VM do notebook](quickstart-run-cloud-notebook.md#stop-the-notebook-vm). 

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

Quando você estiver usando um computador local (que também pode ser uma máquina virtual remota), crie um ambiente Anaconda e instale o SDK fazendo o seguinte:

1. Baixe e instale o [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3,7 versão) se você ainda não o tiver.

1. Abra um prompt do Anaconda e crie um ambiente com os seguintes comandos:

    Execute o comando a seguir para criar o ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Em seguida, ative o ambiente.

    ```shell
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando Python 3.6.5, mas qualquer subversão específica pode ser escolhida. A compatibilidade do SDK pode não ser garantida com determinadas versões principais (o 3.5 + é recomendado) e é recomendável tentar uma versão/subversão diferente em seu ambiente Anaconda se você encontrar erros. Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados.

1. Execute os seguintes comandos em seu novo ambiente para habilitar kernels ipython específicos do ambiente. Isso garantirá o comportamento esperado de importação de kernel e pacote ao trabalhar com notebooks Jupyter em ambientes Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, execute o seguinte comando para criar o kernel:

    ```shell
    ipython kernel install --user
    ```

1. Use os seguintes comandos para instalar pacotes do:

    Esse comando instala o SDK de Azure Machine Learning base com os extras do bloco de anotações e do automl. O `automl` extra é uma instalação grande e pode ser removido dos colchetes se você não pretende executar experimentos de aprendizado de máquina automatizados. O `automl` extra também inclui o Azure Machine Learning SDK de preparação de dados por padrão como uma dependência.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > Se você receber uma mensagem informando que o PyYAML não pode ser desinstalado, use o seguinte comando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`

   Levará vários minutos para instalar o SDK. Consulte o [Guia de instalação](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre as opções de instalação.

1. Instale outros pacotes para sua experimentação do Machine Learning.

    Use qualquer um dos comandos a seguir e substitua  *\<o novo pacote >* pelo pacote que você deseja instalar. A instalação de `conda install` pacotes via requer que o pacote faça parte dos canais atuais (novos canais podem ser adicionados no Anaconda Cloud).

    ```shell
    conda install <new package>
    ```

    Como alternativa, você pode instalar pacotes via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação, em que você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. O Jupyter Notebook também é uma ótima maneira de compartilhar seus resultados com outras pessoas, já que é possível salvar a saída das seções de código no documento. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

O procedimento na seção [computador local](#local) instala os componentes necessários para executar blocos de anotações do Jupyter em um ambiente Anaconda. Para habilitar esses componentes no ambiente do Jupyter Notebook, faça o seguinte:

1. Abra um prompt do Anaconda e ative seu ambiente.

    ```shell
    conda activate myenv
    ```
    
1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks) para um conjunto de blocos de anotações de exemplo.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de Jupyter Notebook com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para verificar se Jupyter Notebook pode usar o SDK, crie um **novo** bloco de anotações, selecione **Python 3** como kernel e, em seguida, execute o seguinte comando em uma célula do notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se você encontrar problemas ao importar módulos e receber `ModuleNotFoundError`um, verifique se o kernel do Jupyter está conectado ao caminho correto para o seu ambiente executando o seguinte código em uma célula do bloco de anotações.

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
Azure Databricks é um ambiente baseado em Apache Spark na nuvem do Azure. Ele fornece um ambiente com base em blocos de anotações colaborativos com CPU ou cluster de computação baseado em GPU.

Como Azure Databricks funciona com o serviço Azure Machine Learning:
+ Você pode treinar um modelo usando o Spark MLlib e implantar o modelo em ACI/AKS de dentro Azure Databricks. 
+ Você também pode usar recursos automatizados de [aprendizado de máquina](concept-automated-ml.md) em um SDK do Azure ml especial com Azure Databricks.
+ Você pode usar Azure Databricks como um destino de computação de um [pipeline de Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configurar o cluster do databricks

Crie um [cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)do databricks. Algumas configurações se aplicam somente se você instalar o SDK do Machine Learning automatizado no databricks.
**Levará alguns minutos para criar o cluster.**

Use estas configurações:

| Configuração |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |Constante| nomedoseucluster |
| Databricks Runtime |Constante| Qualquer tempo de execução não ML (não ML 4.x, 5.x) |
| Versão do Python |Constante| 3 |
| Trabalhos |Constante| 2 ou superior |
| Tipos de VM do nó de trabalho <br>(determina o número máximo de iterações simultâneas) |ML automatizado<br>somente| Uma VM otimizada para memória é preferível |
| Habilitar o dimensionamento automático |ML automatizado<br>somente| Desmarcar |

Aguarde até que o cluster está em execução antes de continuar.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalar o SDK correto em uma biblioteca do databricks
Depois que o cluster estiver em execução, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote SDK do Azure Machine Learning apropriado ao cluster. 

1. Escolha **apenas uma** opção (não há suporte para nenhuma outra instalação do SDK)

   |Extras&nbsp;do&nbsp;pacote do SDK|Origem|Nome&nbsp;do PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para databricks| Carregar Python Egg ou PyPI | azureml-sdk[databricks]|
   |Para databricks-com-<br> recursos de ML automatizados| Carregar Python Egg ou PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Nenhum outro adicional do SDK pode ser instalado. Escolha apenas uma das opções anteriores [databricks] ou [automl_databricks].

   * Não selecione **anexar automaticamente a todos os clusters**.
   * Selecione **anexar** ao lado do nome do cluster.

1. Monitorar erros até que o status seja alterado para **anexado**, o que pode levar vários minutos.  Se essa etapa falhar, verifique o seguinte: 

   Tente reiniciar o cluster da:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do cluster.
   1. Na guia **Bibliotecas**, selecione **Reiniciar**.
      
   Considere também:
   + Na configuração do Automl, ao usar Azure Databricks adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations```o é baseado no número de nós de trabalho no cluster. 
        2. ```spark_context=sc```baseia-se no contexto padrão do Spark. 
   + Ou, se você tiver uma versão antiga do SDK, desmarque-a do bibliotecas instalado do cluster e mude para Trash. Instale a nova versão do SDK e reinicie o cluster. Se houver um problema depois de fazer isso, desanexe e anexe novamente o cluster.

Se a instalação tiver sido bem-sucedida, a biblioteca importada deverá ser semelhante a uma destas:
   
SDK para databricks **_sem_** o Machine Learning ![Azure Machine Learning SDK para databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para databricks **com** o SDK do ![Machine Learning automatizado com o Machine Learning automatizado instalado no databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Comece a explorar

Experimente:
+ Baixe o [arquivo morto do bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) para Azure Databricks/Azure Machine Learning SDK e [importe o arquivo morto](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) para o cluster do databricks.  
  Embora muitos blocos de anotações de exemplo estejam disponíveis, **somente [esses blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com Azure Databricks.**
  
+ Saiba como [criar um pipeline com o databricks como a computação de treinamento](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure Notebooks

O [Azure Notebooks](https://notebooks.azure.com) (versão prévia) é um ambiente de desenvolvimento interativo na nuvem do Azure. É uma maneira fácil de começar a usar o desenvolvimento de Azure Machine Learning.

* O SDK do Azure Machine Learning já está instalado.
* Após criar um workspace de serviço do Azure Machine Learning no portal do Azure, você poderá clicar em um botão para configurar automaticamente o ambiente do Azure Notebook para trabalhar com o workspace.

Use o [portal do Azure](https://portal.azure.com) para começar a usar o Azure Notebooks.  Abra seu espaço de trabalho e, na seção **visão geral** , selecione introdução **em Azure notebooks**.

Por padrão, o Azure Notebooks usa uma camada de serviço gratuita que está limitada a 4 GB de memória e 1 GB de dados. No entanto, é possível remover esses limites anexando uma instância de Máquina Virtual de Ciência de Dados ao projeto do Azure Notebooks. Para saber mais, confira [Gerenciar e configurar projetos do Azure Notebooks: camada de computação](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="workspace"></a>Criar um arquivo de configuração do workspace

O arquivo de configuração do workspace é um arquivo JSON que informa ao SDK como comunicar-se com o workspace de serviço do Azure Machine Learning. O arquivo é nomeado *config.json* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo JSON deve estar na estrutura de diretório que contém os scripts Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório chamado *. azureml*ou em um diretório pai.

Para usar esse arquivo de seu código, use `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e se conecta ao seu workspace.

É possível criar o arquivo de configuração de três maneiras:

* **Siga as etapas em [criar um espaço de trabalho de serviço de Azure Machine Learning](setup-create-workspace.md#sdk)** : Um arquivo *config.json* é criado na biblioteca do Azure Notebooks. O arquivo contém as informações de configuração do workspace. É possível baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Baixe o arquivo**: No [portal do Azure](https://ms.portal.azure.com), selecione **Baixar config.json** na seção **Visão geral** do seu espaço de trabalho.

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

    Esse código grava o arquivo de configuração no arquivo *. azureml/config. JSON* .


## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](tutorial-train-models-with-aml.md) no Azure Machine Learning com o conjunto de dados MNIST
- Veja a referência do [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk)
- Saiba mais sobre o [pacote de preparação de dados para Azure Machine Learning](https://aka.ms/data-prep-sdk)

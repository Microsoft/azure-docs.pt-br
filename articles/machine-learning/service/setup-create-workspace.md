---
title: Criar um workspace
titleSuffix: Azure Machine Learning service
description: Use o portal do Azure, SDK, um modelo ou a CLI para criar seu espaço de trabalho do serviço de Azure Machine Learning. Este espaço de trabalho fornece um local centralizado para trabalhar com todos os artefatos que você cria quando você usa o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: ca43a6cff6a32a30d93e42f6a6624439b2fe83a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766816"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Criar um espaço de trabalho do serviço de Azure Machine Learning

Para usar o serviço Azure Machine Learning, você precisa de um [ **espaço de trabalho de serviço de Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Este espaço de trabalho é o recurso de nível superior para o serviço e fornece um local centralizado para trabalhar com todos os artefatos que você cria. 

Neste artigo, você aprenderá a criar um espaço de trabalho usando qualquer um destes métodos: 
* O [portal do Azure](#portal) interface
* O [do Azure Machine Learning do SDK para Python](#sdk)
* Um modelo do Azure Resource Manager
* O [CLI de aprendizado de máquina do Azure](#cli)

O espaço de trabalho que você criar usando as etapas aqui-in pode ser usado como um pré-requisito para outros tutoriais e artigos de instruções.

Se você gostaria de usar um script para aprendizado de máquina automatizados em um ambiente de Python local de instalação, consulte o [GitHub do Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter instruções.  

Quando você cria um espaço de trabalho os seguintes recursos do Azure são adicionados automaticamente (se eles forem regionalmente disponíveis):
 
- [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
- [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Cofre da Chave do Azure](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Assim como ocorre em outros serviços do Azure, há limites e cotas associados ao Machine Learning. [Saiba mais sobre cotas e como solicitar mais.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Pré-requisitos
Para criar um workspace, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="portal"></a> Portal do Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Não importa como ele foi criado, você pode exibir em seu espaço de trabalho do [portal do Azure](https://portal.azure.com/).  Ver [exibir um espaço de trabalho](how-to-manage-workspace.md#view) para obter detalhes.

## <a name="sdk"></a> SDK do Python

Crie seu espaço de trabalho usando o SDK do Python. Primeiro, você precisa instalar o SDK.

> [!IMPORTANT]
> Ignorar instalação do SDK se você usar uma máquina de Virtual de ciência de dados do Azure ou o Azure Databricks.
> * As Máquinas Virtuais de Ciência de Dados do Azure criadas após 27 de setembro de 2018 vêm com o SDK do Python pré-instalado. Ignorar a instalação e começar com [criar um espaço de trabalho com o SDK](#sdk-create).
> * No ambiente do Azure Databricks, siga as [etapas de instalação do Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Use estas instruções para instalar e usar o SDK do seu computador local. Para usar o Jupyter em uma máquina virtual remota, configure um controle remoto da área de trabalho ou sessão de terminal.

Antes de instalar o SDK, recomendamos que você crie um ambiente de Python isolado primeiro. Embora este artigo use o [Miniconda](https://docs.conda.io/en/latest/miniconda.html), você também poderá usar o [Anaconda](https://www.anaconda.com/) completo instalado ou o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

As instruções neste artigo instalará todos os pacotes que você precisa executar os blocos de anotações do tutorial e guia de início rápido.  Outros notebooks de exemplo podem exigir a instalação de componentes adicionais.  Para obter mais informações sobre esses componentes, veja [Instalar o SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Instalar o Miniconda

[Baixe e instale o Miniconda](https://docs.conda.io/en/latest/miniconda.html). Selecione a versão 3.7 do Python para instalar. Não selecione a versão do Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Criar um ambiente isolado do Python

1. Abra um Prompt do Anaconda, em seguida, criar um novo ambiente de conda chamado *myenv* e instale o Python 3.6.5. O SDK do Azure Machine Learning funcionará com o Python 3.5.2 ou posterior, mas os componentes de aprendizado de máquina automatizado não serão totalmente funcionais no Python 3.7.  Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Ative o ambiente.

    ```shell
    conda activate myenv
    ```

1. Habilite os kernels ipython específicos do ambiente:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, crie o kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Instalar o SDK

1. No ambiente Conda ativado, instale os componentes principais do SDK do Machine Learning com funcionalidades do Jupyter Notebook. A instalação leva alguns minutos para ser concluída, dependendo da configuração do computador.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Para usar esse ambiente para os tutoriais do Azure Machine Learning, instale esses pacotes.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Para usar esse ambiente para tutoriais do Azure Machine Learning, instale os componentes de aprendizado de máquina automatizado.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> Em algumas ferramentas de linha de comando, talvez seja necessário adicionar aspas conforme a seguir:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Criar um espaço de trabalho com o SDK

Crie seu workspace em um Jupyter Notebook usando o SDK do Python.

1. Crie e/ou CD para o diretório que você deseja usar para o guia de início rápido e tutoriais.

1. Para iniciar o Jupyter Notebook, insira este comando:

    ```shell
    jupyter notebook
    ```

1. Na janela do navegador, crie um novo notebook usando o kernel `Python 3` padrão. 

1. Para exibir a versão do SDK, insira e, em seguida, execute o seguinte código Python em uma célula do notebook:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Encontre um valor para o parâmetro `<azure-subscription-id>` na [lista de assinaturas no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Use qualquer assinatura em que sua função seja proprietário ou colaborador. Para obter mais informações sobre funções, consulte [gerenciar o acesso a um espaço de trabalho do Azure Machine Learning](how-to-assign-roles.md) artigo.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Ao executar o código, talvez você precise entrar em sua conta do Azure. Depois de entrar, o token de autenticação será armazenado no cache local.

1. Para exibir os detalhes do workspace, como o armazenamento, o registro de contêiner e o cofre de chaves associados, insira o seguinte código:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Escrever um arquivo de configuração

Salve os detalhes do workspace em um arquivo de configuração no diretório atual. Esse arquivo é denominado *.azureml/config.json*.  

Esse arquivo de configuração do workspace facilita o carregamento do mesmo workspace mais tarde. Você pode carregá-lo com outros blocos de anotações e scripts no mesmo diretório ou subdiretório usando o código `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Esta chamada à API `write_config()` cria o arquivo de configuração no diretório atual. O arquivo *config.json* contém o seguinte:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Para usar seu espaço de trabalho em scripts Python ou blocos de anotações do Jupyter localizado em outros diretórios, copie esse arquivo para esse diretório. O arquivo pode estar no mesmo diretório, um subdiretório chamado *.azureml*, ou em um diretório pai.

## <a name="resource-manager-template"></a>Modelo do Resource manager

Para criar um espaço de trabalho com um modelo, consulte [criar um espaço de trabalho do serviço de Azure Machine Learning usando um modelo](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interface de linha de comando

Para criar um espaço de trabalho com a CLI, consulte [Use a extensão CLI do serviço Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

* Não importa como ele foi criado, você pode exibir em seu espaço de trabalho do [portal do Azure](https://portal.azure.com/).  Ver [exibir um espaço de trabalho](how-to-manage-workspace.md#view) para obter detalhes.

* Experimente o espaço de trabalho com esses tutoriais e guias de início rápido.

    * Início Rápido: [Execute o bloco de anotações do Jupyter na nuvem](quickstart-run-cloud-notebook.md).
    * Início Rápido: [Execute o bloco de anotações do Jupyter em seu próprio servidor](quickstart-run-local-notebook.md).
    * Tutorial de duas partes: [Treinar](tutorial-train-models-with-aml.md) e [implantar](tutorial-deploy-models-with-aml.md) um modo de classificação de imagem.
    * Tutorial de duas partes: [Preparar dados](tutorial-data-prep.md) e [usar o aprendizado de máquina automatizado](tutorial-auto-train-models.md) para criar um modelo de regressão.

* Saiba mais sobre como [configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

* Saiba mais sobre o [SDK de aprendizado de máquina do Azure para Python](https://aka.ms/aml-sdk).

---
title: Criar e gerenciar recursos VS Code extensão (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como criar e gerenciar Azure Machine Learning recursos usando a extensão de Visual Studio Code de Azure Machine Learning.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010666"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Gerenciar Azure Machine Learning recursos com a extensão de VS Code (versão prévia)

Saiba como gerenciar Azure Machine Learning recursos com a extensão VS Code.

![Extensão de VS Code de Azure Machine Learning](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure. Caso não tenha uma, inscreva-se para experimentar a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se você não tiver, [instale-o](https://code.visualstudio.com/docs/setup/setup-overview).
- Extensão de Azure Machine Learning de VS Code. Siga o [Guia de instalação da extensão Azure Machine Learning vs Code](tutorial-setup-vscode-extension.md#install-the-extension) para instalar a extensão.

Todos os processos a seguir pressupõem que você esteja na exibição de Azure Machine Learning no Visual Studio Code. Para iniciar a extensão, selecione o ícone **do Azure** na barra de atividade do vs Code.

## <a name="workspaces"></a>Workspaces

Para obter mais informações, consulte [espaços de trabalho](concept-workspace.md).

### <a name="create-a-workspace"></a>Criar um workspace

1. Na exibição Azure Machine Learning, clique com o botão direito do mouse no nó da assinatura e selecione **criar espaço de trabalho**.
1. No prompt:
    1. Forneça um nome para seu espaço de trabalho
    1. Escolha a sua assinatura do Azure
    1. Escolha ou crie um novo grupo de recursos para provisionar o espaço de trabalho em
    1. Selecione o local onde provisionar o espaço de trabalho.

Os métodos alternativos para criar um espaço de trabalho incluem:

- Abra a exibição paleta de comandos **> paleta de comandos** e insira no prompt de texto **Azure ml: criar espaço de trabalho**.
- Clique no `+` ícone na parte superior da exibição de Azure Machine Learning.
- Crie um novo espaço de trabalho quando solicitado a selecionar um espaço de trabalho durante o provisionamento de outros recursos.

### <a name="remove-a-workspace"></a>Remover um espaço de trabalho

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Clique com o botão direito do mouse no espaço de trabalho que você deseja remover.
1. Selecione se deseja remover:
    - *Somente o espaço de trabalho*: esta opção exclui **somente** o recurso do Azure do espaço de trabalho. O grupo de recursos, as contas de armazenamento e quaisquer outros recursos aos quais o espaço de trabalho foi anexado ainda estão no Azure.
    - *Com recursos associados*: essa opção exclui o espaço de trabalho **e** todos os recursos associados a ele.

## <a name="datastores"></a>Armazenamentos de dados

Atualmente, a extensão VS Code dá suporte a repositórios de armazenamento dos seguintes tipos:

- Compartilhamento de arquivos do Azure
- Armazenamento do Blobs do Azure

Quando você cria um espaço de trabalho, um repositório de armazenamento é criado para cada um desses tipos.

Para obter mais informações, consulte [armazenamentos](concept-data.md#datastores)de dados.

### <a name="create-a-datastore"></a>Criar um repositório de dados

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho em que você deseja criar o repositório de armazenamento.
1. Clique com o botão direito do mouse no nó **repositórios de armazenamento** e selecione **registrar repositório de armazenamento**.
1. No prompt:
    1. Forneça um nome para seu repositório de armazenamento.
    1. Escolha o tipo de repositório de armazenamento.
    1. Selecione o recurso de armazenamento. Você pode escolher um recurso de armazenamento associado ao seu espaço de trabalho ou selecionar um recurso de armazenamento válido em suas assinaturas do Azure.
    1. Escolha o contêiner em que os dados estão dentro do recurso de armazenamento selecionado anteriormente.
1. Um arquivo de configuração aparece em VS Code. Se estiver satisfeito com o arquivo de configuração, selecione **salvar e continuar** ou abra a vs Code paleta de comandos (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="manage-a-datastore"></a>Gerenciar um repositório de armazenamento

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **repositórios de armazenamento** dentro de seu espaço de trabalho.
1. Selecione o repositório de armazenamento que você deseja:
    - *Definir como padrão*. Sempre que você executar experimentos, esse será o repositório de armazenamento que será usado.
    - *Inspecione as configurações somente leitura*.
    - *Modificar*. Altere o tipo de autenticação e as credenciais. Os tipos de autenticação com suporte incluem chave de conta e token SAS.

## <a name="datasets"></a>Conjunto de dados

Atualmente, a extensão dá suporte aos seguintes tipos de conjunto de conjuntos:

- *Tabular*: permite que você materializa dados em um dataframe (pandas ou PySpark).
- *Arquivo*: um arquivo ou uma coleção de arquivos. Permite que você baixe ou monte arquivos em sua computação.

Para obter mais informações, consulte [conjuntos](concept-data.md#datasets) de dados

### <a name="create-dataset"></a>Criar conjunto de dados

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho em que você deseja criar o repositório de armazenamento.
1. Clique com o botão direito do mouse no nó **DataSets** e selecione **Create DataSet**.
1. No prompt:
    1. Escolher o tipo de conjunto de texto
    1. Defina se os dados estão localizados em seu computador ou na Web
    1. Forneça o local dos seus dados. Pode ser um único arquivo ou um diretório que contém os arquivos de dados.
    1. Escolha o repositório de dados para o qual você deseja carregar seus dados.
    1. Forneça um prefixo que ajuda a identificar seu conjunto de seus conjuntos de armazenamentos.

### <a name="version-datasets"></a>Conjuntos de itens de versão

Ao criar modelos de aprendizado de máquina, à medida que os dados são alterados, talvez você queira fazer a versão do conjunto. Para fazer isso na extensão de VS Code:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **conjuntos de valores** .
1. Clique com o botão direito do mouse no conjunto de um que você deseja versão e selecione **criar nova versão**.
1. No prompt:
    1. Selecionar o tipo de conjunto de texto
    1. Defina se os dados estão localizados em seu computador ou na Web.
    1. Forneça o local dos seus dados. Pode ser um único arquivo ou um diretório que contém os arquivos de dados.
    1. Escolha o repositório de dados para o qual você deseja carregar seus dados.
    1. Forneça um prefixo que ajuda a identificar seu conjunto de seus conjuntos de armazenamentos.

### <a name="view-dataset-properties"></a>Exibir Propriedades do conjunto de

Essa opção permite que você veja os metadados associados a um conjunto de informações específico. Para fazer isso na extensão de VS Code:

1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **conjuntos de valores** .
1. Clique com o botão direito do mouse no conjunto de um que você deseja inspecionar e selecione **Exibir Propriedades do conjunto** de Isso exibirá um arquivo de configuração com as propriedades da versão mais recente do conjunto de informações.

> [!NOTE]
> Se você tiver várias versões de seu conjunto de informações, poderá optar por exibir apenas as propriedades do conjunto de informações de uma versão específica expandindo o nó do conjunto de informações e executando as mesmas etapas descritas nesta seção sobre a versão de interesse.

### <a name="unregister-datasets"></a>Cancelar registro de conjuntos de os

Para remover um conjunto de registros e todas as versões dele, cancele seu registro. Para fazer isso na extensão de VS Code:

1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **conjuntos de valores** .
1. Clique com o botão direito do mouse no conjunto de registros que você deseja cancelar o registro e selecione **Cancelar registro do conjunto de registros**.

## <a name="environments"></a>Ambientes

Para obter mais informações, consulte [ambientes](concept-environments.md).

### <a name="create-environment"></a>Criar ambiente

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho em que você deseja criar o repositório de armazenamento.
1. Clique com o botão direito do mouse no nó **ambientes** e selecione **criar ambiente**.
1. No prompt:
    1. Forneça um nome para o seu ambiente
    1. Defina a configuração do seu ambiente:
        - *Ambientes organizados*: ambientes pré-configurados no Azure Machine Learning. Você pode personalizar ainda mais o ambiente modificando a `dependencies` propriedade no arquivo JSON. Saiba mais sobre os [ambientes organizados](resource-curated-environments.md).
        - *Arquivo de dependências Conda*: para ambientes Anaconda, o arquivo que contém a definição de ambiente pode ser fornecido.
        - *Arquivo de requisitos Pip*: para ambientes Pip, o arquivo que contém a definição de ambiente pode ser fornecido.
        - *Ambiente Conda existente*: essa opção procura os ambientes Conda no seu computador local e tenta criar um ambiente a partir do ambiente selecionado.
        - *Personalizado*: definir seus próprios canais e dependências
    1. Um arquivo de configuração é aberto no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="view-environment-configurations"></a>Exibir configurações de ambiente

Para exibir as dependências e as configurações de um ambiente específico na extensão:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **ambientes** .
1. Clique com o botão direito do mouse no ambiente que você deseja exibir e selecione **Exibir ambiente**.

### <a name="edit-environment-configurations"></a>Editar configurações de ambiente

Para editar as dependências e configurações de um ambiente específico na extensão:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **ambientes** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no ambiente que você deseja exibir e selecione **Editar ambiente**.
1. Depois de fazer as modificações, se você estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

## <a name="experiments"></a>Testes

Para obter mais informações, consulte [experimentos](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Criar um experimento

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito do mouse no nó **experimentos** em seu espaço de trabalho e selecione **criar experimento**.
1. No prompt, forneça um nome para o experimento.

### <a name="run-experiment"></a>Executar o experimento

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no experimento que você deseja executar.
1. Selecione o ícone **executar experimento** na barra de atividade.
1. Selecione se deseja executar seu experimento localmente ou remotamente. Consulte o [Guia de depuração](how-to-debug-visual-studio-code.md) para obter mais informações sobre a execução e a depuração de experimentos localmente.
1. Escolha sua assinatura.
1. Escolha o Workspace do ML do Azure para executar o experimento em.
1. Escolha seu experimento.
1. Escolha ou crie uma computação na qual executar o experimento.
1. Escolha ou crie uma configuração de execução para seu experimento.

Como alternativa, você pode selecionar o botão **executar experimento** na parte superior da extensão e configurar a execução do experimento no prompt.

### <a name="view-experiment"></a>Exibir experimento

Para exibir seu experimento no Azure Machine Learning Studio:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no experimento que você deseja exibir e selecione **Exibir experimento**. 
1. Um prompt é exibido solicitando que você abra a URL do experimento no Azure Machine Learning Studio. Selecione **Abrir**.

### <a name="track-run-progress"></a>Progresso da execução do rastreamento

Enquanto estiver executando seu experimento, talvez você queira ver seu progresso. Para acompanhar o progresso de uma execução no Azure Machine Learning Studio a partir da extensão:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro de seu espaço de trabalho.
1. Expanda o nó experimento para o qual você deseja acompanhar o progresso.
1. Clique com o botão direito do mouse na execução e selecione **Exibir executar em portal do Azure**.
1. Um prompt é exibido solicitando que você abra a URL de execução no Azure Machine Learning Studio. Selecione **Abrir**.

### <a name="download-run-logs--outputs"></a>Baixar logs de execução & saídas

Quando uma execução for concluída, talvez você queira baixar os logs e ativos, como o modelo gerado como parte de uma execução de experimento.

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro de seu espaço de trabalho.
1. Expanda o nó experimento para o qual você deseja acompanhar o progresso.
1. Clique com o botão direito do mouse na execução:
    - Para baixar as saídas, selecione **baixar saídas**.
    - Para baixar os logs, selecione **baixar logs**.

### <a name="view-run-metadata"></a>Exibir metadados de execução

Na extensão, você pode inspecionar metadados como a configuração de execução usada para a execução, bem como os detalhes de execução.

## <a name="compute-instances"></a>Instâncias de computação

Para obter mais informações, consulte [Compute instances](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Criar instância de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho em que você deseja criar a instância de computação.
1. Clique com o botão direito do mouse no nó **instâncias de computação** e selecione **criar instância de computação**.
1. No prompt:
    1. Forneça um nome para a instância de computação.
    1. Selecione um tamanho de VM na lista.
    1. Escolha se deseja habilitar o acesso SSH.
        1. Se você habilitar o acesso SSH, também terá que fornecer a chave SSH pública ou o arquivo que contém a chave. Para obter mais informações, consulte o [guia sobre como criar e usar chaves SSH no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>Parar ou reiniciar instância de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó da **instância de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na instância de computação que você deseja parar ou reiniciar e selecione **parar a instância de computação** ou reiniciar a instância de **computação** , respectivamente.

### <a name="view-compute-instance-configuration"></a>Exibir configuração da instância de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó da **instância de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na instância de computação que você deseja inspecionar e selecione **Exibir Propriedades da instância de computação**.

### <a name="delete-compute-instance"></a>Excluir instância de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó da **instância de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na instância de computação que você deseja excluir e selecione **excluir instância de computação**.

## <a name="compute-clusters"></a>Clusters de cálculo

A extensão dá suporte aos seguintes tipos de computação:

- Cluster de computação do Azure Machine Learning
- Serviço de Kubernetes do Azure

Para obter mais informações, consulte [destinos de computação](concept-compute-target.md#train).

### <a name="create-compute"></a>Criar computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho em que você deseja criar o cluster de computação.
1. Clique com o botão direito do mouse no nó **clusters de computação** e selecione **criar computação**.
1. No prompt:
    1. Escolher um tipo de computação
    1. Escolha um tamanho de VM. Saiba mais sobre [tamanhos de VM](../virtual-machines/sizes.md).
    1. Forneça um nome para a computação.

### <a name="view-compute-configuration"></a>Exibir configuração de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **clusters de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na computação que você deseja exibir e selecione **Exibir Propriedades de computação**.

### <a name="edit-compute-scale-settings"></a>Editar configurações de escala de computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **clusters de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na computação que você deseja editar e selecione **Editar computação**.
1. Um arquivo de configuração para a computação é aberto no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="delete-compute"></a>Excluir computação

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **clusters de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na computação que você deseja excluir e selecione **excluir computação**.

### <a name="create-run-configuration"></a>Criar configuração de execução

Para criar uma configuração de execução na extensão:

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **clusters de computação** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no destino de computação no qual você deseja criar a configuração de execução e selecione **criar configuração de execução**.
1. No prompt:
    1. Forneça um nome para seu destino de computação
    1. Escolha ou crie um novo ambiente.
    1. Digite o nome do script que você deseja executar ou pressione **Enter** para o navegador para o script no computador local.
    1. Adicional Escolha se deseja criar uma referência de dados para sua execução de treinamento. Isso solicitará que você defina um conjunto de um DataSet na sua configuração de execução.
        1. Selecione um dos seus conjuntos de arquivos registrados para vincular à configuração de execução um arquivo de configuração para o conjunto de seus DataSets é aberto no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.
    1. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="edit-run-configuration"></a>Editar configuração de execução

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do cluster de computação no nó **clusters de computação** do seu espaço de trabalho.
1. Clique com o botão direito do mouse na configuração de execução que você deseja editar e selecione **Editar configuração de execução**.
1. Um arquivo de configuração para sua configuração de execução é aberto no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="delete-run-configuration"></a>Excluir configuração de execução

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expanda o nó de cluster de computação de interesse dentro do nó **clusters de computação** .
1. Clique com o botão direito do mouse na configuração de execução que você deseja editar e selecione **Excluir configuração de execução**.

## <a name="models"></a>Modelos

Para obter mais informações, consulte [modelos](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Registrar modelo

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito do mouse no nó **modelos** e selecione **registrar modelo**.
1. No prompt:
    1. Forneça um nome para seu modelo
    1. Escolha se o modelo é um arquivo ou uma pasta.
    1. Localize o modelo em seu PC local.
    1. Um arquivo de configuração para seu modelo no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

### <a name="view-model-properties"></a>Exibir Propriedades do modelo

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **modelos** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no modelo cujas propriedades você deseja ver e selecione **Exibir Propriedades do modelo**. Um arquivo é aberto no editor que contém as propriedades do modelo.

### <a name="download-model"></a>Baixar modelo

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **modelos** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no modelo que você deseja baixar e selecione **baixar arquivo de modelo**.

### <a name="delete-a-model"></a>Excluir um modelo

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **modelos** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse no modelo que você deseja excluir e selecione **remover modelo**.

## <a name="endpoints"></a>Pontos de extremidade

A extensão de VS Code dá suporte aos seguintes destinos de implantação:

- Instâncias de Contêiner do Azure
- Serviço de Kubernetes do Azure

Para obter mais informações, consulte [pontos de extremidade de serviço Web](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Criar implantações

> [!NOTE]
> A criação da implantação atualmente funciona apenas com ambientes Conda.

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito do mouse no nó **pontos de extremidade** e selecione **implantar serviço**.
1. No prompt:
    1. Escolha se deseja usar um modelo já registrado ou um arquivo de modelo local.
    1. Selecione o modelo
    1. Escolha o destino de implantação no qual você deseja implantar seu modelo.
    1. Forneça um nome para seu modelo.
    1. Forneça o script a ser executado ao pontuar o modelo.
    1. Forneça um arquivo de dependências Conda.
    1. Um arquivo de configuração para sua implantação é exibido no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.

> [!NOTE]
> Como alternativa, você pode clicar com o botão direito do mouse em um modelo registrado no nó *modelos* e selecionar **implantar serviço do modelo registrado**.

### <a name="delete-deployments"></a>Excluir implantações

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **pontos de extremidade** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na implantação que você deseja remover e selecione **remover serviço**.
1. Um prompt é exibido confirmando que você deseja remover o serviço. Selecione **OK**.

### <a name="manage-deployments"></a>Gerenciar implantações

Além de criar e excluir implantações, você pode exibir e editar as configurações associadas à implantação.

1. Expanda o nó de assinatura que contém seu espaço de trabalho.
1. Expanda o nó **pontos de extremidade** dentro de seu espaço de trabalho.
1. Clique com o botão direito do mouse na implantação que você deseja gerenciar:
    - Para editar as configurações, selecione **Editar serviço**.
        - Um arquivo de configuração para sua implantação é exibido no editor. Se estiver satisfeito com sua configuração, selecione **salvar e continuar** ou abra a paleta de comandos vs Code (**Exibir > paleta de comandos**) e digite **Azure ml: salvar e continuar**.
    - Para exibir as definições de configuração de implantação, selecione **Exibir Propriedades do serviço**.

## <a name="next-steps"></a>Próximas etapas

[Treine um modelo de classificação de imagem](tutorial-train-deploy-image-classification-model-vscode.md) com a extensão vs Code.
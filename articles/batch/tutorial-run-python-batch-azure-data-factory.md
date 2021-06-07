---
title: Tutorial – Executar scripts do Python por meio do Data Factory
description: Saiba como executar scripts do Python como parte de um pipeline por meio do Azure Data Factory usando o Lote do Azure.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 241a47ccf9021c6065fea907b4d9914744a64972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461684"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Tutorial: Executar scripts do Python por meio de Azure Data Factory usando o Lote do Azure

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Autenticar com as contas do Lote e do Armazenamento
> * Desenvolver e executar um script em Python
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Agendar suas cargas de trabalho do Python
> * Monitorar seu pipeline de análise
> * Acessar seus arquivos de log

O exemplo a seguir executa um script do Python que recebe entrada CSV de um contêiner do Armazenamento de Blobs, executa um processo de manipulação de dados e grava a saída em um contêiner do Armazenamento de Blobs separado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma distribuição instalada do [Python](https://www.python.org/downloads/) para teste local.
* O pacote [azure-storage-blob](https://pypi.org/project/azure-storage-blob/) `pip`.
* O [conjunto de dados iris.csv](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv)
* Uma conta do Lote do Azure e uma conta de Armazenamento do Azure vinculada. Confira [Criar uma conta do Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e vincular contas de Lote a contas de armazenamento.
* Uma conta do Azure Data Factory. Confira [Criar um data factory](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) para obter mais informações sobre como criar um data factory por meio do portal do Azure.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Criar um pool do Lote usando o Batch Explorer

Nesta seção, você usará o Batch Explorer para criar o pool do Lote que será usado pelo pipeline do Azure Data Factory. 

1. Entre no Batch Explorer usando suas credenciais do Azure.
1. Selecione sua conta do Lote
1. Crie um pool selecionando **Pools** na barra do lado esquerdo e selecionando o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha uma ID e o nome de exibição. Usaremos `custom-activity-pool` para este exemplo.
    1. Defina o tipo de escala para **Tamanho fixo** e defina a contagem do nó dedicado para 2.
    1. Em **Ciência de dados**, selecione **Dsvm Windows** como o sistema operacional.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Habilite a tarefa de início e adicione o comando `cmd /c "pip install azure-storage-blob pandas"`. A identidade do usuário pode permanecer como o padrão do **Usuário do pool**.
    1. Selecione **OK**.

## <a name="create-blob-containers"></a>Criar contêineres de blob

Aqui você vai criar contêineres de blob que armazenarão arquivos de entrada e saída para o trabalho em lotes de OCR.

1. Entre no Gerenciador de Armazenamento usando suas credenciais do Azure.
1. Usando a conta de armazenamento vinculada à sua conta do Lote, crie dois contêineres de blob (um para arquivos de entrada, um para arquivos de saída) seguindo as etapas em [Criar um contêiner de blob](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * Neste exemplo, chamaremos nosso contêiner de entrada `input` e nosso contêiner de saída `output`.
1. Carregue [`iris.csv`](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv) no contêiner de entrada `input` usando o Gerenciador de Armazenamento, seguindo as etapas em [Gerenciar blobs em um contêiner de blobs](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)

## <a name="develop-a-script-in-python"></a>Desenvolver um script em Python

O script do Python a seguir carrega o conjunto de dados `iris.csv` do seu contêiner de `input`, executa um processo de manipulação de dados e salva os resultados no contêiner `output`.

``` python
# Load libraries
from azure.storage.blob import BlobClient
import pandas as pd

# Define parameters
connectionString = "<storage-account-connection-string>"
containerName = "output"
outputBlobName  = "iris_setosa.csv"

# Establish connection with the blob storage account
blob = BlobClient.from_connection_string(conn_str=connectionString, container_name=containerName, blob_name=outputBlobName)

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Take a subset of the records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv(outputBlobName, index = False)

with open(outputBlobName, "rb") as data:
    blob.upload_blob(data)
```

Salve o script como `main.py` e carregue-o para o contêiner **Armazenamento do Azure** `input`. Teste e valide a funcionalidade localmente antes de carregá-la para seu contêiner de blob:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Configurar um pipeline do Azure Data Factory

Nesta seção, você criará e validará um pipeline usando o script do Python.

1. Siga as etapas para criar um data factory na seção "Criar um data factory" [deste artigo](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. Na caixa **Recursos de Fábrica**, selecione o botão + (mais) e, em seguida, selecione **Pipeline**
1. Na guia **Geral**, defina o nome do pipeline como "Executar Python"

    ![Na guia Geral, defina o nome do pipeline como "Executar Python"](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Na caixa **Atividades**, expanda **Serviço em Lote**. Arraste a atividade personalizada da caixa de ferramentas **Atividades** para a superfície do designer do pipeline. Preencha as seguintes guias para a atividade personalizada:
    1. Na guia **Geral**, especifique **testPipeline** como Nome ![Na guia Geral, especifique testPipeline como Nome](./media/run-python-batch-azure-data-factory/create-custom-task.png)
    1. Na guia **Lote do Azure**, adicione a **Conta do Lote** criada nas etapas anteriores e **Teste a conexão** para garantir que ela seja bem-sucedida.
    ![Na guia Lote do Azure, adicione a Conta do Lote criada nas etapas anteriores, depois teste a conexão](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)
    1. Na guia **Configurações**:
        1. Defina o **Comando** como `python main.py`.
        1. Para o **Serviço Vinculado de Recurso**, adicione a conta de armazenamento que foi criada nas etapas anteriores. Teste a conexão para garantir que ela seja bem-sucedida.
        1. No **Caminho da Pasta**, selecione o nome do contêiner **Armazenamento de Blobs do Azure** que contém o script Python e as entradas associadas. Isso baixará os arquivos selecionados do contêiner para as instâncias de nó de pool antes da execução do script Python.

        ![No Caminho da Pasta, selecione o nome do contêiner de Armazenamento de Blobs do Azure](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)

1. Clique em **Validar** na barra de ferramentas do pipeline sobre a tela para validar as configurações de pipeline. Confirme se esse pipeline foi validado com êxito. Para fechar a saída de validação, selecione o botão &gt;&gt; (seta para a direita).
1. Clique em **Depurar** para testar o pipeline e garantir que ele funcione com precisão.
1. Clique em **Publicar** para publicar o pipeline.
1. Clique em **Gatilho** para executar o script Python como parte de um processo em lote.

    ![Clique em Gatilho para executar o script Python como parte de um processo em lote](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorar os arquivos de log

Caso os avisos ou erros sejam produzidos pela execução do seu script, você poderá conferir `stdout.txt` ou `stderr.txt` para obter mais informações sobre a saída que foi registrada.

1. Selecione **Trabalhos** do lado esquerdo do Batch Explorer.
1. Escolha o trabalho criado pelo seu data factory. Supondo que você tenha nomeado o pool `custom-activity-pool`, selecione `adfv2-custom-activity-pool`.
1. Clique na tarefa que tinha um código de saída de falha.
1. Veja `stdout.txt` e `stderr.txt` para investigar e diagnosticar o problema.

## <a name="clean-up-resources"></a>Limpar os recursos

Embora você não seja cobrado pelos trabalhos e pelas tarefas, será cobrado pelos nós de computação. Portanto, recomendamos que você aloque os pools conforme a necessidade. Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas. No entanto, os arquivos de entrada e saída permanecerão na conta de armazenamento. Quando não for mais necessário, você também poderá excluir a conta do Lote e a conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Autenticar com as contas do Lote e do Armazenamento
> * Desenvolver e executar um script em Python
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Agendar suas cargas de trabalho do Python
> * Monitorar seu pipeline de análise
> * Acessar seus arquivos de log

Para saber mais sobre o Azure Data Factory, confira:

> [!div class="nextstepaction"]
> [Visão geral do Azure Data Factory](../data-factory/introduction.md)

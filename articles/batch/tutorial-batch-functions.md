---
title: Disparar um trabalho em lotes usando o Azure Functions
description: Tutorial – Aplicar OCR a documentos digitalizados conforme eles são adicionados a um blob de armazenamento
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343076"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: Disparar um trabalho em lotes usando o Azure Functions

Neste tutorial, você aprenderá como disparar um trabalho em lotes usando o Azure Functions. Vamos examinar um exemplo no qual os documentos adicionados a um contêiner de blobs do Armazenamento do Microsoft Azure têm OCR (reconhecimento óptico de caracteres) aplicado a eles por meio do Lote do Azure. Para simplificar o processamento de OCR, configuraremos uma função do Azure que executa um trabalho OCR do Lote cada vez que um arquivo é adicionado ao contêiner de blob.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Lote do Azure e uma conta de Armazenamento do Azure vinculada. Confira [Criar uma conta do Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e vincular contas.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Criar um pool em lotes e trabalho em lotes usando o Batch Explorer

Nesta seção, você usará o Batch Explorer para criar o pool em lotes e o trabalho em lotes que executarão tarefas de OCR. 

### <a name="create-a-pool"></a>Criar um pool

1. Entre no Batch Explorer usando suas credenciais do Azure.
1. Crie um pool selecionando **Pools** na barra do lado esquerdo e selecionando o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha uma ID e o nome de exibição. Usaremos `ocr-pool` para este exemplo.
    1. Defina o tipo de escala para **Tamanho fixo** e defina a contagem do nó dedicado para 3.
    1. Selecione **Ubuntu 18.04-LTS** como o sistema operacional.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Habilite a tarefa de início e adicione o comando `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Defina a identidade do usuário como **Usuário padrão da tarefa (Admin)** , que permite que as tarefas de início incluam comandos com `sudo`.
    1. Selecione **OK**.
### <a name="create-a-job"></a>Criar um trabalho

1. Crie um trabalho no pool selecionando **Trabalhos** na barra do lado esquerdo e selecionando o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha uma ID e o nome de exibição. Usaremos `ocr-job` para este exemplo.
    1. Defina o pool como `ocr-pool`, ou qualquer nome que você queira para o pool.
    1. Selecione **OK**.


## <a name="create-blob-containers"></a>Criar contêineres de blob

Aqui você vai criar contêineres de blob que armazenarão arquivos de entrada e saída para o trabalho em lotes de OCR.

1. Entre no Gerenciador de Armazenamento usando suas credenciais do Azure.
1. Usando a conta de armazenamento vinculada à sua conta do Lote, crie dois contêineres de blob (um para arquivos de entrada, um para arquivos de saída) seguindo as etapas em [Criar um contêiner de blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

Neste exemplo, o contêiner de entrada é denominado `input` e é onde todos os documentos sem OCR inicialmente são carregados para processamento. O contêiner de saída é denominado `output` e é onde o trabalho em lotes grava documentos processados com o OCR.  
    * Neste exemplo, chamaremos nosso contêiner de entrada `input` e nosso contêiner de saída `output`.  
    * O contêiner de entrada é onde todos os documentos sem OCR são inicialmente carregados.  
    * O contêiner de saída é onde o trabalho em lotes grava documentos com OCR.  

Crie uma assinatura de acesso compartilhado para seu contêiner de saída no Gerenciador de Armazenamento. Faça isso clicando com o botão direito do mouse no contêiner de saída e selecionando **Obter Assinatura de Acesso Compartilhado...** . Em **Permissões**, marque **Gravar**. Não são necessárias outras permissões.  

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Nesta seção, você criará a Função do Azure, que dispara o trabalho em lotes de OCR sempre que um arquivo é carregado no seu contêiner de entrada.

1. Siga as etapas em [Criar uma função disparada pelo armazenamento de blob do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) para criar uma função.
    1. Quando uma conta de armazenamento for solicitada, use a mesma conta de armazenamento que você vinculou à sua conta do Lote.
    1. Para **pilha de tempo de execução**, escolha .NET. Gravaremos nossa função em C# para aproveitar o SDK do .NET em Lote.
1. Depois de criar a função disparada pelo blob, use o [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) e [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) do GitHub, na Função.
    * `run.csx` é executado quando um novo blob é adicionado ao seu contêiner de blob de entrada.
    * `function.proj` lista as bibliotecas externas no código da Função, por exemplo, o SDK do .NET em Lote.
1. Altere os valores de espaço reservado das variáveis na função `Run()` do arquivo `run.csx` para refletir suas credenciais de Lote e armazenamento. Você pode encontrar as credenciais da conta de armazenamento e do Lote no portal do Azure, na seção **Chaves** da sua conta do Lote.
    * Recupere as credenciais da conta de armazenamento e do Lote no portal do Azure, na seção **Chaves** da sua conta do Lote. 

## <a name="trigger-the-function-and-retrieve-results"></a>Disparar a função e recuperar resultados

Carregue um ou todos os arquivos digitalizados do diretório [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) no GitHub para seu contêiner de entrada. Monitore o Batch Explorer para confirmar que uma tarefa foi adicionada a `ocr-pool` para cada arquivo. Depois de alguns segundos, o arquivo com OCR aplicado é adicionado ao contêiner de saída. O arquivo se torna visível e pode ser recuperado no Gerenciador de Armazenamento.

Além disso, é possível inspecionar o arquivo de logs na parte inferior da janela do editor Web do Azure Functions, onde você verá mensagens como esta para cada arquivo que carregou no contêiner de entrada:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Para baixar os arquivos de saída do Gerenciador de Armazenamento em seu computador local, primeiramente selecione os arquivos que deseja e, em seguida, selecione **Baixar** na faixa de opções superior. 

> [!TIP]
> Os arquivos baixados poderão ser pesquisados se abertos em um leitor de PDF.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a: 

> [!div class="checklist"]
> * Usar o Batch Explorer para criar pools e trabalhos
> * Usar o Gerenciador de Armazenamento para criar contêineres de blob e uma SAS (assinatura de acesso compartilhado)
> * Criar uma função do Azure disparada por blob
> * Carregar arquivos de entrada no armazenamento
> * Monitorar a execução de tarefas
> * Recuperar arquivos de saída

* Para obter mais exemplos de como usar a API do .NET para agendar e processar cargas de trabalho do Lote, confira [os exemplos no GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Para ver mais gatilhos do Azure Functions que você pode usar para executar cargas de trabalho do Lote, confira [a documentação do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).

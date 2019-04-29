---
title: Criar uma tarefa de observador na conta de Automação do Azure
description: Saiba como criar uma tarefa de observador na conta de Automação do Azure para inspecionar novos arquivos criados em uma pasta.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304569"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar tarefas de observador de Automação do Azure para controlar alterações de arquivo em um computador local

A Automação do Azure usa tarefas de observador para cuidar de eventos e disparar ações com runbooks do PowerShell. Este tutorial orienta na criação de uma tarefa de observador para monitorar quando um novo arquivo é adicionado a um diretório.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar um runbook observador
> * Criar uma variável da Automação
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Disparar um observador
> * Inspecionar a saída

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter os runbooks de observador e de ação e a tarefa do observador.
* Um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) no qual a tarefa do observador é executada.

> [!NOTE]
> Tarefas do Inspetor não têm suporte no Azure China.

## <a name="import-a-watcher-runbook"></a>Importar um runbook observador

Este tutorial usa um runbook observador chamado **Watch-NewFile** para procurar novos arquivos em um diretório. O runbook observador recupera a última hora de gravação conhecido dos arquivos de uma pasta e examina todos os arquivos que sejam mais recentes do que essa marca-d'água. Nesta etapa, você importará esse runbook na sua conta de automação.

1. Abra a sua conta da Automação e clique na página **Runbooks**.
2. Clique no botão **Procurar na galeria**.
3. Pesquise "Runbook observador", selecione **Runbook observador que procura novos arquivos em um diretório** e selecione **Importar**.
  ![Importar o runbook de automação por meio da interface do usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dê um nome e uma descrição ao runbook e selecione **OK** para importar o runbook na sua conta da Automação.
1. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, selecione **Sim** para publicar o runbook.

## <a name="create-an-automation-variable"></a>Criar uma variável da Automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de data/hora que o runbook anterior lê e armazena de cada arquivo.

1. Selecione **Variáveis** em **RECURSOS COMPARTILHADOS** e selecione **+ Adicionar uma variável**.
1. Digite "Watch-NewFileTimestamp" para o nome
1. Selecione a DateTime para o Tipo.
1. Clique no botão **Criar**. Isso cria a variável de automação.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é usado em uma tarefa de observador para realizar ação nos dados passados para ela, provenientes de um runbook observador. Runbooks de Fluxo de Trabalho do PowerShell não são compatíveis com tarefas de observador, você deve usar os runbooks do PowerShell. Nesta etapa, você atualiza a importação de um runbook de ação predefinido chamado "Process-NewFile".

1. Navegue até a sua conta de automação e selecione **Runbooks** na categoria **AUTOMAÇÃO DE PROCESSO**.
1. Clique no botão **Procurar na galeria**.
1. Pesquise "Ação de observador" e selecione **Ação de observador que processa eventos disparados por um runbook observador** e selecione **Importar**.
  ![Importar runbook de ação por meio da interface do usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê um nome e uma descrição ao runbook e selecione **OK** para importar o runbook na sua conta da Automação.
1. Selecione **Editar** e, em seguida, clique em **Publicar**. Quando solicitado, selecione **Sim** para publicar o runbook.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

A tarefa de observador de contém duas partes. O observador e a ação. O observador é executado em um intervalo definido na tarefa de observador. Os dados do runbook observador são passados para o runbook de ação. Nesta etapa, você configura a tarefa de observador, referenciando os runbooks de ação e observador definidos nas etapas anteriores.

1. Navegue até a sua conta de automação e selecione **Tarefas de observador** na categoria **AUTOMAÇÃO DE PROCESSO**.
1. Selecione a página Tarefas do observador e clique no botão **+ Adicionar uma tarefa de observador**.
1. Digite "WatchMyFolder" como nome.

1. Selecione **Configurar observador** e selecione o runbook **Watch-NewFile**.

1. Insira os seguintes valores para os parâmetros:

   * **FOLDERPATH** – uma pasta no Hybrid Worker em que os novos arquivos são criados. d:\examplefiles
   * **EXTENSION** – deixe em branco para processar todas as extensões de arquivo.
   * **RECURSE** – deixe este valor como o padrão.
   * **RUN SETTINGS** – escolha o Hybrid Worker.

1. Clique em OK e selecione Voltar à página do observador.
1. Selecione **Configurar ação** e o runbook "Process-NewFile".
1. Digite os seguintes valores para os parâmetros:

   * **EVENTDATA** – deixe em branco. Os dados são passados do runbook de observador.  
   * **Run Settings** – deixe como Azure, pois este runbook é executado no serviço da Automação.

1. Clique em **OK** e, sem seguida, em Selecionar para voltar à página do observador.
1. Clique em **OK** para criar a tarefa de observador.

![Configurar ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Disparar um observador

Para testar se o observador está funcionando conforme o esperado, você precisa criar um arquivo de teste.

Deixe remoto para o Hybrid Worker. Abra o **PowerShell** e crie um arquivo de teste na pasta.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

O exemplo a seguir mostra a saída esperada.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspecionar a saída

1. Navegue até a sua conta de automação e selecione **Tarefas de observador** na categoria **AUTOMAÇÃO DE PROCESSO**.
1. Selecione a tarefa de observador "WatchMyFolder".
1. Clique em **Exibir fluxos do observador** em **Fluxos** para ver que o observador encontrou o novo arquivo e iniciou o runbook de ação.
1. Para ver os trabalhos do runbook de ação, clique em **Exibir trabalhos de ação do observador**. Cada trabalho pode ser selecionado para exibir os respectivos detalhes.

   ![Trabalhos de ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/WatcherActionJobs.png)

A saída esperada, quando o novo arquivo é encontrado, pode ser vista no exemplo a seguir:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Importar um runbook observador
> * Criar uma variável da Automação
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Disparar um observador
> * Inspecionar a saída

Siga este link para saber mais sobre como criar seu próprio runbook.

> [!div class="nextstepaction"]
> [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).


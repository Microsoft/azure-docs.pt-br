---
title: Início rápido do Azure - Executar trabalho do Lote - Portal
description: Aprenda rapidamente a executar um trabalho do Lote com o portal do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 33455a95dff946676e3dfffbd2737ed36be59c64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322357"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Início Rápido: Executar o primeiro trabalho em Lote no portal do Azure

Este guia de início rápido mostra como usar o portal do Azure para criar uma conta do Lote, um *pool* de nós de computação (máquinas virtuais) e um *trabalho* que executa *tarefas* básicas no pool. Depois de concluir este guia de início rápido, você entenderá os conceitos principais do serviço Lote e estará pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Siga estas etapas para criar uma conta do Lote de exemplo para fins de teste. Você precisa de uma conta do Lote para criar pools e trabalhos. Conforme mostrado aqui, você pode vincular uma conta de armazenamento do Azure à conta do lote. Embora não seja necessário para este guia de início rápido, a conta de armazenamento é útil para implantar aplicativos e armazenar dados de entrada e saída para a maioria das cargas de trabalho da vida real.


1. Selecione **Criar um recurso** >  **Computação** > **Serviço de lote**. 

   ![Lote no Marketplace][marketplace_portal]

2. Insira valores para **Nome do registro** e **Grupo de recursos**. O nome da conta deve ser exclusivo dentro da **Localização** do Azure selecionada; use apenas caracteres em minúsculas ou números e 3 a 24 caracteres. 

3. Na **Conta de armazenamento**, selecione uma conta de armazenamento existente ou crie uma nova.

4. Mantenha os padrões para as configurações restantes e selecione **Criar** para criar a conta.

   ![Criar uma conta do Batch][account_portal]  

Quando a mensagem **Implantação bem-sucedida** for exibida, vá para a conta do Lote no portal.

## <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Agora que você tem uma conta do Lote, crie um pool de exemplo de nós de computação do Windows para fins de teste. O pool para este exemplo rápido consiste em dois nós executando uma imagem do Windows Server 2012 R2 no Azure Marketplace.


1. Na conta do Lote, selecione **Pools** > **Adicionar**.

2. Insira um **ID do Pool** chamado *mypool*. 

3. Em **Sistema Operacional**, selecione as configurações a seguir (você pode explorar outras opções).
  
   |Configuração  |Valor  |
   |---------|---------|
   |**Tipo de imagem**|Marketplace (Linux/Windows)|
   |**Publicador**     |MicrosoftWindowsServer|
   |**Oferta**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![Selecione um sistema operacional de pool][pool_os] 

4. Role para baixo para inserir as configurações de **Tamanho de Nó** e **Escala**. O tamanho de nó sugerido oferece um bom equilíbrio entre desempenho e custo para este exemplo rápido.
  
   |Configuração  |Valor  |
   |---------|---------|
   |**Tipo de preço do nó**     |Standard_A1|
   |**Nós dedicados de destino**     |2|

   ![Selecione um tamanho de pool][pool_size] 

5. Mantenha os padrões para as configurações restantes e selecione **OK** para criar o pool.

O Lote cria o pool imediatamente, mas leva alguns minutos para alocar e iniciar os nós de computação. Durante esse tempo, o **Estado de alocação** do pool é **Resizing**. Você pode prosseguir e criar trabalho e tarefas enquanto o pool está redimensionando. 

![Pool no estado Resizing][pool_resizing]

Depois de alguns minutos, o estado do pool é **Constante** e os nós são iniciados. Selecione **Nós** para verificar o estado dos nós. Quando o estado do nó é **Ocioso**, ele está pronto para executar tarefas. 

## <a name="create-a-job"></a>Criar um trabalho

Agora que você tem um pool, crie um trabalho para executar nele. Um trabalho do Lote é um grupo lógico para uma ou mais tarefas. Um trabalho inclui configurações comuns às tarefas, como prioridade e o pool onde elas devem ser executadas. Inicialmente, o trabalho não tem nenhuma tarefa. 

1. No modo de exibição de conta do Lote, selecione **Trabalhos** > **Adicionar**. 

2. Insira uma **ID do trabalho** chamada *myjob*. Em **Pool**, selecione *mypool*. Mantenha os padrões para as configurações restantes e clique em **OK**.

   ![Criar um trabalho][job_create]

Depois que o trabalho é criado, a página **Tarefas** é aberta.

## <a name="create-tasks"></a>Criar tarefas

Agora, crie tarefas de exemplo para executar o trabalho. Normalmente, você cria várias tarefas que o Lote enfileira e distribui para execução em nós de computação. Neste exemplo, você criará duas tarefas idênticas. Cada tarefa executa uma linha de comando para exibir as variáveis de ambiente do Lote em um nó de computação e, em seguida, aguardar 90 segundos. 

Quando você usa o Lote, a linha de comando é onde você especifica seu aplicativo ou script. O Lote fornece várias maneiras para implantar aplicativos e scripts em nós de computação. 

Para criar a primeira tarefa:

1. Selecione **Adicionar**.

2. Insira uma **ID da tarefa** chamada *mytask*. 

3. Na **Linha de comando**, digite `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Mantenha os padrões para as configurações restantes e clique em **OK**.

   ![Cria uma tarefa][task_create]

Depois de criar uma tarefa, o Lote a enfileira para ser executada no pool. Quando um nó fica disponível para executá-lo, a tarefa é executada.

Para criar uma segunda tarefa, volte para a etapa 1. Insira outra **ID da tarefa**, mas especifique uma linha de comando idêntica. Se a primeira tarefa ainda está em execução, o Lote começa a segunda tarefa no outro nó no pool.

## <a name="view-task-output"></a>Exibir saída da tarefa

Os exemplos de tarefa anteriores são concluídos em alguns minutos. Para exibir a saída de uma tarefa concluída, selecione **Arquivos no nó**e selecione o arquivo `stdout.txt`. Esse arquivo mostra a saída padrão da tarefa. O conteúdo é semelhante ao seguinte:

![Exibir saída da tarefa][task_output]

O conteúdo mostra as variáveis de ambiente do Lote do Azure que são definidas no nó. Ao criar seus próprios trabalhos e tarefas do Lote, você pode consultar essas variáveis de ambiente nas linhas de comando da tarefa e nos aplicativos e scripts executados pelas linhas de comando.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar com exemplos e tutoriais do Lote, use a conta do Lote e a conta de armazenamento vinculada criada neste guia de início rápido. Não há nenhum encargo pela conta do Lote.

Você é cobrado pelo pool enquanto os nós estão em execução, mesmo se não há trabalhos agendados. Quando você não precisa mais do pool, exclua-o. Na exibição da conta, selecione **Pools** e o nome do pool. Em seguida, selecione **Excluir**.  Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas. 

Quando não for mais necessário, exclua o grupo de recursos, a conta do Lote e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a conta do Lote e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta do Lote, um pool do Lote e um trabalho do Lote. O trabalho executou tarefas de exemplo e você exibiu a saída criada em um dos nós. Agora que você conhece os conceitos principais do serviço Lote, está pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala. Para saber mais sobre o Lote do Azure, prossiga para os tutoriais do Lote do Azure. 

> [!div class="nextstepaction"]
> [Tutoriais do Lote do Azure](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png
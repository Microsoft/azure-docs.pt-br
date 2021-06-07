---
title: Guia de Início Rápido do Azure – Executar seu primeiro trabalho do Lote no portal do Azure
description: Este guia de início rápido mostra como usar o portal do Azure para criar uma conta do Lote, um pool de nós de computação e um trabalho que executa tarefas básicas no pool.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538539"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Início rápido: executar o primeiro trabalho do Lote no portal do Azure

Comece a usar o Lote do Azure com o portal do Azure para criar uma conta do Lote, um pool de nós de computação (máquinas virtuais) e um trabalho que executa tarefas no pool. Depois de concluir este guia de início rápido, você entenderá os principais conceitos do serviço Lote e estará pronto para experimentá-lo com cargas de trabalho mais realistas em maior escala.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Siga estas etapas para criar uma conta do Lote de exemplo para fins de teste. Você precisa de uma conta do Lote para criar pools e trabalhos. Conforme mostrado aqui, você pode vincular uma conta de armazenamento do Azure à conta do lote. Embora não seja necessário para este guia de início rápido, a conta de armazenamento é útil para implantar aplicativos e armazenar dados de entrada e saída para a maioria das cargas de trabalho da vida real.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Computação** > **Serviço Lote**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Captura de tela do Serviço Lote no Azure Marketplace.":::

1. No campo **Grupo de recursos**, selecione **Criar** e insira um nome para o grupo de recursos.

1. Insira um valor em **Nome da conta**. Esse nome precisa ser exclusivo na **Localização** do Azure selecionada. Ele só pode conter letras minúsculas e números e precisa ter entre 3 e 24 caracteres.

1. Em **Conta de armazenamento**, selecione uma conta de armazenamento existente ou crie uma.

1. Não altere as outras configurações. Selecione **Examinar + criar** e **Criar** para criar a conta do Lote.

Quando a mensagem **Implantação bem-sucedida** for exibida, acesse a conta do Lote criada.

## <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Agora que você tem uma conta do Lote, crie um pool de exemplo de nós de computação do Windows para fins de teste. O pool deste exemplo rápido consiste em dois nós que executam uma imagem do Windows Server 2019 do Azure Marketplace.

1. Na conta do Lote, selecione **Pools** > **Adicionar**.

1. Insira um **ID do Pool** chamado *mypool*.

1. Em **Sistema Operacional**, selecione as configurações a seguir (você pode explorar outras opções).
  
   |Configuração  |Valor  |
   |---------|---------|
   |**Tipo de imagem**|Marketplace|
   |**Publicador**     |microsoftwindowsserver|
   |**Oferta**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. Role para baixo para inserir as configurações de **Tamanho de Nó** e **Escala**. O tamanho de nó sugerido oferece um bom equilíbrio entre desempenho e custo para este exemplo rápido.
  
   |Configuração  |Valor  |
   |---------|---------|
   |**Tipo de preço do nó**     |Standard A1|
   |**Nós dedicados de destino**     |2|

1. Mantenha os padrões para as configurações restantes e selecione **OK** para criar o pool.

O Lote cria o pool imediatamente, mas leva alguns minutos para alocar e iniciar os nós de computação. Durante esse tempo, o **Estado de alocação** do pool é **Resizing**. Você pode prosseguir e criar trabalho e tarefas enquanto o pool está redimensionando.

Após alguns minutos, o estado de alocação mudará para **Constante** e os nós serão iniciados. Para verificar o estado dos nós, selecione o pool e escolha **Nós**. Quando o estado do nó é **Ocioso**, ele está pronto para executar tarefas.

## <a name="create-a-job"></a>Criar um trabalho

Agora que você tem um pool, crie um trabalho para executar nele. Um Trabalho em lotes é um grupo lógico de uma ou mais tarefas. Um trabalho inclui configurações comuns às tarefas, como prioridade e o pool onde elas devem ser executadas. Inicialmente, o trabalho não tem nenhuma tarefa.

1. No modo de exibição de conta do Lote, selecione **Trabalhos** > **Adicionar**.

1. Insira uma **ID do trabalho** chamada *myjob*. Em **Pool**, selecione *mypool*. Mantenha os padrões para as configurações restantes e clique em **OK**.

## <a name="create-tasks"></a>Criar tarefas

Agora, selecione o trabalho para abrir a página **Tarefas**. É nela que você criará tarefas de exemplo que serão executadas no trabalho. Normalmente, você cria várias tarefas que o Lote coloca na fila e distribui para execução nos nós de computação. Neste exemplo, você criará duas tarefas idênticas. Cada tarefa executa uma linha de comando para exibir as variáveis de ambiente do Lote em um nó de computação e, em seguida, aguardar 90 segundos.

Quando você usa o Lote, a linha de comando é onde você especifica seu aplicativo ou script. O Lote fornece várias maneiras para implantar aplicativos e scripts em nós de computação.

Para criar a primeira tarefa:

1. Selecione **Adicionar**.

1. Insira uma **ID da tarefa** chamada *mytask*.

1. Na **Linha de comando**, digite `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Mantenha os padrões para as configurações restantes e selecione **Enviar**.

Depois de criar uma tarefa, o Lote a enfileira para ser executada no pool. Quando um nó fica disponível para executá-lo, a tarefa é executada.

Para criar uma segunda tarefa, repita as etapas acima. Insira outra **ID da tarefa**, mas especifique uma linha de comando idêntica. Se a primeira tarefa ainda está em execução, o Lote começa a segunda tarefa no outro nó no pool.

## <a name="view-task-output"></a>Exibir saída da tarefa

As tarefas de exemplo que você criou serão concluídas em alguns minutos. Para ver a saída de uma tarefa concluída, selecione a tarefa e **Arquivos no nó**. Selecione o arquivo `stdout.txt` para ver a saída padrão da tarefa. O conteúdo é semelhante ao seguinte:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Captura de tela da saída de uma tarefa concluída.":::

O conteúdo mostra as variáveis de ambiente do Lote do Azure que são definidas no nó. Ao criar seus próprios trabalhos e tarefas do Lote, você pode consultar essas variáveis de ambiente nas linhas de comando da tarefa e nos aplicativos e scripts executados pelas linhas de comando.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser continuar com exemplos e tutoriais do Lote, use a conta do Lote e a conta de armazenamento vinculada criada neste guia de início rápido. Não há nenhum encargo pela conta do Lote.

Você é cobrado pelo pool enquanto os nós estão em execução, mesmo se não há trabalhos agendados. Quando você não precisa mais do pool, exclua-o. Na exibição da conta, selecione **Pools** e o nome do pool. Em seguida, selecione **Excluir**.  Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas.

Quando não for mais necessário, exclua o grupo de recursos, a conta do Lote e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a conta do Lote e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta do Lote, um pool do Lote e um trabalho do Lote. O trabalho executou tarefas de exemplo e você exibiu a saída criada em um dos nós. Agora que você conhece os conceitos principais do serviço Lote, está pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala. Para saber mais sobre o Lote do Azure, prossiga para os tutoriais do Lote do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Lote do Azure](./tutorial-parallel-dotnet.md)

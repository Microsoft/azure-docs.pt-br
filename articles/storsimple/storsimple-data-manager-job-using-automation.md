---
title: Usar a Automação do Azure para inicializar um trabalho no Gerenciador de Dados StorSimple | Microsoft Docs
description: Saiba como usar a Automação do Azure para disparar trabalhos do Gerenciador de Dados do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633446"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Usar a Automação do Azure para disparar um trabalho

Este artigo explica como você pode usar o recurso de transformação de dados dentro do serviço do Gerenciador de Dados StorSimple para transformar os dados do dispositivo StorSimple. Você pode inicializar um trabalho de transformação de dados de duas maneiras: 

 - Usar o SDK .NET
 - Usar o Runbook de Automação do Azure
 
Este artigo fornece detalhes sobre como criar um runbook de Automação do Azure e, em seguida, usá-lo para iniciar um trabalho de transformação de dados. Para saber mais sobre como iniciar a transformação de dados por meio do SDK do .NET, acesse [Usar o SDK do .NET para acionar os trabalhos de transformação de dados](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

*   Microsoft Azure PowerShell instalado no computador do cliente. [Fazer o download do Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Uma definição de trabalho configurada corretamente em um serviço do Gerenciador de Dados StorSimple dentro de um grupo de recursos.
*   Fazer o download do arquivo  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) no repositório GitHub. 
*   Fazer o download do script [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) no repositório GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

### <a name="set-up-the-automation-account"></a>Configurar a conta de Automação

1. Crie uma conta de automação Executar Como do Azure no Portal do Azure. Para fazer isso, vá para **Marketplace do Azure > Tudo** e, em seguida, pesquise **Automação**. Selecione **Contas de automação**.

    ![Criar a conta de Automação Executar como](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para adicionar uma nova conta de Automação, clique em **+ Adicionar**.

    ![Criar a conta de Automação Executar como](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Em **Adicionar Automação**:

   1. Forneça o **Nome** da sua Conta de Automação.
   2. Selecione a **Assinatura** vinculada ao seu serviço de Gerenciador de Dados StorSimple.
   3. Crie um novo grupo de recursos ou selecione um existente.
   4. Selecione um **Local**.
   5. Deixe a opção **Criar Conta Executar Como** padrão selecionada.
   6. Para obter um link de acesso rápido no painel, verifique **Fixar no painel**. Clique em **Criar**.

      ![Criar a conta de Automação Executar como](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Depois de a conta de automação ser criada com sucesso, você será notificado.
    
      ![Notificação para implantação da conta de Automação](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para obter mais informações, acesse [Criar uma conta Executar como](../automation/automation-create-runas-account.md).

3. Na conta recém-criada, vá para **Recursos Compartilhados > Módulos** e clique em **+ Adicionar módulo**.

    ![Importar módulo 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navegue até o local do arquivo `DataTransformationApp.zip` no seu computador local e selecione e abra o módulo. Clique em **OK** para importar o módulo.

    ![Importar módulo 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando a Automação do Azure importa um módulo para sua conta, ele extrai os metadados sobre o módulo. Essa operação pode levar alguns minutos.

   ![Importar módulo 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Você recebe uma notificação informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.  O status nos **Módulos** é alterado para **Disponível**.

    ![Importar módulo 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook de automação

Execute as etapas a seguir para importar, publicar e executar o runbook para disparar a definição de trabalho.

1. No portal do Azure, abra sua conta da Automação. Vá para **Automação de Processos > Runbooks** e clique em **+ Adicionar um runbook**.

    ![Adicionar Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Em **Adicionar runbook**, clique em **Importar um runbook existente**.

3. Aponte para o arquivo de script `Trigger-DataTransformation-Job.ps1` do Azure PowerShell para o **Arquivo de Runbook**. O tipo de runbook é selecionado automaticamente. Forneça um nome e uma descrição opcional para o runbook. Clique em **Criar**.

    ![Adicionar Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. O novo runbook é exibido na lista de runbooks da conta de Automação. Selecione e clique neste runbook.

    ![Adicionar Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edite o runbook e clique em painel de **Teste**.

    ![Adicionar Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Forneça os parâmetros, como o nome do seu serviço do Gerenciador de Dados StorSimple, o grupo de recursos associados e o nome da definição de trabalho. **Inicie** o teste. O relatório é gerado quando a execução é concluída. Para obter mais informações, acesse Como [testar um runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Adicionar Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Inspecione a saída do runbook no painel de teste. Se estiver satisfeito, feche o painel. Clique em **Publicar** e, quando a confirmação for solicitada, confirme e publique o runbook.

    ![Adicionar Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Volte para **Runbooks** e selecione o runbook recém-criado.

    ![Adicionar Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Iniciar** o runbook. Em **Iniciar runbook**, insira todos os parâmetros. Clique em **OK** para enviar e iniciar o trabalho de Transformação de Dados.

10. Para monitorar o andamento do trabalho no portal do Azure, vá para **Trabalhos** no seu serviço do Gerenciador de Dados StorSimple. Selecione e clique no trabalho para exibir os detalhes do trabalho.

    ![Adicionar Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).
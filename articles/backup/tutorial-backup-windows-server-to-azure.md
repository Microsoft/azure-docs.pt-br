---
title: Fazer backup do Windows Server para o Azure
description: Este tutorial oferece detalhes de como fazer backups do Windows Server local para um Cofre dos Serviços de Recuperação.
author: dcurwin
manager: carmonm
keywords: backup do windows server; fazer backup do windows server; backup e recuperação de desastre
ms.service: backup
ms.topic: tutorial
ms.date: 8/22/2018
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 0a8a4795cf35402ebecbf6838aaeb5f17768aa06
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467045"
---
# <a name="back-up-windows-server-to-azure"></a>Fazer backup do Windows Server para o Azure


Você pode usar o Backup do Azure para proteger seu Windows Server de corrupções, ataques e desastres. O Backup do Azure fornece uma ferramenta simples conhecida como Agente MARS (Serviços de Recuperação do Microsoft Azure). O Agente MARS está instalado no Windows Server para proteger arquivos, pastas e as informações de configuração do servidor por meio do estado do sistema do Windows Server. Este tutorial explica como você pode usar o Agente MARS para fazer backup do seu Windows Server no Azure. Neste tutorial, você aprenderá a: 


> [!div class="checklist"]
> * Baixar e configurar o Agente MARS
> * Configurar horários de backup e cronograma de retenção para os backups do servidor
> * Executar um backup ad hoc


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Antes que possa fazer backup do Windows Server, você deverá criar um local para que os backups ou os pontos de restauração, sejam armazenados. Um [Cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner do Azure que armazena os backups do Windows Server. Siga as etapas abaixo para criar um Cofre dos Serviços de Recuperação no Portal do Azure. 

1. No menu à esquerda, selecione **Todos os serviços** e na lista de serviços, digite **Serviços de Recuperação**. Clique em **Cofres dos Serviços de Recuperação**.

   ![Abra o cofre dos Serviços de Recuperação](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. No menu **Cofres de Serviços de Recuperação**, clique em **Adicionar**.

   ![Forneça informações para o cofre](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. No menu **Cofre dos Serviços de Recuperação**,

    - Digite *myRecoveryServicesVault* em **Nome**.
    - A ID da assinatura atual aparecerá em **Assinatura**.
    - Em **Grupo de recursos**, selecione **Usar existente** e selecione *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Criar novo** e digite *myResourceGroup*. 
    - No menu suspenso **Localização**, escolha *Europa Ocidental*.
    - Clique em **Criar** para criar seu Cofre dos Serviços de Recuperação.
 
Depois que o cofre é criado, ele aparece na lista de cofres dos Serviços de Recuperação.

## <a name="download-recovery-services-agent"></a>Baixe o agente dos Serviços de Recuperação

O Agente MARS (Serviços de Recuperação do Microsoft Azure) cria uma associação entre o Windows Server e o seu Cofre dos Serviços de Recuperação. O procedimento a seguir explica como baixar o agente em seu servidor.

1. Na lista de Cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir seu painel.

   ![Forneça informações para o cofre](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. No menu do painel do cofre, clique em **Backup**.

3. No menu **Meta de Backup**:

   * em **Onde sua carga de trabalho é executada?** , selecione **Localmente**, 
   * Em **Do que você deseja fazer backup?** , selecione **Arquivos e pastas** e **Estado do Sistema**

   ![Forneça informações para o cofre](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Clique em **Preparar Infraestrutura** para abrir o menu **Preparar Infraestrutura**.

5. No menu **Preparar infraestrutura**, clique em **Baixar agente do Windows Server ou do Windows Client** para baixar o *MARSAgentInstaller.exe*. 

    ![Preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    O instalador abrirá um navegador separado e baixará o **MARSAgentInstaller.exe**.
 
6. Antes de executar o arquivo baixado, no menu de Preparar infraestrutura, clique em **Baixar** e salve o arquivo **Credenciais do Cofre**. As credenciais do cofre são necessárias para conectar o Agente MARS com o cofre dos Serviços de Recuperação.

    ![Preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

1. Localize e clique duas vezes no **MARSagentinstaller.exe** baixado.
2. O **Assistente de Instalação do Agente de Serviços de Recuperação do Microsoft Azure** aparecerá. Conforme avançar no assistente, forneça as informações a seguir quando solicitado e clique em **Registrar**.
   - Local para a instalação e a pasta de cache.
   - Informações do servidor proxy, se você usar um servidor proxy para se conectar à Internet.
   - Seus detalhes de nome de usuário e senha, se você usar um proxy autenticado.

     ![Preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. No final do assistente, clique em **Prosseguir com o Registro** e forneça o arquivo **Credenciais do Cofre** baixado no procedimento anterior.
 
4. Quando solicitado, forneça uma frase secreta de criptografia para criptografar os backups pelo Windows Server. Salve a frase secreta em um local seguro, pois a Microsoft não poderá recuperá-la se ela for perdida.

5. Clique em **Concluir**. 

## <a name="configure-backup-and-retention"></a>Configurar Backup e Retenção

Você pode usar o Agente de Serviços de Recuperação do Microsoft Azure para agendar quando os backups do Azure ocorrerão no Windows Server. Execute as etapas a seguir no servidor em que você baixou o agente.

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

2.  No console do agente dos Serviços de Recuperação, clique em **Agendar Backup** no **Painel de Ações**.

    ![Preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Clique em **Avançar** para navegar até a página **Selecionar itens para fazer backup**.

4. Clique em **Adicionar Itens** e, na caixa de diálogo que é aberta, selecione **Estado do Sistema** e as pastas ou os arquivos dos quais você deseja fazer backup. Em seguida, clique em **OK**.

5. Clique em **Próximo**.

6. Na página **Especificar Agendamento de Backup (Estado do Sistema)** , especifique a hora do dia ou da semana e que os backups precisam ser disparados para o Estado do Sistema e clique em **Avançar**.

7. Na página **Selecionar Política de Retenção (Estado do Sistema)** , selecione a Política de Retenção para a cópia de backup do Estado do Sistema e clique em **Avançar**.

8. Da mesma forma, selecione o agendamento de backup e a política de retenção para as pastas e os arquivos selecionados. 

9. Na página **Escolher Tipo Inicial de Backup**, selecione **Automaticamente pela rede** e clique em **Avançar**.

10. Na página **Confirmação**, examine as informações e clique em **Concluir**.

11. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

## <a name="perform-an-ad-hoc-back-up"></a>Executar um backup ad hoc

Você estabeleceu a agenda de quando os trabalhos de backup serão executados. No entanto, você não fez backup do servidor. Uma melhor prática de recuperação de desastre é executar um backup sob demanda para garantir a resiliência de dados para o servidor.

1.  No console do Agente de Serviços de Recuperação do Microsoft Azure, clique em **Fazer backup agora**.

    ![Preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  No assistente **Fazer Backup Agora**, selecione um dos **Arquivos e Pastas** ou o **Estado do Sistema** que você deseja fazer backup e clique em **Avançar** 
3. Na página **Confirmação**, examine as configurações que o assistente **Fazer Backup Agora** usa para fazer backup do servidor. Em seguida, clique em **Fazer Backup**.
4.  Clique em **Fechar** para fechar o assistente. Se você fechar o assistente antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.
4.  Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no painel **Trabalhos** do console do Agente MARS.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o portal do Azure para: 
 
> [!div class="checklist"] 
> * Criar um cofre dos Serviços de Recuperação 
> * Baixar o Agente de Serviços de Recuperação do Microsoft Azure 
> * Instalar o agente 
> * Configurar o backup para o Windows Server 
> * Executar um backup sob demanda 

Continue no próximo tutorial para recuperar arquivos do Azure para o Windows Server

> [!div class="nextstepaction"] 
> [Restaurar arquivos do Azure para o Windows Server](./tutorial-backup-restore-files-windows-server.md) 


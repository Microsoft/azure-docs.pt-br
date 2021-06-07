---
title: Fazer backup de arquivos em VMs Azure Stack
description: Use o Backup do Azure para fazer backup e recuperar aplicativos e arquivos no ambiente do Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: cae95c10c510969cc0553a54a506789d6be427d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180976"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fazer backup de arquivos e aplicativos no Azure Stack

É possível usar o Backup do Azure para proteger (ou fazer backup) de arquivos e aplicativos no Azure Stack. Para fazer backup de arquivos e aplicativos, instale o Servidor de Backup do Microsoft Azure como uma máquina virtual em execução no Azure Stack. Você pode proteger os arquivos no servidor do Azure Stack na mesma rede virtual. Depois de instalar o Servidor de Backup do Azure, adicione discos do Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. O Servidor de Backup do Azure usa o armazenamento do Azure para retenção de longo prazo.

> [!NOTE]
> Embora Servidor de Backup do Azure e o System Center Data Protection Manager (DPM) sejam semelhantes, o DPM não tem suporte para uso com Azure Stack.
>

Este artigo não aborda a instalação de Servidor de Backup do Azure no ambiente de Azure Stack. Para instalar o Servidor de Backup do Azure no Azure Stack, confira o artigo [Instalar o Servidor de Backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Realize o backup dos Arquivos e Pastas nas VMs do Azure Stack para o Azure

Para configurar o Servidor de Backup do Azure para proteger máquinas virtuais das máquinas virtuais do Azure Stack, abra o console do Servidor de Backup do Azure. Você usará o console para configurar grupos de proteção e para proteger os dados em suas máquinas virtuais.

1. No console do Servidor de Backup do Azure, selecione **proteção** e, na barra de ferramentas, selecione **novo** para abrir o assistente para **criar novo grupo de proteção** .

   ![Configurar a proteção no console do Servidor de Backup do Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode levar alguns segundos para abrir o assistente. Quando o assistente for aberto, selecione **Avançar** para ir para a tela **Selecionar tipo de grupo de proteção** .

   ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na tela **Selecionar tipo de grupo de proteção** , escolha **servidores** e selecione **Avançar**.

    ![Selecionar tipo de grupo de proteção](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    A tela **Selecionar Membros do Grupo** se abre.

    ![Selecionar Membros do Grupo](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na tela **selecionar membros do grupo** , selecione **+** para expandir a lista de subitens. Para todos os itens que você deseja proteger, selecione a caixa de seleção. Depois que todos os itens tiverem sido selecionados, selecione **Avançar**.

    ![Selecione cada item que você deseja proteger](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda colocar todos os dados que compartilharão uma política de proteção em um grupo de proteção. Para obter informações completas sobre planejamento e implantação de grupos de proteção, consulte o artigo do System Center DPM, [Implantar Grupos de Proteção](/system-center/dpm/create-dpm-protection-groups).

4. Na tela **Selecionar Método de Proteção de Dados**, digite um nome para o grupo de proteção. Selecione a caixa de seleção para **Desejo proteção a curto prazo usando:** e **Desejo proteção online**. Selecione **Avançar**.

    ![Selecionar Método de Proteção de Dados](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **Desejo proteção online**, primeiro você deve selecionar **Desejo proteção a curto prazo using:** Disk. O Servidor de Backup do Azure não protege a fita, portanto o disco é a única opção para proteção de curto prazo.

5. Na tela **Especificar Objetivos De Curto Prazo**, escolha por quanto tempo manter os pontos de recuperação salvos em disco e quando salvar backups incrementais. Selecione **Avançar**.

    > [!IMPORTANT]
    > **Não** retenha os dados de recuperação operacional (backup) nos discos anexados ao Servidor de Backup do Azure por mais de cinco dias.
    >

    ![Especificar Objetivos de Curto Prazo](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Em vez de selecionar um intervalo para backups incrementais, para executar um backup completo expresso antes de cada ponto de recuperação agendado, selecione **logo antes de um ponto de recuperação**. Se você estiver protegendo cargas de trabalho do aplicativo, o Servidor de Backup do Azure criará pontos de recuperação de acordo com a agenda de frequência de Sincronização (desde que o aplicativo dê suporte a backups incrementais). Se o aplicativo não oferece suporte a backups incrementais, o Servidor de Backup do Azure executa um backup completo expresso.

    Para **Pontos de recuperação de arquivo**, especifique quando criar pontos de recuperação. Selecione **Modificar** para definir as horas e os dias da semana em que os pontos de recuperação são criados.

6. Na tela **Examinar alocação do disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

    **Tamanho total dos dados** é o tamanho dos dados de que você deseja fazer backup e **Espaço em disco a ser provisionado** no Servidor de Backup do Azure é o espaço recomendado para o grupo de proteção. O Servidor de Backup do Azure escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup nos Detalhes de alocação de disco. Para as cargas de trabalho, selecione o armazenamento preferido no menu suspenso. Suas edições alteram os valores de Armazenamento Total e Armazenamento Gratuito no painel de Armazenamento em Disco Disponível. Espaço sem provisionamento é a quantidade de armazenamento que o Servidor de Backup do Azure recomenda que você adicione ao volume, para garantir backups estáveis no futuro.

7. Em **Escolher método de criação de réplica**, selecione como você deseja controlar a replicação inicial de dados completo. Caso decida replicar pela rede, o Azure recomenda que você escolha um horário de menos movimento. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados usando mídia removível.

8. Em **Escolher opções de verificação de consistência**, selecione como você deseja automatizar as verificações de consistência. Habilite verificações de consistência para serem executadas apenas quando a replicação de dados se tornar inconsistente, ou de acordo com uma agenda. Se você não deseja configurar a verificação de consistência automática, execute uma verificação manual a qualquer momento deste modo:
    * Na área **Proteção** do console do Servidor de Backup do Azure, clique com o botão direito do mouse no grupo de proteção e selecione **Executar Verificação de Consistência**.

9. Se optar por fazer backup para o Azure, na página **Especificar dados de proteção online**, verifique se as cargas de trabalho das quais você deseja fazer backup para o Azure estão selecionadas.

10. Em **Especificar agendamento de backup online**, selecione quando os backups incrementais para o Azure devem ocorrer.

    Você pode agendar backups para executar a cada dia/semana/mês/ano e também a data/hora em que eles devem ser executados. Os backups poderão ocorrer até duas vezes por dia. Cada vez que um trabalho de backup é executado, um ponto de recuperação de dados é criado no Azure da cópia dos dados de backup armazenados no disco do Servidor de Backup do Azure.

11. Em **Especificar política de retenção online**, especifique como os pontos de recuperação criados dos backups diários, semanais, mensais e anuais são mantidos no Azure.

12. Em **Escolher Replicação Online**, especifique como ocorre a replicação inicial completa de dados.

13. Em **Resumo**, examine as configurações. Quando você seleciona **Criar grupo**, ocorre a replicação de dados inicial. Quando a replicação de dados termina, na página **Status**, o status do grupo de proteção é mostrado como **OK**. O trabalho de backup inicial ocorre em linha com as configurações do grupo de proteção.

## <a name="recover-file-data"></a>Recuperar dados de arquivo

Use o console do Servidor de Backup do Azure para recuperar dados para sua máquina virtual.

1. No console do Servidor de Backup do Azure, na barra de navegação, selecione **recuperação** e procure os dados que você deseja recuperar. No painel Resultados, selecione os dados.

2. No calendário da seção de pontos de recuperação, as datas em negrito indicam que os pontos de recuperação estão disponíveis. Selecione a data para recuperar.

3. No painel **Item recuperável**, selecione o item que você deseja recuperar.

4. No painel **ações** , selecione **recuperar** para abrir o assistente de recuperação.

5. Você pode recuperar os dados da seguinte maneira:

    * **Recuperar para o local original** – se o computador cliente estiver conectado através de VPN, essa opção não funcionará. Em vez disso, use um local alternativo e, em seguida, copie os dados desse local.
    * **Recuperar em um local alternativo**

6. Especifique as opções de recuperação:

    * Para **Comportamento de recuperação da versão existente**, selecione **Criar cópia**, **Ignorar** ou **Substituir**. Substituir está disponível somente ao recuperar para o local original.
    * Para **Restaurar segurança**, escolha **Aplicar as configurações do computador de destino** ou **Aplicar as configurações de segurança da versão do ponto de recuperação**.
    * Para **limitação do uso de largura de banda**, selecione **Modificar** para habilitar a limitação do uso de largura de banda da rede.
    * **Notificação** do Selecione **enviar um email quando a recuperação for concluída** e especifique os destinatários que receberão a notificação. Separe os endereços de email por vírgulas.
    * Depois de fazer as seleções, selecione **Avançar**

7. Examine as configurações de recuperação e selecione **recuperar**.

    >[!Note]
    >Enquanto o trabalho de recuperação está em andamento, todos os trabalhos de sincronização para os itens de recuperação selecionados são cancelados.

Se você está usando o MBS (Armazenamento de Backup Moderno), a EUR (recuperação do usuário final) do servidor de arquivos não é compatível. A EUR do servidor de arquivos tem uma dependência no VSS (Serviço de Cópias de Sombra de Volume), que não é usado pelo Armazenamento de Backup Moderno. Se a EUR estiver habilitada, use as etapas a seguir para recuperar dados:

1. Navegue até os arquivos protegidos, clique com o botão direito do mouse no nome do arquivo e selecione **Propriedades**.

2. No menu **Propriedades** , selecione **versões anteriores** e escolha a versão que você deseja recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Visualizar o Servidor de Backup do Azure com um cofre

Para exibir Servidor de Backup do Azure entidades no portal do Azure, você pode seguir as seguintes etapas:

1. Abra o cofre dos Serviços de Recuperação.
2. Selecione infraestrutura de backup.
3. Exiba a lista de Servidores de Gerenciamento de Backup.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o Servidor de Backup do Azure para proteger outras cargas de trabalho, confira um dos seguintes artigos:

* [Fazer backup do farm do SharePoint](./backup-mabs-sharepoint-azure-stack.md)
* [Fazer backup do SQL Server](./backup-mabs-sql-azure-stack.md)

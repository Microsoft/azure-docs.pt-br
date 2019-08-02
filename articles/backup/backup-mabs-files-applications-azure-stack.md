---
title: Fazer backup nas VMs do Microsoft Azure Stack
description: Use o Backup do Azure para fazer backup e recuperar aplicativos e arquivos no ambiente do Azure Stack.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 36e7fceb63e6013ca09e8c2e2db18d6795f61273
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465090"
---
# <a name="back-up-files-on-azure-stack"></a>Fazer backup no Microsoft Azure Stack
É possível usar o Backup do Azure para proteger (ou fazer backup) de arquivos e aplicativos no Azure Stack. Para fazer backup de arquivos e aplicativos, instale o Servidor de Backup do Microsoft Azure como uma máquina virtual em execução no Azure Stack. Você pode proteger os arquivos no servidor do Azure Stack na mesma rede virtual. Após instalar o Servidor de Backup do Azure, adicione os discos do Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. O Servidor de Backup do Azure usa o armazenamento do Azure para retenção de longo prazo.

> [!NOTE]
> Embora o Servidor de Backup do Azure e o DPM (System Center Data Protection Manager) sejam semelhantes, o DPM não é compatível com o Azure Stack.
>

Este artigo não aborda a instalação de Servidor de Backup do Azure no ambiente do Azure Stack. Para instalar o Servidor de Backup do Azure no Azure Stack, confira o artigo [Instalar o Servidor de Backup do Azure](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Realize o backup dos Arquivos e Pastas nas VMs do Azure Stack para o Azure

Para configurar o Servidor de Backup do Azure para proteger máquinas virtuais das máquinas virtuais do Azure Stack, abra o console do Servidor de Backup do Azure. Você usará o console para configurar grupos de proteção e para proteger os dados em suas máquinas virtuais.

1. No console do Servidor de Backup do Azure, clique em **Proteção** e, na barra de ferramentas, clique em **Novo** para abrir o assistente **Criar Novo Grupo de Proteção**.

   ![Configurar a proteção no console do Servidor de Backup do Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode levar alguns segundos para abrir o assistente. Após o assistente abrir, clique em **Avançar** para ir para a tela **Selecionar tipo do grupo de proteção**.

   ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na tela **Selecionar Tipo do Grupo de Proteção**, escolha **Servidores** e clique em **Próximo**.

    ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    A tela **Selecionar Membros do Grupo** se abre. 

    ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na tela **Selecionar Membros do Grupo**, clique em **+** para expandir a lista de subitens. Para todos os itens que você deseja proteger, selecione a caixa de seleção. Depois que todos os itens tiverem sido selecionados, clique em **Próximo**.

    ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda colocar todos os dados que compartilharão uma política de proteção em um grupo de proteção. Para obter informações completas sobre planejamento e implantação de grupos de proteção, consulte o artigo do System Center DPM, [Implantar Grupos de Proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na tela **Selecionar Método de Proteção de Dados**, digite um nome para o grupo de proteção. Selecione a caixa de seleção para **Desejo proteção a curto prazo usando:** e **Desejo proteção online**. Clique em **Avançar**.

    ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **Desejo proteção online**, primeiro você deve selecionar **Desejo proteção a curto prazo usando:** Disk. O Servidor de Backup do Azure não protege em fita, então o disco é a única opção de proteção a curto prazo.

5. Na tela **Especificar Objetivos De Curto Prazo**, escolha por quanto tempo manter os pontos de recuperação salvos em disco e quando salvar backups incrementais. Clique em **Avançar**.

    > [!IMPORTANT]
    > **Não** retenha os dados de recuperação operacional (backup) nos discos anexados ao Servidor de Backup do Azure por mais de cinco dias.
    >

    ![O assistente de Novo Grupo de Proteção se abre](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Em vez de selecionar um intervalo para backups incrementais, para executar um backup completo expresso logo antes de cada ponto de recuperação agendado, clique em **Logo antes de um ponto de recuperação**. Se você estiver protegendo cargas de trabalho do aplicativo, o Servidor de Backup do Azure criará pontos de recuperação de acordo com a agenda de frequência de Sincronização (desde que o aplicativo dê suporte a backups incrementais). Se o aplicativo não oferece suporte a backups incrementais, o Servidor de Backup do Azure executa um backup completo expresso.

    Para **Pontos de recuperação de arquivo**, especifique quando criar pontos de recuperação. Clique em **Modificar** para definir os horários e dias da semana em que pontos de recuperação são criados.

6. Na tela **Examinar alocação do disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

    **Tamanho total dos dados** é o tamanho dos dados de que você deseja fazer backup e **Espaço em disco a ser provisionado** no Servidor de Backup do Azure é o espaço recomendado para o grupo de proteção. O Servidor de Backup do Azure escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup nos Detalhes de alocação de disco. Para as cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. Suas edições alteram os valores de Armazenamento Total e Armazenamento Gratuito no painel de Armazenamento em Disco Disponível. Espaço sem provisionamento é a quantidade de armazenamento que o Servidor de Backup do Azure recomenda que você adicione ao volume, para garantir backups estáveis no futuro.

7. Em **Escolher método de criação de réplica**, selecione como você deseja controlar a replicação inicial de dados completo. Caso decida replicar pela rede, o Azure recomenda que você escolha um horário de menos movimento. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados usando mídia removível.

8. Em **Opções de verificação de consistência**, selecione como e quando automatizar as verificações de consistência. Habilite verificações de consistência para serem executadas apenas quando a replicação de dados se tornar inconsistente, ou de acordo com uma agenda. Se você não deseja configurar a verificação de consistência automática, execute uma verificação manual a qualquer momento deste modo:
    * Na área **Proteção** do console do Servidor de Backup do Azure, clique com o botão direito do mouse no grupo de proteção e selecione **Executar Verificação de Consistência**.

9. Se optar por fazer backup para o Azure, na página **Especificar dados de proteção online**, verifique se as cargas de trabalho das quais você deseja fazer backup para o Azure estão selecionadas.

10. Em **Especificar agendamento de backup online**, selecione quando os backups incrementais para o Azure devem ocorrer. 

    Você pode agendar backups para executar a cada dia/semana/mês/ano e também a data/hora em que eles devem ser executados. Backups podem ocorrer até duas vezes por dia. Cada vez que um trabalho de backup é executado, um ponto de recuperação de dados é criado no Azure da cópia dos dados de backup armazenados no disco do Servidor de Backup do Azure.

11. Em **Especificar política de retenção online**, especifique como os pontos de recuperação criados dos backups diários, semanais, mensais e anuais são mantidos no Azure.

12. Em **Escolher Replicação Online**, especifique como ocorre a replicação inicial completa de dados. 

13. Em **Resumo**, examine as configurações. Quando você clica em **Criar Grupo**, ocorre a replicação inicial de dados. Quando a replicação de dados termina, na página **Status**, o status do grupo de proteção é mostrado como **OK**. O trabalho de backup inicial ocorre em linha com as configurações do grupo de proteção.

## <a name="recover-file-data"></a>Recuperar dados de arquivo

Use o console do Servidor de Backup do Azure para recuperar dados para sua máquina virtual.

1. No console do Servidor de Backup do Azure, na barra de navegação, clique em **Recuperação** e procure os dados que você deseja recuperar. No painel de resultados, selecione os dados.

2. No calendário da seção de pontos de recuperação, as datas em negrito indicam que os pontos de recuperação estão disponíveis. Selecione a data para recuperar.

3. No painel **Item recuperável**, selecione o item que você deseja recuperar.

4. No painel **Ações**, clique em **Recuperar** para abrir o Assistente de Recuperação.

5. Você pode recuperar dados da seguinte maneira:

    * **Recuperar para o local original** – se o computador cliente estiver conectado através de VPN, essa opção não funcionará. Em vez disso, use um local alternativo e, em seguida, copie os dados desse local.
    * **Recuperar para um local alternativo**

6. Especifique as opções de recuperação:

    * Para **Comportamento de recuperação da versão existente**, selecione **Criar cópia**, **Ignorar** ou **Substituir**. Substituir está disponível somente ao recuperar para o local original.
    * Para **Restaurar segurança**, escolha **Aplicar as configurações do computador de destino** ou **Aplicar as configurações de segurança da versão do ponto de recuperação**.
    * Para **Limitação do uso da largura de banda de rede**, clique em **Modificar** para habilitar a limitação do uso da largura de banda de rede.
    * **Notificação** Clique em **Enviar um email ao concluir a recuperação** e especifique os destinatários que receberão a notificação. Separe os endereços de email com vírgulas.
    * Depois de fazer as seleções, clique em **Avançar**

7. Examine as configurações de recuperação e, em seguida, clique em **Recuperar**. 

    > [!Note] 
    > Enquanto o trabalho de recuperação está em andamento, todos os trabalhos de sincronização para os itens de recuperação selecionados são cancelados.
    >

Se você está usando o MBS (Armazenamento de Backup Moderno), a EUR (recuperação do usuário final) do servidor de arquivos não é compatível. A EUR do servidor de arquivos tem uma dependência no VSS (Serviço de Cópias de Sombra de Volume), que não é usado pelo Armazenamento de Backup Moderno. Se a EUR estiver habilitada, use as etapas a seguir para recuperar dados:

1. Navegue até os arquivos protegidos, clique com o botão direito do mouse no nome do arquivo e selecione **Propriedades**.

2. No menu **Propriedades**, clique em **Versões Anteriores** e escolha a versão que você deseja recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Visualizar o Servidor de Backup do Azure com um cofre
Para visualizar as entidades do Servidor de Backup do Azure no Portal do Azure, você pode seguir as seguintes etapas:
1. Abra o cofre dos Serviços de Recuperação.
2. Clique em Infraestrutura de Backup.
3. Exiba a lista de Servidores de Gerenciamento de Backup.

## <a name="see-also"></a>Consulte também
Para saber mais sobre como usar o Servidor de Backup do Azure para proteger outras cargas de trabalho, confira um dos seguintes artigos:
- [Fazer backup do farm do SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Fazer backup do SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)

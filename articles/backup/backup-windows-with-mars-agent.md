---
title: Fazer backup de computadores Windows usando o agente MARS
description: Use o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup de computadores Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702761"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Fazer backup de arquivos e pastas do Windows Server no Azure

Este artigo explica como fazer backup de computadores Windows usando o serviço de [backup do Azure](backup-overview.md) e o agente de serviços de recuperação do Microsoft Azure (MARS). O MARS também é conhecido como o agente de backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Verificar os pré-requisitos
> * Crie uma política e um agendamento de backup.
> * Execute um backup sob demanda.

## <a name="before-you-start"></a>Antes de começar

* Saiba como [o backup do Azure usa o agente Mars para fazer backup de computadores Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Saiba mais sobre a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente Mars em um servidor mAbs ou de Data Protection Manager secundário.
* Examine [o que tem suporte e o que você pode fazer backup](backup-support-matrix-mars-agent.md) pelo agente Mars.
* [Verifique o acesso à Internet](install-mars-agent.md#verify-internet-access) nos computadores que você deseja fazer backup.
* Se o agente MARS não estiver instalado, saiba como instalá-lo [aqui](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Criar uma política de backup

A política de backup especifica quando tirar instantâneos dos dados para criar pontos de recuperação. Ele também especifica por quanto tempo manter os pontos de recuperação. Você usa o agente MARS para configurar uma política de backup.

O Backup do Azure não leva em conta automaticamente o horário de verão. Esse padrão pode causar alguma discrepância entre a hora real e a hora em que o backup foi agendado.

Para criar uma política de backup:

1. Depois de baixar e registrar o agente MARS, abra o console do Agent. Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.  

1. Em **Ações**, selecione **Agendar backup**.

    ![Agendar um backup do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. No assistente de agendamento de backup, selecione **Guia de introdução**  >  **Avançar**.
1. Em **Selecionar itens a incluir no backup**, escolha **Adicionar itens**.

    ![Adicionar itens para fazer backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Na caixa **Selecionar itens**, escolha os itens a incluir no backup e selecione **OK**.

    ![Selecionar itens para fazer backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na página **Selecionar itens a incluir no backup**, escolha **Avançar**.
1. Na página **especificar agendamento de backup** , especifique quando fazer backups diários ou semanais. Em seguida, selecione **Avançar**.

    * Um ponto de recuperação é criado quando um backup é realizado.
    * O número de pontos de recuperação criados no ambiente depende da agenda de backups.
    * Você pode agendar até três backups por dia. No exemplo a seguir, dois backups diários ocorrem, um à meia-noite e um às 6:00 PM.

        ![Configurar um agendamento de backup diário](./media/backup-configure-vault/day-schedule.png)

    * Você também pode executar backups semanais. No exemplo a seguir, os backups são feitos em todos os domingos e quarta-feiras alternativos às 9:30 AM e 1:00.

        ![Configurar um agendamento de backup semanal](./media/backup-configure-vault/week-schedule.png)

1. Na página **selecionar política de retenção** , especifique como armazenar cópias históricas de seus dados. Em seguida, selecione **Avançar**.

    * As configurações de retenção especificam quais pontos de recuperação serão mantidos no armazenamento e por quanto tempo.
    * Para uma configuração de retenção diária, você indica que no momento especificado para a retenção diária, o último ponto de recuperação será retido para o número de dias especificado. Ou você pode especificar uma política de retenção mensal para indicar que o ponto de recuperação criado no dia 30 de cada mês deve ser armazenado por 12 meses.
    * A retenção para pontos de recuperação diários e semanais geralmente coincide com o agendamento de backup. Assim, quando o agendamento dispara um backup, o ponto de recuperação que o backup cria é armazenado pela duração que a política de retenção diária ou semanal especifica.
    * No exemplo a seguir:

        * Os backups diários à meia-noite e 6:00 PM são mantidos por sete dias.
        * Os backups feitos em um sábado à meia-noite e 6:00 PM são mantidos por quatro semanas.
        * Os backups feitos no último sábado do mês à meia-noite e 6:00 PM são mantidos por 12 meses.
        * Os backups feitos no último sábado em março são mantidos por 10 anos.

        ![Exemplo de uma política de retenção](./media/backup-configure-vault/retention-example.png)

1. Na página **escolher tipo de backup inicial** , decida se deseja fazer o backup inicial pela rede ou usar o backup offline. Para fazer o backup inicial pela rede, selecione **automaticamente na rede**  >  **Avançar**.

    Para obter mais informações sobre o backup offline, consulte [usar Azure data box para backup offline](offline-backup-azure-data-box.md).

    ![Escolher um tipo de backup inicial](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na página **confirmação** , examine as informações e selecione **concluir**.

    ![Confirmar o tipo de backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Depois que o assistente terminar de criar o agendamento de backup, escolha **Fechar**.

    ![Exibir o progresso do agendamento de backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Crie uma política em cada computador em que o agente está instalado.

### <a name="do-the-initial-backup-offline"></a>Fazer o backup inicial offline

Você pode executar um backup inicial automaticamente pela rede ou pode fazer backup offline. A propagação offline para um backup inicial será útil se você tiver grandes quantidades de dados que exigirão muita largura de banda de rede para transferência.

Para fazer uma transferência offline:

1. Grave os dados de backup em um local de preparo.
1. Use a ferramenta AzureOfflineBackupDiskPrep para copiar os dados do local de preparo para um ou mais discos SATA.

    A ferramenta cria um trabalho de importação do Azure. Para obter mais informações, consulte [o que é o serviço de importação/exportação do Azure](../import-export/storage-import-export-service.md).
1. Envie os discos SATA para um datacenter do Azure.

    No datacenter, os dados do disco são copiados para uma conta de armazenamento do Azure. O backup do Azure copia os dados da conta de armazenamento para o cofre e os backups incrementais são agendados.

Para obter mais informações sobre a propagação offline, consulte [usar Azure data box para backup offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Habilitar a limitação de rede

Você pode controlar como o agente MARS usa a largura de banda de rede habilitando a limitação de rede. A limitação será útil se você precisar fazer backup de dados durante o horário de trabalho, mas quiser controlar a quantidade de largura de banda que a atividade de backup e restauração usa.

A limitação de rede no backup do Azure usa [QoS (qualidade de serviço)](/windows-server/networking/technologies/qos/qos-policy-top) no sistema operacional local.

A limitação de rede para backups está disponível no Windows Server 2012 e posterior e no Windows 8 e posterior. Os sistemas operacionais devem estar executando os service packs mais recentes.

Para habilitar a limitação de rede:

1. No agente MARS, selecione **alterar propriedades**.
1. Na guia **limitação** , selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**.

    ![Configurar a limitação de rede para operações de backup](./media/backup-configure-vault/throttling-dialog.png)
1. Especifique a largura de banda permitida durante as horas de trabalho e as horas não úteis. Os valores de largura de banda começam em 512 kbps e vão até 1.023 Mbps. Depois, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

1. No agente de MARS, selecione **Fazer backup agora**.

    ![Fazer backup agora no Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se a versão do agente de MARS for 2.0.9169.0 ou mais recente, você poderá definir uma data de retenção personalizada. Na seção **Manter backup até**, escolha uma data no calendário.

   ![Usar o calendário para personalizar uma data de retenção](./media/backup-configure-vault/mars-ondemand.png)

1. Na página **confirmação** , examine as configurações e selecione **fazer backup**.
1. Selecione **Fechar** para fechar o assistente. Se você fechar o assistente antes de o backup ser concluído, o assistente continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status do **trabalho concluído** será exibido no console de backup.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Configurar o comportamento de retenção da política de backup sob demanda

> [!NOTE]
> Essas informações se aplicam somente às versões do agente MARS anteriores ao 2.0.9169.0.
>

| Opção backup-Schedule | Duração da retenção de dados
| -- | --
| Dia | **Retenção padrão**: equivalente a "retenção em dias para backups diários". <br/><br/> **Exceção**: se um backup agendado diário definido para retenção de longo prazo (semanas, meses ou anos) falhar, um backup sob demanda disparado logo após a falha é considerado para retenção de longo prazo. Caso contrário, o próximo backup agendado será considerado para retenção de longo prazo.<br/><br/> **Cenário de exemplo**: falha no backup agendado na quinta-feira às 8:00. Esse backup foi considerado para retenção semanal, mensal ou anual. Portanto, o primeiro backup sob demanda disparado antes do próximo backup agendado na sexta-feira às 8:00 é marcado automaticamente para retenção semanal, mensal ou anual. Este backup substitui o backup da quinta-feira 8:00.
| Semana | **Retenção padrão**: um dia. Os backups sob demanda que são feitos para uma fonte de dados que tem uma política de backup semanal são excluídos no dia seguinte. Eles são excluídos mesmo que sejam os backups mais recentes para a fonte de dados. <br/><br/> **Exceção**: se um backup semanal agendado definido para retenção de longo prazo (semanas, meses ou anos) falhar, um backup sob demanda disparado logo após a falha é considerado para retenção de longo prazo. Caso contrário, o próximo backup agendado será considerado para retenção de longo prazo. <br/><br/> **Cenário de exemplo**: falha no backup agendado na quinta-feira às 8:00. Esse backup foi considerado para retenção mensal ou anual. Portanto, o primeiro backup sob demanda que é disparado antes do próximo backup agendado na quinta-feira às 8:00 AM é automaticamente marcado para retenção mensal ou anual. Este backup substitui o backup da quinta-feira 8:00.

Para obter mais informações, consulte [criar uma política de backup](#create-a-backup-policy).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar arquivos no Azure](backup-azure-restore-windows-server.md).
* Encontre [perguntas comuns sobre como fazer backup de arquivos e pastas](backup-azure-file-folder-backup-faq.md)

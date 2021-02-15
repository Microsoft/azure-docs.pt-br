---
title: Fazer backup do Exchange Server com o Servidor de Backup do Azure
description: Saiba como fazer backup de um servidor do Exchange no Backup do Azure usando o Servidor de Backup do Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 86389ef89ced74905db0fc8bfeaabcea6140762a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262987"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Fazer backup de um servidor do Exchange no Azure com o Servidor de Backup do Azure

Este artigo descreve como configurar o Servidor de Backup do Microsoft Azure (MABS) para fazer backup de um servidor do Microsoft Exchange no Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se o Servidor de Backup do Azure está [instalado e preparado](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente de proteção do MABS

Execute estas etapas para instalar o agente de proteção do MABS no servidor do Exchange:

1. Verifique se os firewalls estão configurados corretamente. Confira [Configurar exceções de firewall do agente](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Instale o agente no Exchange Server selecionando **Management > agents > instalar** no mAbs console do administrador. Confira [Instalar o agente de proteção do MABS](/system-center/dpm/deploy-dpm-protection-agent) para conhecer as etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção do Exchange server

1. No Console do Administrador MABS, selecione **proteção**e, em seguida, selecione **novo** na faixa de opções da ferramenta para abrir o assistente para **criar novo grupo de proteção** .
2. Na tela de **boas-vindas** do assistente, selecione **Avançar**.
3. Na tela **Selecionar tipo de grupo de proteção** , selecione **servidores** e selecione **Avançar**.
4. Selecione o banco de dados do Exchange Server que você deseja proteger e selecione **Avançar**.

   > [!NOTE]
   > Se você estiver protegendo o Exchange 2013, verifique os [pré-requisitos do Exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    No exemplo abaixo, o banco de dados do Exchange 2010 foi selecionado.

    ![Selecionar membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Dê um nome ao grupo de proteção e escolha as duas opções a seguir:

   * Desejo a proteção de curto prazo usando Disco.
   * Desejo a proteção online.
6. Selecione **Avançar**.
7. Escolha a opção **Executar Eseutil para verificar a integridade dos dados** se você quiser verificar a integridade dos bancos de dados do Exchange Server.

    Depois de selecionar essa opção, a verificação de consistência do backup será executada em MABS para evitar o tráfego de e/s gerado com a execução do comando **Eseutil** no servidor Exchange.

   > [!NOTE]
   > Para usar essa opção, você deve copiar os arquivos de Ese.dll e Eseutil.exe para o diretório C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin no servidor MABS. Caso contrário, o seguinte erro será disparado:   
   > ![erro de eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selecione **Avançar**.
9. Selecione o banco de dados para **backup de cópia**e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Se você não selecionar "backup completo" para pelo menos uma cópia DAG de um banco de dados, os logs não serão truncados.
   >
   >
10. Configure as metas para **backup de curto prazo**e, em seguida, selecione **Avançar**.
11. Examine o espaço em disco disponível e, em seguida, selecione **Avançar**.
12. Selecione a hora em que o servidor MABS criará a replicação inicial e, em seguida, selecione **Avançar**.
13. Selecione as opções de verificação de consistência e, em seguida, selecione **Avançar**.
14. Escolha o banco de dados do qual você deseja fazer backup no Azure e, em seguida, selecione **Avançar**. Por exemplo:

    ![Especificar dados de proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o agendamento para o **backup do Azure**e, em seguida, selecione **Avançar**. Por exemplo:

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observe que os Pontos de recuperação online têm base nos pontos de recuperação completos expressos. Portanto, você deve agendar o ponto de recuperação online após o horário especificado para o ponto de recuperação completo expresso.
    >
    >
16. Configure a política de retenção para o **backup do Azure**e, em seguida, selecione **Avançar**.
17. Escolha uma opção de replicação online e selecione **Avançar**.

    Se você tiver um banco de dados de grande porte, talvez a criação do backup inicial na rede demore bastante. Para evitar esse problema, crie um backup offline.  

    ![Especificar política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as configurações e, em seguida, selecione **Criar grupo**.
19. Selecione **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar o banco de dados do Exchange

1. Para recuperar um banco de dados do Exchange, selecione **recuperação** no console do administrador mAbs.
2. Localize o banco de dados do Exchange que você deseja recuperar.
3. Selecione um ponto de recuperação online na lista suspensa *tempo de recuperação* .
4. Selecione **recuperar** para iniciar o **Assistente de recuperação**.

Há cinco tipos de recuperação para os pontos de recuperação online:

* **Recuperar no local original do Exchange Server:** os dados serão recuperados no servidor original do Exchange.
* **Recuperar em outro banco de dados em um servidor do Exchange:** os dados serão recuperados em outro banco de dados, em outro servidor do Exchange.
* **Recuperar um Banco de Dados de Recuperação:** os dados serão recuperados em um RDB (Banco de Dados de Recuperação do Exchange).
* **Copiar em uma pasta de rede:** os dados serão recuperados em uma pasta de rede.
* **Cópia em fita:** se você tiver uma biblioteca de fitas ou uma unidade de fita autônoma conectada e configurada no MABS, o ponto de recuperação será copiado em uma fita livre.

    ![Escolher a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Próximas etapas

* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)

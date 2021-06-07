---
title: Fazer backup de um servidor Exchange por meio do DPM do System Center
description: Saiba como fazer backup de um servidor do Exchange no Backup do Azure usando o System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519384"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Fazer backup de um servidor do Exchange no Backup do Azure com o System Center 2012 R2 DPM

Este artigo descreve como configurar um servidor do System Center 2012 R2 DPM (Data Protection Manager) para fazer backup de um servidor do Microsoft Exchange no Backup do Azure.  

## <a name="updates"></a>Atualizações

Para registrar o servidor DPM no Backup do Azure, você deve instalar o pacote cumulativo de atualizações mais recente do System Center 2012 R2 DPM e a versão mais recente do Agente de Backup do Azure. Obtenha o pacote cumulativo de atualizações mais recente no [Catálogo da Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Para os exemplos neste artigo, a versão 2.0.8719.0 do Agente de Backup do Azure está instalada, e Atualização Cumulativa 6 está instalada no System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se todos os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para uso do Backup do Microsoft Azure como proteção às cargas de trabalho foram atendidos. Esses pré-requisitos incluem os seguintes:

* Criação de um cofre de backup no site do Azure.
* Download das credenciais do agente e do cofre no servidor DPM.
* Instalação do agente no servidor DPM.
* Uso das credenciais do cofre para registro no servidor DPM.
* Caso esteja protegendo o Exchange 2016, execute uma atualização para o DPM 2012 R2 UR9 ou posterior.

## <a name="dpm-protection-agent"></a>Agente de proteção do DPM

Execute estas etapas para instalar o agente de proteção do DPM no servidor do Exchange:

1. Verifique se os firewalls estão configurados corretamente. Confira [Configurar exceções de firewall do agente](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Instale o agente no servidor do Exchange selecionando as opções **Gerenciamento > Agentes > Instalar** no Console do Administrador do DPM. Confira [Instalar o agente de proteção do DPM](/system-center/dpm/deploy-dpm-protection-agent) para conhecer as etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção do Exchange server

1. No Console do Administrador do DPM, selecione a opção **Proteção**, depois clique em **Novo** na faixa de opções de ferramentas para abrir o assistente **Criar Grupo de Proteção**.
2. Na tela **Bem-vindo(a)** do assistente, clique em **Próximo**.
3. Na tela **Selecionar tipo do grupo de proteção**, selecione a opção **Servidores**, depois clique em **Próximo**.
4. Selecione o banco de dados do servidor do Exchange que você deseja proteger, depois clique em **Próximo**.

   > [!NOTE]
   > Caso esteja protegendo o Exchange 2013, verifique os [pré-requisitos do Exchange 2013](/system-center/dpm/back-up-exchange).
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

    Depois de selecionar essa opção, a verificação de consistência do backup será executada no servidor DPM a fim de evitar o tráfego de E/S gerado pela execução do comando **eseutil** no servidor Exchange.

   > [!NOTE]
   > Para usar essa opção, você deve copiar os arquivos Ese.dll e Eseutil.exe no diretório C:\Arquivos de Programas\Microsoft System Center 2012 R2\DPM\DPM\bin no servidor DPM. Caso contrário, o seguinte erro será disparado:   
   > ![erro de eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selecione **Avançar**.
9. Selecione o banco de dados para **Copiar Backup**, depois clique em **Próximo**.

   > [!NOTE]
   > Caso não selecione o "Backup completo" de pelo menos uma cópia do DAG de um banco de dados, os logs ficarão truncados.
   >
   >
10. Configure as metas de **Backup de curto prazo**, depois clique em **Próximo**.
11. Examine o espaço em disco disponível, depois clique em **Próximo**.
12. Escolha o horário em que o servidor DPM criará a replicação inicial, depois clique em **Próximo**.
13. Selecione opções de verificação de consistência, depois clique em **Próximo**.
14. Escolha o banco de dados em que você deseja fazer backup no Azure, depois clique em **Próximo**. Por exemplo:

    ![Especificar dados de proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o agendamento do **Backup do Azure**, depois clique em **Próximo**. Por exemplo:

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observe que os Pontos de recuperação online têm base nos pontos de recuperação completos expressos. Portanto, será necessário agendar o ponto de recuperação online após o horário especificado para executar o ponto de recuperação expresso e completo.
    >
    >
16. Configure a política de retenção do **Backup do Azure**, depois clique em **Próximo**.
17. Escolha uma opção de replicação online, depois clique em **Próximo**.

    Se você tiver um banco de dados de grande porte, talvez a criação do backup inicial na rede demore bastante. Para evitar esse problema, crie um backup offline.  

    ![Especificar política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as configurações, depois clique em **Criar Grupo**.
19. Selecione **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar o banco de dados do Exchange

1. Para recuperar um banco de dados do Exchange, selecione a opção **Recuperação** no Console do Administrador do DPM.
2. Localize o banco de dados do Exchange que você deseja recuperar.
3. Selecione um ponto de recuperação online na lista suspensa *tempo de recuperação* .
4. Clique em **Recuperar** para iniciar o **Assistente de Recuperação**.

Há cinco tipos de recuperação para os pontos de recuperação online:

* **Recuperar no local original do Exchange Server:** os dados serão recuperados no servidor original do Exchange.
* **Recuperar em outro banco de dados em um servidor do Exchange:** os dados serão recuperados em outro banco de dados, em outro servidor do Exchange.
* **Recuperar um Banco de Dados de Recuperação:** os dados serão recuperados em um RDB (Banco de Dados de Recuperação do Exchange).
* **Copiar em uma pasta de rede:** os dados serão recuperados em uma pasta de rede.
* **Cópia em fita:** se você tiver uma biblioteca de fitas ou uma unidade de fita autônoma conectada e configurada no servidor DPM, o ponto de recuperação será copiado em uma fita livre.

    ![Escolher a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Próximas etapas

* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.yml)

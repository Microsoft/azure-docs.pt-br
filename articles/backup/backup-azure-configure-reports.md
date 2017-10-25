---
title: "Configurar relatórios de Backup do Azure"
description: "Este artigo fala sobre como configurar relatórios do Power BI para Backup do Azure usando o cofre dos Serviços de Recuperação."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/13/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e817e327b8890c91bd7db640b083fd6c5c11aa14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure
Este artigo aborda as etapas para configurar relatórios para o Backup do Azure usando o cofre dos Serviços de Recuperação e para acessar esses relatórios usando o Power BI. Depois de executar essas etapas, você poderá ir diretamente para o Power BI para exibir todos os relatórios, personalizar e criar relatórios. 

## <a name="supported-scenarios"></a>Cenários com suporte
1. Os relatórios de Backup do Azure têm suporte para backup da máquina virtual do Azure e backup de arquivo/pasta para a nuvem usando o Agente de serviços de recuperação do Azure.
2. Não há suporte para relatórios do SQL Azure, DPM e Servidor de Backup do Azure no momento.
3. Você pode exibir relatórios em cofres e em assinaturas, se a mesma conta de armazenamento estiver configurada para cada um dos cofres. A conta de armazenamento selecionada deve estar na mesma região que o cofre dos Serviços de Recuperação.
4. A frequência de atualização agendada para os relatórios é de 24 horas no Power BI. Você também pode executar uma atualização local dos relatórios no Power BI, nos quais os dados mais recentes na conta de armazenamento do cliente são usados para renderizar relatórios. 

## <a name="prerequisites"></a>Pré-requisitos
1. Criar uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) e configurá-la para relatórios. Essa conta de armazenamento é usada para armazenar dados relacionados a relatórios.
2. [Criar uma conta do Power BI](https://powerbi.microsoft.com/landing/signin/) para exibir, personalizar e criar seus próprios relatórios usando o portal do Power BI.
3. Registrar o provedor de recursos **Microsoft.insights** se já não estiver registrado, com a assinatura da conta de armazenamento e também com a assinatura do cofre de Serviços de Recuperação para habilitar os dados de relatório e analisar o fluxo para a conta de armazenamento. Para fazer o mesmo, você deve ir ao portal do Azure > Assinatura > Provedores de recursos e verificar se há esse provedor para registrá-lo. 

## <a name="configure-storage-account-for-reports"></a>Configurar a conta de armazenamento para relatórios
Use as etapas a seguir para configurar a conta de armazenamento para o cofre de serviços de recuperação usando o portal do Azure. Essa é uma configuração única e depois de configurar a conta de armazenamento, você pode ir para o Power BI diretamente para exibir o pacote de conteúdo e aproveitar os relatórios.
1. Se você já tiver um cofre dos Serviços de Recuperação aberto, vá para a próxima etapa. Se você não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu Hub, clique em **Procurar**.

   * Na lista de recursos, digite **Serviços de Recuperação**.
   * Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando vir a opção **Cofres de Serviços de Recuperação**, clique nela.

      ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     A lista de cofres de Serviços de Recuperação aparecerá. Na lista de cofres de Serviços de Recuperação, selecione um cofre.

     O painel de cofres selecionados será aberto.
2. Na lista de itens que aparecem no cofre, clique em **Backup de Relatórios** na seção Monitoramento e Relatórios para configurar a conta de armazenamento para relatórios.

      ![Etapa 2 Selecione o item de menu Backup de Relatórios](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Na folha Backup de Relatórios, clique no botão **Configurar**. Isso abre a folha Azure Application Insights que é usada para enviar dados por push para a conta de armazenamento do cliente.

      ![Etapa 3 Configurar a conta de armazenamento](./media/backup-azure-configure-reports/configure-storage-account.PNG)
4. Definir o botão de alternância de Status como **Habilitado** e marque a caixa de seleção **Arquivo para uma Conta de Armazenamento** para que os dados de relatório possam iniciar o fluxo para a conta de armazenamento.

      ![Etapa 4 Habilitar o diagnóstico](./media/backup-azure-configure-reports/set-status-on.png)
5. Clique no seletor da Conta de armazenamento e selecione a conta de armazenamento na lista para armazenar dados de relatório e clique em **OK**.

      ![Etapa 5 Selecionar a conta de armazenamento](./media/backup-azure-configure-reports/select-storage-account.png)
6. Marque a caixa de seleção **AzureBackupReport** e também mova o controle deslizante ao período de retenção selecionado para esses dados de relatório. Os dados de relatório na conta de armazenamento são mantidos pelo período selecionado usando este controle deslizante.

      ![Etapa 6 Selecione a conta de armazenamento](./media/backup-azure-configure-reports/save-configuration.png)
7. Examine todas as alterações e clique no botão **Salvar** na parte superior, conforme mostrado na figura acima. Esta ação garante que todas as alterações sejam salvas e a conta de armazenamento agora está configurada para armazenar os dados de relatório.

> [!NOTE]
> Após configurar os relatórios salvando a conta de armazenamento, você deverá **aguardar 24 horas** para concluir o push de dados iniciais. Você deverá importar o pacote de conteúdo do Backup do Azure no Power BI somente após esse período. Para obter mais detalhes, consulte a [seção de Perguntas Frequentes](#frequently-asked-questions). 
>
>

## <a name="view-reports-in-power-bi"></a>Exibir relatórios no Power BI 
Depois de configurar a conta de armazenamento para relatórios usando o cofre de serviços de recuperação, leva cerca de 24 horas para os dados de relatório começarem a fluir. Após 24 horas da configuração da conta de armazenamento, use as etapas a seguir para exibir relatórios no Power BI:
1. [Entre](https://powerbi.microsoft.com/landing/signin/) no Power BI.
2. Clique em **Obter Dados** e clique em Obter em **Serviços** na Biblioteca do pacote de conteúdo. Use as etapas mencionadas na documentação do [Power BI para acessar o pacote de conteúdo](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importar o pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-import.png)
3. Digite o **Backup do Azure** na barra Search e clique em **Obter agora**.

      ![Obter pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-get.png)
4. Insira o nome da conta de armazenamento configurada na etapa 5 acima e clique no botão **Avançar**.

    ![Insira o nome da conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Insira a chave de conta de armazenamento para esta conta de armazenamento. Você pode [exibir e copiar as chaves de acesso de armazenamento](../storage/common/storage-create-storage-account.md#manage-your-storage-account) navegando para sua conta de armazenamento no portal do Azure. 

     ![Insira a conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Clique no botão **Entrar**. Depois que o logon for bem-sucedido, você obterá a notificação **Importando dados**.

    ![Importando o pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Depois de algum tempo, você obtém a notificação **Sucesso** após a importação ser concluída. Pode levar um pouco mais de tempo para importar o pacote de conteúdo, se houver muitos dados na conta de armazenamento.
    
    ![Importar o pacote de conteúdo com sucesso](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Quando os dados são importados com êxito, o pacote de conteúdo do **Backup do Azure** fica visível em **Aplicativos** no painel de navegação. A lista agora mostra o painel de Backup do Azure, os relatórios e o conjunto de dados com uma estrela amarela indicando os relatórios recém-importados. 

     ![Pacote de conteúdo de Backup do Azure](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Clique em **Backup do Azure** em Painéis, que mostra um conjunto de relatórios chave fixados.

      ![Painel de Backup do Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para exibir o conjunto completo de relatórios, clique em qualquer relatório no painel.

      ![Integridade do trabalho de Backup do Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Clique em cada guia nos relatórios para exibir os relatórios nessa área.

      ![Guias para relatórios de Backup do Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Perguntas frequentes
1. **Como verificar se os dados de relatório começaram a fluir na conta de armazenamento?**
    
    Você pode ir para a conta de armazenamento configurada e selecionar os contêineres. Se o contêiner tem uma entrada para insights-logs-azurebackupreport, ele indica que dados de relatórios começaram a fluir.

2. **Qual é a frequência de push de dados para a conta de armazenamento e para o pacote de conteúdo de Backup do Azure no Power BI?**

   Para usuários do dia 0, leva cerca de 24 horas para enviar dados por push para a conta de armazenamento. Após esse envio por push inicial ser concluído, os dados são atualizados com a frequência mostrada na figura abaixo. 
      * Os dados relacionados a **trabalhos, alertas, itens de backup, cofres, servidores protegidos e políticas** são enviados por push para a conta de armazenamento do cliente como e quando ela estiver conectada.
      * Os dados relacionados a **armazenamento** são enviados por push para a conta de armazenamento do cliente a cada 24 horas.
   
    ![Frequência de push de dados de relatórios de Backup do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  O Power BI tem uma [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Você pode executar uma atualização manual dos dados no Power BI para o pacote de conteúdo.

3. **Quanto tempo é possível reter os relatórios?** 

   Ao configurar a conta de armazenamento, você pode selecionar o período de retenção de dados de relatório na conta de armazenamento (usando a etapa 6 em Configurar conta de armazenamento para a seção de relatórios acima). Além disso, você pode [Analisar relatórios no excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los para um período de retenção mais longo, de acordo com as suas necessidades. 

4. **Todos os meus dados em relatórios serão exibidos depois de configurar a conta de armazenamento?**

   Todos os dados gerados após a **"Configurando a conta de armazenamento"** serão enviados para a conta de armazenamento e estarão disponíveis em relatórios. No entanto, **Os trabalhos em andamento não são enviados por push** para a emissão de relatórios. Depois que o trabalho for concluído ou falhar, ele será enviado para os relatórios.

5. **Se eu já tiver configurado a conta de armazenamento para exibir relatórios, posso alterar a configuração para usar outra conta de armazenamento?** 

   Sim, você pode alterar a configuração para apontar para uma conta de armazenamento diferente. Você deve usar a conta de armazenamento recém-configurada ao conectar-se ao pacote de conteúdo de Backup do Azure. Além disso, depois que uma conta de armazenamento diferente for configurada, novos dados fluirão nesta conta de armazenamento. Mas os dados mais antigos (antes de alterar a configuração) permanecerão na conta de armazenamento mais antiga.

6. **Posso exibir relatórios entre cofres e assinaturas?** 

   Sim, você pode configurar a mesma conta de armazenamento entre vários cofres para exibir relatórios de cofre cruzado. Além disso, você pode configurar a mesma conta de armazenamento para cofres entre assinaturas. Você pode usar essa conta de armazenamento durante a conexão com o pacote de conteúdo de Backup do Azure no Power BI para exibir os relatórios. No entanto, a conta de armazenamento selecionada deve estar na mesma região que o cofre dos serviços de recuperação.
   
## <a name="troubleshooting-errors"></a>Solucionar erros
| Detalhes do erro | Resolução |
| --- | --- |
| Depois de configurar a conta de armazenamento para Relatórios de Backup, a **Conta de armazenamento** ainda mostra **Não configurada**. | Se você configurou a conta de armazenamento com êxito, os dados do relatório serão transmitidos mesmo com esse problema. Para resolver esse problema, vá até o portal do Azure > Mais serviços > Configurações de diagnóstico > Cofre RS > Editar configuração. Exclua a configuração previamente definida e crie uma nova configuração na mesma folha. Desta vez defina o campo **Nome** para **serviço**. Isso deve mostrar a conta de armazenamento configurada. |
|Depois de importar o pacote de conteúdo do Backup do Azure no Power BI, o erro **404 - contêiner não encontrado** é exibido. | Como sugerido neste documento, você deve aguardar 24 horas depois de configurar relatórios no cofre de Serviços de Recuperação para vê-los corretamente no Power BI. Se você tentar acessar os relatórios antes de 24 horas, você receberá esse erro pois os dados completos ainda não estão presente para mostrar relatórios válidos. |

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou a conta de armazenamento e importou o pacote de conteúdo de Backup do Azure, a próxima etapa é personalizar esses relatórios e usar o modelo de dados de relatório para criar relatórios. Consulte os seguintes artigos para obter mais detalhes.

* [Usando o modelo de dados de relatórios de Backup do Azure](backup-azure-reports-data-model.md)
* [Filtrando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Criando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)


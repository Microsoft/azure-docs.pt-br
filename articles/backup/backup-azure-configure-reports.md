---
title: Configurar relatórios para Backup do Azure
description: Configure relatórios do Power BI para Backup do Azure usando um cofre dos Serviços de Recuperação.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: adigan
ms.openlocfilehash: e3004a44958d75d18d608a2fbed7ccc44a00dc93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60642496"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure
Este artigo mostra as etapas a seguir para configurar relatórios para o Backup do Azure por meio de um cofre dos Serviços de Recuperação. Ele também mostra como acessar relatórios por meio do Power BI. Depois de concluir essas etapas, você pode ir diretamente para o Power BI para exibir, personalizar e criar relatórios.

> [!IMPORTANT]
> A partir de 1º de novembro de 2018, alguns clientes poderão ter problemas ao carregar os dados no Aplicativo de Backup do Azure no Power BI, dizendo “Encontramos caracteres extras no final da entrada JSON. A exceção foi levantada pela interface IDataReader. ”
Isso ocorre devido a uma alteração no formato no qual os dados são carregados na conta de armazenamento.
Faça o download do último aplicativo (versão 1.8) para evitar esse problema.
>
>

## <a name="supported-scenarios"></a>Cenários com suporte
- Os relatórios de Backup do Azure têm suporte para backup da máquina virtual do Azure e backup de arquivo e de pasta para a nuvem usando o Agente de Serviços de Recuperação do Azure.
- Relatórios do Banco de Dados SQL do Azure, Compartilhamentos de Arquivos do Azure, Gerenciador de Proteção de Dados e servidor de Backup do Azure não são suportados no momento.
- Você poderá exibir relatórios em cofres e em assinaturas se a mesma conta de armazenamento estiver configurada para cada um dos cofres. A conta de armazenamento selecionada deve estar na mesma região do cofre dos Serviços de Recuperação.
- A frequência de atualização agendada para os relatórios é de 24 horas no Power BI. Você também pode executar uma atualização ad hoc dos relatórios no Power BI. Nesse caso, os dados mais recentes na conta de armazenamento do cliente são usados para renderizar relatórios.

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) e configurá-la para relatórios. Essa conta de armazenamento é usada para armazenar dados relacionados a relatórios.
- [Crie uma conta do Power BI](https://powerbi.microsoft.com/landing/signin/) para exibir, personalizar e criar seus próprios relatórios usando o portal do Power BI.
- Registre o provedor de recursos **Microsoft.Insights** se ele ainda não estiver registrado. Use as assinaturas para a conta de armazenamento e o cofre dos Serviços de Recuperação para que os dados de relatório possam fluir para a conta de armazenamento. Para realizar essa etapa, vá para o portal do Azure e selecione **Assinatura** > **Provedores de recursos** e verificar se há esse provedor a fim de registrá-lo.

## <a name="configure-storage-account-for-reports"></a>Configurar a conta de armazenamento para relatórios
Siga estas etapas para configurar a conta de armazenamento para o cofre dos Serviços de Recuperação usando o portal do Azure. Essa configuração é realizada uma única vez. Depois que a conta de armazenamento é configurada, você pode ir diretamente para o Power BI para exibir o pacote de conteúdo e usar os relatórios.

1. Se já tiver um cofre dos Serviços de Recuperação aberto, vá para a próxima etapa. Se você não tiver um cofre dos Serviços de Recuperação aberto, selecione **Todos os serviços** no portal do Azure.

   * Na lista de recursos, digite **Serviços de Recuperação**.
   * Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.
   * A lista de cofres de Serviços de Recuperação aparecerá. Na lista de cofres de Serviços de Recuperação, selecione um cofre.

     O painel de cofres selecionados será aberto.
2. Na lista de itens que aparecem no cofre, na seção **Monitoramento e Relatórios**, selecione **Relatórios de Backup** para configurar a conta de armazenamento para relatórios.

      ![Etapa 2 Selecione o item de menu Backup de Relatórios](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Na folha de **Relatórios de Backup**, selecione o link **Configurações de Diagnóstico**. Esse link abre as configurações de **Diagnóstico de interface do usuário**, que é usada para enviar dados por push para uma conta de armazenamento do cliente.

      ![Habilite Diagnósticos etapa 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Selecione **Ativar o diagnóstico** para abrir uma interface do usuário a ser usada para configurar uma conta de armazenamento.

      ![Ativar o status de diagnóstico etapa 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Na caixa **Nome**, insira um nome de configuração. Marque a caixa de seleção **Arquivos para uma Conta de Armazenamento** para que os dados de relatório possam iniciar o fluxo para a conta de armazenamento.

      ![Habilite diagnósticos etapa 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Selecione **Conta de Armazenamento** e depois selecione a conta de armazenamento e a assinatura relevante na lista para armazenar dados de relatório. Em seguida, clique em **OK**.

      ![Etapa 6 Selecione a conta de armazenamento](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Na seção **Log**, selecione a caixa de seleção **AzureBackupReport**. Mova o controle deslizante para selecionar um período de retenção para esses dados de relatórios. Os dados de relatório na conta de armazenamento são mantidos pelo período selecionado usando este controle deslizante.

      ![Salve a conta de armazenamento etapa 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Examine todas as alterações e selecione **Salvar**. Esta ação garante que todas as alterações sejam salvas e a conta de armazenamento agora está configurada para armazenar dados de relatório.

9. A tabela **Configurações de diagnóstico** agora mostra a nova configuração habilitada para o cofre. Se ela não aparecer, atualize a tabela para ver a configuração atualizada.

      ![Veja a configuração de diagnóstico etapa 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Após configurar os relatórios salvando a conta de armazenamento, *aguarde 24 horas* para concluir o push de dados inicial. Importe o aplicativo de backup do Azure no Power BI somente após esse período. Para obter mais informações, confira a [Seção de perguntas frequentes](#frequently-asked-questions).
>
>

## <a name="view-reports-in-power-bi"></a>Exibir relatórios no Power BI
Depois de configurar uma conta de armazenamento para relatórios usando um cofre dos Serviços de Recuperação, leva cerca de 24 horas para os dados de relatório começarem a fluir. Após 24 horas da configuração da conta de armazenamento, siga as etapas a seguir para exibir relatórios no Power BI.
Se você quiser personalizar e compartilhar o relatório, criar um espaço de trabalho e execute as seguintes etapas

1. [Entre](https://powerbi.microsoft.com/landing/signin/) no Power BI.
2. Selecione **Obter Dados**. Em **Mais maneiras de criar seu próprio conteúdo**, selecione **Service content packs**. Siga as etapas na documentação do [Power BI para se conectar a um serviço](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Na barra **Pesquisar**, insira **Backup do Azure** e selecione **Obter agora**.

      ![Obter pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-get.png)
4. Insira o nome da conta de armazenamento que foi configurada na etapa 5 anterior e selecione **Próximo**.

    ![Insira o nome da conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Usando o método de autenticação "Chave", insira a chave da conta de armazenamento para essa conta de armazenamento. Para [exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys), vá para a sua conta de armazenamento no portal do Azure.

     ![Insira a conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Selecione **Entrar**. Depois que a conexão for bem-sucedida, você verá uma notificação de que dados estão sendo importados.

    ![Importando o pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Após a importação ser concluída, você verá uma notificação de **Êxito**. Se a quantidade de dados na conta de armazenamento for grande, poderá levar um pouco mais de tempo para importar o pacote de conteúdo.

    ![Importar o pacote de conteúdo com sucesso](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Após os dados serem importados com êxito, o pacote de conteúdo do **Backup do Azure** ficará visível em **Aplicativos** no painel de navegação. Em **Painéis**, **Relatórios** e **Conjuntos de Dados**, a lista agora mostra o Backup do Azure.

8. Em **Painéis**, selecione **Backup do Azure**, que mostra um conjunto de relatórios-chave fixados.

      ![Painel de Backup do Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para exibir o conjunto completo de relatórios, selecione qualquer relatório no painel.

      ![Integridade do trabalho de Backup do Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Selecione cada guia nos relatórios para exibir os relatórios nessa área.

      ![Guias para relatórios do Backup do Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Como verificar se os dados do relatório começaram a entrar em uma conta de armazenamento?
Vá para a conta de armazenamento que você configurou e selecione os contêineres. Se o contêiner tem uma entrada para insights-logs-azurebackupreport, ele indica que dados de relatórios começaram a fluir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Qual é a frequência do push de dados para uma conta de armazenamento e o pacote do conteúdo de Backup do Azure no Power BI?
  Para usuários do dia 0, leva cerca de 24 horas para enviar dados por push para uma conta de armazenamento. Após esse envio por push inicial ser concluído, os dados são atualizados com a frequência mostrada na figura abaixo.

  * Os dados relacionados a **Trabalhos**, **Alertas**, **Itens de backup**, **Cofres**, **Servidores protegidos** e **Políticas** são enviados por push para uma conta de armazenamento do cliente como e quando ela está conectada.

  * Os dados relacionados a **Armazenamento** são enviados por push para a conta de armazenamento do cliente a cada 24 horas.

       ![Frequência de push de dados de relatórios de Backup do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * O Power BI tem uma [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Você pode executar uma atualização manual dos dados no Power BI para o pacote de conteúdo.

### <a name="how-long-can-i-retain-reports"></a>Por quanto tempo é possível reter os relatórios?
Quando configura uma conta de armazenamento, você pode selecionar um período de retenção para dados de relatório na conta de armazenamento. Siga a etapa 6 na seção [Configurar conta de armazenamento para relatórios](backup-azure-configure-reports.md#configure-storage-account-for-reports). Além disso, você pode [analisar relatórios no excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los por um período de retenção mais longo, de acordo com as suas necessidades.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Todos os meus dados em relatórios serão exibidos após a configuração da conta de armazenamento?
 Todos os dados gerados após você configurar uma conta de armazenamento são enviados por push para a conta de armazenamento e estão disponíveis em relatórios. Os trabalhos em andamento não são enviados por push para a geração de relatórios. Depois que o trabalho é concluído ou falha, ele é enviado para relatórios.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se eu já configurei a conta de armazenamento para exibir relatórios, posso alterar a configuração para usar outra conta de armazenamento?
Sim, você pode alterar a configuração para apontar para uma conta de armazenamento diferente. Use a conta de armazenamento recém-configurada ao conectar-se ao pacote de conteúdo de Backup do Azure. Além disso, depois que uma conta de armazenamento diferente for configurada, novos dados fluirão nesta conta de armazenamento. Mas os dados mais antigos (anteriores à alteração da configuração) permanecerão na conta de armazenamento mais antiga.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso exibir relatórios entre cofres e assinaturas?
Sim, você pode configurar a mesma conta de armazenamento entre vários cofres para exibir relatórios de cofre cruzado. Além disso, você pode configurar a mesma conta de armazenamento para cofres entre assinaturas. Você pode usar essa conta de armazenamento enquanto se conecta ao pacote de conteúdo de Backup do Azure no Power BI para exibir os relatórios. A conta de armazenamento selecionada deve estar na mesma região do cofre dos Serviços de Recuperação.

## <a name="troubleshooting-errors"></a>Solucionar erros
| Detalhes do Erro | Resolução |
| --- | --- |
| Depois de configurar a conta de armazenamento para Relatórios de Backup, a **Conta de armazenamento** ainda mostra **Não configurada**. | Se você configurou uma conta de armazenamento com êxito, os dados do relatório são transmitidos mesmo com esse problema. Para resolver esse problema, vá para o portal do Azure e selecione **Todos os serviços** > **Configurações de diagnóstico** > **Cofre dos Serviços de Recuperação** > **Editar configuração**. Exclua a configuração previamente definida e crie uma nova configuração na mesma folha. Desta vez, além da caixa **Nome**, selecione **serviço**. Agora, a conta de armazenamento configurada aparece. |
|Depois de importar o pacote de conteúdo de Backup do Azure no Power BI, uma mensagem de erro "404 – contêiner não encontrado" será exibida. | Conforme mencionado anteriormente, você precisa aguardar 24 horas depois de configurar relatórios no cofre dos Serviços de Recuperação para vê-los corretamente no Power BI. Se você tentar acessar os relatórios antes de 24 horas, você receberá esse erro porque os dados completos ainda não estarão presentes para mostrar relatórios válidos. |

## <a name="next-steps"></a>Próximas etapas
Após você configurar a conta de armazenamento e importar o pacote de conteúdo do Backup do Azure, a próxima etapa é personalizar esses relatórios e usar um modelo de dados de relatório para criar relatórios. Para obter mais informações, consulte os seguintes artigos.

* [Usar um modelo de dados de relatórios do Backup do Azure](backup-azure-reports-data-model.md)
* [Filtrar relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Criar relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

---
title: Fazer backup de um farm do SharePoint no Azure Stack
description: Use o Servidor de Backup do Azure para fazer backup e restaurar seus dados do SharePoint no Azure Stack. Este artigo fornece informações para configurar seu farm do SharePoint para que os dados desejados possam ser armazenados no Azure. Você pode restaurar dados protegidos do SharePoint do disco ou do Azure.
ms.topic: conceptual
ms.date: 06/07/2020
ms.openlocfilehash: 1e237e63b92468fafff4f8f8f525d1388840d162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378314"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Fazer backup de um farm do SharePoint no Azure Stack

Faça backup de um farm do SharePoint no Azure Stack para o Microsoft Azure usando o MABS (Servidor de Backup do Microsoft Azure) da mesma maneira que você faz backup de outras fontes de dados. O Backup do Azure fornece flexibilidade no agendamento de backup para criar pontos de backup diariamente, semanalmente, mensalmente ou anualmente e fornece opções de política de retenção para diversos pontos de backup. Ele também fornece a capacidade de armazenar cópias de disco locais para obter RTOs (Objetivos de Tempo de Recuperação) rápidos e armazenar cópias no Azure para uma retenção econômica e de longo prazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões do SharePoint com suporte e cenários de proteção relacionados

O Backup do Azure para MABS dá suporte aos seguintes cenários:

| Carga de trabalho | Versão | Implantação do SharePoint | Proteção e recuperação |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint implantado como uma máquina virtual Azure Stack <br> -------------- <br>  AlwaysOn do SQL | Opções recuperação para proteger o Farm do SharePoint: farm de recuperação, banco de dados e um arquivo ou item de lista dos pontos de recuperação de disco.  Recuperação do farm e do banco de dados dos pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de começar

Há alguns elementos que você precisa confirmar antes de fazer o backup de um farm do SharePoint para o Azure.

### <a name="whats-not-supported"></a>O que não tem suporte

* O MABS que protege um farm do SharePoint não protege os índices de pesquisa ou os bancos de dados do serviço de aplicativo. Você precisará configurar a proteção desses bancos de dados separadamente.

* O MABS não fornece backup dos bancos de dados do SQL Server do SharePoint hospedados no servidor de arquivos de expansão (SOFS).

### <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se atendeu a todos os [pré-requisitos de uso do Backup do Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para proteger cargas de trabalho. Algumas tarefas para os pré-requisitos incluem: criar um cofre de backup, baixar as credenciais do cofre, instalar o Agente de Backup do Azure e registrar o Servidor de Backup do Azure com o cofre.

Pré-requisitos e limitações adicionais:

* Por padrão, quando você protege o SharePoint, todos os bancos de dados de conteúdo (e os bancos de dados SharePoint_Config e SharePoint_AdminContent*) serão protegidos. Se você quiser adicionar personalizações como índices de pesquisa, modelos ou bancos de dados de serviço de aplicativo, ou o serviço de perfil de usuário, você precisará configurar a proteção deles separadamente. Certifique-se de habilitar a proteção para todas as pastas que incluem esses tipos de recursos ou arquivos de personalização.

* Você não pode proteger os bancos de dados do SharePoint como uma fonte de dados do SQL Server. Você pode recuperar bancos de dados individuais de um backup de farm.

* Lembre-se de que o MABS é executado como **Sistema Local** e, para fazer backup do banco de dados do SQL Server, ele precisa de privilégios sysadmin nessa conta para o SQL Server. No SQL Server em que você deseja fazer backup, defina NT AUTHORITY\SYSTEM para **sysadmin**.

* Para cada 10 milhões de itens no farm, deve haver pelo menos 2 GB de espaço no volume em que a pasta do MABS está localizada. Esse espaço é necessário para a geração de catálogo. Para permitir que você use o MABS para executar uma recuperação específica de itens (conjuntos de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogo cria uma lista das URLs contidas em cada banco de dados de conteúdo. Você pode exibir a lista de URLs no painel de item recuperável na área de tarefa Recuperação do Console do Administrador do MABS.

* No farm do SharePoint, se você possuir bancos de dados do SQL Server configurados com aliases do SQL Server, instale os componentes cliente do SQL Server no servidor Web front-end que o MABS protegerá.

* Não há suporte para a proteção de itens da loja de aplicativos com o SharePoint 2013.

* O MABS não oferece suporte à proteção do FILESTREAM remoto. O FILESTREAM deve fazer parte do banco de dados.

## <a name="configure-backup"></a>Configurar o backup

Para fazer backup do farm do SharePoint, configure a proteção do SharePoint usando ConfigureSharePoint.exe e, em seguida, crie um grupo de proteção no MABS.

1. **Execute ConfigureSharePoint.exe** — essa ferramenta configura o serviço de gravador VSS do SharePoint \(WSS\) e fornece o agente de proteção com credenciais para o farm do SharePoint. Depois de implantar o agente de proteção, o arquivo ConfigureSharePoint.exe pode ser encontrado na pasta `<MABS Installation Path\>\bin` no servidor Web front\-end.  Se você tiver vários servidores WFE, precisará instalá-lo apenas em um deles. Execute da seguinte maneira:

    * No servidor WFE, em um prompt de comando, navegue até `\<MABS installation location\>\\bin\\` e execute `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]`, em que:

        * **EnableSharePointProtection** habilita a proteção do farm do SharePoint, habilita o gravador VSS e registra a identidade do aplicativo DCOM WssCmdletsWrapper para ser executado como um usuário, cujas credenciais são inseridas com essa opção. Essa conta deve ser um administrador de farm e também um administrador local no servidor Web front\-end.

        * **EnableSPSearchProtection** habilita a proteção da pesquisa do WSS 3.0 SP usando a chave do registro SharePointSearchEnumerationEnabled em HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ no servidor Web front\-end e registra a identidade do aplicativo DCOM WssCmdletsWrapper para execução como um usuário cujas credenciais são inseridas com esta opção. Esta conta deve ser uma administradora do farm e também uma administradora local no servidor Web front\-end.

        * **ResolveAllSQLAliases** exibe todos os aliases relatados pelo gravador VSS do SharePoint e os resolve de acordo com o SQL Server correspondente. Também exibe os nomes de instância resolvidos. Se os servidores forem espelhados, ele também exibirá o servidor espelhado. Ele relata todos os aliases que não estão sendo resolvidos para um SQL Server.

        * **SetTempPath** define as variáveis de ambiente TEMP e TMP para o caminho especificado. A recuperação em nível de item falhará se um conjunto de site grande, site, lista ou item estiver sendo recuperado e não houver espaço suficiente na pasta temporária no administrador do farm. Esta opção permite alterar o caminho da pasta dos arquivos temporários para um volume que possua espaço suficiente para armazenar o site ou conjunto de sites que está sendo recuperado.

    * Insira as credenciais de administrador do farm. Essa conta deve ser um membro do grupo de administradores local no servidor WFE. Se o administrador do farm não for um administrador local, conceda as seguintes permissões no servidor WFE:

        * Conceda ao grupo **WSS_Admin_WPG** controle total para a pasta MABS (`%Program Files%\Data Protection Manager\DPM\`).

        * Conceda ao grupo **WSS_Admin_WPG** acesso de leitura à chave do Registro MABS (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`).

        Depois de executar o ConfigureSharePoint.exe, você precisará executá-lo novamente se houver uma alteração nas credenciais de administrador do farm do SharePoint.

1. Para criar um grupo de proteção, **Protection**selecione  >  **ações**  >  de proteção**Criar grupo de proteção** para abrir o assistente para **criar novo grupo de proteção** no console do mAbs.

1. Em **Selecionar tipo de grupo de proteção**, selecione **Servidores**.

1. Em **Selecionar membros do grupo**, expanda o servidor que contém a função WFE. Se houver mais de um servidor WFE, selecione aquele em que você instalou o ConfigureSharePoint.exe.

    Quando você expande o VSS das consultas do SharePoint Server MABS para ver quais dados o MABS pode proteger.  Se o banco de dados do SharePoint for remoto, o MABS se conectará a ele. Se as fontes de dados do SharePoint não aparecerem, verifique se o gravador VSS está em execução no servidor do SharePoint e em qualquer SQL Server remoto e se o agente MABS está instalado tanto no servidor do SharePoint quanto no SQL Server remoto. Além disso, verifique se os bancos de dados do SharePoint não estão sendo protegidos em outro lugar como bancos de dados do SQL Server.

1. Em **Selecionar método de proteção de dados**, especifique como deseja lidar com o backup de longo e curto\-prazo. \-O backup de curto prazo é sempre para o disco primeiro, com a opção de fazer backup do disco para a nuvem do Azure com o backup do Azure \( por curto ou longo \- prazo \) .

1. Em **Selecione metas de curto\-prazo**, especifique como você deseja fazer backup no armazenamento de curto\-prazo no disco.   Em **Período de retenção**, especifique por quanto tempo deseja manter os dados no disco. Em **Frequência de sincronização**, especifique a frequência na qual você deseja executar um backup incremental no disco. Se você não quiser definir um intervalo de backup, poderá fazer a verificação logo antes de um ponto de recuperação, para que o MABS execute um backup completo expresso antes de cada ponto de recuperação ser agendado.

1. Na página Rever alocação do disco, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

    O **Tamanho total dos dados** é o tamanho dos dados de que você deseja fazer backup e **Espaço em disco a ser provisionado no MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup nos **Detalhes de alocação do disco**. Para as cargas de trabalho, selecione o armazenamento preferido no menu suspenso. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de MABS de armazenamento que sugere que você adicione ao volume para continuar com os backups sem problemas no futuro.

1. Em **Escolher método de criação de réplica**, selecione como você deseja controlar a replicação inicial de dados completo.  Se você optar por replicar pela rede, recomendamos que escolha um horário fora de pico. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando mídia removível.

1. Em **Escolher opções de verificação de consistência**, selecione como você deseja automatizar as verificações de consistência. Você pode habilitar uma verificação para ser somente executada quando os dados de réplica se tornarem inconsistentes ou de acordo com uma agenda. Se você não quiser configurar a verificação de consistência automática, poderá executar uma verificação manual a qualquer momento clicando com o botão direito do mouse no grupo de proteção na área **Proteção** do console do MABS e selecionando **Executar Verificação de Consistência**.

1. Se você optou por fazer backup na nuvem com o Backup do Azure, na página **Especificar dados de proteção online**, verifique se as cargas de trabalho das quais você deseja fazer backup no Azure estão selecionadas.

1. Em **Especificar agendamento de backup online**, especifique com que frequência os backups incrementais para o Azure devem ocorrer. Você pode agendar backups para executar a cada dia/semana/mês/ano e também a data/hora em que eles devem ser executados. Backups podem ocorrer até duas vezes por dia. Cada vez que um backup é executado, um ponto de recuperação de dados é criado no Azure a partir da cópia dos dados de backup armazenados no disco do MABS.

1. Em **Especificar política de retenção online**, você pode especificar como os pontos de recuperação criados nos backups diários, semanais, mensais e anuais são mantidos no Azure.

1. Em **Escolher replicação online**, especifique como ocorrerá a replicação inicial completa de dados. Você pode replicar pela rede ou fazer um backup offline (propagação offline). O backup offline usa o recurso Importação do Azure. [Leia mais](./backup-azure-backup-import-export.md).

1. Na página **Resumo**, examine as configurações. Depois de selecionar **Criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação terminar, o status do grupo de proteção será exibido como **OK** na página **Status**. Em seguida, ocorre o backup em linha com as configurações do grupo de proteção.

## <a name="monitoring"></a>Monitoramento

Após a criação do grupo de proteção, a replicação inicial ocorre e o MABS inicia o backup e a sincronização dos dados do SharePoint. O MABS monitora a sincronização inicial e os backups subsequentes.  Você pode monitorar os dados do SharePoint de duas maneiras:

* Usando o monitoramento do MABS padrão, você pode configurar notificações para o monitoramento proativo, publicando alertas e configurando notificações. Você pode enviar notificações por email para alertas críticos, de aviso ou informativos e para o status de recuperações instanciadas.

* Se você usar o Operations Manager, poderá publicar alertas centralmente.

### <a name="set-up-monitoring-notifications"></a>Configurar notificações de monitoramento

1. Na console do administrador mAbs, selecione opções de ação de **monitoramento**  >  **Action**  >  **Options**.

2. Selecione **servidor SMTP**, digite o nome do servidor, a porta e o endereço de email do qual as notificações serão enviadas. O endereço deve ser válido.

3. Em **Servidor SMTP autenticado**, digite um nome de usuário e uma senha. O nome de usuário e a senha devem ser o nome da conta de domínio da pessoa cujo endereço “de” é descrito na etapa anterior. Caso contrário, a entrega da notificação falhará.

4. Para testar as configurações do servidor SMTP, selecione **Enviar email de teste**, digite o endereço de email para o qual você deseja que o mAbs envie a mensagem de teste e, em seguida, selecione **OK**. Selecione **Opções**  >  **notificações** e selecione os tipos de alertas sobre os quais deseja notificar os destinatários. Em **Destinatários** digite o endereço de email de cada destinatário para o qual você deseja que o MABS envie cópias das notificações.

### <a name="publish-operations-manager-alerts"></a>Publicar alertas do Operations Manager

1. Na console do administrador mAbs, selecione opções de ação de **monitoramento**  >  **Action**  >  **Options**  >  **publicação de alerta**  >  **publicar alertas ativos**

2. Depois de habilitar **Publicação de alertas**, todos os alertas do MABS existentes que poderão exigir uma ação do usuário serão publicados no log de eventos **Alertas do MABS**. O agente de Operations Manager que está instalado no servidor MABS publica esses alertas no Operations Manager e continua a atualizar o console à medida que novos alertas são gerados.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item do SharePoint do disco usando o MABS

No exemplo a seguir, o *item Recuperando SharePoint* foi excluído acidentalmente e precisa ser recuperado.
![Proteção do SharePoint do MABS 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **Console do Administrador do MABS**. Todos os farms do SharePoint protegidos pelo MABS são mostrados na guia **Proteção**.

    ![Proteção do SharePoint do MABS 3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione a guia **Recuperação** .

    ![Proteção do SharePoint do MABS 5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. É possível pesquisar no SharePoint para localizar o *item Recuperando SharePoint* usando uma pesquisa baseada em curinga dentro de um intervalo de ponto de recuperação.

    ![Proteção do SharePoint do MABS 6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado dentre os resultados da pesquisa, clique com o botão direito do mouse no item e selecione **Recuperar**.
5. Você também pode navegar pelos diversos pontos de recuperação e selecionar um banco de dados ou item para recuperar. Selecione **Data > Hora da recuperação** e escolha o **Banco de Dados > Farm do SharePoint > Ponto de recuperação > Item** correto.

    ![Proteção do SharePoint do MABS 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique com o botão direito do mouse no item e selecione **Recuperar** para abrir o **Assistente de Recuperação**. Selecione **Avançar**.

    ![Rever Seleção de Recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que você deseja executar e, em seguida, selecione **Avançar**.

    ![Tipo de Recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **Recuperar para o original** no exemplo recupera o item para o site do SharePoint original.
   >
   >
8. Selecione o **Processo de Recuperação** que você deseja usar.

   * Selecione **recuperar sem usar um farm de recuperação** se o farm do SharePoint não tiver sido alterado e for o mesmo que o ponto de recuperação que está sendo restaurado.
   * Selecione **Recuperar usando um farm de recuperação** se o farm do SharePoint tiver sido alterado desde que o ponto de recuperação foi criado.

     ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça um local de preparo da instância do SQL Server para recuperar o banco de dados temporariamente e fornecer um compartilhamento de arquivos de preparo no MABS e no servidor que executa o SharePoint a fim de recuperar o item.

    ![Local de Preparo1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS anexa o banco de dados de conteúdo que está hospedando o item do SharePoint à instância de SQL Server temporária. Do banco de dados de conteúdo, ele recupera o item e o coloca no local do arquivo de preparo no MABS. O item recuperado no local de preparo agora precisa ser exportado para o local de preparo no farm do SharePoint.

    ![Local de Preparo2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecione **Especificar opções de recuperação**e aplique as configurações de segurança ao farm do SharePoint ou aplique as configurações de segurança do ponto de recuperação. Selecione **Avançar**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Você pode optar por limitar o uso de largura de banda da rede. Isso minimiza o impacto no servidor de produção durante o horário de produção.
    >
    >
11. Examine as informações de resumo e, em seguida, selecione **recuperar** para iniciar a recuperação do arquivo.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Agora selecione a guia **Monitoramento** no **Console do Administrador do MABS** para exibir o **Status** da recuperação.

    ![Status da Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Agora o arquivo está restaurado. Você pode atualizar o site do SharePoint para verificar o arquivo restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Restaurar um banco de dados do SharePoint do Azure usando o MABS

1. Para recuperar um banco de dados de conteúdo do SharePoint, navegue pelos vários pontos de recuperação (conforme mostrado acima) e selecione o ponto de recuperação que você deseja recuperar.

    ![Proteção do SharePoint do MABS 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Como o farm do SharePoint está protegido para retenção de longo prazo no Azure, não há nenhuma informação de catálogo (metadados) disponível no servidor MABS. Desta forma, sempre que um banco de dados de conteúdo do SharePoint pontual precisar ser recuperado, será necessário recatalogar o farm do SharePoint.
   >
   >
3. Selecione **recatalogar**.

    ![Proteção do SharePoint do MABS 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de status **Recatalogação de Nuvem** é exibida.

    ![Proteção do SharePoint do MABS 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação ser concluída, o status é alterado para *Êxito*. Selecione **Fechar**.

    ![Proteção do SharePoint do MABS 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecione o objeto do SharePoint mostrado na guia **recuperação** mAbs para obter a estrutura do banco de dados de conteúdo. Clique com o botão direito do mouse no item e selecione **recuperar**.

    ![Proteção do SharePoint do MABS 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nesse ponto, siga as etapas de recuperação descritas anteriormente neste artigo para recuperar um banco de dados de conteúdo do SharePoint do disco.

## <a name="switching-the-front-end-web-server"></a>Alternando o servidor Web front-end

Se você tiver mais de um servidor Web front-end e quiser alternar o servidor que o MABS usa para proteger o farm, siga estas instruções:

O procedimento a seguir usa o exemplo de um farm de servidores com dois servidores Web front-end: *Server1* e *Server2*. O MABS usa *Server1* para proteger o farm. Altere o servidor Web front-end que o MABS usa no *Server2* para que você possa remover o *Server1* do farm.

> [!NOTE]
> Se o servidor Web front-end que o MABS usa para proteger o farm estiver indisponível, use o procedimento a seguir para alterar o servidor Web front-end iniciando na etapa 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Para alterar o servidor Web front-end que o MABS usa para proteger o farm

1. Pare o serviço do Gravador VSS do SharePoint no *Server1* executando o seguinte comando no prompt de comando:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. Em *Server1*, abra o editor do registro e navegue até a seguinte chave:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Verifique todos os valores listados na subchave VssAccessControl. Se alguma entrada tiver um valor de dados 0 e outro gravador VSS estiver sendo executado com as credenciais da conta associada, altere os dados do valor para 1.

1. Instale um agente de proteção no *Server2*.

   > [!WARNING]
   > Você só poderá mudar servidores Web front-end se ambos os servidores estiverem no mesmo domínio.

1. No *Server2*, em um prompt de comando, altere o diretório para `_MABS installation location_\bin\` e execute **ConfigureSharepoint**. Para obter mais informações sobre ConfigureSharePoint, consulte [Configurar backup](#configure-backup).

1. Selecione o grupo de proteção ao qual o farm de servidores pertence e, em seguida, selecione **Modificar Grupo de proteção**.

1. No Assistente para Modificar Grupo, na página **Selecionar membros do grupo**, expanda *Server2*, selecione o farm de servidores e conclua o assistente.

   Uma verificação de consistência será iniciada.

1. Se você executou a etapa 6, agora você pode remover o volume do grupo de proteção.

## <a name="next-steps"></a>Próximas etapas

* Consulte o artigo [Backup files and application](backup-mabs-files-applications-azure-stack.md) (Arquivos e aplicativo de backup).
* Veja o artigo [Backup SQL Server on Azure Stack](backup-mabs-sql-azure-stack.md) (Instância de backup do SQL Server no Azure Stack).

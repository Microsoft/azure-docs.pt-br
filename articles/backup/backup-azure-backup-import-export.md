---
title: Propagar o backup offline com o serviço de importação/exportação do Azure
description: Saiba como você pode usar o backup do Azure para enviar dados da rede usando o serviço de importação/exportação do Azure. Este artigo explica a propagação offline dos dados de backup iniciais usando o serviço de importação/exportação do Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704356"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup offline no Backup do Azure

O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os backups completos iniciais normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas os deltas/incrementais. Durante o processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup offline no Azure.

O processo de propagação offline do backup do Azure está totalmente integrado ao [serviço de importação/exportação do Azure](../import-export/storage-import-export-service.md). Você pode usar esse serviço para transferir dados de backup iniciais para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup inicial que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você poderá usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial, em um ou mais discos rígidos para um datacenter do Azure. A imagem a seguir fornece uma visão geral das etapas no fluxo de trabalho.

  ![Visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve estas etapas:

1. Em vez de enviar os dados de backup pela rede, grave os dados de backup em um local de preparo.
1. Use o utilitário *AzureOfflineBackupDiskPrep* para gravar os dados no local de preparo para um ou mais discos SATA.
1. Como parte do trabalho preparatória, o utilitário *AzureOfflineBackupDiskPrep* cria um trabalho de importação do Azure. Envie as unidades SATA para o datacenter do Azure mais próximo e referencie o trabalho de importação para conectar as atividades.
1. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
1. O Backup do Azure copia os dados de backup da conta de armazenamento para o cofre de Serviços de Recuperação, e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte

Os seguintes recursos ou cargas de trabalho de backup do Azure dão suporte ao uso de backup offline para:

> [!div class="checklist"]
>
> * Backup de arquivos e pastas com o agente de Serviços de Recuperação do Microsoft Azure (MARS), também conhecido como o agente de backup do Azure.
> * Backup de todas as cargas de trabalho e arquivos com o System Center Data Protection Manager (DPM).
> * Backup de todas as cargas de trabalho e arquivos com o Backup do Microsoft Azure Server.

   > [!NOTE]
   > O backup offline não tem suporte para backups de estado do sistema feito usando o agente de backup do Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os pré-requisitos e o fluxo de trabalho a seguir se aplicam somente ao backup offline de arquivos e pastas usando o [agente de serviços de recuperação do Azure mais recente](https://aka.ms/azurebackup_agent). Para executar backups offline para cargas de trabalho usando o System Center DPM ou Servidor de Backup do Azure, consulte [fluxo de trabalho de backup offline para o DPM e servidor de backup do Azure](backup-azure-backup-server-import-export.md).

Antes de iniciar o fluxo de trabalho de backup offline, conclua os seguintes pré-requisitos:

* Crie um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md). Para criar um cofre, siga as etapas em [criar um cofre dos serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Verifique se apenas a [versão mais recente do agente de backup do Azure](https://aka.ms/azurebackup_agent) está instalada no Windows Server ou no cliente Windows, conforme aplicável, e se o computador está registrado no cofre dos serviços de recuperação.
* Azure PowerShell 3.7.0 é necessário no computador que executa o agente de backup do Azure. Baixe e [Instale a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o agente de backup do Azure, verifique se o Microsoft Edge ou o Internet Explorer 11 está instalado e se o JavaScript está habilitado.
* Crie uma conta de armazenamento do Azure na mesma assinatura que o cofre dos serviços de recuperação.
* Verifique se você tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar o aplicativo do Azure Active Directory. O fluxo de trabalho de backup offline cria um aplicativo Azure Active Directory na assinatura associada à conta de armazenamento do Azure. O objetivo do aplicativo é fornecer ao backup do Azure o acesso seguro e com escopo ao serviço de importação/exportação do Azure, que é necessário para o fluxo de trabalho de backup offline.
* Registre o provedor de recursos *Microsoft. ImportExport* com a assinatura que contém a conta de armazenamento do Azure. Para registrar o provedor de recursos:
    1. No menu principal, selecione **assinaturas**.
    1. Se você estiver inscrito em várias assinaturas, selecione a assinatura que você planeja usar para o backup offline. Se você usar apenas uma assinatura, ela será exibida.
    1. No menu assinatura, selecione **provedores de recursos** para exibir a lista de provedores.
    1. Na lista de provedores, role para baixo até *Microsoft. ImportExport*. Se o **status** for não **registrado**, selecione **registrar**.

        ![Registre o provedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)

* Um local de preparo, que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interna ou externa, com espaço em disco suficiente para manter a cópia inicial, é criado. Por exemplo, se você quiser fazer backup de um servidor de arquivos de 500 GB, verifique se a área de preparação tem pelo menos 500 GB. (Uma quantidade menor é usada devido à compactação.)
* Ao enviar discos para o Azure, use somente unidades de disco rígido internas SATA II de 2,5 polegadas ou de 2,5 polegadas ou 3,5 polegadas. Você pode usar discos rígidos de até 10 TB. Verifique a [documentação de serviço de Importação/Exportação do Azure](../import-export/storage-import-export-requirements.md#supported-hardware) para obter o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como *computador de cópia*) de onde é realizada a cópia de dados de backup do local de preparo para as unidades SATA. Verifique se o BitLocker está habilitado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

Esta seção descreve o fluxo de trabalho de backup offline para que seus dados possam ser entregues a um datacenter do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a [documentação visão geral do serviço de importação/exportação do Azure](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar o backup offline

1. Ao agendar um backup no agente dos serviços de recuperação, você verá esta página.

    ![Importar página](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecione a opção **transferir usando meus próprios discos**.

    > [!NOTE]
    > Use a opção Azure Data Box para transferir dados de backup iniciais offline. Essa opção economiza o esforço necessário para adquirir seus próprios discos compatíveis com o Azure. Ele fornece dispositivos de Azure Data Box de propriedade da Microsoft, seguros e à prova de violação para os quais os dados de backup podem ser gravados diretamente pelo agente dos serviços de recuperação.

1. Selecione **Avançar** e preencha as caixas com cuidado.

    ![Insira os detalhes do disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   As caixas que você preenche são:

    * **Local de Preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho de rede completo do local de preparo.
    * **Conta de Armazenamento do Azure Resource Manager**: nome da conta de armazenamento do tipo Resource Manager (uso geral v1 ou uso geral v2) em qualquer assinatura do Azure.
    * **Contêiner de armazenamento do Azure**: o nome do contêiner de armazenamento de blob de destino na conta de armazenamento do Azure em que os dados de backup são importados antes de serem copiados para o cofre dos serviços de recuperação.
    * **ID da Assinatura do Azure**: ID da assinatura do Azure em que a conta de armazenamento do Azure é criada.
    * **Nome do trabalho de importação do Azure**: o nome exclusivo pelo qual o serviço de importação/exportação do Azure e o backup do Azure acompanham a transferência de dados enviados em discos para o Azure.
  
   Depois de preencher as caixas, selecione **Avançar**. Salve o **local de preparo** e as informações de **nome do trabalho de importação do Azure** . É necessário preparar os discos.

1. Quando solicitado, entre em sua assinatura do Azure. Você deve entrar para que o backup do Azure possa criar o aplicativo Azure Active Directory. Insira as permissões necessárias para acessar o serviço de importação/exportação do Azure.

    ![Página de entrada da assinatura do Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Conclua o fluxo de trabalho. No console do agente de backup do Azure, selecione **fazer backup agora**.

    ![Fazer Backup Agora](./media/backup-azure-backup-import-export/backupnow.png)

1. Na página **confirmação** do assistente, selecione **fazer backup**. O backup inicial é gravado na área de preparo como parte da instalação.

   ![Confirme que você está pronto para fazer backup agora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Após a conclusão da operação, o local de preparo está pronto para ser usado na preparação do disco.

   ![Página de assistente fazer backup agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas ao datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente de backup do Azure no seguinte caminho:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Vá para o diretório e copie o diretório *AzureOfflineBackupDiskPrep* para outro computador em que as unidades SATA estão conectadas. No computador com as unidades SATA conectadas, verifique se:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na seção "Iniciar backup offline".
   * O BitLocker está habilitado no computador de cópia.
   * O Azure PowerShell 3.7.0 está instalado.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está habilitado.
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, o computador de cópia deverá ser um computador cliente ou servidor físico diferente do computador de origem.

1. Abra um prompt de comando com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o comando a seguir:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |Essa entrada obrigatória é usada para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |Essa entrada opcional é usada para fornecer o caminho para o arquivo de configurações de publicação do Azure.  |

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparo fornecido, você verá uma página como esta.

    ![Entrada da ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Insira a letra da unidade sem os dois pontos à direita para o disco montado que você deseja preparar para a transferência do Azure.
1. Forneça a confirmação para a formatação da unidade quando solicitado.
1. Você será solicitado a entrar em sua assinatura do Azure. Insira suas credenciais.

    ![Entrada da assinatura do Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Em seguida, a ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar discos adicionais quando solicitado pela ferramenta se o disco fornecido não tiver espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornece três informações principais:

   1. Um ou mais discos que você forneceu estão preparados para envio ao Azure.
   1. Você recebe a confirmação de que o trabalho de importação foi criado. O trabalho de importação usa o nome fornecido.
   1. A ferramenta exibe o endereço de envio para o datacenter do Azure.

      ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/console2.png)<br/>

1. No final da execução do comando, você pode atualizar as informações de envio.

1. Envie os discos para o endereço fornecido pela ferramenta. Mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Dois trabalhos de importação do Azure não podem ter o mesmo número de acompanhamento. Verifique se as unidades preparadas pelo utilitário em um único trabalho de importação do Azure são enviadas juntas em um único pacote e se há um único número de controle exclusivo para o pacote. Não combine unidades preparadas como parte de trabalhos de importação do Azure separados em um único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes de envio no trabalho de importação do Azure

O procedimento a seguir atualiza os detalhes de envio do trabalho de importação do Azure. Essas informações incluem detalhes sobre:

* O nome da transportadora que entrega os discos para o Azure.
* Retornar detalhes de envio para seus discos.

1. Entre na sua assinatura do Azure.
1. No menu principal, selecione **todos os serviços**. Na caixa de diálogo **todos os serviços** , digite **importar**. Quando você vir **trabalhos de importação/exportação**, selecione-o.

    ![Inserir informações de remessa](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    O menu **importar/exportar trabalhos** é aberto e a lista de todos os trabalhos de importação/exportação na assinatura selecionada é exibida.

1. Se você tiver várias assinaturas, selecione a assinatura usada para importar os dados de backup. Em seguida, selecione o trabalho de importação criado recentemente para abrir seus detalhes.

    ![Examinar informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. No menu **configurações** do trabalho de importação, selecione **gerenciar informações de envio**. Insira os detalhes de envio de retorno.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando você tiver o número de rastreamento de sua operadora de entrega, selecione a faixa na página Visão geral do trabalho de importação do Azure e insira os detalhes a seguir.

   > [!IMPORTANT]
   > Verifique se as informações da transportadora e o número de rastreamento são atualizados dentro de duas semanas após a criação do trabalho de importação do Azure. A falha de verificação dessas informações dentro de duas semanas pode resultar na exclusão do trabalho e nas unidades não processadas.

   ![Alerta de atualização de informações de acompanhamento](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informações da operadora e número de rastreamento](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades

A quantidade de tempo que leva para processar um trabalho de importação do Azure varia. O tempo de processamento é baseado em fatores como tempo de envio, tipo de trabalho, tipo e tamanho dos dados que estão sendo copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA. Depois que os discos são recebidos, o serviço se esforça para concluir a cópia de dados de backup para sua conta de armazenamento do Azure em 7 a 10 dias.

### <a name="monitor-azure-import-job-status"></a>Monitorar o status do trabalho de importação do Azure

Você pode monitorar o status do trabalho de importação na portal do Azure. Vá para a página **trabalhos de importação/exportação** e selecione seu trabalho. Para obter mais informações sobre o status dos trabalhos de importação, consulte [o que é o serviço de importação/exportação do Azure?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Concluir o fluxo de trabalho

Depois que o trabalho de importação for concluído com êxito, os dados de backup inicias estarão disponíveis em sua conta de armazenamento. No momento do próximo backup agendado, o backup do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos serviços de recuperação.

   ![Copiar dados para o cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executará um backup incremental.

### <a name="clean-up-resources"></a>Limpar os recursos

Depois que o backup inicial for concluído, você poderá excluir com segurança os dados importados para o contêiner de armazenamento do Azure e os dados de backup no local de preparo.

## <a name="next-steps"></a>Próximas etapas

* Para dúvidas sobre o fluxo de trabalho do serviço de importação/exportação do Azure, consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../import-export/storage-import-export-service.md).

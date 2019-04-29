---
title: Backup do Azure - backup offline para DPM e Servidor de Backup do Azure
description: Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de Importação/Exportação do Azure. Este artigo explica a propagação offline de dados de backup iniciais usando o serviço de Importação/Exportação do Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 8d15eb03055aed32c8a99121b750ee5767a87b50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647880"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho do backup offline do DPM e do Servidor de Backup do Azure
O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. O Backup do Azure compacta os backups inicias. O processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup iniciais compactados de forma offline no Azure.

O processo de propagação offline do Backup do Azure está totalmente integrado com o [serviço Importação/Exportação do Azure](../storage/common/storage-import-export-service.md) , que permite transferir dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup iniciais que precisam ser transferidos por meio de uma rede de alta latência e baixa largura de banda, pode usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um data center do Azure. Este artigo fornece uma visão geral e mais detalhes de etapas que concluem este fluxo de trabalho para o DPM do System Center e Servidor de Backup do Azure.

> [!NOTE]
> O processo de backup Offline para o agente do Microsoft Azure Recovery Services (MARS) é diferente do DPM do System Center e o servidor de Backup do Azure. Para obter informações sobre como usar o backup offline com o agente de MARS, consulte [neste artigo](backup-azure-backup-import-export.md). Não há suporte para Backup Offline para backups de Estado do Sistema realizados usando o agente de Backup do Azure. 
>

## <a name="overview"></a>Visão geral
Com a capacidade de propagação offline do Backup do Azure e a Importação/Exportação do Azure, é simples carregar os dados offline no Azure usando discos. O processo de Backup Offline envolve as etapas a seguir:

> [!div class="checklist"]
> * Os dados de backup, em vez de serem enviados pela rede, são gravados em um *local de preparo*
> * Os dados no *local de preparo* são gravados em um ou mais discos SATA usando o utilitário *AzureOfflineBackupDiskPrep*
> * Um trabalho de Importação do Azure é criado automaticamente pelo utilitário
> * As unidades SATA são enviadas para o datacenter do Azure mais próximo
> * Depois que o upload dos dados de backup no Azure for concluído, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte 
Há suporte para o Backup Offline para todos os modelos de implantação de Backup do Azure que exportam dados de backup de locais para o Microsoft Cloud. Isso inclui

> [!div class="checklist"]
> * Backup de arquivos e pastas com o agente de Serviços de Recuperação do Microsoft Azure (MARS) ou o agente de Backup do Azure. 
> * Backup de todas as cargas de trabalho e arquivos com o System Center Data Protection Manager (SC DPM) 
> * Backup de todas as cargas de trabalho e arquivos com o Servidor de Backup do Azure <br/>

## <a name="prerequisites"></a>Pré-requisitos
Verifique se os pré-requisitos a seguir foram atendidos antes de iniciar o fluxo de trabalho de Backup Offline
* Um [cofre de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, consulte as etapas [neste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* O agente de Backup do Azure ou o Servidor de Backup do Azure ou SC DPM foi instalado no Windows Server/cliente do Windows conforme aplicável e o computador está registrado no Cofre de Serviços de Recuperação do Azure. Verifique se apenas a [versão mais recente do Backup do Azure](https://go.microsoft.com/fwlink/?linkid=229525) é usada. 
* [Faça download do arquivo de configurações de Publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador por meio do qual você planeja fazer backup dos seus dados. A assinatura da qual você fizer o download do arquivo de configurações de publicação pode ser diferente da assinatura que contém o Cofre de Serviços de Recuperação. Se sua assinatura estiver em Azure Clouds soberanas, use os links a seguir conforme for apropriado para fazer o download do arquivo de configurações de Publicação do Azure.

    | Região da nuvem soberana | Link do arquivo de configurações de Publicação do Azure |
    | --- | --- |
    | Estados Unidos | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Uma conta de Armazenamento do Microsoft Azure com modelo de implantação *clássico* foi criada na assinatura da qual você fez o download do arquivo de configurações de publicação conforme mostrado abaixo: 

  ![Criar uma conta de armazenamento clássica](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Um local de preparo, o que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criado. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparo tenha pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Com relação a discos que serão enviados para o Azure, certifique-se de que apenas unidades de disco rígido internas SSD de 2,5 polegadas ou SATA II/III de 2,5 ou 3,5 polegadas sejam usados. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como um *computador de cópia*) de onde a cópia de dados de backup do *local de preparo* para unidades SATA é feita. Verifique se o BitLocker está habilitado no *computador de cópia* 

## <a name="workflow"></a>Fluxo de trabalho
As informações desta seção ajudam você a concluir o fluxo de trabalho de backup offline para que os dados possam ser entregues em um datacenter do Azure e carregados no Armazenamento do Azure. Se você tem dúvidas sobre o serviço de Importação ou qualquer aspecto do processo, confira a documentação sobre a [Visão geral do serviço de importação](../storage/common/storage-import-export-service.md) indicada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar o backup offline
1. Quando você agenda um backup, vê a tela a seguir (no Windows Server, no cliente do Windows ou no System Center Data Protection Manager).

    ![Tela de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Esta é a tela correspondente no System Center Data Protection Manager:  <br/>
    ![Tela de importação do SC DPM e do servidor de Backup do Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é a seguinte:

   * **Local de preparo**: O local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem são diferentes, é recomendável especificar o caminho completo de rede do local de preparo.
   * **Nome do trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço Importação do Azure e o Backup do Azure controlam a transferência de dados enviados em discos no Azure.
   * **Configurações de Publicação do Azure**: Forneça o caminho local para o arquivo de configurações de publicação.
   * **ID de assinatura do Azure**: A ID de assinatura do Azure para a assinatura da qual você fez o download do arquivo de configurações da Publicação do Azure. 
   * **Conta de Armazenamento do Azure**: O nome da conta de armazenamento na assinatura do Azure associada ao arquivo de configurações de Publicação do Azure.
   * **Contêiner do Armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure onde os dados de backup são importados.

     Salve o *local de preparo* e o *Nome do Trabalho de Importação do Azure* fornecidos, pois eles são necessários para preparar os discos.  
     
2. Conclua o fluxo de trabalho e selecione a cópia de backup offline, clique em **Fazer Backup Agora** no console de gerenciamento do Backup do Azure. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no System Center Data Protection Manager ou no servidor de Backup do Azure, clique com o botão direito do mouse no **Grupo de Proteção** e escolha a opção **Criar ponto de recuperação**. Então, você escolhe a opção **Proteção online** .

    ![Fazer backup do SC DPM e do servidor de Backup do Azure agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

    ![Andamento do backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O utilitário *AzureOfflineBackupDiskPrep* é usado para preparar as unidades SATA que são enviadas para o Datacenter do Azure mais próximo. Este utilitário está disponível no diretório de instalação do agente de Serviços de Recuperação no caminho a seguir:

   *\Microsoft* *Azure* *Recovery* *Services* \*Agent\Utils\*

1. Vá até o diretório e copie o diretório **AzureOfflineBackupDiskPrep** para um computador de cópia no qual as unidades SATA a serem preparadas estejam conectadas. Verifique os itens a seguir em relação ao computador de cópia:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede fornecido no fluxo de trabalho de **Iniciar o backup offline** .
   * O BitLocker está habilitado no computador de cópia.
   * O computador de cópia pode acessar o portal do Azure.

     Se necessário, o computador de cópia também pode ser o computador de origem. 
    
     > [!IMPORTANT] 
     > Se o computador de origem for uma máquina virtual, é obrigatório usar um servidor físico diferente ou um computador cliente como o computador de cópia.
    
    
2. Abra um prompt de comandos com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual e execute o comando a seguir:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |A entrada obrigatória usada para fornecer o caminho para o local de preparo inserido no fluxo de trabalho de **Iniciar o backup offline** . |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |A entrada opcional usada para fornecer o caminho para o arquivo **Configurações de Publicação do Azure** inserido no fluxo de trabalho de **Iniciar o backup offline**. |

    > [!NOTE]
    > O valor &lt;Caminho para AzurePublishSettingFile&gt; será obrigatório quando o computador de cópia e o computador de origem forem diferentes.
    >
    >

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de Importação do Azure que corresponde às unidades que precisam ser preparadas. Se houver somente um único trabalho de importação associado ao local de preparo fornecido, será uma tela como a mostrada a seguir.

    ![Entrada da ferramenta de preparação do disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure. Confirme a formatação da unidade quando solicitado.

    A ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar mais discos quando solicitado pela ferramenta, caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, um ou mais discos que você forneceu estarão preparados para envio no Azure. Além disso, um trabalho de importação com o nome fornecido durante o fluxo de trabalho **Iniciar backup offline** é criado no Azure. Por fim, a ferramenta exibe o endereço de entrega para o datacenter do Azure para onde os discos precisam ser enviados.

    ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. No final da execução do comando você também vê a opção para atualizar as informações de envio, conforme mostrado abaixo:

    ![Opção Atualizar informações de envio](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Você pode inserir os detalhes imediatamente. A ferramenta orienta você pelo processo que envolve uma série de entradas. No entanto, se não tiver informações como o número de rastreio ou outros detalhes relacionados ao envio, você pode encerrar a sessão. As etapas para atualizar os detalhes do envio posteriormente são fornecidas neste artigo. 

6. Envie os discos para o endereço fornecido pela ferramenta e mantenha o número de controle para referência futura.

   > [!IMPORTANT] 
   > Dois Trabalhos de Importação do Azure não podem ter o mesmo número de rastreio. Certifique-se de que as unidades preparadas pelo utilitário em um único Trabalho de Importação do Azure são enviadas juntas em um único pacote e se há um número de rastreio exclusivo para o pacote. Não combine unidades preparadas como parte de **diferentes** Trabalhos de Importação do Azure em um único pacote. 

5. Quando tiver informações do número de rastreio, vá ao computador de origem, que está aguardando a conclusão do Trabalho de Importação, e execute o prompt de comandos com privilégios elevados a seguir com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcionalmente, você pode executar o comando a seguir de um computador diferente, como o *computador de cópia*, com o diretório de utilitário *AzureOfflineBackupDiskPrep* como o diretório atual:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | u: | Entrada obrigatória usada para atualizar os detalhes de envio para um trabalho de Importação do Azure |
    | s:&lt;*Caminho do Local de Preparo*&gt; | Entrada obrigatória quando o comando não é executado no computador de origem. Usada para fornecer o caminho para o local de preparo inserido no fluxo de trabalho **Iniciar o backup offline**. |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; | Entrada obrigatória quando o comando não é executado no computador de origem. Usada para fornecer o caminho para o arquivo **Configurações de Publicação do Azure** inserido no fluxo de trabalho **Iniciar o backup offline**. |
    
    O utilitário detecta automaticamente o trabalho de Importação que o computador de origem está aguardando ou os trabalhos de importação associados ao local de preparo quando o comando é executado em um computador diferente. Ele fornece a opção de atualizar as informações de envio por meio de uma série de entradas, conforme mostrado abaixo: 
    
    ![Inserir informações de envio](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Depois que todas as entradas forem fornecidas, revise os detalhes com cuidado e confirme as informações de envio fornecidas digitando *yes*. 

    ![Revisar informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Com a atualização de informações de envio bem-sucedida, o utilitário fornece uma localização local onde os detalhes de envio inseridos por você são armazenados como mostrado abaixo 

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Certifique-se de que as unidades alcancem o Datacenter do Azure dentro de duas semanas do fornecimento das informações de envio usando o utilitário *AzureOfflineBackupDiskPrep*. A falha em fazer isso pode resultar no não processamento das unidades.  

Depois de concluir as etapas acima, o Datacenter do Azure está pronto para receber as unidades e processá-las ainda mais para transferir os dados de backup de unidades para a conta de armazenamento clássico do Azure que você criou. 

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades 
O tempo necessário para processar um trabalho de importação do Azure varia de acordo com diversos fatores, como tempo de envio, tipo do trabalho, tipo e tamanho dos dados sendo copiados, e tamanho dos discos fornecidos. O serviço de Importação/Exportação do Azure não tem um SLA, mas depois que os discos forem recebidos, o serviço se esforçará para concluir a cópia dos dados de backup para sua conta de armazenamento do Azure no prazo de 7 a 10 dias. A próxima seção fornece detalhes sobre como você pode monitorar o status do trabalho de importação do Azure. 

### <a name="monitoring-azure-import-job-status"></a>Monitorar o status do Trabalho de Importação do Azure
Enquanto as unidades estão em trânsito ou no data center do Azure a ser copiado para a conta de armazenamento, o agente de Backup do Azure ou o SC DPM ou o console de servidor de Backup do Azure no computador de origem mostra o status do trabalho a seguir para backups agendados. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Siga as etapas abaixo para verificar o status do Trabalho de Importação. 
1. Abra um prompt de comando elevado no computador de origem e execute o seguinte comando:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  A saída mostra o status atual do Trabalho de Importação, conforme mostrado abaixo: 

    ![Verificar o Status do Trabalho de Importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os diversos estados do trabalho de importação do Azure, consulte [este artigo](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Após a conclusão da importação, os dados de backup inicias estarão disponíveis na sua conta de armazenamento. No horário do próximo backup agendado, o backup do Azure copia os conteúdos dos dados da conta de armazenamento para o cofre de Serviços de Recuperação, conforme mostrado abaixo: 

   ![Copiar dados para o Cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executa o backup incremental sobre a cópia de backup inicial.

## <a name="next-steps"></a>Próximas etapas
* Para qualquer dúvida sobre o fluxo de trabalho de Importação/Exportação do Azure, veja [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o armazenamento de Blobs](../storage/common/storage-import-export-service.md).
* Consulte a seção sobre o backup offline das [perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure se tiver dúvidas sobre o fluxo de trabalho.

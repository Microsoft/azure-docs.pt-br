---
title: Backup do Azure - backup offline ou propagação inicial usando o serviço de Importação/Exportação do Azure
description: Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de Importação/Exportação do Azure. Este artigo explica a propagação offline de dados de backup iniciais usando o serviço de Importação/Exportação do Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: b6f0ce1939b2a78ca191d2feb0140506d130b9b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648186"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup offline no Backup do Azure
O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. Durante o processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup offline no Azure.

O processo de propagação offline do Backup do Azure está totalmente integrado ao [serviço de Importação/Exportação do Azure](../storage/common/storage-import-export-service.md) que permite a transferência de dados de backup iniciais para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup iniciais que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, será possível usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um datacenter do Azure. A imagem a seguir fornece uma visão geral das etapas no fluxo de trabalho.

  ![Visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve as seguintes etapas:

1. Em vez de enviar os dados de backup pela rede, grave os dados de backup em um local de preparo.
2. Use o utilitário AzureOfflineBackupDiskPrep para gravar os dados no local de preparo em um ou mais discos SATA.
3. Como parte do trabalho preparatório, o utilitário AzureOfflineBackupDiskPrep cria um trabalho de Importação do Azure. Envie as unidades SATA para o datacenter do Azure mais próximo e referencie o trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O Backup do Azure copia os dados de backup da conta de armazenamento para o cofre de Serviços de Recuperação, e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte 
Os seguintes recursos ou cargas de trabalho do Backup do Azure são compatíveis com o uso do Backup Offline.

> [!div class="checklist"]
> * O backup de arquivos e pastas com o agente do MARS (Serviços de Recuperação do Microsoft Azure), também conhecido como agente de Backup do Azure. 
> * Backup de todas as cargas de trabalho e arquivos com o System Center Data Protection Manager (SC DPM) 
> * Backup de todas as cargas de trabalho e arquivos com o Servidor de Backup do Azure <br/>

   > [!NOTE]
   > Não há suporte para Backup Offline para backups de Estado do Sistema realizados usando o agente de Backup do Azure. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxo de trabalho aplicam-se somente ao backup offline de arquivos e pastas usando o [agente mais recente do MARS](https://aka.ms/azurebackup_agent). Para executar backups offline para cargas de trabalho que usam o DPM do System Center ou Servidor de Backup do Azure, confira [este artigo](backup-azure-backup-server-import-export-.md). 

Antes de iniciar o fluxo de trabalho de Backup Offline, execute os seguintes pré-requisitos: 
* Crie um [Cofre de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md). Para criar um cofre, veja as etapas [neste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Verifique se apenas a [versão mais recente do agente de Backup do Azure](https://aka.ms/azurebackup_agent) foi instalada no cliente Windows Server/Windows, conforme se aplique, e se o computador está registrado no Cofre de Serviços de Recuperação.
* O Azure PowerShell 3.7.0 é necessário no computador que executa o agente de Backup do Azure. Recomendamos o download e a instalação da [versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o agente de Backup do Azure, verifique se o Microsoft Edge ou o Internet Explorer 11 está instalado e se o JavaScript está habilitado. 
* Crie uma conta de Armazenamento do Azure na mesma assinatura que o cofre de Serviços de Recuperação. 
* Verifique se você tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar o aplicativo do Azure Active Directory. O fluxo de trabalho de Backup Offline cria um aplicativo do Azure Active Directory na assinatura associada à conta de Armazenamento do Azure. A meta do aplicativo é fornecer ao Backup do Azure o acesso seguro e com escopo para o serviço de Importação do Azure, necessário para o fluxo de trabalho de Backup Offline. 
* Registre o provedor de recursos Microsoft.ImportExport na assinatura que contém a conta de Armazenamento do Azure. Para registrar o provedor de recursos:
    1. No menu principal, clique em **Assinaturas**.
    2. Se você tiver várias assinaturas, selecione a assinatura que está usando para o backup offline. Se você usa apenas uma assinatura, essa assinatura será exibida.
    3. No menu de assinatura, clique em **Provedores de Recursos** para exibir a lista de provedores.
    4. Na lista de provedores role para baixo até Microsoft.ImportExport. Se o Status for NotRegistered, clique em **Registrar**.
    ![Registrando o provedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)
* Um local de preparo, o que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criado. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparo tenha pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Ao enviar discos para o Azure, use apenas SSD de 2,5 polegadas ou discos rígidos internos SATA II/III de 2,5 ou 3,5 polegadas. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como *computador de cópia*) de onde é realizada a cópia de dados de backup do *local de preparo* para as unidades SATA. Verifique se o BitLocker está habilitado no *computador de cópia*.

## <a name="workflow"></a>Fluxo de trabalho
Esta seção descreve o fluxo de trabalho de backup offline para que os dados possam ser entregues a um datacenter do Azure e carregados no Armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou sobre qualquer aspecto do processo, confira a [Documentação de visão geral do serviço de importação](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar o backup offline
1. Ao agendar um backup no Agente do MARS, você verá a tela a seguir.

    ![Tela de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   A descrição das entradas é a seguinte:

    * **Local de preparo**: O local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem são diferentes, é recomendável especificar o caminho completo de rede do local de preparo.
    * **Conta de Armazenamento do Azure Resource Manager**: O nome da conta de armazenamento de tipo do Resource Manager, em qualquer assinatura do Azure.
    * **Contêiner do Armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de Armazenamento do Azure em que os dados de backup são importados antes de serem copiados para o cofre de Serviços de Recuperação.
    * **ID de assinatura do Azure**: A ID da assinatura do Azure em que a conta de Armazenamento do Azure é criada.
    * **Nome do trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço Importação do Azure e o Backup do Azure controlam a transferência de dados enviados em discos no Azure. 
  
   Forneça as entradas na tela e clique em **Avançar**. Salve o *Local de preparo* e o *Nome do Trabalho de Importação do Azure* fornecidos, pois essas informações são necessárias para preparar os discos.

2. Quando solicitado, entre em sua assinatura do Azure. Você precisa entrar para que o Backup do Azure possa criar o aplicativo do Azure Active Directory e fornecer as permissões necessárias para acessar o serviço de Importação do Azure.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/azurelogin.png)

3. Conclua o fluxo de trabalho e, em seguida, no console do agente de Backup do Azure, clique em **Fazer Backup Agora**.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

4. Na página de confirmação do assistente, clique em **Fazer Backup**. O backup inicial é gravado na área de preparo como parte da instalação.

   ![Confirme que você está pronto para fazer o Backup agora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

   ![Fazer backup agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente de Backup do Azure (no caminho a seguir):

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Acesse o diretório e copie o diretório **AzureOfflineBackupDiskPrep** para outro computador no qual as unidades SATA estão conectadas. No computador com as unidades SATA conectadas, verifique se:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede fornecido no fluxo de trabalho de **Iniciar o backup offline** .
   * O BitLocker está habilitado no computador de cópia.
   * O Azure PowerShell 3.7.0 está instalado.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e se o JavaScript está habilitado. 
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia também pode ser o computador de origem.
    
     > [!IMPORTANT] 
     > Se o computador de origem for uma máquina virtual, o computador de cópia precisará ser um servidor ou um computador cliente físico do computador de origem.

2. Abra um prompt de comandos com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual e execute o comando a seguir:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |A entrada obrigatória usada para fornecer o caminho para o local de preparo inserido no fluxo de trabalho de **Iniciar backup offline**. |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |A entrada opcional usada para fornecer o caminho para o arquivo **Configurações de Publicação do Azure** inserido no fluxo de trabalho de **Iniciar o backup offline**. |

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de Importação do Azure que corresponde às unidades que precisam ser preparadas. Se houver somente um único trabalho de importação associado ao local de preparo fornecido, será uma tela como a mostrada a seguir.

    ![Entrada da ferramenta de preparação do disco do Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure. 
4. Confirme a formatação da unidade quando solicitado.
5. Você precisará entrar em sua assinatura do Azure. Forneça suas credenciais.

    ![Entrada da ferramenta de preparação do disco do Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    A ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar mais discos quando solicitado pela ferramenta, caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornecerá três tipos de informação:
   1. Um ou mais discos que você forneceu estão preparados para o envio ao Azure. 
   2. O recebimento da confirmação de que o trabalho de importação foi criado. O trabalho de importação usa o nome fornecido.
   3. A ferramenta exibe o endereço para entrega do datacenter do Azure.

      ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/console2.png)<br/>

6. No final da execução do comando, você pode atualizar as informações de envio.

7. Envie os discos para o endereço fornecido pela ferramenta e mantenha o número de controle para referência futura.

   > [!IMPORTANT] 
   > Dois Trabalhos de Importação do Azure não podem ter o mesmo número de rastreio. Verifique se as unidades preparadas pelo utilitário em um único trabalho de Importação do Azure são enviadas juntas em um único pacote e se há um número de controle exclusivo para o pacote. Não combine as unidades preparadas como parte de trabalhos de Importação do Azure separados em um único pacote.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes do envio no trabalho de Importação do Azure

O procedimento a seguir atualiza os detalhes de envio do trabalho de Importação do Azure. Essas informações incluem detalhes sobre:
* o nome da transportadora que fornece os discos para o Azure
* detalhes de envio de devolução para os discos

1. Entre em sua assinatura do Azure.
2. No menu principal, clique em **Todos os serviços** e, na caixa de diálogo Todos os serviços, digite Importação. Quando a opção **Trabalhos de Importação/Exportação** aparecer, clique nela.
    ![Inserindo informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    A lista do menu **Trabalhos de importação/exportação** é aberta mostrando todos os trabalhos de importação/exportação na assinatura selecionada. 

3. Se você tiver várias assinaturas, selecione a assinatura usada para importar os dados de backup. Em seguida, selecione o trabalho de importação recém-criado para abrir seus detalhes.

    ![Revisar informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. No menu Configurações do trabalho de importação, clique em **Gerenciar Informações de Envio** e insira os detalhes de envio de devolução.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Quando você tiver o número de controle da transportadora, clique na faixa na página de visão geral do trabalho de Importação do Azure e digite os seguintes detalhes:

   > [!IMPORTANT] 
   > Verifique se as informações da transportadora e o número de controle são atualizados em até duas semanas após a criação do trabalho de importação do Azure. Se essas informações não forem verificadas em até duas semanas, o trabalho poderá ser excluído e as unidades poderão não ser processadas.

   ![Armazenar informações de envio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Armazenar informações de envio](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades 
O tempo necessário para processar um trabalho de importação do Azure varia de acordo com diversos fatores, como tempo de envio, tipo do trabalho, tipo e tamanho dos dados sendo copiados, e tamanho dos discos fornecidos. O serviço de Importação/Exportação do Azure não tem um SLA, mas depois que os discos forem recebidos, o serviço se esforçará para concluir a cópia dos dados de backup para sua conta de armazenamento do Azure no prazo de 7 a 10 dias. 

### <a name="monitoring-azure-import-job-status"></a>Monitorar o status do Trabalho de Importação do Azure
Você pode monitorar o status do trabalho de importação no portal do Azure navegando até a página **Trabalhos de Importação/Exportação** e selecionando o trabalho. Para obter mais informações sobre o status dos trabalhos de importação, confira o artigo [Serviço de Importação e Exportação de Armazenamento](../storage/common/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois que o trabalho de importação for concluído com êxito, os dados de backup inicias estarão disponíveis em sua conta de armazenamento. No horário do próximo backup agendado, o Backup do Azure copiará o conteúdo dos dados da conta de armazenamento para o cofre de Serviços de Recuperação, conforme é mostrado abaixo: 

   ![Copiar dados para o Cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executará um backup incremental.

### <a name="cleaning-up-resources"></a>Limpando recursos
Depois que o backup inicial for concluído, você poderá excluir com segurança os dados importados para o Contêiner de Armazenamento do Azure e os dados de backup no local de preparo.

## <a name="next-steps"></a>Próximas etapas
* Para qualquer dúvida sobre o fluxo de trabalho de Importação/Exportação do Azure, veja [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o armazenamento de Blobs](../storage/common/storage-import-export-service.md).
* Consulte a seção sobre o backup offline das [perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure se tiver dúvidas sobre o fluxo de trabalho.

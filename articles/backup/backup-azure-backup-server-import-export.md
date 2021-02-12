---
title: Backup offline do DPM e do Servidor de Backup do Azure
description: Com o backup do Azure, você pode enviar dados da rede usando o serviço de importação/exportação do Azure. Este artigo explica o fluxo de trabalho de backup offline para o DPM e o Servidor de Backup do Azure.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701806"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Fluxo de trabalho de backup offline para o DPM e o Servidor de Backup do Azure (MABS)

>[!IMPORTANT]
> Essas etapas são aplicáveis para o DPM 2019 UR1 (ou superior) e o MABS v3 UR1 (ou superior).

O System Center Data Protection Manager e o Servidor de Backup do Azure (MABS) se integram ao backup do Azure e usam várias eficiências internas que economizam os custos de rede e armazenamento durante os backups completos iniciais dos dados no Azure. Os backups completos iniciais normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas os deltas/incrementais. O backup do Azure compacta os backups iniciais. Com o processo de propagação offline, o backup do Azure pode usar discos para carregar os dados de backup offline inicial compactado no Azure.

O processo de propagação offline do backup do Azure é totalmente integrado ao serviço de [importação/exportação do Azure](../import-export/storage-import-export-service.md). Você pode usar esse serviço para transferir dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup inicial que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você poderá usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um datacenter do Azure. Este artigo fornece uma visão geral e etapas adicionais que concluem esse fluxo de trabalho para o System Center Data Protection Manager (DPM) e o Backup do Microsoft Azure Server (MABS).

> [!NOTE]
> O processo de backup offline para o agente de Serviços de Recuperação do Microsoft Azure (MARS) é diferente do DPM e do MABS. Para obter informações sobre como usar o backup offline com o agente MARS, consulte [fluxo de trabalho de backup offline no backup do Azure](backup-azure-backup-import-export.md). O backup offline não tem suporte para backups de estado do sistema feito usando o agente de backup do Azure.
>
> A atualização MABS UR1 também traz a visualização para backup offline usando o Azure Data Box no MABS. Entre em contato [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) para saber mais.

## <a name="overview"></a>Visão geral

Com a capacidade de propagação offline do backup do Azure e o serviço de importação/exportação do Azure, é simples carregar os dados offline no Azure usando discos. O processo de backup offline envolve as seguintes etapas:

> [!div class="checklist"]
>
> * Os dados de backup são gravados em um local de preparo em vez de serem enviados pela rede.
> * Os dados no local de preparo são gravados em um ou mais discos SATA usando o utilitário *AzureOfflineBackupDiskPrep* .
> * Um trabalho de importação do Azure é criado automaticamente pelo utilitário.
> * As unidades SATA são então enviadas para o datacenter do Azure mais próximo.
> * Após o término do carregamento dos dados de backup no Azure, o Backup do Azure copia esses dados no cofre de backup e programa o agendamento dos backups incrementais.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se os pré-requisitos a seguir foram atendidos antes de iniciar o fluxo de trabalho de backup offline:

* Um [Cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, siga as etapas em [criar um cofre de serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)tutorial-backup-Windows-Server-to-Azure # CREATE-a-Recovery-Services-Vault).
* Verifique se apenas a [versão mais recente do agente de serviços de recuperação do Microsoft Azure](https://aka.ms/azurebackup_agent) está instalada no SC DPM ou mAbs e registrada no cofre dos serviços de recuperação.
* O pacote cumulativo de atualizações 1 está instalado no SC DPM 2019 ou MABS v3.

  > [!NOTE]
  > Com o DPM 2019 UR1 e MABS v3 UR1, a propagação offline é autenticada usando Azure Active Directory.

* No servidor DPM ou MABS, verifique se o Microsoft Edge ou o Internet Explorer 11 está instalado e se o JavaScript está habilitado.
* Crie uma conta de Armazenamento do Azure na mesma assinatura que o cofre dos Serviços de Recuperação.
* Verifique se você tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar o aplicativo do Azure Active Directory. O fluxo de trabalho do Backup Offline cria um aplicativo do Azure Active Directory na assinatura associada à conta de Armazenamento do Azure. O objetivo do aplicativo é fornecer o Backup do Azure com acesso seguro e escopo definido ao Serviço de Importação do Azure, necessário para o fluxo de trabalho de Backup Offline.
* Registre o provedor de recursos Microsoft.ImportExport com a assinatura que possui a conta de Armazenamento do Azure. Para registrar o provedor de recursos:
    1. No menu principal, selecione **assinaturas**.
    2. Se você estiver inscrito em várias assinaturas, selecione a assinatura que você está usando para o backup offline. Se você usar apenas uma assinatura, ela será exibida.
    3. No menu assinatura, selecione **provedores de recursos** para exibir a lista de provedores.
    4. Na lista de provedores, role para baixo até Microsoft.ImportExport. Se o status for não registrado, selecione **registrar**.

       ![Registrando o provedor de recursos](./media/backup-azure-backup-server-import-export/register-import-export.png)

* Um local de preparo, que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interna ou externa, com espaço em disco suficiente para manter a cópia inicial, é criado. Por exemplo, se você quiser fazer backup de um servidor de arquivos de 500 GB, verifique se a área de preparação tem pelo menos 500 GB. (Uma quantidade menor é usada devido à compactação.)
* Para discos enviados para o Azure, certifique-se de que apenas discos rígidos internos de SSD de 2,5 polegadas ou de 2,5 polegadas ou 3,5 pol. SATA II/III sejam usados. Você pode usar discos rígidos de até 10 TB. Verifique a [documentação de serviço de Importação/Exportação do Azure](../import-export/storage-import-export-requirements.md#supported-hardware) para obter o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como *computador de cópia*) de onde é realizada a cópia de dados de backup do local de preparo para as unidades SATA. Verifique se o BitLocker está habilitado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

As informações nesta seção ajudam você a concluir o fluxo de trabalho de backup offline, para que seus dados possam ser entregues a um datacenter do Azure e carregados no Armazenamento do Azure. Se tiver dúvidas sobre o Serviço de importação ou qualquer aspecto do processo, confira a documentação da [Visão geral do serviço de importação](../import-export/storage-import-export-service.md) mencionada anteriormente.

## <a name="initiate-offline-backup"></a>Iniciar o backup offline

1. Ao criar um novo grupo de proteção com proteção online ou adicionar proteção online ao grupo existente, você verá a tela a seguir. Para selecionar o método de replicação online inicial, selecione **transferir usando meu próprio disco** e selecione **Avançar**.

    ![Tela de importação](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. A página de entrada do Azure será aberta. Entre usando sua conta de usuário do Azure, que tem a permissão de função de *proprietário* na assinatura do Azure.

    ![Página de entrada do Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Forneça as entradas na página **Usar seu Próprio Disco**.

   ![Entradas para usar seu próprio disco](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   A descrição das entradas é a seguinte:

   * **Local de Preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho de rede completo do local de preparo.
   * **Conta de Armazenamento do Azure Resource Manager**: nome da conta de armazenamento do tipo Resource Manager (uso geral v1 ou uso geral v2) em qualquer assinatura do Azure.
   * **Contêiner de Armazenamento do Azure**: nome do contêiner de armazenamento do blob de destino na conta de armazenamento do Azure em que os dados de backup são importados.
   * **ID da Assinatura do Azure**: ID da assinatura do Azure em que a conta de armazenamento do Azure é criada.
   * **Nome do Trabalho de Importação do Azure**: o nome exclusivo pelo qual o Serviço de Importação e o Backup do Azure acompanham a transferência de dados enviados em discos para o Azure.

    Salve o **local de preparo** e as informações de **nome do trabalho de importação do Azure** que você forneceu. É necessário preparar os discos.

4. Conclua o fluxo de trabalho para criar ou atualizar a proteção. Para iniciar a cópia de backup offline, clique com o botão direito do mouse no **Grupo de Proteção** e escolha a opção **Criar ponto de recuperação**. Em seguida, escolha a opção **Proteção Online**.

   ![Criar Ponto de Recuperação](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Monitore o trabalho de Criação de Réplica Online no painel de monitoramento. O trabalho deve ser concluído com êxito com o aviso *Aguardando a conclusão do trabalho de Importação do Azure*.

   ![Ponto de recuperação completo](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Após a conclusão da operação, o local de preparo está pronto para ser usado na preparação do disco.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas ao datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente de backup do Azure (no seguinte caminho): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Vá até o diretório e copie o diretório **AzureOfflineBackupDiskPrep** para outro computador no qual as unidades SATA estão conectadas. No computador com as unidades SATA conectadas, verifique se:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na seção "Iniciar backup offline".
   * O BitLocker está habilitado no computador de cópia.
   * Azure PowerShell 3.7.0 é instalado no computador de cópia (não é necessário se você estiver executando o utilitário AzureOfflineBackupDiskPrep no servidor DPM ou MABS).
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está habilitado.
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, será obrigatório usar um servidor físico ou computador cliente diferente como o computador de cópia.

1. Abra um prompt de comando com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o comando a seguir:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |Essa entrada obrigatória é usada para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |Essa entrada opcional é usada para fornecer o caminho para o arquivo de configurações de publicação do Azure. |

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de Importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um trabalho de importação estiver associado ao local de preparo fornecido, você verá uma tela como a mostrada a seguir.

      ![Console de preparação do disco](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Insira a letra da unidade sem os dois pontos à direita para o disco montado que você deseja preparar para a transferência do Azure.
1. Forneça a confirmação para a formatação da unidade quando solicitado.
1. Você será solicitado a entrar em sua assinatura do Azure. Forneça as suas credenciais.

    ![Tela de entrada do Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Em seguida, a ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar discos adicionais quando solicitado pela ferramenta se o disco fornecido não tiver espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornece três informações principais:
    * Um ou mais discos que você forneceu estão preparados para envio ao Azure.
    * Você recebe a confirmação de que o trabalho de importação foi criado. O trabalho de importação usa o nome fornecido.
    * A ferramenta exibe o endereço de envio para o datacenter do Azure.

     ![Preparação do disco do Azure concluída](./media/backup-azure-backup-server-import-export/console.png)

1. No final da execução do comando, você também verá a opção para atualizar as informações de envio.

1. Envie os discos para o endereço fornecido pela ferramenta e mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Dois trabalhos de importação do Azure não podem ter o mesmo número de acompanhamento. Verifique se as unidades preparadas pelo utilitário em um único trabalho de importação do Azure são enviadas juntas em um único pacote e se há um único número de controle exclusivo para o pacote. Não combine unidades preparadas como parte de diferentes trabalhos de importação do Azure em um único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar os detalhes de envio no trabalho de Importação do Azure

O procedimento a seguir atualiza os detalhes de envio do trabalho de Importação do Azure. Essas informações incluem detalhes sobre:

* o nome da transportadora que entregará os discos para o Azure
* detalhes da remessa de retorno para seus discos

   1. Entre na sua assinatura do Azure.
   2. No menu principal, selecione **todos os serviços** e, na caixa de diálogo todos os serviços, digite importar. Quando você vir **trabalhos de importação/exportação**, selecione-o.
       ![Inserir informações de envio](./media/backup-azure-backup-server-import-export/search-import-job.png)

       A lista de menu **Importar/exportar trabalhos** é aberta, e a lista de todos os trabalhos de Importação/exportação na assinatura selecionada é exibida.

   3. Se você tiver várias assinaturas, selecione aquela usada para importar os dados de backup. Em seguida, selecione o trabalho de Importação criado recentemente para abrir seus detalhes.

       ![Examinar Informações de Envio](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. No menu configurações do trabalho de importação, selecione **gerenciar informações de envio** e insira os detalhes de envio de retorno.

       ![Armazenar Informações de Envio](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Quando você tiver o número de rastreamento de sua operadora de entrega, selecione a faixa na página Visão geral do trabalho de importação do Azure e insira os seguintes detalhes:

      > [!IMPORTANT]
      > Verifique se as informações da transportadora e o número de rastreamento são atualizados dentro de duas semanas após a criação do trabalho de importação do Azure. A falha em verificar essas informações dentro do prazo de duas semanas pode resultar na exclusão do trabalho e no não processamento das unidades.

      ![Visão geral do trabalho](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Informações de rastreamento](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades

A quantidade de tempo que leva para processar um trabalho de importação do Azure varia. O tempo de processo depende de fatores como tempo de envio, tipo de trabalho, tipo e tamanho dos dados que estão sendo copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA. Depois que os discos são recebidos, o serviço se esforça para concluir a cópia dos dados de backup em sua conta de armazenamento do Azure em 7 a 10 dias. A próxima seção descreve como você pode monitorar o status do trabalho de importação do Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorar o status do trabalho de importação do Azure

Você pode monitorar o status do trabalho de Importação no portal do Azure navegando até a página **trabalhos de Importação/Exportação** e selecionando seu trabalho. Para obter mais informações sobre o status dos trabalhos de Importação, confira o artigo [Serviço de Exportação e Importação de armazenamento](../import-export/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho

Depois que o trabalho de importação for concluído, os dados de backup iniciais estarão disponíveis na sua conta de armazenamento. No momento do próximo backup agendado, o backup do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos serviços de recuperação.

No momento do próximo trabalho de criação de réplica online agendado, o Data Protection Manager executa o backup incremental sobre a cópia do backup inicial.

## <a name="next-steps"></a>Próximas etapas

* Para dúvidas sobre o fluxo de trabalho do serviço de importação/exportação do Azure, consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../import-export/storage-import-export-service.md).

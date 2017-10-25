---
title: "Backup do Azure - backup offline ou propagação inicial usando o serviço de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de Importação/Exportação do Azure. Este artigo explica a propagação offline de dados de backup iniciais usando o serviço de Importação/Exportação do Azure."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/20/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 074d21269206b243f8b0e8747811544132805229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup offline no Backup do Azure
O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. O Backup do Azure compacta os backups inicias. O processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup iniciais compactados de forma offline no Azure.  

O processo de propagação offline do Backup do Azure está totalmente integrado com o [serviço Importação/Exportação do Azure](../storage/common/storage-import-export-service.md) , que permite transferir dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup iniciais que precisam ser transferidos por meio de uma rede de alta latência e baixa largura de banda, pode usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um data center do Azure. Este artigo fornece uma visão geral das etapas que concluem este fluxo de trabalho.

## <a name="overview"></a>Visão geral
Com a capacidade de propagação offline do Backup do Azure e a Importação/Exportação do Azure, é simples carregar os dados offline no Azure usando discos. Em vez de transferir a cópia completa inicial através da rede, os dados de backup são gravados em um *local de preparo*. Depois da cópia para o local de preparo ser concluída usando a ferramenta Importação/Exportação do Azure, esses dados são gravados para uma ou mais unidades SATA, dependendo da quantidade de dados. Essas unidades eventualmente são enviadas para o data center do Azure mais próximo.

A [atualização do Backup do Azure de agosto de 2016 (e posterior)](http://go.microsoft.com/fwlink/?LinkID=229525) inclui a *ferramenta de preparação do Azure Disk*chamada AzureOfflineBackupDiskPrep, que:

* Ajuda você a preparar suas unidades para a Importação do Azure usando a ferramenta Importação/Exportação do Azure.
* Cria automaticamente um trabalho de importação do Azure para o serviço Importação/Exportação do Azure no [portal clássico do Azure](https://manage.windowsazure.com) em vez de criá-lo manualmente com versões mais antigas do Backup do Azure.

Depois que o upload dos dados de backup no Azure for concluído, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

> [!NOTE]
> Para usar a ferramenta de preparação do Azure Disk, verifique se você instalou a atualização do Backup do Azure de agosto de 2016 (ou posterior) e execute todas as etapas do fluxo de trabalho com ele. Se você está usando uma versão mais antiga do Backup do Azure, pode preparar a unidade SATA usando a ferramenta Importação/Exportação do Azure, conforme detalhado nas seções posteriores deste artigo.
>
>

## <a name="prerequisites"></a>Pré-requisitos
* [Familiarize-se com o fluxo de trabalho de Importação/Exportação do Azure](../storage/common/storage-import-export-service.md).
* Antes de iniciar o fluxo de trabalho, verifique o seguinte:
  * Um cofre de Backup do Azure foi criado.
  * As credenciais do cofre foram baixadas.
  * O agente de Backup do Azure foi instalado no Windows Server/cliente do Windows ou no servidor do System Center Data Protection Manager e o computador é registrado no Cofre de Backup do Azure.
* [Baixe as configurações do arquivo de publicação do Azure](https://manage.windowsazure.com/publishsettings) no computador por meio do qual você planeja fazer backup dos nossos dados.
* Configure um local de preparo, que pode ser um compartilhamento de rede ou um disco adicional no computador. O local de preparo é um “armazenamento provisório” e é usado temporariamente durante esse fluxo de trabalho. Certifique-se de que o local de preparo tenha espaço em disco suficiente para armazenar sua cópia inicial. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparo tenha pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Verifique se você está usando uma unidade com suporte. Somente discos rígidos internos SSD de 2,5 polegadas ou SATA II ou III de 2,5 ou 3,5 polegadas têm suporte para uso com o serviço de Importação/Exportação. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-service.md#hard-disk-drives) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* Habilite o BitLocker no computador ao qual o gravador de unidade SATA está conectado.
* [Baixe a ferramenta de Importação/Exportação](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para o computador ao qual o gravador de unidade SATA está conectado. Esta etapa não será necessária se você tiver baixado e instalado a atualização de agosto de 2016 do Backup do Azure (ou posterior).

## <a name="workflow"></a>Fluxo de trabalho
As informações desta seção ajudam você a concluir o fluxo de trabalho de backup offline para que os dados possam ser entregues em um datacenter do Azure e carregados no Armazenamento do Azure. Se você tem dúvidas sobre o serviço de Importação ou qualquer aspecto do processo, confira a documentação sobre a [Visão geral do serviço de importação](../storage/common/storage-import-export-service.md) indicada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar o backup offline
1. Quando você agenda um backup, vê a tela a seguir (no Windows Server, no cliente do Windows ou no System Center Data Protection Manager).

    ![Tela de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Esta é a tela correspondente no System Center Data Protection Manager:  <br/>
    ![Tela de importação do DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é a seguinte:

    * **Local de preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. Isso pode estar em um computador local ou em um compartilhamento de rede. Se o computador de cópia e o computador de origem são diferentes, é recomendável especificar o caminho completo de rede do local de preparo.
    * **Nome do trabalho de importação do Azure**: o nome exclusivo pelo qual o serviço Importação do Azure e o Backup do Azure controlam a transferência de dados enviados em discos no Azure.
    * **Configurações de publicação do Azure**: um arquivo XML que contém informações sobre seu perfil de assinatura. Ele também contém credenciais seguras associadas à sua assinatura. Você pode [baixar o arquivo](https://manage.windowsazure.com/publishsettings). Forneça o caminho local para o arquivo de configurações de publicação.
    * **ID da assinatura do Azure**: a ID da assinatura do Azure na qual você planeja iniciar o trabalho de Importação do Azure. Se você tem várias assinaturas do Azure, use a ID à qual deseja associar o trabalho de importação.
    * **Conta de Armazenamento do Azure**: a conta de armazenamento do tipo clássico na assinatura do Azure fornecida que será associada ao trabalho de importação do Azure.
    * **Contêiner de Armazenamento do Azure**: o nome do blob de armazenamento de destino na Conta de Armazenamento do Azure onde os dados desse trabalho são importados.

    > [!NOTE]
    > Se você registrou o servidor em um cofre dos Serviços de Recuperação do Azure no [portal do Azure](https://portal.azure.com) para seus backups e não está em uma assinatura de CSP (Provedor de Solução de Nuvem), pode ainda criar uma conta de armazenamento do tipo clássico no portal do Azure e usá-la para o fluxo de trabalho de backup offline.
    >
    >

     Salve todas essas informações porque você precisará digitá-las novamente nas próximas etapas. Somente o *local de preparo* será necessário se a ferramenta Azure Disk Preparation for usada para preparar os discos.    
2. Conclua o fluxo de trabalho e selecione **Fazer Backup Agora** no console de gerenciamento do Backup do Azure para iniciar a cópia de backup offline. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no System Center Data Protection Manager, clique com o botão direito do mouse no **Grupo de Proteção** e escolha a opção **Criar ponto de recuperação**. Então, você escolhe a opção **Proteção online** .

    ![Fazer backup agora do DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

    ![Andamento do backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Prepare uma unidade SATA e crie o trabalho de Importação do Azure usando a ferramenta Azure Disk Preparation
A ferramenta Azure Disk Preparation está disponível no diretório de instalação do agente dos Serviços de Recuperação (atualização de agosto de 2016 e posterior) no caminho a seguir.

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Vá até o diretório e copie o diretório **AzureOfflineBackupDiskPrep** em um computador de cópia no qual as unidades a serem preparadas estão montadas. Verifique os itens a seguir em relação ao computador de cópia:

    * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede fornecido no fluxo de trabalho de **Iniciar o backup offline** .
    * O BitLocker está habilitado no computador.
    * O computador pode acessar o portal do Azure.

    Se necessário, o computador de cópia também pode ser o computador de origem.
2. Abra um prompt de comando no computador de cópia com o diretório da ferramenta de preparação do Azure Disk como o diretório atual e execute o comando a seguir:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |A entrada obrigatória usada para fornecer o caminho para o local de preparo inserido no fluxo de trabalho de **Iniciar o backup offline** . |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |A entrada opcional usada para fornecer o caminho para o arquivo **Configurações de Publicação do Azure** inserido no fluxo de trabalho de **Iniciar o backup offline**. |

    > [!NOTE]
    > O valor &lt;Caminho para PublishSettingFile&gt; será obrigatório quando o computador de cópia e o computador de origem forem diferentes.
    >
    >

    Quando você executa o comando, a ferramenta solicita a seleção do trabalho de Importação do Azure que corresponde às unidades que precisam ser preparadas. Se houver somente um único trabalho de importação associado ao local de preparo fornecido, será uma tela como a mostrada a seguir.

    ![Entrada da ferramenta de preparação do disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure. Confirme a formatação da unidade quando solicitado.

    A ferramenta começa a preparar o disco com os dados de backup. Talvez seja necessário anexar mais discos quando solicitado pela ferramenta, caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, um ou mais discos que você forneceu estarão preparados para envio no Azure. Além disso, um trabalho de importação com o nome fornecido durante o fluxo de trabalho **Iniciar backup offline** é criado no portal clássico do Azure. Por fim, a ferramenta exibe o endereço de entrega para o datacenter do Azure para onde os discos precisam ser enviados e o link para localizar o trabalho de importação no portal clássico do Azure.

    ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Envie os discos para o endereço fornecido pela ferramenta e mantenha o número de controle para referência futura.<br/>

5. Quando você seguir o link exibido pela ferramenta, você verá a conta de armazenamento do Azure especificada no fluxo de trabalho **Iniciar backup offline** . Aqui você pode ver o trabalho de importação recém-criado na guia **IMPORTAÇÃO/EXPORTAÇÃO** da conta de armazenamento.

    ![Trabalho de importação criado](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Clique em **INFORMAÇÕES DE ENVIO** na parte inferior da página para atualizar seus detalhes de contato como mostrado abaixo. A Microsoft usa essas informações para enviar os discos para você quando o trabalho de importação for concluído.

    ![Informações de contato](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Insira os detalhes de envio na próxima tela. Forneça os detalhes da **Transportadora** e do **Número de Rastreamento** correspondentes aos discos enviados para o datacenter do Azure.

    ![INFORMAÇÕES DE ENVIO](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Após a conclusão da importação, os dados de backup inicias estarão disponíveis na sua conta de armazenamento. O agente dos Serviços de Recuperação copia o conteúdo dos dados dessa conta para o cofre de Backup ou para um cofre dos Serviços de Recuperação, dependendo do caso. No próximo horário de backup agendado, o agente de Backup do Azure executará o backup incremental sobre a cópia de backup inicial.

> [!NOTE]
> As seções a seguir se aplicam a usuários de versões anteriores do Backup do Azure que não têm acesso à Ferramenta de preparação do Azure Disk.
>
>

### <a name="prepare-a-sata-drive"></a>Preparar uma unidade SATA
1. Baixe a [ferramenta de Importação/Exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) no computador de cópia. Verifique se o local de preparo está acessível no computador no qual você pretende executar o próximo conjunto de comandos. Se necessário, o computador de cópia também pode ser o computador de origem.

2. Descompacte o arquivo WAImportExport.zip. Execute a ferramenta WAImportExport, que formata a unidade SATA, grava os dados de backup na unidade SATA e os criptografa. Antes de executar o comando a seguir, verifique se o BitLocker está habilitado no computador. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Se você instalou a atualização do Backup do Azure de agosto de 2016 (ou posterior), verifique se o local de preparo inserido é o mesmo que o exibido na tela **Fazer Backup Agora** e se contém os arquivos AIB e Blob de Base.
    >
    >

| Parâmetro | Descrição |
| --- | --- |
| /j:<*JournalFile*> |O caminho para o arquivo de diário. Cada unidade deve ter exatamente um arquivo de diário. O arquivo de diário não deve estar na unidade de destino. A extensão de arquivo de diário é .jrn e é criada como parte da execução desse comando. |
| /id:<*SessionId*> |A ID de sessão identifica uma sessão de cópia. Ela é usada para garantir a recuperação correta de uma sessão de cópia interrompida. Os arquivos copiados em uma sessão de cópia são armazenados em um diretório nomeado como a ID de sessão na unidade de destino. |
| /sk:<*StorageAccountKey*> |A chave da conta de armazenamento para a qual os dados são importados. Ela precisa ser a mesma que foi inserida durante a criação do grupo de política de backup/proteção. |
| /BlobType |O tipo de blob. Esse fluxo de trabalho só será bem-sucedido se **PageBlob** for especificado. Essa não é a opção padrão e deve ser mencionada nesse comando. |
| /t:<*TargetDriveLetter*> |A letra da unidade do disco rígido de destino sem dois-pontos no final para a sessão de cópia atual. |
| /format |A opção para formatar a unidade. Especifique esse parâmetro quando for necessário formatar a unidade; caso contrário, omita-o. Antes de a ferramenta formatar a unidade, ela solicitará uma confirmação no console. Para suprimir a confirmação, especifique o parâmetro /silentmode. |
| /encrypt |A opção para criptografar a unidade. Especifique esse parâmetro quando a unidade ainda não tiver sido criptografada com o BitLocker e precisar ser criptografada pela ferramenta. Se a unidade já tiver sido criptografada com o BitLocker, omita esse parâmetro, especifique o parâmetro /bk e forneça a chave do BitLocker existente. Se você especificar o parâmetro /format, também deverá especificar o parâmetro /encrypt. |
| /srcdir:<*SourceDirectory*> |O diretório de origem que contém os arquivos a serem copiados para a unidade de destino. Verifique se o nome do diretório especificado tem um caminho completo em vez de um caminho relativo. |
| /dstdir:<*DestinationBlobVirtualDirectory*> |O caminho para o diretório virtual de destino em sua conta de armazenamento do Azure. Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais ou blobs de destino. Tenha em mente que os nomes de contêiner devem estar em minúsculas.  Esse nome de contêiner deve ser aquele que foi inserido durante a criação do grupo de política de backup/proteção. |

> [!NOTE]
> Um arquivo de diário é criado na pasta WAImportExport que captura todas as informações do fluxo de trabalho. Ao criar um trabalho de importação no portal do Azure, você precisará desse arquivo.
>
>

  ![Saída do PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Criar um trabalho de importação no Portal do Azure
1. Navegue até a sua conta de armazenamento no [Portal clássico do Azure](https://manage.windowsazure.com/), clique em **Importação/Exportação** e em **Criar Trabalho de Importação** no painel de tarefas.

    ![Guia de importação/exportação no portal do Azure](./media/backup-azure-backup-import-export/azureportal.png)

2. Na etapa 1 do assistente, indique que você preparou a unidade e que o arquivo de diário de unidade está disponível.

3. Na etapa 2 do assistente, forneça as informações de contato da pessoa responsável por esse trabalho de importação.

4. Na etapa 3, carregue os arquivos de diário de unidade obtidos na seção anterior.

5. Na etapa 4, insira um nome descritivo para o trabalho de importação que foi inserido durante a criação da política de backup/grupo de proteção de backup. O nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar por letra e não pode conter espaços. O nome escolhido será usado para acompanhar os trabalhos enquanto eles estiverem em andamento e depois de concluídos.

6. Em seguida, selecione a região do datacenter na lista. A região do data center indica o data center e o endereço para o qual você deve enviar seu pacote.

    ![Selecionar a região do datacenter](./media/backup-azure-backup-import-export/dc.png)

7. Na etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft usa essa conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de importação.

8. Envie o disco e insira o número de acompanhamento para consultar o status da remessa. Depois que o disco chega ao datacenter, ele é copiado para a conta de armazenamento e o status é atualizado.

    ![Status concluído](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois que os dados de backup iniciais estão disponíveis em sua conta de armazenamento, o agente dos Serviços de Recuperação do Microsoft Azure copia o conteúdo dos dados dessa conta para o cofre de Backup ou para o cofre dos Serviços de Recuperação, dependendo do caso. No próximo horário de backup agendado, o agente de Backup do Azure executará o backup incremental sobre a cópia de backup inicial.

## <a name="next-steps"></a>Próximas etapas
* Para qualquer dúvida sobre o fluxo de trabalho de Importação/Exportação do Azure, veja [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o armazenamento de Blobs](../storage/common/storage-import-export-service.md).
* Consulte a seção sobre o backup offline das [perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure se tiver dúvidas sobre o fluxo de trabalho.

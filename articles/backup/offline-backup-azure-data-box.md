---
title: Backup offline usando Azure Data Box
description: Saiba como você pode usar Azure Data Box para propagar grandes dados de backup inicial offline do agente MARS para um cofre dos serviços de recuperação.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752576"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup offline do Backup do Azure usando Azure Data Box

Você pode usar [Azure data Box](../databox/data-box-overview.md) para propagar seus BACKUPs MARS (serviços de recuperação do Microsoft Azure inicial) grandes offline (sem usar a rede) para um cofre dos serviços de recuperação. Esse processo poupa tempo e largura de banda de rede que, de outra forma, seriam consumidos durante a migração online de grandes quantidades de dados de backups em uma rede de alta latência. No momento, esse aprimoramento está em versão prévia. O backup offline baseado no Azure Data Box fornece duas vantagens distintas em relação ao [backup offline baseado no serviço de Importação/Exportação do Azure](./backup-azure-backup-import-export.md):

- Não é necessário adquirir seus próprios discos e conectores compatíveis com o Azure. O Azure Data Box envia os discos associados ao [SKU de Data Box](https://azure.microsoft.com/services/databox/data/) selecionado.
- O Backup do Azure (Agente MARS) pode gravar dados de backup diretamente nos SKUs compatíveis do Azure Data Box. Essa funcionalidade elimina a necessidade de provisionar um local de preparo para os dados do backup inicial. Também não é necessário usar utilitários para formatar e copiar esses dados nos discos.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box com o agente MARS

Este artigo explica como você pode usar Azure Data Box para propagar grandes dados de backup inicial offline do agente MARS para um cofre dos serviços de recuperação.

## <a name="supported-platforms"></a>Plataformas com suporte

O processo para propagar dados do agente MARS usando Azure Data Box tem suporte nas seguintes SKUs do Windows.

| **SO**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Estação de Trabalho**                        |                                                              |
| Windows 10 de 64 bits                     | Enterprise, Pro, Home                                       |
| Windows 8.1 de 64 bits                    | Enterprise, Pro                                             |
| Windows 8 de 64 bits                      | Enterprise, Pro                                             |
| Windows 7 de 64 bits                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Servidor**                             |                                                              |
| Windows Server 2019 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 de 64 bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 de 64 bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 de 64 bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 de 64 bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 de 64 bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 de 64 bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs de Data Box compatíveis

| Tamanho dos dados de backup (pós-compactação por MARS) * por servidor | SKU de Azure Data Box compatível                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box disco](../databox/data-box-disk-overview.md) |
| >7,2 TB e <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* As taxas de compactação típicas variam entre 10% e 20%. <br>
* * Se você espera ter mais de 80 TB de dados de backup inicial para um único servidor MARS, entre em contato com [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Os dados de backup iniciais de um único servidor devem estar contidos em uma única instância de Azure Data Box ou em um disco de Azure Data Box e não podem ser compartilhados entre vários dispositivos do mesmo ou de SKUs diferentes. Mas um dispositivo de Azure Data Box pode conter backups iniciais de vários servidores.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura do Azure e permissões necessárias

- O processo requer uma assinatura do Azure.
- O processo requer que o usuário designado para executar a política de backup offline seja proprietário da assinatura do Azure.
- O trabalho de Data Box e o cofre dos serviços de recuperação (para o qual os dados precisam ser propagados) precisam estar nas mesmas assinaturas.
- Recomendamos que a conta de armazenamento de destino associada ao trabalho de Azure Data Box e ao cofre dos serviços de recuperação estejam na mesma região. No entanto, isso não é necessário.

### <a name="get-azure-powershell-370"></a>Obter Azure PowerShell 3.7.0

*Esse é o pré-requisito mais importante para o processo*. Antes de instalar Azure PowerShell, versão 3.7.0, execute as verificações a seguir.

#### <a name="step-1-check-the-powershell-version"></a>Etapa 1: verificar a versão do PowerShell

1. Abra o Windows PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Se a saída exibir uma versão superior a 3.7.0, faça a "etapa 2". Caso contrário, pule para "Step 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Etapa 2: desinstalar a versão do PowerShell

Desinstale a versão atual do PowerShell.

1. Remova os módulos dependentes executando o seguinte comando no PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Para garantir a exclusão bem-sucedida de todos os módulos dependentes, execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Etapa 3: instalar o PowerShell versão 3.7.0

Depois de verificar se nenhum módulo AzureRM está presente, instale a versão 3.7.0 usando um dos seguintes métodos:

- No GitHub, use [este link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Ou você pode:

- Execute o seguinte comando na janela do PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell também pode ter sido instalado usando um arquivo MSI. Para removê-lo, desinstale-o usando a opção **desinstalar programas** no painel de controle.

### <a name="order-and-receive-the-data-box-device"></a>Solicitar e receber o dispositivo do Data Box

O processo de backup offline usando MARS e Azure Data Box requer que os dispositivos Data Box estejam em um estado entregue antes de disparar o backup offline usando o agente MARS. Para solicitar o SKU mais adequado para seu requisito, consulte [backup data size e data Box SKUs com suporte](#backup-data-size-and-supported-data-box-skus). Siga as etapas em [tutorial: encomendar um disco Azure data Box](../databox/data-box-disk-deploy-ordered.md) para solicitar e receber seus dispositivos de data box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo de conta**. O agente MARS requer uma conta que ofereça suporte a blobs de página, que não tem suporte quando *BlobStorage* é selecionado. Selecione **armazenamento v2 (uso geral v2)** como o **tipo de conta** ao criar a conta de armazenamento de destino para seu trabalho de Azure data box.

![Escolher o tipo de conta nos detalhes da instância](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalar e configurar o agente MARS

1. Certifique-se de desinstalar todas as instalações anteriores do agente MARS.
1. Baixe o agente MARS mais recente deste [site](https://aka.ms/azurebackup_agent).
1. Execute *MARSAgentInstaller.exe* e execute *apenas* as etapas para [instalar e registrar o agente](./install-mars-agent.md#install-and-register-the-agent) no cofre dos serviços de recuperação em que você deseja que os backups sejam armazenados.

   > [!NOTE]
   > O cofre dos serviços de recuperação deve estar na mesma assinatura que o trabalho de Azure Data Box.

   Depois que o agente estiver registrado no cofre dos serviços de recuperação, siga as etapas nas próximas seções.

## <a name="set-up-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Dependendo do SKU Azure Data Box que você solicitou, execute as etapas abordadas nas seções apropriadas a seguir. As etapas mostram como configurar e preparar os dispositivos Data Box para o agente MARS para identificar e transferir os dados de backup inicial.

### <a name="set-up-azure-data-box-disks"></a>Configurar discos Azure Data Box

Se você solicitou um ou mais discos de Azure Data Box (até 8 TB cada), siga as etapas mencionadas aqui para [desempacotar, conectar e desbloquear seu disco de data Box](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>É possível que o servidor com o agente MARS não tenha uma porta USB. Nessa situação, você pode conectar o disco Azure Data Box a outro servidor ou cliente e expor a raiz do dispositivo como um compartilhamento de rede.

### <a name="set-up-azure-data-box"></a>Configurar Azure Data Box

Se você solicitou uma instância de Azure Data Box (até 100 TB), siga as etapas aqui [para configurar sua instância de data Box](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montar sua instância de Azure Data Box como um sistema local

O agente MARS opera no contexto do sistema local, portanto, requer que o mesmo nível de privilégio seja fornecido ao caminho de montagem em que a instância de Azure Data Box está conectada.

Para garantir que você possa montar seu dispositivo de Data Box como um sistema local usando o protocolo NFS:

1. Habilite o cliente para o recurso NFS no Windows Server que tem o agente MARS instalado. Especifique o WIM de origem alternativo *: D: \sources\install.wim: 4*.
1. Baixe o PsExec da página [Sysinternals](/sysinternals/downloads/psexec) para o servidor com o agente Mars instalado.
1. Abra um prompt de comando com privilégios elevados e execute o comando a seguir com o diretório que contém *PSExec.exe* como o diretório atual.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   A janela de comando que é aberta devido ao comando anterior está no contexto do sistema local. Use esta janela de comando para executar as etapas para montar o compartilhamento de blob de páginas do Azure como uma unidade de rede no seu Windows Server.
1. Siga as etapas em [conectar-se a data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) para conectar o servidor com o agente Mars ao dispositivo de data Box via NFS. Execute o comando a seguir no prompt de comando do sistema local para montar o compartilhamento de blobs de páginas do Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Depois que o compartilhamento for montado, verifique se você pode acessar o X: do seu servidor. Se você puder, continue com a próxima seção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup inicial para dispositivos Azure Data Box

1. Abra o aplicativo **backup do Microsoft Azure** no servidor.
1. No painel **ações** , selecione **agendar backup**.

    ![Selecione agendar backup](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Siga as etapas no **Assistente de agendamento de backup**.

1. Adicione itens selecionando o botão **Adicionar itens** . Mantenha o tamanho total dos itens dentro dos [limites de tamanho com suporte pelo SKU Azure data Box](#backup-data-size-and-supported-data-box-skus) que você solicitou e recebeu.

    ![Adicionar itens ao backup](./media/offline-backup-azure-data-box/add-items.png)

1. Selecione a agenda de backup e a política de retenção apropriadas para **arquivos e pastas** e **estado do sistema**. O estado do sistema é aplicável somente para servidores Windows e não para clientes Windows.
1. Na página **escolher tipo de backup inicial (arquivos e pastas)** do assistente, selecione a opção **transferir usando Microsoft Azure discos Data Box** e selecione **Avançar**.

    ![Escolher tipo de backup inicial](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Entre no Azure quando solicitado usando as credenciais de usuário que têm acesso de proprietário na assinatura do Azure. Depois de fazer isso, você deverá ver uma página parecida com esta.

    ![Criar recursos e aplicar as permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

   Em seguida, o agente MARS busca os trabalhos de Data Box na assinatura que estão no estado entregue.

    ![Buscar trabalhos de Data Box para a ID da assinatura](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecione a ordem de Data Box correta para a qual você desempacotau, conectou e desbloqueou o disco Data Box. Selecione **Avançar**.

    ![Selecionar pedidos de Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecione **detectar dispositivo** na página de **detecção de dispositivo data Box** . Essa ação faz com que o agente MARS procure discos Azure Data Box conectados localmente e os detecte.

    ![Detecção de dispositivo Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se você conectou a instância de Azure Data Box como um compartilhamento de rede (devido à indisponibilidade de portas USB ou porque você solicitou e montou o dispositivo de 100 TB Data Box), a detecção falhará a princípio. Você tem a opção de inserir o caminho de rede para o dispositivo Data Box.

    ![Insira o caminho de rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do Azure Data Box Disk. Esse diretório deve conter um diretório com o nome *PageBlob*.
    >
    >![Diretório raiz de Azure Data Box disco](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *DISK1* contiver um diretório chamado *PageBlob*, o caminho que você inserir na página do assistente do agente Mars será `\\mydomain\myserver\disk1\` .
    >
    >Se você [configurar um dispositivo Azure Data Box 100-TB](#set-up-azure-data-box-devices), insira `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` como o caminho de rede para o dispositivo.

1. Selecione **Avançar** e selecione **concluir** na página seguinte para salvar a política de backup e retenção com a configuração de backup offline usando Azure data box.

   A página a seguir confirma que a política foi salva com êxito.

    ![A política foi salva com êxito](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecione **fechar** na página anterior.

1. Selecione **fazer backup agora** no painel **ações** do console do agente Mars. Selecione **fazer backup** na página do assistente.

    ![Assistente de backup agora](./media/offline-backup-azure-data-box/backup-now.png)

O agente MARS inicia o backup dos dados selecionados para o dispositivo Azure Data Box. Esse processo pode levar de várias horas a alguns dias. A quantidade de tempo depende do número de arquivos e da velocidade de conexão entre o servidor com o agente MARS e o disco Azure Data Box.

Depois que o backup dos dados for concluído, você verá uma página no agente MARS que se assemelha a este.

![Progresso do backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Etapas após o backup

Esta seção explica as etapas a serem executadas depois que o backup dos dados para o Disco do Azure Data Box for bem-sucedido.

- Siga as etapas neste artigo para [enviar o disco Azure data box para o Azure](../databox/data-box-disk-deploy-picked-up.md). Se você usou um dispositivo Azure Data Box 100-TB, siga estas etapas para [enviar o dispositivo de Azure data box para o Azure](../databox/data-box-deploy-picked-up.md).

- [Monitore o trabalho do Data Box](../databox/data-box-disk-deploy-upload-verify.md) no portal do Azure. Depois que o trabalho de Azure Data Box for concluído, o agente MARS moverá automaticamente os dados da conta de armazenamento para o cofre dos serviços de recuperação no momento do próximo backup agendado. Em seguida, ele marcará o trabalho de backup como *trabalho concluído* se um ponto de recuperação for criado com êxito.

    >[!NOTE]
    >O agente MARS dispara backups nos horários agendados durante a criação da política. Esses trabalhos sinalizam "aguardando Azure Data Box trabalho seja concluído" até a hora em que o trabalho for concluído.

- Depois que o agente MARS cria com êxito um ponto de recuperação que corresponde ao backup inicial, você pode excluir a conta de armazenamento ou o conteúdo específico associado ao trabalho de Azure Data Box.

## <a name="troubleshooting"></a>Solução de problemas

O agente de Serviços de Recuperação do Microsoft Azure (MARS) cria um aplicativo Azure Active Directory (Azure AD) para você em seu locatário. Esse aplicativo requer um certificado para autenticação que é criado e carregado quando você configura uma política de propagação offline. Usamos Azure PowerShell para criar e carregar o certificado para o aplicativo do Azure AD.

### <a name="problem"></a>Problema

Ao configurar o backup offline, você pode enfrentar um problema devido a um bug no cmdlet Azure PowerShell. Talvez você não consiga adicionar vários certificados ao mesmo aplicativo do Azure AD criado pelo agente MAB. Esse problema afetará se você configurou uma política de propagação offline para o mesmo servidor ou para outro.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verificar se o problema é causado por essa causa raiz específica

Para ver se o problema é o mesmo descrito anteriormente, execute uma das etapas a seguir.

#### <a name="step-1-of-verification"></a>Etapa 1 da verificação

Verifique se a seguinte mensagem de erro aparece no console do MAB quando você configurou o backup offline.

![Não é possível criar a política de backup offline para a conta atual do Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Etapa 2 da verificação

1. Abra a pasta **Temp** no caminho de instalação. O caminho padrão da pasta Temp é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Procure o arquivo *CBUICurr* e abra o arquivo.

1. No arquivo *CBUICurr* , role até a última linha e verifique se o problema é o mesmo da seguinte mensagem de erro: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Solução alternativa

Como solução alternativa para resolver esse problema, execute as etapas a seguir e repita a configuração da política.

#### <a name="step-1-of-workaround"></a>Etapa 1 da solução alternativa

Entre no PowerShell que aparece na interface do usuário do amMAB usando uma conta diferente com acesso de administrador na assinatura que terá o trabalho de Data Box criado.

#### <a name="step-2-of-workaround"></a>Etapa 2 da solução alternativa

Se nenhum outro servidor tiver a propagação offline configurada e nenhum outro servidor depender do `AzureOfflineBackup_<Azure User Id>` aplicativo, exclua este aplicativo. Selecione **portal do Azure**  >  **Azure Active Directory**  >  **registros de aplicativo**.

>[!NOTE]
> Verifique se o `AzureOfflineBackup_<Azure User Id>` aplicativo não tem nenhuma outra propagação offline configurada e também se nenhum outro servidor depende desse aplicativo. Vá para **configurações**  >  **chaves** na seção **chaves públicas** . Ele não deve ter outras chaves públicas adicionadas. Consulte a seguinte captura de tela para referência.
>
>![Chaves públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Etapa 3

No servidor que você está tentando configurar para o backup offline, execute as seguintes ações.

1. Vá para a guia **gerenciar aplicativo de certificado do computador**  >  **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Selecione o certificado, clique com o botão direito do mouse em **todas as tarefas** e selecione **Exportar** sem uma chave privada no formato. cer.

3. Vá para o aplicativo de backup offline do Azure mencionado na etapa 2. Selecione **configurações**  >  **chaves**  >  **carregar chave pública**. Carregue o certificado que você exportou na etapa anterior.

    ![Carregar chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registro digitando **regedit** na janela Executar.

5. Vá para oComputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider do registro *.* Clique com o botão direito do mouse em **CloudBackupProvider** e adicione um novo valor de cadeia de caracteres com o nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Para obter a ID de usuário do Azure, execute uma destas ações:
    >
    >- No PowerShell conectado ao Azure, execute o comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`.
    > - Vá para o caminho do registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` com o nome *CurrentUserId*.

6. Clique com o botão direito do mouse na cadeia de caracteres adicionada na etapa anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou na etapa 2. Selecione **OK**.

7. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione a guia **detalhes** e role para baixo até ver o campo impressão digital. Selecione **impressão digital** e copie o valor.

    ![Campo de impressão digital do certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Perguntas

Para quaisquer dúvidas ou esclarecimentos sobre os problemas enfrentados, entre em contato com [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

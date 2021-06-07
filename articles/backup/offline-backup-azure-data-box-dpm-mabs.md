---
title: Backup offline com Azure Data Box para o DPM e o MABS
description: Você pode usar Azure Data Box para propagar dados de backup inicial offline do DPM e do MABS.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752542"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Propagação offline usando Azure Data Box para o DPM e MABS (visualização)

> [!NOTE]
> Esse recurso é aplicável para o DPM (Data Protection Manager) 2019 UR2 e posterior.<br><br>
> Este recurso está atualmente em visualização para o Backup do Microsoft Azure Server (MABS). Se você estiver interessado em usar Azure Data Box para propagação offline com MABS, entre em contato conosco em [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Este artigo explica como você pode usar Azure Data Box para propagar dados de backup inicial offline do DPM e MABS para um cofre dos serviços de recuperação do Azure.

Você pode usar [Azure data Box](../databox/data-box-overview.md) para propagar seus grandes backups do DPM/mAbs inicial offline (sem usar a rede) para um cofre dos serviços de recuperação. Esse processo poupa tempo e largura de banda de rede que, de outra forma, seriam consumidos durante a migração online de grandes quantidades de dados de backups em uma rede de alta latência. Esse recurso está atualmente na visualização.

O backup offline baseado no Azure Data Box fornece duas vantagens distintas em relação ao [backup offline baseado no serviço de Importação/Exportação do Azure](backup-azure-backup-server-import-export.md):

- Você não precisa adquirir seus próprios discos e conectores compatíveis com o Azure. O Azure Data Box envia os discos associados ao [SKU de Data Box](https://azure.microsoft.com/services/databox/data/) selecionado.

- O Backup do Azure (Agente MARS) pode gravar dados de backup diretamente nos SKUs compatíveis do Azure Data Box. Essa funcionalidade elimina a necessidade de provisionar um local de preparo para os dados do backup inicial. Também não é necessário usar utilitários para formatar e copiar esses dados nos discos.

## <a name="supported-platforms"></a>Plataformas compatíveis

As seguintes plataformas são compatíveis:

- Windows Server 2019 64 bits (Standard, Datacenter e Essentials)
- Windows Server 2016 64 bits (Standard, Datacenter e Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs de Data Box compatíveis

Os seguintes SKUs de Data Box são compatíveis:

| Tamanho dos dados de backup (após a compactação por MARS) \* por servidor | SKU de Azure Data Box compatível |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB e <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*As taxas de compactação típicas variam de 10% a 20% <br>
\*\*Entre em contato com [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) caso espere ter mais do que 80 TB de dados do backup inicial para uma única fonte de dados.

> [!IMPORTANT]
> Os dados de backup iniciais de uma única fonte de dados devem estar contidos em um único Azure Data Box ou disco de Azure Data Box e não podem ser compartilhados entre vários dispositivos do mesmo ou de SKUs diferentes. No entanto, o Azure Data Box pode conter backups iniciais de várias fontes de dados.

## <a name="before-you-begin"></a>Antes de começar

O agente MARS em execução no DPM/MABS deve ser atualizado para a [versão mais recente](https://aka.ms/azurebackup_agent) (2.0.9171.0 ou posterior).

Verifique o seguinte:

### <a name="azure-subscription-and-required-permissions"></a>Assinatura do Azure e permissões necessárias

- Uma assinatura válida do Azure.
- O usuário que pretende executar a política de backup offline deve ser o proprietário de uma assinatura do Azure.
- O trabalho de Data Box e o cofre dos serviços de recuperação nos quais os dados precisam ser propagados devem estar disponíveis nas mesmas assinaturas.
    > [!NOTE]
    > É recomendável que a conta de armazenamento de destino e o cofre dos serviços de recuperação estejam na mesma região. No entanto, isso não é obrigatório.

### <a name="order-and-receive-the-data-box-device"></a>Solicitar e receber o dispositivo do Data Box

Verifique se os dispositivos de Data Box necessários estão no estado *entregue* antes de disparar o backup offline. Confira as informações de [Tamanho dos Dados de Backup e SKUs de Data Box compatíveis](#backup-data-size-and-supported-data-box-skus) para solicitar o SKU mais adequado para seus requisitos. Siga as etapas [deste artigo](../databox/data-box-disk-deploy-ordered.md) para solicitar e receber seus dispositivos do Data Box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo de conta**. O servidor DPM/MABS requer uma conta que dê suporte a blobs de página que não tem suporte quando *BlobStorage* está selecionado. Selecione  **armazenamento v2 (uso geral v2)** como o **tipo de conta** ao criar a conta de armazenamento de destino para seu trabalho de Azure data box.

![Configurar o Azure Data Box](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Depois de receber o dispositivo Azure Data Box, dependendo do SKU Azure Data Box que você solicitou, execute as etapas nas seções apropriadas abaixo para configurar e preparar os dispositivos Data Box para o servidor DPM/MABS para identificar e transferir os dados de backup iniciais.

### <a name="setup-azure-data-box-disk"></a>Configurar o Azure Data Box Disk

Caso tenho solicitado um ou mais Azure Data Box Disks (até 8 TB cada), siga as etapas mencionadas [aqui](../databox/data-box-disk-deploy-set-up.md) para desempacotar, conectar e desbloquear seu Data Box Disk.

> [!NOTE]
> É possível que o servidor DPM/MABS não tenha uma porta USB. Nesse caso, é possível conectar o Azure Data Box Disk a outro servidor/cliente e expor a raiz do dispositivo como um compartilhamento de rede.

## <a name="setup-azure-data-box"></a>Configurar o Azure Data Box

Caso tenha solicitado um Azure Data Box (até 100 TB), siga as etapas mencionadas [aqui](../databox/data-box-deploy-set-up.md) para configurar seu Data Box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Monte o Azure Data Box como um sistema local

O servidor DPM/MABS opera no contexto do sistema e, portanto, requer que o mesmo nível de privilégio seja fornecido ao caminho de montagem em que o Azure Data Box está conectado. Siga as etapas abaixo para garantir que você possa montar seu dispositivo de Data Box como sistema local usando o protocolo NFS.

1. Habilite o recurso Client for NFS no servidor DPM/MABS.
Especifique uma fonte alternativa: *WIM:D:\Sources\Install.wim:4*
2. Baixe o **PsExec** de [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) para o servidor DPM/mAbs.
3. Abra um prompt de comandos com privilégios elevados e execute o comando a seguir com o diretório que contém *PSExec.exe* como o diretório atual.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. A janela de comando que é aberta como resultado do comando acima está no contexto do sistema local. Use essa janela Comando com o objetivo de executar as etapas para montar o Compartilhamento de Blobs de Páginas do Azure como uma unidade de rede no Windows Server.
5. Siga as etapas [aqui](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) para conectar seu servidor DPM/mAbs ao dispositivo de data Box via NFS e execute o seguinte comando no prompt de comando do sistema local para montar o compartilhamento de blobs de páginas do Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Depois de montado, verifique se é possível acessar X: do seu servidor. Se você puder, continue com a próxima seção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup inicial para dispositivos Azure Data Box

1. No servidor DPM/MABS, siga as etapas para [criar um novo grupo de proteção](/system-center/dpm/create-dpm-protection-groups). Se você estiver adicionando uma proteção online ao grupo de proteção existente, clique com o botão direito do mouse no grupo de proteção existente e selecione **Adicionar proteção online** e iniciar na **etapa 8**.
2. Na página **Selecionar Membros do Grupo**, especifique os computadores nos quais deseja fazer backup e as fontes das quais ele será feito.
3. Na página **Selecionar método de proteção de dados**, especifique como deseja lidar com backups de curto e longo prazo. Certifique-se de selecionar **Desejo proteção online.**

   ![Criar um grupo de proteção](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Na página **Selecionar objetivos de curto prazo**, especifique como deseja fazer backup no armazenamento em disco de curto prazo.
5. Na página **Examinar alocação de disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.
6. Na página **Escolher método de criação de réplica**, selecione **Automaticamente pela rede.**
7. Na página **Escolher opções de verificação de consistência**, selecione como deseja automatizar as verificações de consistência.
8. Na página **especificar dados de proteção online** , selecione o membro que você deseja habilitar a proteção online.

    ![Especificar dados de proteção online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Na página **Especificar agendamento de backup online**, especifique com que frequência os backups incrementais deverão ocorrer no Azure.
10. Na página **Especificar a política de retenção online**, especifique como os pontos de recuperação criados dos backups diários/semanais/mensais/anuais serão mantidos no Azure.
11. Na tela **escolher replicação online** do assistente, escolha a opção **transferir usando os discos de propriedade da Microsoft** e selecione **Avançar**.

    ![Escolher a replicação online inicial](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > A opção de selecionar **transferir usando os discos de propriedade da Microsoft** não está disponível para o mAbs v3, já que o recurso está em versão prévia. Entre em contato conosco em [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) se quiser usar esse recurso para mAbs v3.

12. Entre no Azure quando solicitado, usando as credenciais de usuário que têm acesso de proprietário na assinatura do Azure. Após uma entrada bem-sucedida, a tela a seguir será exibida:

    ![Após o logon bem-sucedido](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     O servidor DPM/MABS irá então buscar os trabalhos de Data Box na assinatura que estão no estado *entregue* .

     > [!NOTE]
     > A primeira entrada leva mais tempo do que o normal. O módulo do Azure PowerShell será instalado em segundo plano e o Aplicativo do Azure AD será registrado.
     >
     >  - Os seguintes módulos do PowerShell serão instalados:<br>
          - AzureRM.Profile     *5.8.3*<br>
          - AzureRM.Resources   *6.7.3*<br>
          - AzureRM.Storage     *5.2.0*<br>
          - Azure.Storage       *4.6.1*<br>
     >  - O aplicativo do Azure AD será registrado como *AzureOfflineBackup_\<object GUID of the user>* .

13. Selecione a ordem de caixa de dados correta para a qual você desempacotau, conectou e desbloqueou o disco Data Box. Selecione **Avançar**.

    ![Selecionar o Data Box](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Na tela **detectar o data Box** , insira o caminho do dispositivo data box e, em seguida, selecione **detectar dispositivo**.

    ![Inserir o caminho de rede](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do Azure Data Box Disk. Esse diretório raiz deve conter um diretório chamado *PageBlob*, conforme mostrado abaixo:
    >
    > ![Unidade USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *DISK1* contiver um diretório chamado *PageBlob*, o caminho a ser fornecido no assistente do servidor DPM/mAbs será `\\mydomain\myserver\disk1\` .
    > Caso [configure um dispositivo de 100 TB do Azure Data Box](./offline-backup-azure-data-box.md#set-up-azure-data-box), forneça o seguinte caminho de rede para o dispositivo: `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`.

15. Selecione **Avançar**. Na página **Resumo** , examine as configurações e selecione **Criar grupo**.

    ![Detectar o Data Box](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    A tela a seguir confirma que o Grupo de Proteção foi criado com êxito.

    ![Grupo de proteção criado](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Selecione **fechar** na tela acima.

    Com isso, a replicação inicial dos dados ocorre no disco do DPM/MABS. Quando a proteção for concluída, o status do grupo mostrará o status de proteção como **OK** na página **proteção** .

17. Para iniciar a cópia de backup offline para seu dispositivo Azure Data Box, clique com o botão direito do mouse no **grupo de proteção** e escolha a opção **criar ponto de recuperação** . Em seguida, escolha a opção **Proteção Online**.

    ![Criar Ponto de Recuperação](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Ponto de recuperação](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   O servidor DPM/MABS começará a fazer backup dos dados selecionados para o dispositivo Azure Data Box. Isso pode levar de várias horas a alguns dias, dependendo do tamanho dos dados e da velocidade de conexão entre o servidor DPM/MABS e o Disco do Azure Data Box.

   Será possível monitorar o status do trabalho no painel de **Monitoramento**. Depois que o backup dos dados for concluído, você verá uma tela semelhante à seguinte:

   ![Console dos administradores](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Etapas após o backup

Siga estas etapas quando o backup de dados para o Azure Data Box Disk for bem-sucedido.

- Siga as etapas [deste artigo](../databox/data-box-disk-deploy-picked-up.md) para enviar o Azure Data Box Disk para o Azure. Caso tenha usado um dispositivo de 100 TB do Azure Data Box, siga [estas etapas](../databox/data-box-deploy-picked-up.md) para enviar o Azure Data Box para o Azure.
- [Monitore o trabalho do Data Box](../databox/data-box-disk-deploy-upload-verify.md) no portal do Azure. Depois que o trabalho de Azure Data Box for *concluído*, o servidor DPM/mAbs moverá automaticamente os dados da conta de armazenamento para o cofre dos serviços de recuperação no momento do próximo backup agendado. Ele marcará o trabalho de backup como *Trabalho Concluído*, caso um ponto de recuperação seja criado com êxito.

  > [!NOTE]
  > O servidor DPM/MABS dispara os backups nos horários agendados durante a criação do grupo de proteção. No entanto, esses trabalhos sinalizarão *Aguardando a conclusão do trabalho do Azure Data Box* até que o trabalho seja concluído.

- Depois que o servidor DPM/MABS criar com êxito um ponto de recuperação correspondente ao backup inicial, você poderá excluir a conta de armazenamento (ou o conteúdo específico) associada ao trabalho de Azure Data Box.

## <a name="troubleshooting"></a>Solução de problemas

O agente MAB (Backup do Microsoft Azure) no servidor DPM criará um aplicativo do Azure AD para você, em seu locatário. Este aplicativo requer um certificado para autenticação que é criado e carregado ao configurar a política de propagação offline.

Usamos o Azure PowerShell para criar e carregar o certificado para o aplicativo do Azure AD.

### <a name="issue"></a>Problema

No momento da configuração do backup offline, devido a um defeito de código conhecido no cmdlet Azure PowerShell, você não pode adicionar vários certificados ao mesmo aplicativo do Azure AD criado pelo agente MAB. Isso afetará você se você tiver configurado uma política de propagação offline para o mesmo servidor ou para outro.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Verificar se o problema é resultante dessa causa raiz específica

Para garantir que a falha seja devido ao [Problema](#issue) acima, execute uma das seguintes etapas:

#### <a name="step-1"></a>Etapa 1

Verifique se você vê a seguinte mensagem de erro no console do DPM/MABS no momento da configuração do backup offline:

![Agente dos serviços de recuperação do Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Etapa 2

1. Abra a pasta **Temp** no caminho de instalação. O caminho da pasta Temp padrão é *C:\Arquivos de Programas\Agente de Serviços de Recuperação do Microsoft Azure\Temp*. Procure o arquivo *CBUICurr* e abra-o.
2. No arquivo *CBUICurr*, role até a última linha e verifique se a falha é devido a esta mensagem: "Não foi possível criar uma credencial de aplicativo do Azure AD na conta do cliente. Exceção: a atualização para credencial existente com KeyId \<some guid> não é permitida ".

### <a name="workaround"></a>Solução alternativa

Para resolver esse problema, execute as etapas a seguir e tente a configuração da política novamente.

1. Entre na página de entrada do Azure que aparece na interface do usuário do servidor DPM/MABS usando uma conta diferente com acesso de administrador na assinatura que terá o trabalho de Data Box criado.
2. Caso nenhum outro servidor tenha a propagação offline configurada, nem seja dependente do aplicativo `AzureOfflineBackup_<Azure User Id>`, exclua esse aplicativo em **portal do Azure > Azure Active Directory > Registros de aplicativo**.

   > [!NOTE]
   > Verifique se o aplicativo `AzureOfflineBackup_<Azure User Id>` não tem nenhuma outra propagação offline configurada e se nenhum outro servidor depende desse aplicativo. Vá para **configurações > chaves** na seção chaves públicas. Ele não deve ter outras **chaves públicas** adicionadas. Confira a seguinte captura de tela para referência:
   >
   > ![Chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Etapa 3

No servidor DPM/MABS que você está tentando configurar o backup offline, execute as seguintes ações:

1. Abra a guia **Gerenciar aplicativo de certificado de computador** > **Pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
2. Selecione o certificado acima, clique com o botão direito do mouse em **todas as tarefas** e **exporte** sem chave privada, no formato. cer.
3. Acesse o aplicativo de Backup Offline do Azure mencionado no **ponto 2**. Em **Configurações** > **Chaves** > **Carregar Chave Pública,** carregue o certificado exportado na etapa anterior.

   ![Carregar chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. No servidor, abra o registro digitando **regedit** na janela **executar** .
5. Acesse o Registro *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Microsoft Azure Backup\Config\CloudBackupProvider*. Clique com o botão direito do mouse em **CloudBackupProvider** e adicione um valor de cadeia de caracteres com o nome `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Para obter a ID de usuário do Azure, execute uma destas ações:
    >
    >- No PowerShell conectado ao Azure, execute o comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`.
    > - Navegue até o caminho do registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` com o nome *CurrentUserId*.

6. Clique com o botão direito do mouse na cadeia de caracteres adicionada na etapa anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou no **ponto 2** e selecione **OK**.
7. Para obter o valor de impressão digital, clique duas vezes no certificado, selecione **Detalhes** e role para baixo até ver o campo Impressão digital. Selecione **impressão digital** e copie o valor.

   ![Valor da impressão digital](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Próximas etapas

- [Propagação offline usando um disco próprio (com o serviço de Importação/Exportação do Azure)](backup-azure-backup-server-import-export.md)

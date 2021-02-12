---
title: Faça backup de VMs VMware com o Servidor de Backup do Azure
description: Neste artigo, saiba como usar Servidor de Backup do Azure para fazer backup de VMs VMware em execução em um servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002946"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Faça backup de VMs VMware com o Servidor de Backup do Azure

Este artigo explica como fazer backup de VMs VMware em execução em hosts VMware ESXi/vCenter Server no Azure usando o Servidor de Backup do Azure (MABS).

Este artigo explica como:

- Configurar um canal seguro para que o Servidor de Backup do Azure possa se comunicar com servidores VMware via HTTPS.
- Configure uma conta do VMware que o Servidor de Backup do Azure usa para acessar o servidor VMware.
- Adicione as credenciais da conta ao Backup do Azure.
- Adicione o servidor ESXi ou vCenter ao Servidor de Backup do Azure.
- Configure um grupo de proteção que contenha as VMs do VMware de que você deseja fazer backup, especifique as configurações de backup e agende o backup.

## <a name="supported-vmware-features"></a>Recursos do VMware com suporte

O MABS fornece os seguintes recursos ao fazer backup de máquinas virtuais VMware:

- Backup sem agente: o MABS não exige que um agente seja instalado no servidor vCenter ou ESXi para fazer backup da máquina virtual. Em vez disso, forneça apenas o endereço IP ou FQDN (nome de domínio totalmente qualificado) e as credenciais de logon usadas para autenticar o servidor VMware com o MABS.
- Backup integrado em nuvem: o MABS protege as cargas de trabalho para o disco e a nuvem. O fluxo de trabalho de backup e recuperação do MABS ajuda a gerenciar a retenção de longo prazo e o backup externo.
- Detectar e proteger VMs gerenciadas pelo vCenter: o MABS detecta e protege as VMs implantadas em um servidor VMware (servidor vCenter ou ESXi). Conforme a implantação vai aumentando, use o vCenter para gerenciar seu ambiente VMware. O MABS também detecta VMs gerenciadas pelo vCenter, permitindo que você proteja grandes implantações.
- Proteção automática no nível de pastas: o vCenter permite organizar suas VMs em pastas de VM. O MABS detecta essas pastas e permite que você proteja as VMs no nível da pasta e inclui todas as subpastas. Ao proteger pastas, o MABS só protege as VMs nessa pasta, mas também protege as VMs adicionadas posteriormente. O MABS detecta novas VMs diariamente e as protege automaticamente. Conforme você organiza suas VMs em pastas recursivas, o MABS detecta e protege automaticamente as novas VMs implantadas nas pastas recursivas.
- O MABS protege as VMs armazenadas em um disco local, NFS (sistema de arquivos de rede) ou armazenamento de cluster.
- O MABS protege as VMs migradas para balanceamento de carga: à medida que as VMs são migradas para balanceamento de carga, o MABS automaticamente detecta e continua a proteção da VM.
- O MABS pode recuperar arquivos/pastas de uma VM do Windows sem recuperar toda a VM, o que ajuda a recuperar os arquivos necessários mais rapidamente.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

Antes de iniciar o backup de uma máquina virtual VMware, examine a lista de pré-requisitos e limitações a seguir.

- Se você estiver usando o MABS para proteger um servidor vCenter (em execução no Windows) como um servidor Windows usando o FQDN do servidor, não será possível proteger o servidor vCenter como um servidor VMware usando o FQDN do servidor.
  - Você pode usar o endereço IP estático de vCenter Server como uma solução alternativa.
  - Se você quiser usar o FQDN, deverá interromper a proteção como um servidor do Windows, remover o agente de proteção e, em seguida, adicionar como um servidor VMware usando o FQDN.
- Se você usar o vCenter para gerenciar servidores ESXi em seu ambiente, adicione o vCenter (e não o ESXi) ao grupo de proteção MABS.
- Não é possível fazer backup de instantâneos do usuário antes do primeiro backup do MABS. Depois que o MABS concluir o primeiro backup, você poderá fazer backup de instantâneos do usuário.
- O MABS não pode proteger VMs VMware com discos de passagem e mapeamentos de dispositivo brutos físicos (pRDM).
- O MABS não pode detectar ou proteger o VMware vApps.
- O MABS não pode proteger VMs VMware com instantâneos existentes.

## <a name="before-you-start"></a>Antes de começar

- Verifique se você está executando uma versão do vCenter/ESXi com suporte para backup. Consulte a matriz de suporte [aqui](./backup-mabs-protection-matrix.md).
- Verifique se que você configurou o Servidor de Backup do Azure. Caso contrário, [faça isso](backup-azure-microsoft-azure-backup.md) antes de começar. Você deve estar executando o Servidor de Backup do Azure com as atualizações mais recentes.
- Verifique se as seguintes portas de rede estão abertas:
  - TCP 443 entre MABS e vCenter
  - TCP 443 e TCP 902 entre MABS e host ESXi

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o servidor vCenter

Por padrão, o Servidor de Backup do Azure se comunica com servidores VMware via HTTPS. Para configurar a conexão HTTPS, baixe o certificado de AC (autoridade de certificação) do VMware e importe-o para o Servidor de Backup do Azure.

### <a name="before-you-begin"></a>Antes de começar

- Se você não quiser usar HTTPS, poderá [desabilitar a validação de certificado HTTPS para todos os servidores VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Você normalmente se conecta de um navegador no computador do Servidor de Backup do Azure para o servidor vCenter/ESXi usando o cliente Web vSphere. Na primeira vez que você fizer isso, a conexão não será segura e mostrará o seguinte.
- É importante entender como o Servidor de Backup do Azure lida com backups.
  - Como uma primeira etapa, o Servidor de Backup do Azure faz backup dos dados para armazenamento em disco local. O Servidor de Backup do Azure usa um pool de armazenamento, um conjunto de discos e volumes nos quais o Servidor de Backup do Azure armazena os pontos de recuperação de disco para seus dados protegidos. O pool de armazenamento pode ser DAS (armazenamento diretamente anexado), uma SAN Fibre Channel ou uma SAN ou dispositivo de armazenamento iSCSI. É importante garantir que você tenha armazenamento suficiente para o backup local de seus dados de VM do VMware.
  - O Servidor de Backup do Azure então faz backup do armazenamento em disco local para o Azure.
  - [Obtenha ajuda](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) para descobrir de quanto espaço de armazenamento você precisa. As informações são para DPM, mas também podem ser usadas para o Servidor de Backup do Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Configure um canal seguro da seguinte maneira:

1. No navegador no Servidor de Backup do Azure, insira a URL do Cliente Web do vSphere. Se a página de logon não aparecer, verifique as configurações de proxy do navegador e da conexão.

    ![Cliente Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de logon do cliente Web vSphere, selecione **baixar certificados de AC raiz confiável**.

    ![Baixar Certificado de Autoridade de Certificação raiz confiável](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um arquivo chamado **download** é baixado. Dependendo do navegador, você recebe uma mensagem que pergunta se deseja abrir ou salvar o arquivo.

    ![Baixar Certificado de Autoridade de Certificação](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Salve o arquivo no computador do Servidor de Backup do Azure com uma extensão .zip.

5. Clique com o botão direito do mouse em **download.zip**  >  **extrair tudo**. O arquivo .zip extrai seu conteúdo para a pasta **certs** que contém:
   - O arquivo de certificado raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - O arquivo da CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O arquivo da CRL é associado a um certificado.

    ![Certificados baixados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Na pasta **certs**, clique com o botão direito do mouse no arquivo de certificado raiz > **Renomear**.

    ![Renomear o certificado raiz](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Altere a extensão do certificado raiz para .crt e confirme. O ícone de arquivo muda para um que representa um certificado raiz.

8. Clique com o botão direito no certificado raiz e no menu pop-up, selecione **Instalar certificado**.

9. No **Assistente para importação de certificados**, selecione **computador local** como o destino do certificado e, em seguida, selecione **Avançar**. Confirme se perguntado se deseja permitir alterações no computador.

    ![Boas-Vindas do Assistente](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na página **repositório de certificados** , selecione **Coloque todos os certificados no repositório a seguir** e, em seguida, selecione **procurar** para escolher o repositório de certificados.

    ![Armazenamento de certificado](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Em **selecionar repositório de certificados**, selecione **autoridades de certificação raiz confiáveis** como a pasta de destino para os certificados e, em seguida, selecione **OK**.

    ![Pasta de destino do certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Em **concluindo o assistente para importação de certificados**, verifique a pasta e, em seguida, selecione **concluir**.

    ![Verificar se o certificado está na pasta correta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Após a importação de certificados ser confirmada, entre no vCenter Server para confirmar que sua conexão é segura.

### <a name="disable-https-certificate-validation"></a>Desabilitar validação de certificado HTTPS

Se você tiver limites de segurança em sua organização e não quiser usar o protocolo HTTPS entre servidores VMware e o computador Servidor de Backup do Azure, desabilite o HTTPS da seguinte maneira:

1. Copie e cole o texto a seguir em um arquivo .txt.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Salve o arquivo no computador do Servidor de Backup do Azure com o nome **DisableSecureAuthentication.reg**.

3. Clique duas vezes no arquivo para ativar a entrada do registro.

## <a name="create-a-vmware-role"></a>Criar uma função do VMware

O Servidor de Backup do Azure precisa de uma conta de usuário com permissões para acessar o host ESXi/vCenter Server. Crie uma função do VMware com privilégios específicos e, em seguida, associe uma conta de usuário à função.

1. Entre no vCenter Server (ou no host ESXi se não estiver usando o vCenter Server).
2. No painel **navegador** , selecione **Administração**.

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Em **Administration**  >  **funções** de administração, selecione o ícone Adicionar função (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Em **criar**  >  **nome da função** de função, insira *BackupAdminRole*. O nome da função pode ser o que você quiser, mas deve ser reconhecível para o objetivo da função.

5. Selecione os privilégios conforme resumido na tabela abaixo e, em seguida, selecione **OK**.  A nova função é exibida na lista no painel **Funções**.
   - Selecione o ícone ao lado do rótulo pai para expandir o pai e exibir os privilégios filho.
   - Para selecionar os privilégios da VirtualMachine, você precisa acessar vários níveis da hierarquia pai-filho.
   - Você não precisa selecionar todos os privilégios do filho dentro de um privilégio pai.

    ![Hierarquia de privilégios pai-filho](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de função

A seguinte tabela captura os privilégios que precisam ser atribuídos à conta de usuário que você criar:

| Privilégios para conta de usuário do vCenter 6.5                          | Privilégios para conta de usuário do vCenter 6.7                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Datastore cluster.Configure a datastore cluster                           | Datastore cluster.Configure a datastore cluster                           |
| Datastore.AllocateSpace                                                    | Datastore.AllocateSpace                                                    |
| Datastore.Browse datastore                                                 | Datastore.Browse datastore                                                 |
| Datastore.Low-level file operations                                        | Datastore.Low-level file operations                                        |
| Global.Disable methods                                                     | Global.Disable methods                                                     |
| Global.Enable methods                                                      | Global.Enable methods                                                      |
| Global.Licenses                                                            | Global.Licenses                                                            |
| Global.Log event                                                           | Global.Log event                                                           |
| Global.Manage custom attributes                                            | Global.Manage custom attributes                                            |
| Global.Set custom attribute                                                | Global.Set custom attribute                                                |
| Host.Local operations.Create virtual machine                               | Host.Local operations.Create virtual machine                               |
| Network.Assign network                                                     | Network.Assign network                                                     |
| Resource. Assign virtual machine to resource pool                          | Resource. Assign virtual machine to resource pool                          |
| vApp.Add virtual machine                                                   | vApp.Add virtual machine                                                   |
| vApp.Assign resource pool                                                  | vApp.Assign resource pool                                                  |
| vApp.Unregister                                                            | vApp.Unregister                                                            |
| VirtualMachine.Configuration. Add Or Remove Device                         | VirtualMachine.Configuration. Add Or Remove Device                         |
| Virtual machine.Configuration.Disk lease                                   | Virtual machine.Configuration.Acquire disk lease                           |
| Virtual machine.Configuration.Add new disk                                 | Virtual machine.Configuration.Add new disk                                 |
| Virtual machine.Configuration.Advanced                                     | Virtual machine.Configuration.Advanced configuration                       |
| Virtual machine.Configuration.Disk change tracking                         | Virtual machine.Configuration.Toggle disk change tracking                  |
| Virtual machine.Configuration.Host USB device                              | Virtual machine.Configuration.Configure Host USB device                    |
| Virtual machine.Configuration.Extend virtual disk                          | Virtual machine.Configuration.Extend virtual disk                          |
| Virtual machine.Configuration.Query unowned files                          | Virtual machine.Configuration.Query unowned files                          |
| Virtual machine.Configuration.Swapfile placement                           | Virtual machine.Configuration.Change Swapfile placement                    |
| Virtual machine.Guest Operations.Guest Operation Program Execution         | Virtual machine.Guest Operations.Guest Operation Program Execution         |
| Virtual machine.Guest Operations.Guest Operation Modifications             | Virtual machine.Guest Operations.Guest Operation Modifications             |
| Virtual machine.Guest Operations.Guest Operation Queries                   | Virtual machine.Guest Operations.Guest Operation Queries                   |
| Virtual machine .Interaction .Device connection                            | Virtual machine .Interaction .Device connection                            |
| Virtual machine .Interaction .Guest operating system management by VIX API | Virtual machine .Interaction .Guest operating system management by VIX API |
| Virtual machine .Interaction .Power Off                                    | Virtual machine .Interaction .Power Off                                    |
| Virtual machine .Inventory.Create new                                      | Virtual machine .Inventory.Create new                                      |
| Virtual machine .Inventory.Remove                                          | Virtual machine .Inventory.Remove                                          |
| Virtual machine .Inventory.Register                                        | Virtual machine .Inventory.Register                                        |
| Virtual machine .Provisioning.Allow disk access                            | Virtual machine .Provisioning.Allow disk access                            |
| Virtual machine .Provisioning.Allow file access                            | Virtual machine .Provisioning.Allow file access                            |
| Virtual machine .Provisioning.Allow read-only disk access                  | Virtual machine .Provisioning.Allow read-only disk access                  |
| Virtual machine .Provisioning.Allow virtual machine download               | Virtual machine .Provisioning.Allow virtual machine download               |
| Virtual machine .Snapshot management. Create snapshot                      | Virtual machine .Snapshot management. Create snapshot                      |
| Virtual machine .Snapshot management.Remove Snapshot                       | Virtual machine .Snapshot management.Remove Snapshot                       |
| Virtual machine .Snapshot management.Revert to snapshot                    | Virtual machine .Snapshot management.Revert to snapshot                    |

> [!NOTE]
> A tabela a seguir lista os privilégios para as contas de usuário do vCenter 6.0 e do vCenter 5.5.

| Privilégios para conta de usuário do vCenter 6.0 | Privilégios para conta de usuário do vCenter 5.5 |
| --- | --- |
| Datastore.AllocateSpace | Network.Assign |
| Global.Manage custom attributes | Datastore.AllocateSpace |
| Global.Set custom attribute | VirtualMachine.Config.ChangeTracking |
| Host.Local operations.Create virtual machine | VirtualMachine.State.RemoveSnapshot |
| Network. Assign network | VirtualMachine.State.CreateSnapshot |
| Resource. Assign virtual machine to resource pool | VirtualMachine.Provisioning.DiskRandomRead |
| Virtual machine.Configuration.Add new disk | VirtualMachine.Interact.PowerOff |
| Virtual machine.Configuration.Advanced | VirtualMachine.Inventory.Create |
| Virtual machine.Configuration.Disk change tracking | VirtualMachine.Config.AddNewDisk |
| Virtual machine.Configuration.Host USB device | VirtualMachine.Config.HostUSBDevice |
| Virtual machine.Configuration.Query unowned files | VirtualMachine.Config.AdvancedConfig |
| Virtual machine.Configuration.Swapfile placement | VirtualMachine.Config.SwapPlacement |
| Virtual machine.Interaction.Power Off | Global.ManageCustomFields |
| Virtual machine.Inventory. Create new |   |
| Virtual machine.Provisioning.Allow disk access |   |
| Virtual machine.Provisioning. Allow read-only disk access |   |
| Virtual machine.Snapshot management.Create snapshot |   |
| Virtual machine.Snapshot management.Remove Snapshot |   |

## <a name="create-a-vmware-account"></a>Criar uma conta do VMware

1. No painel vCenter Server **navegador** , selecione **usuários e grupos**. Se você não usar o vCenter Server, crie a conta no host ESXi apropriado.

    ![Opção de Usuários e Grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O painel **usuários e grupos do vCenter** é exibido.

2. No painel **usuários e grupos do vCenter** , selecione a guia **usuários** e, em seguida, selecione o ícone Adicionar usuários (o símbolo +).

    ![Painel Usuários e Grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Na caixa de diálogo **Novo Usuário**, adicione as informações do usuário > **OK**. Neste procedimento, o nome de usuário é BackupAdmin.

    ![Caixa de diálogo Novo Usuário](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Para associar a conta de usuário à função, no painel **navegador** , selecione **permissões globais**. No painel **permissões globais** , selecione a guia **gerenciar** e, em seguida, selecione o ícone Adicionar (o símbolo +).

    ![Painel Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Na permissão **global-adicionar permissão de raiz**, selecione **Adicionar** para escolher o usuário ou grupo.

    ![Escolher o usuário ou o grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar Usuários/Grupos**, escolha **BackupAdmin** > **Adicionar**. Em **Usuários**, o formato *domain\username* é usado para a conta de usuário. Se desejar usar outro domínio, escolha-o na lista **Domínios**. Selecione **OK** para adicionar os usuários selecionados à caixa de diálogo **adicionar permissão** .

    ![Adicionar um usuário BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Em **Função Atribuída**, na lista suspensa, selecione **BackupAdminRole** > **OK**.

    ![Atribuir o usuário à função ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Na guia **Gerenciar** no painel **Permissões Globais**, a nova conta de usuário e a função associada são exibidas na lista.

## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Servidor de Backup do Azure

1. Abra o Servidor de Backup do Azure. Se você não conseguir localizar o ícone na área de trabalho, abra o Backup do Microsoft Azure da lista de aplicativos.

    ![Ícone do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. No console do servidor de backup do Azure, selecione **Gerenciamento**  >   **servidores de produção**  >  **gerenciar VMware**.

    ![Console do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Na caixa de diálogo **gerenciar credenciais** , selecione **Adicionar**.

    ![Caixa de diálogo gerenciar credenciais](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Em **Adicionar credencial**, insira um nome e uma descrição para a nova credencial e especifique o nome de usuário e a senha que você definiu no servidor VMware. O nome *Credencial do Contoso Vcenter* é utilizado para identificar a credencial neste procedimento. Se o servidor VMware e o Servidor de Backup do Azure não estiverem no mesmo domínio, especifique o domínio no nome de usuário.

    ![Caixa de diálogo Adicionar Credencial do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Selecione **Adicionar** para adicionar a nova credencial.

    ![Adicionar novas credenciais](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Adicionar o vCenter Server

Adicionar vCenter Server para o Servidor de Backup do Azure.

1. No console do servidor de backup do Azure, selecione **Gerenciamento**  >  **servidores de produção**  >  **Adicionar**.

    ![Abra o Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. No **Assistente de adição de servidor de produção**,  >  Selecione a página **tipo de servidor de produção** , selecione **servidores VMware** e, em seguida, selecione **Avançar**.

    ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Em **Selecionar Computadores**  **Nome do Servidor/Endereço IP**, especifique o endereço IP ou o FQDN do servidor VMware. Se todos os servidores ESXi forem gerenciados pelo mesmo vCenter, especifique usar o nome do vCenter. Caso contrário, adicione o host ESXi.

    ![Especifique o servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Em **Porta SSL**, insira a porta usada para se comunicar com o servidor do VMware. 443 é a porta padrão, mas você poderá alterá-la se o servidor do VMware escutar em uma porta diferente.

5. Em **Especificar Credencial**, selecione a credencial que você criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Selecione **Adicionar** para adicionar o servidor VMware à lista servidores. Em seguida, selecione **Avançar**.

    ![Adicionar servidor do VMware e a credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo** , selecione **Adicionar** para adicionar o servidor VMware ao servidor de backup do Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor do VMware.

    ![Adicionar servidor VMware para o Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as configurações na página **Concluir**.

   ![Página Concluir](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se você tiver vários hosts ESXi não gerenciados pelo vCenter Server ou você tiver várias instâncias do vCenter Server, precisará executar novamente o Assistente para adicionar os servidores.

## <a name="configure-a-protection-group"></a>Configurar grupo de proteção

Adicione VMs do VMware para o backup. Grupos de proteção reúnem várias VMs e aplicam as mesmas configurações de retenção de dados e backup a todas as VMs no grupo.

1. No console do Servidor de Backup do Azure, selecione **proteção**> **nova**.

    ![Abra o assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas do assistente para **criar novo grupo de proteção** , selecione **Avançar**.

    ![Caixa de diálogo Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página **Selecionar tipo de grupo de proteção** , selecione **servidores** e, em seguida, selecione **Avançar**. A página **Selecionar membros do grupo** é exibida.

1. Em **selecionar membros do grupo**, selecione as VMs (ou pastas de VM) das quais você deseja fazer backup. Em seguida, selecione **Avançar**.

    - Quando você seleciona uma pasta, VMs ou pastas dentro dessa pasta também são selecionadas para backup. Você pode desmarcar as pastas ou VMs das quais não deseja fazer backup.
1. Se já estiver sendo feito backup de uma VM ou pasta, você não poderá selecioná-la. Isso garante que os pontos de recuperação duplicados não sejam criados para uma VM.

    ![Selecionar membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página **Selecionar Método de Proteção de Dados**, insira um nome para o grupo de proteção e as configurações de proteção. Para fazer backup do Azure, defina a proteção de curto prazo como **Disco** e habilite a proteção online. Em seguida, selecione **Avançar**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **Especificar Metas de Curto Prazo**, especifique por quanto tempo deseja manter os dados submetidos a backup em disco.
   - Em **Período de Retenção**, especifique por quantos dias os pontos de recuperação de disco devem ser mantidos.
   - Em **Frequência de sincronização**, especifique a frequência com que os pontos de recuperação de disco serão criados.
       - Se você não quiser definir um intervalo de backup, poderá verificar **logo antes de um ponto de recuperação** para que um backup seja executado logo antes de cada ponto de recuperação ser agendado.
       - Backups de curto prazo são backups completos e não incrementais.
       - Selecione **Modificar** para alterar os horários/datas em que ocorrem os backups de curto prazo.

         ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Em **Examinar Alocação do Disco**, examine o espaço em disco fornecido para os backups de VM. para as VMs.

   - As alocações de disco recomendadas baseiam-se no período de retenção especificado, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, selecione **Avançar**.
   - **Tamanho dos dados:** tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** a quantidade de espaço em disco recomendada para o grupo de proteção. Se você quiser modificar essa configuração, deverá alocar um espaço total que seja um pouco maior do que o valor estimado de cada fonte de dados.
   - **Colocar dados:** se você ativar a colocação, várias fontes de dados na proteção poderão ser mapeadas para um único volume de réplica e ponto de recuperação. A colocação não tem suporte para todas as cargas de trabalho.
   - **Aumentar automaticamente:** Se você ativar essa configuração, se os dados no grupo protegido ultrapassarem a alocação inicial, Servidor de Backup do Azure tentará aumentar o tamanho do disco em 25%.
   - **Detalhes do pool de armazenamento:** mostra o status do pool de armazenamento, incluindo o tamanho do disco total e restante.

    ![Examinar a alocação de disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página **escolher método de criação de réplica** , especifique como deseja fazer o backup inicial e, em seguida, selecione **Avançar**.
   - O padrão é **Automaticamente pela rede** e **Agora**.
   - Se você usar o padrão, é recomendável especificar um horário fora do horário de pico. Escolha **Mair tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Em **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Em seguida, selecione **Avançar**.
      - Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com um agendamento definido.
      - Se você não desejar configurar verificações de consistência automáticas, poderá executar uma verificação manual. Para fazer isso, clique com o botão direito do mouse no grupo de proteção > **Executar Verificação de Consistência**.

1. Na página **Especificar Dados de Proteção Online**, selecione as VMs ou as pastas da VM das quais você deseja fazer backup. Você pode selecionar os membros individualmente ou selecionar **selecionar tudo** para escolher todos os membros. Em seguida, selecione **Avançar**.

    ![Especificar dados de proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **Especificar Agendamento de Backup Online**, especifique a frequência com que você deseja fazer backup de dados do armazenamento local para o Azure.

    - Pontos de recuperação de nuvem para os dados serão gerados acordo com o agendamento. Em seguida, selecione **Avançar**.
    - Depois que o ponto de recuperação é gerado, ele é transferido para o cofre dos serviços de recuperação no Azure.

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **Especificar a Política de Retenção Online**, indique por quanto tempo deseja manter os pontos de recuperação criados dos backups diários/semanais/mensais/anuais para o Azure. em seguida, selecione **Avançar**.

    - Não há um tempo limite para manter dados no Azure.
    - O único limite é que você não pode ter mais de 9.999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

    ![Especificar política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo** , examine as configurações e, em seguida, selecione **Criar grupo**.

    ![Resumo da configuração e do membro do grupo de proteção](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>Backups paralelos de VMware

>[!NOTE]
> Esse recurso é aplicável para MABS v3 UR1.

Com versões anteriores do MABS, os backups paralelos eram realizados somente em grupos de proteção. Com o MABS v3 UR1, todos os backups de VMs VMWare em um único grupo de proteção são paralelos, levando a backups de VM mais rápidos. Todos os trabalhos de replicação delta do VMWare são executados em paralelo. Por padrão, o número de trabalhos a serem executados em paralelo é definido como 8.

Você pode modificar o número de trabalhos usando a chave do registro, conforme mostrado abaixo (não presente por padrão, você precisa adicioná-lo):

**Caminho da chave**: `Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Tipo de chave**: valor DWORD (32 bits).

> [!NOTE]
> Você pode modificar o número de trabalhos para um valor mais alto. Se você definir o número de trabalhos como 1, os trabalhos de replicação são executados em série. Para aumentar o número para um valor mais alto, você deve considerar o desempenho do VMWare. Considere o número de recursos em uso e uso adicional necessário no VMWare vSphere Server e determine o número de trabalhos de replicação delta a serem executados em paralelo. Além disso, essa alteração afetará apenas os grupos de proteção recém-criados. Para grupos de proteção existentes, você deve adicionar temporariamente outra VM ao grupo de proteção. Isso deve atualizar a configuração do grupo de proteção de acordo. Você pode remover essa VM do grupo de proteção após a conclusão do procedimento.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Para fazer backup do vSphere 6,7, faça o seguinte:

- Habilitar o TLS 1,2 no servidor MABS

>[!NOTE]
>O VMWare 6,7 em diante tinha o TLS habilitado como protocolo de comunicação.

- Defina as chaves do Registro da seguinte forma:

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="exclude-disk-from-vmware-vm-backup"></a>Excluir disco do backup de VM do VMware

> [!NOTE]
> Esse recurso é aplicável para MABS v3 UR1.

Com o MABS v3 UR1, você pode excluir o disco específico do backup de VM do VMware. O **ExcludeDisk.ps1** de script de configuração está localizado no `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Para configurar a exclusão de disco, siga as etapas abaixo:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Identificar a VM do VMWare e os detalhes do disco a excluir

  1. No console do VMware, vá para configurações de VM para as quais você deseja excluir o disco.
  2. Selecione o disco a ser excluído e anote o caminho para esse disco.

        Por exemplo, para excluir o Disco Rígido 2 do TestVM4, o caminho para o Disco Rígido 2 é **[datastore1] TestVM4/TestVM4\_1.vmdk**.

        ![Disco rígido a ser excluído](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>Configurar o servidor MABS

Navegue até o servidor MABS onde a VM VMware está configurada para proteção para configurar a exclusão de disco.

  1. Obtenha os detalhes do host do VMware que está protegido no servidor MABS.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Selecione o host do VMware e liste a proteção de VMs para o host do VMware.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Selecione a VM cujo disco você deseja excluir.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Para excluir o disco, navegue até a `Bin` pasta e execute o script de *ExcludeDisk.ps1* com os seguintes parâmetros:

        > [!NOTE]
        > Antes de executar esse comando, interrompa o serviço DPMRA no servidor MABS. Caso contrário, o script retornará êxito, mas não atualizará a lista de exclusão. Verifique se não há trabalhos em andamento antes de interromper o serviço.

     **Para adicionar/remover o disco da exclusão, execute o seguinte comando:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Exemplo**:

     Para adicionar a exclusão de disco para TestVM4, execute o seguinte comando:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Verifique se o disco foi adicionado para exclusão.

     **Para adicionar a exclusão existente para VMs específicas, execute o seguinte comando:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Exemplo**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Depois de configurar a proteção para essa VM, o disco excluído não será listado durante a proteção.

        > [!NOTE]
        > Se estiver executando essas etapas para uma VM já protegida, você precisará executar a verificação de consistência manualmente depois de adicionar o disco para exclusão.

### <a name="remove-the-disk-from-exclusion"></a>Remover o disco da exclusão

Para remover o disco da exclusão, execute o seguinte comando:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas ao configurar backups, examine o [guia de solução de problemas para o Servidor de Backup do Azure](./backup-azure-mabs-troubleshoot.md).

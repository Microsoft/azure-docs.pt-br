---
title: Faça backup de VMs VMware com o Servidor de Backup do Azure
description: Use o Servidor de Backup do Azure para fazer backup de VMs do VMware em execução em um servidor vCenter/ESX do VMware.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: adb8cb6a5740b7d09848c792093ea62f69f521a7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466803"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Faça backup de VMs VMware com o Servidor de Backup do Azure

Este artigo explica como fazer backup de VMs VMware em execução em hosts do VMware ESXi/vCenter Server no Azure usando o Servidor de Backup do Azure.

Este artigo explica como:

- Configurar um canal seguro para que o Servidor de Backup do Azure possa se comunicar com servidores VMware via HTTPS.
- Configure uma conta do VMware que o Servidor de Backup do Azure usa para acessar o servidor VMware.
- Adicione as credenciais da conta ao Backup do Azure.
- Adicione o servidor ESXi ou vCenter ao Servidor de Backup do Azure.
- Configure um grupo de proteção que contenha as VMs do VMware de que você deseja fazer backup, especifique as configurações de backup e agende o backup.

## <a name="before-you-start"></a>Antes de iniciar
- Verifique se você está executando uma versão do vCenter/ESXi com suporte para backup – versões 5.5, 6.0 e 6.5.
- Verifique se que você configurou o Servidor de Backup do Azure. Caso contrário, [faça isso](backup-azure-microsoft-azure-backup.md) antes de começar. Você deve estar executando o Servidor de Backup do Azure com as atualizações mais recentes.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o servidor vCenter

Por padrão, o Servidor de Backup do Azure se comunica com servidores VMware via HTTPS. Para configurar a conexão HTTPS, baixe o certificado de AC (autoridade de certificação) do VMware e importe-o para o Servidor de Backup do Azure.


### <a name="before-you-start"></a>Antes de iniciar

- Se você não quiser usar HTTPS, poderá [desabilitar a validação de certificado HTTPS para todos os servidores VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Você normalmente se conecta de um navegador no computador do Servidor de Backup do Azure para o servidor vCenter/ESXi usando o cliente Web vSphere. Na primeira vez em que você fizer isso, a conexão não será segura e mostrará o seguinte.
- É importante entender como o Servidor de Backup do Azure lida com backups.
    - Como uma primeira etapa, o Servidor de Backup do Azure faz backup dos dados para armazenamento em disco local. O Servidor de Backup do Azure usa um pool de armazenamento, um conjunto de discos e volumes nos quais o Servidor de Backup do Azure armazena os pontos de recuperação de disco para seus dados protegidos. O pool de armazenamento pode ser DAS (armazenamento diretamente anexado), uma SAN Fibre Channel ou uma SAN ou dispositivo de armazenamento iSCSI. É importante garantir que você tenha armazenamento suficiente para backup local dos seus dados de VM do VMware.
    - O Servidor de Backup do Azure então faz backup do armazenamento em disco local para o Azure.
    - [Obtenha ajuda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) para descobrir de quanto espaço de armazenamento você precisa. As informações são para DPM, mas também podem ser usadas para o Servidor de Backup do Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Configure um canal seguro da seguinte maneira:

1. No navegador no Servidor de Backup do Azure, insira a URL do Cliente Web do vSphere. Se a página de logon não aparecer, verifique as configurações de proxy do navegador e da conexão.

    ![Cliente Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de logon de cliente Web vSphere, clique em **Baixar certificados de AC raiz confiável**.

    ![Baixar Certificado de Autoridade de Certificação raiz confiável](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um arquivo chamado **download** é baixado. Dependendo do navegador, você recebe uma mensagem que pergunta se deseja abrir ou salvar o arquivo.

    ![Baixar Certificado de Autoridade de Certificação](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Salve o arquivo no computador do Servidor de Backup do Azure com uma extensão .zip.

5. Clique com o botão direito do mouse em **download.zip** > **Extrair tudo**. O arquivo .zip extrai seu conteúdo para a pasta **certs** que contém:
   - O arquivo de certificado raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - O arquivo da CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O arquivo da CRL é associado a um certificado.

     ![Certificados baixados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Na pasta **certs**, clique com o botão direito do mouse no arquivo de certificado raiz > **Renomear**.

    ![Renomear o certificado raiz](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Altere a extensão do certificado raiz para .crt e confirme. O ícone de arquivo muda para um que representa um certificado raiz.

7. Clique com o botão direito no certificado raiz e no menu pop-up, selecione **Instalar certificado**.

8. No **Assistente de Importação de Certificados**, selecione **Computador Local** como o destino para o certificado e clique em **Avançar**. Confirme se perguntado se deseja permitir alterações no computador.

    ![Boas-Vindas do Assistente](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Na página **Repositório de Certificados**, selecione **Colocar todos os certificados no repositório a seguir** e, depois, clique em **Procurar** para escolher o repositório de certificados.

     ![Armazenamento de certificado](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. Em **Selecionar Repositório de Certificados**, selecione **Autoridades de Certificação Confiáveis** como a pasta de destino para os certificados e, depois, clique em **OK**.

    ![Pasta de destino do certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. Em **Concluindo o Assistente de Importação de Certificado**, verifique a pasta e, em seguida, clique em **Concluir**.

    ![Verificar se o certificado está na pasta correta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. Após a importação de certificados ser confirmada, entre no vCenter Server para confirmar que sua conexão é segura.




### <a name="disable-https-certificate-validation"></a>Desabilitar validação de certificado HTTPS

Se você tiver limites de segurança dentro da sua organização e não quiser usar o protocolo HTTPS entre servidores VMware e o computador do Servidor de Backup do Azure, desabilite HTTPS da seguinte maneira:
1. Copie e cole o texto a seguir em um aquivo .txt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Salve o arquivo no computador do Servidor de Backup do Azure com o nome **DisableSecureAuthentication.reg**.

3. Clique duas vezes no arquivo para ativar a entrada do registro.


## <a name="create-a-vmware-role"></a>Criar uma função do VMware

O Servidor de Backup do Azure precisa de uma conta de usuário com permissões para acessar o host ESXi/vCenter Server. Crie uma função do VMware com privilégios específicos e, em seguida, associe uma conta de usuário à função.

1. Entre no vCenter Server (ou no host ESXi se não estiver usando o vCenter Server).
2. No painel **Navegador**, clique em **Administração**.

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Em **Administração** > **Funções**, clique no ícone de adicionar função (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. Em **Criar Função** > **Nome da função**, insira *BackupAdminRole*. O nome da função pode ser o que você quiser, mas deve ser reconhecível para o objetivo da função.

5. Selecione os privilégios conforme resumidos na tabela a seguir e, em seguida, clique em **OK**.  A nova função é exibida na lista no painel **Funções**.
   - Clique no ícone ao lado do rótulo pai para expandi-lo e exibir os privilégios filho.
   - Para selecionar os privilégios da VirtualMachine, você precisa acessar vários níveis da hierarquia pai-filho.
   - Você não precisa selecionar todos os privilégios do filho dentro de um privilégio pai.

     ![Hierarquia de privilégios pai-filho](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões da função
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Criar uma conta do VMware

1. No painel **Navegador** do vCenter Server, clique em **Usuários e Grupos**. Se você não usar o vCenter Server, crie a conta no host ESXi apropriado.

    ![Opção de Usuários e Grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O painel **usuários e grupos do vCenter** é exibido.


2. No painel **Usuários e Grupos do vCenter**, selecione a guia **Usuários** e, depois, clique no ícone Adicionar usuários (o símbolo +).

     ![Painel Usuários e Grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. Na caixa de diálogo **Novo Usuário**, adicione as informações do usuário > **OK**. Neste procedimento, o nome de usuário é BackupAdmin.

    ![Caixa de diálogo Novo Usuário](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Para associar a conta de usuário à função, no painel **Navegador**, clique em **Permissões Globais**. No painel **Permissões Globais**, selecione a guia **Gerenciar** e, em seguida, clique no ícone Adicionar (o símbolo +).

    ![Painel Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. Em **Permissão Global Raiz – Adicionar Permissão** e clique em **Adicionar** para escolher o usuário ou grupo.

    ![Escolher o usuário ou o grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar Usuários/Grupos**, escolha **BackupAdmin** > **Adicionar**. Em **Usuários**, o formato *domain\username* é usado para a conta de usuário. Se desejar usar outro domínio, escolha-o na lista **Domínios**. Clique em **OK** para adicionar os usuários selecionados à caixa de diálogo **Adicionar Permissão**.

    ![Adicionar um usuário BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  Em **Função Atribuída**, na lista suspensa, selecione **BackupAdminRole** > **OK**.

    ![Atribuir o usuário à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Na guia **Gerenciar** no painel **Permissões Globais**, a nova conta de usuário e a função associada são exibidas na lista.


## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Servidor de Backup do Azure


1. Abra o Servidor de Backup do Azure. Se você não conseguir localizar o ícone na área de trabalho, abra o Backup do Microsoft Azure da lista de aplicativos.

    ![Ícone do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. No console do Servidor de Backup do Azure, clique em **Gerenciamento** >  **Servidores de Produção** > **Gerenciar VMware**.

    ![Console do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. Na caixa de diálogo **Gerenciar Credenciais**, clique em **Adicionar**.

    ![Caixa de diálogo Gerenciar Credenciais do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Em **Adicionar Credencial**, insira um nome e uma descrição para a nova credencial e especifique o nome de usuário e senha que você definiu no servidor do VMware. O nome *Credencial do Contoso Vcenter* é utilizado para identificar a credencial neste procedimento. Se o servidor VMware e o Servidor de Backup do Azure não estiverem no mesmo domínio, especifique o domínio no nome de usuário.

    ![Caixa de diálogo Adicionar Credencial do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Clique em **Adicionar** para adicionar a nova credencial.

    ![Caixa de diálogo Gerenciar Credenciais do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Adicionar o vCenter Server

Adicionar vCenter Server para o Servidor de Backup do Azure.


1. No console do Servidor de Backup do Azure, clique em **Gerenciamento** > **Servidores de Produção** > **Adicionar**.

    ![Abra o Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. Na página **Assistente de Adição de Servidor de Produção** > **Selecionar tipo de Servidor de Produção**, selecione **Servidores VMware** e clique em **Avançar**.

     ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Em **Selecionar Computadores** **Nome do Servidor/Endereço IP**, especifique o endereço IP ou o FQDN do servidor VMware. Se todos os servidores ESXi forem gerenciados pelo mesmo vCenter, especifique usar o nome do vCenter. Caso contrário, adicione o host ESXi.

    ![Especifique o servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Em **Porta SSL**, insira a porta usada para se comunicar com o servidor do VMware. 443 é a porta padrão, mas você poderá alterá-la se o servidor do VMware escutar em uma porta diferente.

5. Em **Especificar Credencial**, selecione a credencial que você criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista de servidores. Clique em **Avançar**.

    ![Adicionar servidor do VMware e a credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo**, clique em **Adicionar** para adicionar o servidor do VMware ao Servidor de Backup do Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor do VMware.

    ![Adicionar servidor VMware para o Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as configurações na página **Concluir**.

   ![Página Concluir](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se você tiver vários hosts ESXi não gerenciados pelo vCenter Server ou você tiver várias instâncias do vCenter Server, precisará executar novamente o Assistente para adicionar os servidores.




## <a name="configure-a-protection-group"></a>Configurar grupo de proteção

Adicione VMs do VMware para o backup. Grupos de proteção reúnem várias VMs e aplicam as mesmas configurações de retenção de dados e backup a todas as VMs no grupo.


1. No console do Servidor de Backup do Azure, clique em **Proteção** > **Novo**.

    ![Abra o assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas do assistente **Criar Novo Grupo de Proteção**, clique em **Avançar**.

    ![Caixa de diálogo Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página **Selecionar tipo de grupo de Proteção**, selecione **Servidores** e, depois, clique em **Avançar**. A página **Selecionar membros do grupo** é exibida.

1. Em **Selecionar membros do grupo** > selecione as VMs (ou pastas VM) de que você deseja fazer backup. Clique em **Avançar**.

    - Quando você seleciona uma pasta, VMs ou pastas dentro dessa pasta também são selecionadas para backup. Você pode desmarcar as pastas ou VMs das quais não deseja fazer backup.
1. Se já estiver sendo feito backup de uma VM ou pasta, você não poderá selecioná-la. Isso assegura que não sejam criados pontos de recuperação duplicados para uma VM. .

     ![Selecionar membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. Na página **Selecionar Método de Proteção de Dados**, insira um nome para o grupo de proteção e as configurações de proteção. Para fazer backup do Azure, defina a proteção de curto prazo como **Disco** e habilite a proteção online. Clique em **Avançar**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **Especificar Metas de Curto Prazo**, especifique por quanto tempo deseja manter os dados submetidos a backup em disco.
   - Em **Período de Retenção**, especifique por quantos dias os pontos de recuperação de disco devem ser mantidos.
   - Em **Frequência de sincronização**, especifique a frequência com que os pontos de recuperação de disco serão criados.
       - Se você não quiser configurar um intervalo de backup, poderá marcar **Logo antes de um ponto de recuperação** para que um backup seja executado antes de cada ponto de recuperação ser agendado.
       - Backups de curto prazo são backups completos e não incrementais.
       - Clique em **Modificar** para alterar as datas/horários em que ocorrem os backups de curto prazo.

     ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Em **Examinar Alocação do Disco**, examine o espaço em disco fornecido para os backups de VM. para as VMs.

   - As alocações de disco recomendadas baseiam-se no período de retenção especificado, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, clique em **Avançar**.
   - **Tamanho dos dados:** tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** a quantidade de espaço em disco recomendada para o grupo de proteção. Se você desejar modificar essa configuração, deverá alocar um espaço total ligeiramente maior do que o aumento estimado para cada fonte de dados.
   - **Colocar dados:** se você ativar a colocação, várias fontes de dados na proteção poderão ser mapeadas para um único volume de réplica e ponto de recuperação. A colocação não tem suporte para todas as cargas de trabalho.
   - **Aumentar automaticamente:** se você ativar essa configuração, se os dados no grupo protegido ultrapassarem a alocação inicial, o Servidor de Backup do Azure tentará aumentar o tamanho do disco em 25%.
   - **Detalhes do pool de armazenamento:** mostra o status do pool de armazenamento, incluindo o tamanho do disco total e restante.

     ![Examinar a alocação de disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página **Escolher Método de Criação de Réplica**, especifique como você deseja fazer o backup inicial e, em seguida, clique em **Avançar**.
   - O padrão é **Automaticamente pela rede** e **Agora**.
   - Se você usar o padrão, é recomendável especificar um horário fora do horário de pico. Escolha **Mair tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

     ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Em **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Clique em **Avançar**.
      - Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com um agendamento definido.
      - Se você não desejar configurar verificações de consistência automáticas, poderá executar uma verificação manual. Para fazer isso, clique com o botão direito do mouse no grupo de proteção > **Executar Verificação de Consistência**.

1. Na página **Especificar Dados de Proteção Online**, selecione as VMs ou as pastas da VM das quais você deseja fazer backup. Você pode selecionar os membros individualmente ou clicar em **Selecionar tudo** para escolher todos os membros. Clique em **Avançar**.

      ![Especificar dados de proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **Especificar Agendamento de Backup Online**, especifique a frequência com que você deseja fazer backup de dados do armazenamento local para o Azure.

    - Pontos de recuperação de nuvem para os dados serão gerados acordo com o agendamento. Clique em **Avançar**.
    - Quando o ponto de recuperação é gerado, ele é transferido para o cofre dos Serviços de Recuperação no Azure.

      ![Especifique o cronograma do backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **Especificar a Política de Retenção Online**, indique por quanto tempo deseja manter os pontos de recuperação criados dos backups diários/semanais/mensais/anuais para o Azure. em seguida, clique em **Avançar**.

    - Não há um tempo limite para manter dados no Azure.
    - O único limite é que você não pode ter mais de 9.999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

      ![Especificar política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. Na página **Resumo**, examine as configurações e clique em **Criar Grupo**.

     ![Resumo da configuração e do membro do grupo de proteção](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Para fazer backup do vSphere 6,7, faça o seguinte:

- Habilitar o TLS 1,2 no servidor DPM
  >[!Note]
  >O VMWare 6,7 em diante habilitou o TLS como protocolo de comunicação.

- Defina as chaves do registro da seguinte maneira:  

  Editor do registro do Windows versão 5, 0

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas ao configurar backups, examine o [guia de solução de problemas para o Servidor de Backup do Azure](./backup-azure-mabs-troubleshoot.md).

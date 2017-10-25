---
title: "Faça backup de servidores VMware com o Servidor de Backup do Azure | Microsoft Docs"
description: "Utilize o Servidor de Backup do Azure para fazer backup de servidores VMware vCenter/ESXi para o Azure ou disco. Este artigo fornece instruções passo a passo para fazer backup (ou proteger) suas cargas de trabalho do VMware."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Fazer backup de um servidor do VMware no Azure

Este artigo explica como configurar um Servidor de Backup do Azure para ajudar a proteger cargas de trabalho do servidor do VMware. Este artigo pressupõe que você já tenha o Servidor de Backup do Azure instalado. Se você não tiver o Servidor de Backup do Azure instalado, consulte [Preparar-se para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).

O Servidor de Backup do Azure pode fazer backup ou ajudar a proteger o VMware vCenter Server versão 6.5, 6.0 e 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o servidor vCenter

Por padrão, o Servidor de Backup do Azure se comunica com cada vCenter Server por um canal HTTPS. Para ativar a comunicação segura, recomendamos instalar o Certificado de Autoridade (AC) de Certificação do VMware no Servidor de Backup do Azure. Se não precisar de uma comunicação segura e preferir desabilitar o requisito de HTTPS, consulte [Desabilitar o protocolo de comunicação segura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Para criar uma conexão segura entre o Servidor de Backup do Azure e o vCenter Server, importe o certificado confiável para o Servidor de Backup do Azure.

Normalmente, você usa um navegador no computador do Servidor de Backup do Azure para se conectar ao vCenter Server pelo Cliente Web vSphere. Na primeira vez que usar o navegador do Servidor de Backup do Azure para se conectar ao vCenter Server, a conexão não será segura. A imagem a seguir mostra a conexão não segura.

![Exemplo de conexão não segura ao servidor do VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Para corrigir esse problema e criar uma conexão segura, baixe os certificados CA de raiz confiável.

1. No navegador do Servidor de Backup do Azure, insira a URL do Cliente Web vSphere. A página de logon do cliente Web vSphere aparece.

    ![Cliente Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Na parte inferior das informações para administradores e desenvolvedores, localize o link **Baixar certificados AC raiz confiável**.

    ![Link para baixar os Certificados de AC raiz confiável](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Se você não vir a página de logon do cliente Web vSphere, verifique as configurações de proxy do navegador.

2. Clique em **Baixar certificados de autoridade de certificação raiz confiável**.

    O vCenter Server baixa um arquivo no computador local. O nome do arquivo é **download**. Dependendo do navegador, você recebe uma mensagem que pergunta se deseja abrir ou salvar o arquivo.

    ![Baixar a mensagem quando certificados são baixados](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Salve o arquivo em um local no Servidor de Backup do Azure. Ao salvar o arquivo, adicione a extensão de nome de arquivo .zip.

    O arquivo é um arquivo. zip que contém as informações sobre os certificados. Com a extensão .zip, você pode usar as ferramentas de extração.

4. Clique com o botão direito do mouse em **download.zip** e, depois, selecione **Extrair Tudo** para extrair o conteúdo.

    O arquivo .zip extrai o conteúdo para uma pasta chamada **certs**. Dois tipos de arquivos são exibidos na pasta certs. O arquivo de certificado raiz tem uma extensão que começa com uma sequência numerada como .0 e .1.
    
    O arquivo da CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O arquivo da CRL é associado a um certificado.

    ![Baixar o arquivo extraído localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Na pasta **certs**, clique com o botão direito do mouse no arquivo de certificado raiz e, depois, clique em **Renomear**.

    ![Renomear o certificado raiz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Altere a extensão do certificado raiz para .crt. Quando for perguntado se tem certeza de que deseja alterar a extensão, clique em **Sim** ou em **OK**. Caso contrário, você alterará a função pretendida do arquivo. O ícone do arquivo muda para um ícone que representa um certificado raiz.

6. Clique com o botão direito no certificado raiz e no menu pop-up, selecione **Instalar certificado**.

    A caixa de diálogo **Assistente para Importação de Certificados** é exibida.

7. Na caixa de diálogo **Assistente para Importação de Certificados**, selecione **Computador Local** como o destino do certificado e, depois, clique em **Avançar** para continuar.

    ![Opções de destino do armazenamento de certificados ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Se for solicitado se você deseja permitir alterações no computador, clique em **Sim** ou **OK**, para todas as alterações.

8. Na página **Repositório de Certificados**, selecione **Colocar todos os certificados no repositório a seguir** e, depois, clique em **Procurar** para escolher o repositório de certificados.

    ![Colocar os certificados em um ponto de armazenamento específico](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    A caixa de diálogo **Selecionar Repositório de Certificados** é exibida.

    ![Hierarquia de pastas do armazenamento de certificados](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Selecione **Autoridades de Certificação Confiáveis** como a pasta de destino para os certificados e, depois, clique em **OK**.

    ![Pasta de destino do certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    A pasta **Autoridades de Certificação Confiáveis** é confirmada como o repositório de certificados. Clique em **Avançar**.

    ![Pasta de repositório de certificados](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na página **Concluindo o Assistente para Importação de Certificados**, verifique se o certificado está na pasta desejada e, depois, clique em **Concluir**.

    ![Verificar se o certificado está na pasta correta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Uma caixa de diálogo é exibida e a importação de certificado bem-sucedida é confirmada.

11. Entre no vCenter Server para confirmar que a conexão é segura.

  Se a importação do certificado não for bem-sucedida e não for possível estabelecer uma conexão segura, consulte a documentação do VMware vSphere em [Obtendo certificados do servidor](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Se você tiver limites de segurança em sua organização e não desejar ativar o protocolo HTTPS, use o procedimento a seguir para desabilitar a comunicação segura.

### <a name="disable-secure-communication-protocol"></a>Desabilite seu protocolo de comunicação segura

Caso sua organização não exija o protocolo HTTPS, use as etapas a seguir para desabilitar o HTTPS. Para desabilitar o comportamento padrão, crie uma chave do registro que ignora o comportamento padrão.

1. Copie e cole o texto a seguir em um aquivo .txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Salve o arquivo no computador do Servidor de Backup do Azure. Para o nome de arquivo, use DisableSecureAuthentication.reg.

3. Clique duas vezes no arquivo para ativar a entrada do registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Crie uma conta de usuário e a função no servidor vCenter

No servidor vCenter, uma função é um conjunto predefinido de privilégios. Um administrador do vCenter Server cria as funções. Para atribuir permissões, o administrador emparelha contas de usuário com uma função. Para estabelecer as credenciais de usuário necessárias para fazer backup do computador do vCenter Server, crie uma função com privilégios específicos e, depois, associe a conta de usuário à função.

O Servidor de Backup do Azure usa um nome de usuário e uma senha para se autenticar no vCenter Server. O Servidor de Backup do Azure utiliza essas credenciais como autenticação para todas as operações de backup.

Para adicionar uma função do vCenter Server e seus privilégios a um administrador de backup:

1. Entre no vCenter Server e, em seguida, no painel **Navegador** do vCenter Server, clique em **Administração**.

    ![Opção de administração no painel Navegador do vCenter Server](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Em **Administração**, selecione **Funções** e, depois, no painel **Funções**, clique no ícone Adicionar função (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    A caixa de diálogo **Criar Função** é exibida.

    ![Criar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Na caixa de diálogo **Criar Função**, na caixa **Nome da função**, insira *BackupAdminRole*. O nome da função pode ser o que você quiser, mas deve ser reconhecível para o objetivo da função.

4. Selecione os privilégios para a versão apropriada do vCenter e, em seguida, clique em **OK**. A tabela a seguir identifica os privilégios necessários para o vCenter 6.0 e o vCenter 5.5.

  Ao selecionar os privilégios, clique no ícone ao lado do rótulo pai para expandir o pai e exibir os privilégios filho. Para selecionar os privilégios da VirtualMachine, você precisa acessar vários níveis da hierarquia pai-filho. Você não precisa selecionar todos os privilégios do filho dentro de um privilégio pai.

  ![Hierarquia de privilégios pai-filho](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Depois de clicar em **OK**, a nova função é exibida na lista no painel Funções.

|Privilégios do vCenter 6.0| Privilégios do vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Criar uma conta de usuário do vCenter Server e as permissões

Depois de configurar a função com privilégios, crie uma conta de usuário. A conta de usuário tem um nome e uma senha, que fornece as credenciais usadas para autenticação.

1. Para criar uma conta de usuário, no painel **Navegador** do vCenter Server, clique em **Usuários e Grupos**.

    ![Opção de Usuários e Grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O painel **Usuários e Grupos do vCenter** é exibido.

    ![Painel Usuários e Grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. No painel **Usuários e Grupos do vCenter**, selecione a guia **Usuários** e, depois, clique no ícone Adicionar usuários (o símbolo +).

    A caixa de diálogo **Novo Usuário** é exibida.

3. Na caixa de diálogo **Novo Usuário**, adicione as informações do usuário e, em seguida, clique em **OK**. Neste procedimento, o nome de usuário é BackupAdmin.

    ![Caixa de diálogo Novo Usuário](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    A nova conta de usuário aparece na lista.

4. Para associar a conta de usuário à função, no painel **Navegador**, clique em **Permissões Globais**. No painel **Permissões Globais**, selecione a guia **Gerenciar** e, em seguida, clique no ícone Adicionar (o símbolo +).

    ![Painel Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    A caixa de diálogo **Permissões Globais Raiz – Adicionar Permissão** é exibida.

5. Na caixa de diálogo **Permissão Global Raiz – Adicionar Permissão**, clique em **Adicionar** para escolher o usuário ou o grupo.

    ![Escolher o usuário ou o grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    A caixa de diálogo **Selecionar Usuários/Grupos** é exibida.

6. Na caixa de diálogo **Selecionar Usuários/Grupos**, escolha **BackupAdmin** e, depois, clique em **Adicionar**.

    Em **Usuários**, o formato *domain\username* é usado para a conta de usuário. Se desejar usar outro domínio, escolha-o na lista **Domínios**.

    ![Adicionar um usuário BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Clique em **OK** para adicionar os usuários selecionados à caixa de diálogo **Adicionar Permissão**.

7. Agora que você identificou o usuário, atribua o usuário à função. Em **Função Atribuída**, na lista suspensa, selecione **BackupAdminRole** e, em seguida, clique em **OK**.

    ![Atribuir o usuário à função ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na guia **Gerenciar** no painel **Permissões Globais**, a nova conta de usuário e a função associada são exibidas na lista.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Estabelecer as credenciais de servidor do vCenter no servidor de Backup do Azure

Antes de adicionar o servidor do VMware ao Servidor de Backup do Azure, instale a [Atualização 1 do Servidor de Backup do Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Para abrir o servidor de Backup do Azure, clique duas vezes no ícone da área de trabalho do servidor de Backup do Azure.

    ![Ícone do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Se você não conseguir localizar o ícone na área de trabalho, poderá abrir o Servidor de Backup do Azure na lista de aplicativos instalados. O nome de aplicativo do Servidor de Backup do Azure é Backup do Microsoft Azure.

2. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, em **Servidores de Produção** e, depois, na faixa de opções da ferramenta, clique em **Gerenciar VMware**.

    ![Console do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    A caixa de diálogo **Gerenciar Credenciais** é exibida.

    ![Caixa de diálogo Gerenciar Credenciais do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Na caixa de diálogo **Gerenciar Credenciais**, clique em **Adicionar** para abrir a caixa de diálogo **Adicionar Credencial**.

4. Na caixa de diálogo **Adicionar Credencial**, insira um nome e uma descrição para a nova credencial. Em seguida, especifique o nome de usuário e a senha. O nome *credencial do Contoso Vcenter* é utilizado para identificar a credencial no próximo procedimento. Use o mesmo nome de usuário e a senha usados para o vCenter Server. Se o vCenter Server e o Servidor de Backup do Azure não estiverem no mesmo domínio, em **Nome de usuário**, especifique o domínio.

    ![Caixa de diálogo Adicionar Credencial do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Clique em **Adicionar** para adicionar a nova credencial para o Servidor de Backup do Azure. A nova credencial é exibida na lista da caixa de diálogo **Gerenciar Credenciais**.
    
    ![Caixa de diálogo Gerenciar Credenciais do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Para fechar a caixa de diálogo **Gerenciar Credenciais**, clique no **X** no canto superior direito.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicionar servidor VMware para o Servidor de Backup do Azure

O Assistente de Adição de Servidor de Produção é usado para adicionar o vCenter Server ao Servidor de Backup do Azure.

Para abrir o Assistente de Adição de Servidor de Produção, conclua o seguinte procedimento:

1. No console do Servidor de Backup do Azure, clique em **Gerenciamento**, em **Servidores de Produção** e, em seguida, em **Adicionar**.

    ![Abra o Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    A caixa de diálogo **Assistente de Adição de Servidor de Produção** é exibida.

    ![Assistente de Adição de Servidor de Produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na página **Selecionar tipo de Servidor de Produção**, selecione **Servidores do VMware** e, depois, clique em **Avançar**.

3. Em **Nome do Servidor/Endereço IP**, especifique o FQDN (nome de domínio totalmente qualificado) ou o endereço IP do servidor do VMware. Se todos os servidores ESXi forem gerenciados pelo mesmo vCenter, você poderá usar o nome do vCenter.

    ![Especificar o FQDN ou o endereço IP do servidor do VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Em **Porta SSL**, insira a porta usada para se comunicar com o servidor do VMware. Use a porta 443, que é a porta padrão, a menos que você saiba que uma porta diferente é necessária.

5. Em **Especificar Credencial**, selecione a credencial que você criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor do VMware à lista de **Servidores do VMware Adicionados** e, depois, clique em **Avançar** para ir para a próxima página do assistente.

    ![Adicionar servidor do VMware e a credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo**, clique em **Adicionar** para adicionar o servidor do VMware especificado ao Servidor de Backup do Azure.

    ![Adicionar servidor VMware para o Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  O backup do servidor do VMware é um backup sem agente e o novo servidor é adicionado imediatamente. A página **Concluir** mostra os resultados.

  ![Página Concluir](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Para adicionar várias instâncias do vCenter Server ao Servidor de Backup do Azure, repita as etapas anteriores nesta seção.

Depois de adicionar o vCenter Server ao Servidor de Backup do Azure, a próxima etapa é criar um grupo de proteção. O grupo de proteção especifica os diversos detalhes para retenção de curto e longo prazo e é onde você pode definir e aplica a política de backup. A política de backup é o agendamento para quando os backups ocorrem e o que é copiado em backup.


## <a name="configure-a-protection-group"></a>Configurar grupo de proteção

Se você não usou o System Center Data Protection Manager nem o Servidor de Backup do Azure antes, consulte [Planejar backups de disco](https://technet.microsoft.com/library/hh758026.aspx) para preparar o ambiente de hardware. Depois de verificar que você tem um armazenamento adequado, use o assistente para Criar Novo Grupo de Proteção para adicionar máquinas virtuais do VMware.

1. No console do Servidor de Backup do Azure, clique em **Proteção**e, na faixa de opções da ferramenta, clique em **Novo** para abrir o assistente Criar Novo Grupo de Proteção.

    ![Abra o assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    A caixa de diálogo do assistente para **Criar Novo Grupo de Proteção** é exibida.

    ![Caixa de diálogo Assistente para Criar Novo Grupo de Proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Clique em **Avançar** para ir para a página **Selecionar tipo de grupo de proteção**.

2. Na página **Selecionar tipo de grupo de Proteção**, selecione **Servidores** e, depois, clique em **Avançar**. A página **Selecionar membros do grupo** é exibida.

3. Na página **Selecionar membros do grupo**, os membros disponíveis e os membros selecionados são exibidos. Selecione os membros que você deseja proteger e, depois, clique em **Avançar**.

    ![Selecionar membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Ao selecionar um membro, se você selecionar uma pasta que contém outras pastas ou VMs, essas pastas e VMs também serão selecionadas. A inclusão das pastas e VMs na pasta pai é chamada de proteção de nível de pasta. Para remover uma pasta ou VM, desmarque a caixa de seleção.

    Se uma VM ou uma pasta que contenha uma VM já estiver protegida no Azure, você não poderá selecionar essa VM novamente. Ou seja, depois que uma VM é protegida no Azure, ela não pode ser protegida novamente, o que impede a criação de pontos de recuperação duplicados para uma VM. Se desejar ver qual instância do Servidor de Backup do Azure já protege um membro, aponte para o membro para ver o nome do servidor de proteção.

4. Na página **Selecionar Método de Proteção de Dados**, insira um nome para o grupo de proteção. Proteção de curto prazo (no disco) e proteção online são selecionados. Se você quiser usar a proteção online (Azure), você deve usar a proteção de curto prazo em disco. Clique em **Avançar** para prosseguir para o intervalo de proteção de curto prazo.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na página **Especificar Metas de Curto Prazo**, em **Intervalo de Retenção**, especifique o número de dias durante os quais deseja reter os pontos de recuperação *armazenados em disco*. Se você quiser alterar o tempo e os dias quando os pontos de recuperação são criados, clique em **Modificar**. Os pontos de recuperação de curto prazo são backups completos. Não são backups incrementais. Quando estiver satisfeito com as metas de curto prazo, clique em **Avançar**.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na página **Examinar Alocação de Disco**, examine e, se necessário, modifique o espaço em disco das VMs. As alocações de disco recomendadas se baseiam no intervalo de retenção especificado na página **Especificar Metas de Curto Prazo**, no tipo de carga de trabalho e no tamanho dos dados protegidos (identificados na etapa 3).  

  - **Tamanho dos dados:** tamanho dos dados no grupo de proteção.
  - **Espaço em disco:** a quantidade de espaço em disco recomendada para o grupo de proteção. Se você desejar modificar essa configuração, deverá alocar um espaço total ligeiramente maior do que o aumento estimado para cada fonte de dados.
  - **Colocar dados:** se você ativar a colocação, várias fontes de dados na proteção poderão ser mapeadas para um único volume de réplica e ponto de recuperação. A colocação não tem suporte para todas as cargas de trabalho.
  - **Aumentar automaticamente:** se você ativar essa configuração, caso os dados no grupo protegido excedam a alocação inicial, o System Center Data Protection Manager tentará aumentar o tamanho do disco em 25%.
  - **Detalhes do pool de armazenamento:** mostra o status do pool de armazenamento, incluindo o tamanho do disco total e restante.

    ![Examinar a alocação de disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Quando estiver satisfeito com as metas de curto prazo, clique em **Avançar**.

7. Na página **Escolher Método de Criação de Réplica**, especifique como deseja gerar a cópia inicial ou a réplica dos dados protegidos no Servidor de Backup do Azure.

    O padrão é **Automaticamente pela rede** e **Agora**. Se você usar o padrão, é recomendável especificar um horário fora do horário de pico. Escolha **Mair tarde** e especifique um dia e hora.

    Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

    Depois de fazer suas escolhas, clique em **Avançar**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na página **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com um agendamento definido.

    Se você não desejar configurar verificações de consistência automáticas, poderá executar uma verificação manual. Na área de proteção do console do Servidor de Backup do Azure, clique com o botão direito do mouse no grupo de proteção e, depois, selecione **Executar Verificação de Consistência**.

    Clique em **Avançar** para ir para a próxima página.

9. Na página **Especificar Dados de Proteção Online**, selecione uma ou mais fontes de dados que você deseja proteger. Você pode selecionar os membros individualmente ou clicar em **Selecionar tudo** para escolher todos os membros. Depois de escolher os membros, clique em **Avançar**.

    ![Especificar dados de proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na página **Especificar Agendamento de Backup Online**, especifique o agendamento para gerar pontos de recuperação do backup de disco. Quando o ponto de recuperação é gerado, ele é transferido para o cofre dos Serviços de Recuperação no Azure. Quando estiver satisfeito com o agendamento de backup online, clique em **Avançar**.

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na página **Especificar Política de Retenção Online**, indique por quanto tempo deseja manter os dados de backup no Azure. Depois que a política for definida, clique em **Avançar**.

    ![Especificar política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Não há um tempo limite para manter dados no Azure. Ao armazenar dados de ponto de recuperação no Azure, o único limite é que você não pode ter mais de 9.999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

12. Na página **Resumo**, examine os detalhes dos membros do grupo de proteção e as configurações e, em seguida, clique em **Criar Grupo**.

    ![Resumo da configuração e do membro do grupo de proteção](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Próximas etapas
Se você usar o Servidor de Backup do Azure para proteger cargas de trabalho do VMware, talvez esteja interessado em usar o Servidor de Backup do Azure para ajudar a proteger um [Microsoft Exchange Server](./backup-azure-exchange-mabs.md), um [farm do Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) ou um [banco de dados do SQL Server](./backup-azure-sql-mabs.md).

Para obter informações sobre problemas ao registrar o agente, configurar o grupo de proteção ou fazer backup de trabalhos, consulte [Solucionar problemas do Servidor de Backup do Azure](./backup-azure-mabs-troubleshoot.md).

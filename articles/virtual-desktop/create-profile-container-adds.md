---
title: Criar contêiner de perfil FSLogix arquivos do Azure Active Directory Domain Services-Azure
description: Este artigo descreve como criar um contêiner de perfil do FSLogix com arquivos e Azure Active Directory Domain Services do Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302470"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Criar um contêiner de perfil com arquivos do Azure e AD DS do Azure

Este artigo mostrará como criar um contêiner de perfil FSLogix com arquivos do Azure e Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já configurou uma instância de AD DS do Azure. Se você ainda não tiver um, siga as instruções em [criar um domínio gerenciado básico](../active-directory-domain-services/tutorial-create-instance.md) primeiro e, em seguida, retorne aqui.

## <a name="add-azure-ad-ds-admins"></a>Adicionar administradores de AD DS do Azure

Para adicionar administradores adicionais, você cria um novo usuário e concede a eles permissões.

Para adicionar um administrador:

1. Selecione **Azure Active Directory** na barra lateral, selecione **todos os usuários** e, em seguida, selecione **novo usuário**.

2.  Insira os detalhes do usuário nos campos.

3. No painel de Azure Active Directory no lado esquerdo da tela, selecione **grupos**.

4. Selecione o grupo de **Administradores do AAD DC** .

5. No painel esquerdo, selecione **Membros** e, em seguida, selecione **adicionar membros** no painel principal. Isso mostrará uma lista de todos os usuários disponíveis no Azure AD. Selecione o nome do perfil de usuário que você acabou de criar.

## <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Agora é hora de habilitar a autenticação de AD DS do Azure sobre o protocolo SMB.

Para habilitar a autenticação:

1. Se você ainda não fez isso, configure e implante uma conta de armazenamento do Azure de uso geral v2 seguindo as instruções em [criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md).

2. Depois de concluir a configuração da sua conta, selecione **ir para o recurso**.

3. Selecione **configuração** no painel no lado esquerdo da tela e, em seguida, habilite **a autenticação de Azure Active Directory para os arquivos do Azure** no painel principal. Quando terminar, selecione **Salvar**.

4. Selecione **visão geral** no painel no lado esquerdo da tela e, em seguida, selecione **arquivos** no painel principal.

5. Selecione **compartilhamento de arquivos** e insira o **nome** e a **cota** nos campos que aparecem no lado direito da tela.

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Outros usuários precisarão de permissões de acesso para acessar o compartilhamento de arquivos. Para fazer isso, você precisará atribuir a cada usuário uma função com as permissões de acesso apropriadas.

Para atribuir permissões de acesso de usuários:

1. No portal do Azure, abra o compartilhamento de arquivos que você criou em [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account).

2. Selecione **Controle de Acesso (IAM)** .

3. Selecione **Adicionar uma atribuição de função**.

4. Na guia **Adicionar atribuição de função** , selecione a função interna apropriada na lista função. Você precisará, pelo menos, selecionar **colaborador de compartilhamento SMB de dados de arquivo de armazenamento** para a conta para obter as permissões adequadas.

5. Para **atribuir acesso a**, selecione **Azure Active Directory usuário, grupo ou entidade de serviço**.

6. Selecione um nome ou endereço de email para a identidade de Azure Active Directory de destino.

7. Selecione **Salvar**.

## <a name="get-the-storage-account-access-key"></a>Obter a chave de acesso da conta de armazenamento

Em seguida, você precisará obter a chave de acesso para sua conta de armazenamento.

Para obter a chave de acesso da conta de armazenamento:

1. Na barra lateral portal do Azure, selecione **contas de armazenamento**.

2. Na lista de contas de armazenamento, selecione a conta para a qual você habilitou o Azure AD DS e criou as funções personalizadas nas etapas acima.

3. Em **configurações**, selecione **chaves de acesso** e copie a chave da **key1**.

4. Vá para a guia **máquinas virtuais** e localize qualquer VM que se tornará parte do seu pool de hosts.

5. Selecione o nome da máquina virtual (VM) em **máquinas virtuais (adVM)** e selecione **conectar**

    Isso fará o download de um arquivo RDP que permitirá que você entre na VM com suas próprias credenciais.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da guia RDP da janela conectar-se à máquina virtual.](media/rdp-tab.png)

6. Quando você tiver entrado na VM, execute um prompt de comando como administrador.

7. Execute o seguinte comando:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Substitua `<desired-drive-letter>` por uma letra da unidade de sua escolha (por exemplo, `y:` ).
    - Substitua todas as instâncias de `<storage-account-name>` pelo nome da conta de armazenamento especificada anteriormente.
    - Substitua `<share-name>` pelo nome do compartilhamento que você criou anteriormente.
    - Substitua `<storage-account-key>` pela chave da conta de armazenamento do Azure.

    Por exemplo:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Execute os comandos a seguir para permitir que os usuários da área de trabalho virtual do Windows criem seu próprio contêiner de perfil ao bloquear o acesso aos contêineres de perfil de outros usuários.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Substitua `<mounted-drive-letter>` pela letra da unidade usada para mapear a unidade.
    - Substitua `<user-email>` pelo UPN do usuário ou grupo de Active Directory que contém os usuários que precisarão de acesso ao compartilhamento.

    Por exemplo:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Criar um contêiner de perfil

Agora que seus perfis estão prontos para uso, vamos criar um contêiner de perfil FSLogix.

Para configurar um contêiner de perfil do FSLogix:

1. Entre na VM host da sessão que você configurou no início deste artigo e, em seguida, [Baixe e instale o agente do FSLogix](/fslogix/install-ht/).

2. Descompacte o arquivo do agente FSLogix que você **x64** baixou e vá para  >  **versões** x64 e, em seguida, abra **FSLogixAppsSetup.exe**.

3. Depois que o instalador for iniciado, selecione **concordo com os termos e condições de licença.** Se aplicável, forneça uma nova chave.

4. Selecione **Instalar**.

5. Abra a **unidade C** e vá para **arquivos de programas**  >  **FSLogix**  >  **aplicativos** para certificar-se de que o agente FSLogix foi instalado corretamente.

     >[!NOTE]
     > Se houver várias VMs no pool de hosts, você precisará repetir as etapas de 1 a 5 para cada VM.

6. Execute o **Editor do registro** (regedit) como administrador.

7. Navegue até **computador**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**, clique com o botão direito do mouse em **FSLogix**, selecione **novo** e selecione **chave**.

8. Crie uma nova chave chamada **perfis**.

9.  Clique com o botão direito do mouse em **perfis**, selecione **novo** e, em seguida, selecione **valor DWORD (32 bits).** Nomeie o valor como **habilitado** e defina o valor de **dados** como **1**.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da chave de perfis. O arquivo de REG_DWORD é realçado e seu valor de dados é definido como 1.](media/dword-value.png)

10. Clique com o botão direito do mouse em **perfis**, selecione **novo** e, em seguida, selecione **valor de cadeia de caracteres múltipla**. Nomeie o valor **VHDLocations** e defina Insira o URI para o compartilhamento de arquivos do Azure `\\fsprofile.file.core.windows.net\share` como o valor de dados.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da chave de perfis que mostra o arquivo VHDLocations. Seu valor de dados mostra o URI para o compartilhamento de arquivos do Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Atribuir usuários a um host de sessão

Agora, você precisará atribuir usuários ao seu host de sessão.

Para atribuir usuários:

1. Execute o Windows PowerShell como administrador e execute o seguinte cmdlet para entrar na área de trabalho virtual do Windows com o PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando solicitado a fornecer credenciais, insira o mesmo usuário que recebeu a função de colaborador TenantCreator, RDS Owner ou RDS no locatário da área de trabalho virtual do Windows.

2. Execute os seguintes cmdlets para atribuir o usuário ao grupo de área de trabalho remota:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Como os cmdlets anteriores, certifique-se de substituir `<your-wvd-tenant>` , `<wvd-pool>` e `<user-principal>` pelos valores relevantes.

    Por exemplo:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Verifique se o seu perfil funciona

Agora tudo o que você precisa fazer é verificar se o perfil criado existe e funciona como pretendido.

Para verificar seu perfil:

1. Abra um navegador e vá para [o cliente Web da área de trabalho virtual do Windows](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Entre com a conta de usuário atribuída ao grupo de Área de Trabalho Remota.

3. Depois que a sessão de usuário for estabelecida, abra o portal do Azure e entre com uma conta administrativa.

4. Na barra lateral, selecione **contas de armazenamento**.

5. Selecione a conta de armazenamento configurada como o compartilhamento de arquivos para o pool de hosts de sessão e habilitada com o Azure AD DS.

6. Selecione o ícone **arquivos** e expanda seu compartilhamento.

    Se tudo estiver configurado corretamente, você verá um **diretório** com um nome que é formatado da seguinte maneira: `<user SID>-<username>` .

## <a name="next-steps"></a>Próximas etapas

Se você estiver procurando maneiras alternativas de criar contêineres de perfil FSLogix, confira os seguintes artigos:

- [Crie um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md).
- [Criar um contêiner de perfil do FSLogix para um pool de hosts usando Azure NetApp Files](create-fslogix-profile-container.md)

Você pode encontrar informações mais detalhadas sobre os conceitos relacionados aos contêineres do FSlogix para arquivos do Azure em [contêineres de perfil do FSlogix e arquivos do Azure](fslogix-containers-azure-files.md).

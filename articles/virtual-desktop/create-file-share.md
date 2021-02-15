---
title: Criar um compartilhamento de arquivos do Azure files com um controlador de domínio-Azure
description: Configure um contêiner de perfil FSLogix em um compartilhamento de arquivos do Azure em um pool de hosts de área de trabalho virtual do Windows existente com seu domínio Active Directory.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746815"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Criar um contêiner de perfil com arquivos do Azure e AD DS

Neste artigo, você aprenderá a criar um compartilhamento de arquivos do Azure autenticado por um controlador de domínio em um pool de hosts de área de trabalho virtual do Windows existente. Você pode usar esse compartilhamento de arquivos para armazenar perfis de armazenamento.

Esse processo usa Active Directory Domain Services (AD DS), que é um serviço de diretório local. Se você estiver procurando informações sobre como criar um contêiner de perfil FSLogix com o Azure AD DS, consulte [criar um contêiner de perfil FSLogix com os arquivos do Azure](create-profile-container-adds.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se o controlador de domínio está sincronizado com o Azure e resolvido da VNET (rede virtual) do Azure à qual os hosts de sessão estão conectados.

## <a name="set-up-a-storage-account"></a>Configurar uma conta de armazenamento

Primeiro, você precisará configurar uma conta de armazenamento de arquivos do Azure.

Para configurar uma conta de armazenamento:

1. Entre no portal do Azure.

2. Pesquise por **conta de armazenamento** na barra de pesquisa.

3. Selecione **+Adicionar** .

4. Insira as informações a seguir na página  **criar conta de armazenamento** :

    - Criar um grupo de recursos.
    - Insira um nome exclusivo para sua conta de armazenamento.
    - Para o **local** , recomendamos que você escolha o mesmo local do pool de hosts da área de trabalho virtual do Windows.
    - Para **Desempenho** , selecione **Standard** . (Dependendo dos requisitos de IOPS. Para obter mais informações, consulte [Opções de armazenamento para contêineres de perfil FSLogix na área de trabalho virtual do Windows](store-fslogix-profile.md).)
    - Para **tipo de conta** , selecione **StorageV2** ou **FileStorage** (disponível somente se o nível de desempenho for Premium).
    - Para **replicação** , selecione **armazenamento com REDUNDÂNCIA local (LRS)** .

5. Quando terminar, selecione **revisar + criar** e, em seguida, selecione **criar** .

Se você precisar de instruções de configuração mais detalhadas, consulte [disponibilidade regional](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivo do Azure

Em seguida, você precisará criar um compartilhamento de arquivos do Azure.

Para criar um compartilhamento de arquivos:

1. Selecione **Ir para o recurso** .

2. Na página Visão geral, selecione **Compartilhamentos de arquivos** .

3. Selecione **+ compartilhamentos de arquivos** , crie um novo compartilhamento de arquivos denominado **perfis** e, em seguida, insira uma cota apropriada ou deixe o campo em branco para nenhuma cota.

4. Selecione **Criar** .

## <a name="enable-active-directory-authentication"></a>Habilitar autenticação de Active Directory

Em seguida, você precisará habilitar a autenticação do Active Directory (AD). Para habilitar essa política, você precisará seguir as instruções desta seção em um computador que já ingressou no domínio. Para habilitar a autenticação, siga estas instruções na VM que executa o controlador de domínio:

1. Protocolo RDP na VM ingressada no domínio.

2. Siga as instruções em [habilitar a autenticação de AD DS para seus compartilhamentos de arquivos do Azure](../storage/files/storage-files-identity-ad-ds-enable.md) para instalar o módulo AzFilesHybrid e habilitar a autenticação.

3.  Abra o portal do Azure, abra sua conta de armazenamento, selecione **configuração** e confirmar **Active Directory (AD)** está definido como **habilitado** .

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da página de configuração com o Azure Active Directory (AD) habilitado.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Atribuir permissões de RBAC do Azure aos usuários da área de trabalho virtual do Windows

Todos os usuários que precisam ter perfis FSLogix armazenados na conta de armazenamento devem ser atribuídos à função de colaborador de compartilhamento SMB de dados do arquivo de armazenamento.

Os usuários que entram nos hosts da sessão da Área de Trabalho Virtual do Windows precisam de permissões de acesso para acessar o compartilhamento de arquivos. Conceder acesso a um compartilhamento de arquivos do Azure envolve a configuração de permissões no nível de compartilhamento, bem como no nível do NTFS, semelhante a um compartilhamento tradicional do Windows.

Para configurar permissões de nível de compartilhamento, atribua a cada usuário uma função com as permissões de acesso apropriadas. As permissões podem ser atribuídas a usuários individuais ou a um grupo do Azure AD. Para saber mais, consulte [atribuir permissões de acesso a uma identidade](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>As contas ou os grupos aos quais você atribui permissões devem ter sido criados no domínio e sincronizados com o Azure AD. Contas criadas no Azure AD não funcionam.

Para atribuir permissões de controle de acesso baseado em função do Azure (RBAC):

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que você criou em [Configurar uma conta de armazenamento](#set-up-a-storage-account).

3. Selecione **compartilhamentos de arquivos** e, em seguida, selecione o nome do compartilhamento de arquivos que você planeja usar.

4. Selecione **Controle de Acesso (IAM)** .

5. Selecione **Adicionar uma atribuição de função** .

6. Na guia **Adicionar atribuição de função** , selecione **dados de arquivo de armazenamento SMB de compartilhamento elevado de colaborador** para a conta de administrador.

     Para atribuir permissões de usuários nos seus perfis do FSLogix, siga estas mesmas instruções. No entanto, quando chegar à etapa 5, selecione **Storage File data colaborador de compartilhamento SMB** .

7. Clique em **Salvar** .

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Atribuir permissões de usuários no compartilhamento de arquivos do Azure

Depois de atribuir permissões de RBAC do Azure para seus usuários, em seguida, você precisará configurar as permissões de NTFS.

Você precisará conhecer duas coisas da portal do Azure para começar:

- O caminho UNC.
- A chave da conta de armazenamento.

### <a name="get-the-unc-path"></a>Obter o caminho UNC

Veja como obter o caminho UNC:

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que você criou em [Configurar uma conta de armazenamento](#set-up-a-storage-account).

3. Selecione **configurações** e selecione **Propriedades** .

4. Copie o URI do **ponto de extremidade do serviço de arquivo primário** para o editor de texto de sua escolha.

5. Depois de copiar o URI, faça o seguinte para alterá-lo no UNC:

    - Remover `https://` e substituir por `\\`
    - Substitua a barra de avanço `/` por uma barra invertida `\` .
    - Adicione o nome do compartilhamento de arquivos que você criou em [criar um compartilhamento de arquivos do Azure](#create-an-azure-file-share) até o final do UNC.

        Por exemplo: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

Para obter a chave da conta de armazenamento:

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que você criou em [Configurar uma conta de armazenamento](#set-up-a-storage-account).

3. Na guia **conta de armazenamento** , selecione **chaves de acesso** .

4. Copie **key1** ou **Key2** em um arquivo no computador local.

### <a name="configure-ntfs-permissions"></a>Configurar permissões NTFS

Para configurar suas permissões NTFS:

1. Abra um prompt de comando em uma VM ingressada no domínio.

2. Execute o seguinte comando para montar o compartilhamento de arquivos do Azure e atribuir a ele uma letra de unidade:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Execute o seguinte comando para examinar as permissões de acesso para o compartilhamento de arquivos do Azure:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Substitua `<mounted-drive-letter>` pela letra da unidade que você mapeou para.

    Os *usuários do NT Authority\Authenticated* e do *BUILTIN\Users* têm determinadas permissões por padrão. Essas permissões padrão permitem que esses usuários leiam contêineres de perfil de outros usuários. No entanto, as permissões descritas em [configurar permissões de armazenamento para uso com contêineres de perfil e contêineres do Office](/fslogix/fslogix-storage-config-ht) não permitem que os usuários leiam os contêineres de perfil dos outros.

4. Execute os comandos a seguir para permitir que os usuários da área de trabalho virtual do Windows criem seu próprio contêiner de perfil ao bloquear o acesso aos seus contêineres de perfil de outros usuários.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Substitua <> letra de unidade montada pela letra da unidade usada para mapear a unidade.
     - Substitua <> de email do usuário pelo UPN do usuário ou grupo de Active Directory que contém os usuários que precisarão de acesso ao compartilhamento.

     Por exemplo:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Configurar o FSLogix em VMs de host de sessão

Esta seção mostrará como configurar uma VM com o FSLogix. Você precisará seguir essas instruções sempre que configurar um host de sessão. Antes de começar a configurar o, siga as instruções em [baixar e instalar o FSLogix](/fslogix/install-ht). Há várias opções disponíveis que garantem que as chaves do registro sejam definidas em todos os hosts de sessão. Você pode definir essas opções em uma imagem ou configurar uma política de grupo.

Para configurar o FSLogix em sua VM host de sessão:

1. RDP para a VM host da sessão do pool de hosts da área de trabalho virtual do Windows.

2. [Baixe e instale o FSLogix](/fslogix/install-ht).

5. Siga as instruções em [Definir configurações do registro de contêiner de perfil](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Navegue até **computador**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix** .

    - Crie uma chave de **perfis** .

    - Criar **habilitado, DWORD** com um valor de 1.

    - Criar **VHDLocations, MULTI_SZ** .

    - Defina o valor de **VHDLocations** para o caminho UNC gerado em [obter o caminho UNC](#get-the-unc-path).

6. Reinicie a VM.

## <a name="testing"></a>Testando

Depois de instalar e configurar o FSLogix, você pode testar sua implantação entrando com uma conta de usuário que tenha sido atribuída a um grupo de aplicativos ou área de trabalho no pool de hosts. Verifique se a conta de usuário com a qual você se conecta tem permissão no compartilhamento de arquivos.

Se o usuário tiver entrado antes, ele terá um perfil local existente que será usado durante esta sessão. Para evitar a criação de um perfil local, crie uma nova conta de usuário a ser usada para testes ou use os métodos de configuração descritos em [tutorial: configurar contêiner de perfil para redirecionar perfis de usuário](/fslogix/configure-profile-container-tutorial/).

Para verificar suas permissões em sua sessão:

1. Inicie uma sessão na área de trabalho virtual do Windows.

2. Abra o portal do Azure.

3. Abra a conta de armazenamento que você criou em [Configurar uma conta de armazenamento](#set-up-a-storage-account).

4. Selecione **criar um compartilhamento** na página criar um compartilhamento de arquivos do Azure.

5. Verifique se uma pasta que contém o perfil do usuário agora existe em seus arquivos.

Para testes adicionais, siga as instruções em verificar [se o seu perfil funciona](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas do FSLogix, consulte [este guia de solução de problemas](/fslogix/fslogix-trouble-shooting-ht).

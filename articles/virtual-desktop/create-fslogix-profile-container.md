---
title: Contêineres de perfil do FSLogix NetApp Windows Virtual Desktop – Azure
description: Como criar um contêiner de perfil FSLogix usando Azure NetApp Files na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016821"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Criar um contêiner de perfil com Azure NetApp Files e AD DS

É recomendável usar contêineres de perfil FSLogix como uma solução de perfil de usuário para o [serviço de área de trabalho virtual do Windows](overview.md). Os contêineres de perfil do FSLogix armazenam um perfil de usuário completo em um único contêiner e são projetados para perfis de roaming em ambientes de computação remota não persistentes, como área de trabalho virtual do Windows. Quando você entra, o contêiner é anexado dinamicamente ao ambiente de computação usando um VHD (disco rígido virtual) com suporte local e um VHDX (disco rígido virtual) do Hyper-V. Essas tecnologias avançadas de driver de filtro permitem que o perfil do usuário esteja imediatamente disponível e apareça no sistema exatamente como um perfil de usuário local. Para saber mais sobre contêineres de perfil do FSLogix, consulte [contêineres de perfil do FSLogix e arquivos do Azure](fslogix-containers-azure-files.md).

Você pode criar contêineres de perfil FSLogix usando o [Azure NetApp files](https://azure.microsoft.com/services/netapp/), um serviço de plataforma nativa do Azure fácil de usar que ajuda os clientes a provisionar com rapidez e confiança volumes SMB de nível empresarial para seus ambientes de área de trabalho virtual do Windows. Para saber mais sobre Azure NetApp Files, confira [o que é Azure NetApp files?](../azure-netapp-files/azure-netapp-files-introduction.md)

Este guia mostrará como configurar uma conta de Azure NetApp Files e criar contêineres de perfil FSLogix na área de trabalho virtual do Windows.

Este artigo pressupõe que você já tem [pools de hosts](create-host-pools-azure-marketplace.md) configurados e agrupados em um ou mais locatários no seu ambiente de área de trabalho virtual do Windows. Para saber como configurar locatários, confira [criar um locatário na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) e [nossa postagem no blog da comunidade técnica](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

As instruções neste guia são específicas para usuários da área de trabalho virtual do Windows. Se você estiver procurando uma orientação mais geral sobre como configurar Azure NetApp Files e criar contêineres de perfil FSLogix fora da área de trabalho virtual do Windows, consulte o guia de [início rápido configurar Azure NetApp Files e criar um volume de NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Este artigo não aborda as práticas recomendadas para proteger o acesso ao compartilhamento de Azure NetApp Files.

>[!NOTE]
>Se você estiver procurando material de comparação sobre as diferentes opções de armazenamento de contêiner de perfil FSLogix no Azure, consulte [Opções de armazenamento para contêineres de perfil FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um contêiner de perfil do FSLogix para um pool de hosts, você deve:

- Configurar e configurar a área de trabalho virtual do Windows
- Provisionar um pool de hosts de área de trabalho virtual Windows
- [Habilitar sua assinatura do Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Configurar sua conta de Azure NetApp Files

Para começar, você precisa configurar uma conta de Azure NetApp Files.

1. Entre no [portal do Azure](https://portal.azure.com). Verifique se sua conta tem permissões de colaborador ou administrador.

2. Selecione o **ícone de Azure cloud Shell** à direita da barra de pesquisa para abrir Azure cloud Shell.

3. Quando Azure Cloud Shell estiver aberto, selecione **PowerShell**.

4. Se esta for a primeira vez que você usa Azure Cloud Shell, crie uma conta de armazenamento na mesma assinatura e mantenha seu Azure NetApp Files e área de trabalho virtual do Windows.

   > [!div class="mx-imgBorder"]
   > ![A janela da conta de armazenamento com o botão criar armazenamento na parte inferior da janela realçada em vermelho.](media/create-storage-button.png)

5. Quando Azure Cloud Shell for carregado, execute os dois cmdlets a seguir.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. No lado esquerdo da janela, selecione **todos os serviços**. Insira **Azure NetApp files** na caixa de pesquisa que aparece na parte superior do menu.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela de um usuário digitando "Azure NetApp Files" na caixa de pesquisa todos os serviços. Os resultados da pesquisa mostram o recurso de Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Selecione **Azure NetApp files** nos resultados da pesquisa e, em seguida, selecione **criar**.

8. Selecione o botão **Adicionar**.
9. Quando a guia **nova conta do NetApp** for aberta, insira os seguintes valores:

    - Para **nome**, insira o nome da conta do NetApp.
    - Para **assinatura**, selecione a assinatura para a conta de armazenamento que você configurou na etapa 4 no menu suspenso.
    - Para **grupo de recursos**, selecione um grupo de recursos existente no menu suspenso ou crie um novo selecionando **criar novo**.
    - Para **local**, selecione a região da sua conta do NetApp no menu suspenso. Essa região deve ser a mesma região que suas VMs de host de sessão.

   >[!NOTE]
   >Azure NetApp Files atualmente não dá suporte à montagem de um volume entre regiões.

10. Quando tiver terminado, selecione **criar** para criar sua conta do NetApp.

## <a name="create-a-capacity-pool"></a>Criar um pool de capacidade

Em seguida, crie um novo pool de capacidade:

1. Vá para o menu Azure NetApp Files e selecione sua nova conta.
2. No menu conta, selecione **pools de capacidade** em serviço de armazenamento.
3. Selecione **Adicionar pool**.
4. Quando a **nova guia pool de capacidade** for aberta, insira os seguintes valores:

    - Para **nome**, insira um nome para o novo pool de capacidade.
    - Para **nível de serviço**, selecione o valor desejado no menu suspenso. Recomendamos o **Premium** para a maioria dos ambientes.
       >[!NOTE]
       >A configuração Premium fornece a taxa de transferência mínima disponível para um nível de serviço Premium, que é de 256 MBps. Talvez seja necessário ajustar essa taxa de transferência para um ambiente de produção. A taxa de transferência final é baseada na relação descrita nos [limites da taxa de transferência](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Para **tamanho (TIB)**, insira o tamanho do pool de capacidade que melhor atende às suas necessidades. O tamanho mínimo é 4 TiB.

5. Quando tiver terminado, selecione **OK**.

## <a name="join-an-active-directory-connection"></a>Ingressar em uma conexão Active Directory

Depois disso, você precisa ingressar em uma conexão de Active Directory.

1. Selecione **Active Directory conexões** no menu no lado esquerdo da página e, em seguida, selecione o botão de **junção** para abrir a página de **junção Active Directory** .

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela do menu ingressar Active Directory conexões.](media/active-directory-connections-menu.png)

2. Insira os seguintes valores na página **ingressar Active Directory** para ingressar em uma conexão:

    - Para **DNS primário**, insira o endereço IP do servidor DNS em seu ambiente que pode resolver o nome de domínio.
    - Para **domínio**, insira seu nome de domínio totalmente qualificado (FQDN).
    - Para o **prefixo do servidor SMB (conta do computador)**, insira a cadeia de caracteres que você deseja acrescentar ao nome da conta do computador.
    - Para **nome de usuário**, insira o nome da conta com permissões para executar o ingresso no domínio.
    - Para **senha**, insira a senha da conta.

## <a name="create-a-new-volume"></a>Criar um novo volume

Em seguida, você precisará criar um novo volume.

1. Selecione **volumes** e, em seguida, selecione **Adicionar volume**.

2. Quando a guia **criar um volume** for aberta, insira os seguintes valores:

    - Para **nome do volume**, insira um nome para o novo volume.
    - Para **pool de capacidade**, selecione o pool de capacidade que você acabou de criar no menu suspenso.
    - Para **cota (GIB)**, insira o tamanho do volume apropriado para o seu ambiente.
    - Para **rede virtual**, selecione uma rede virtual existente que tenha conectividade com o controlador de domínio no menu suspenso.
    - Em **sub-rede**, selecione **criar novo**. Tenha em mente que essa sub-rede será delegada a Azure NetApp Files.

3.  Selecione **Avançar: protocolo \> \>** para abrir a guia Protocolo e configurar os parâmetros de acesso ao volume.

## <a name="configure-volume-access-parameters"></a>Configurar parâmetros de acesso ao volume

Depois de criar o volume, configure os parâmetros de acesso ao volume.

1.  Selecione **SMB** como o tipo de protocolo.
2.  Em configuração no menu suspenso **Active Directory** , selecione o mesmo diretório que você conectou originalmente em [ingressar em uma conexão de Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenha em mente que há um limite de um Active Directory por assinatura.
3.  Na caixa de texto **nome do compartilhamento** , digite o nome do compartilhamento usado pelo pool de hosts da sessão e seus usuários.

4.  Selecione **Revisar + criar** na parte inferior da página. Isso abre a página validação. Depois que o volume for validado com êxito, selecione **criar**.

5.  Neste ponto, o novo volume começará a ser implantado. Depois que a implantação for concluída, você poderá usar o compartilhamento de Azure NetApp Files.

6.  Para ver o caminho de montagem, selecione **ir para o recurso** e procure-o na guia Visão geral.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela de visão geral com uma seta vermelha apontando para o caminho de montagem.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configurar FSLogix em VMs (máquinas virtuais) do host de sessão

Esta seção baseia-se em [criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md).

1. [Baixe o arquivo. zip do FSLogix Agent](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) enquanto você ainda estiver remotamente na VM do host da sessão.

2. Descompacte o arquivo baixado.

3. No arquivo, vá para versões **x64**  >   e execute **FSLogixAppsSetup.exe**. O menu de instalação será aberto.

4.  Se você tiver uma chave do produto, insira-a na caixa de texto chave do produto (Product Key).

5. Marque a caixa de seleção ao lado de **concordo com os termos e condições da licença**.

6. Selecione **Instalar**.

7. Navegue até **C: \\ arquivos de programas \\ FSLogix \\ aplicativos** para confirmar o agente instalado.

8. No menu Iniciar, execute **regedit** como administrador.

9. Navegue até **computador \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.

10. Crie uma chave chamada **perfis**.

11.  Crie um valor chamado **habilitado** com um tipo de **REG_DWORD** definido como um valor de dados de **1**.

12. Crie um valor chamado **VHDLocations** com um tipo de **cadeia de caracteres múltipla** e defina seu valor de dados como o URI para o compartilhamento de Azure NetApp files.

13. Crie um valor chamado **DeleteLocalProfileWhenVHDShouldApply** com um valor DWORD de 1 para evitar problemas com os perfis locais existentes antes de entrar.

     >[!WARNING]
     >Tenha cuidado ao criar o valor de DeleteLocalProfileWhenVHDShouldApply. Quando o sistema de perfis FSLogix determina que um usuário deve ter um perfil FSLogix, mas um perfil local já existe, o contêiner de perfil excluirá permanentemente o perfil local. Em seguida, o usuário será conectado com o novo perfil FSLogix.

## <a name="assign-users-to-session-host"></a>Atribuir usuários ao host de sessão

1. Abra o **ISE do PowerShell** como administrador e entre na área de trabalho virtual do Windows.

2. Execute os seguintes cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando solicitado a fornecer credenciais, insira as credenciais do usuário com as funções criador do locatário ou colaborador RDS/RDS no locatário da área de trabalho virtual do Windows.

4. Execute os seguintes cmdlets para atribuir um usuário a um grupo de Área de Trabalho Remota:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Verifique se os usuários podem acessar o compartilhamento de arquivos do Azure NetApp

1. Abra seu navegador da Internet e vá para <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Entre com as credenciais de um usuário atribuído ao grupo de Área de Trabalho Remota.

3. Depois de estabelecer a sessão do usuário, entre no portal do Azure com uma conta administrativa.

4. Abra **Azure NetApp files**, selecione sua conta de Azure NetApp Files e, em seguida, selecione **volumes**. Depois que o menu volumes for aberto, selecione o volume correspondente.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela da conta do NetApp que você configurou anteriormente no portal do Azure com o botão volumes selecionado.](media/netapp-account.png)

5. Vá para a guia **visão geral** e confirme se o contêiner do perfil FSLogix está usando espaço.

6. Conecte-se diretamente a qualquer parte da VM do pool de hosts usando Área de Trabalho Remota e abra o **Explorador de arquivos.** Em seguida, navegue até o **caminho de montagem** (no exemplo a seguir, o caminho de montagem é \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ seja-vol).

   Dentro dessa pasta, deve haver um VHD de perfil (ou VHDX) como aquele no exemplo a seguir.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela do conteúdo da pasta no caminho de montagem. Inside é um único arquivo VHD chamado "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Próximas etapas

Você pode usar contêineres de perfil FSLogix para configurar um compartilhamento de perfil de usuário. Para saber como criar compartilhamentos de perfil de usuário com seus novos contêineres, confira [criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md).

Você também pode criar um compartilhamento de arquivos de arquivos do Azure para armazenar seu perfil do FSLogix no. Para saber mais, confira [criar um compartilhamento de arquivos de arquivos do Azure com um controlador de domínio](create-file-share.md).
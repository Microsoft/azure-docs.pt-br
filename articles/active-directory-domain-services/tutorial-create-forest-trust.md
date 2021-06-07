---
title: Tutorial – Criar uma relação de confiança de floresta no Azure AD Domain Services | Microsoft Docs
description: Saiba como criar uma floresta de saída unidirecional para um domínio de AD DS local no portal do Azure para Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: justinha
ms.openlocfilehash: e381c80dddc4484d541f5f81de6b5df712cff69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673461"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Tutorial: Criar uma relação de confiança de floresta de saída com um domínio local no Azure Active Directory Domain Services

Nos ambientes em que você não pode sincronizar hashes de senha ou em que os usuários se conectam exclusivamente usando cartões inteligentes e não sabem a senha, você pode usar uma floresta de recursos no Azure AD DS (Azure Active Directory Domain Services). Uma floresta de recursos usa uma relação de confiança de saída unidirecional do Azure AD DS com um ou mais ambientes do AD DS locais. Essa relação de confiança permite que usuários, aplicativos e computadores se autentiquem em um domínio local por meio do domínio gerenciado do Azure AD DS. Em uma floresta de recursos, os hashes de senha locais nunca são sincronizados.

![Diagrama da relação de confiança de floresta do Azure AD DS com o AD DS local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o DNS em um ambiente do AD DS local para dar suporte à conectividade do Azure AD DS
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente do AD DS local
> * Criar uma relação de confiança de floresta de saída unidirecional no Azure AD DS
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services criado usando uma floresta de recursos e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Crie um domínio gerenciado usando uma floresta de *recursos*. A opção padrão cria uma floresta de *usuários*. Somente as florestas de recursos podem criar relações de confiança com ambientes do AD DS locais.
    >
    > Você também precisa usar o mínimo do SKU *Enterprise* para seu domínio gerenciado. Se necessário, [altere o SKU de um domínio gerenciado][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará e configurará uma relação de confiança de floresta de saída do Azure AD DS usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerações de rede

A rede virtual que hospeda a floresta de recursos do Azure AD DS precisa de conectividade de rede com seu Active Directory local. Aplicativos e serviços também precisam da conectividade de rede com a rede virtual que hospeda a floresta de recursos do Azure AD DS. A conectividade de rede com a floresta de recursos do Azure AD DS deve estar sempre ativa e estável; caso contrário, os usuários podem falhar ao se autenticar ou ao acessar os recursos.

Antes de configurar uma relação de confiança de floresta no Azure AD DS, verifique se a rede entre o Azure e o ambiente local atende aos seguintes requisitos:

* Usar endereços IP privados. Não confiar no DHCP com a atribuição de endereço IP dinâmico.
* Evitar a sobreposição de espaços de endereço IP para permitir o emparelhamento e o roteamento de rede virtual para se comunicar com êxito entre o Azure e o local.
* Uma rede virtual do Azure precisa de uma sub-rede de gateway para configurar uma conexão de [VPN S2S (site a site) do Azure][vpn-gateway] ou do [ExpressRoute][expressroute].
* Criar sub-redes com endereços IP suficientes para dar suporte ao seu cenário.
* Verificar se o Azure AD DS tem a própria sub-rede, não compartilhar essa sub-rede de rede virtual com VMs de aplicativo e serviços.
* Redes virtuais emparelhadas NÃO são transitivas.
    * Os emparelhamentos de rede virtual do Azure devem ser criados entre todas as redes virtuais que você deseja usar a relação de confiança de floresta de recursos do Azure AD DS com o ambiente do AD DS local.
* Fornecer conectividade de rede contínua para sua floresta do Active Directory local. Não usar conexões sob demanda.
* Verificar se há uma resolução de nomes contínua (DNS) entre o nome da floresta de recursos do Azure AD DS e o nome da floresta do Active Directory local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurar a DNS no domínio local

Para resolver corretamente o domínio gerenciado do ambiente local, talvez seja necessário adicionar encaminhadores aos servidores DNS existentes. Se você não configurou o ambiente local para se comunicar com o domínio gerenciado, conclua as seguintes etapas em uma estação de trabalho de gerenciamento para o domínio do AD DS local:

1. Selecione **Iniciar** > **Ferramentas Administrativas** > **DNS**.
1. Selecione o servidor DNS com o botão direito do mouse, como *myAD01*, e escolha **Propriedades**.
1. Escolha **Encaminhadores** e **Editar** para adicionar encaminhadores adicionais.
1. Adicione os endereços IP do domínio gerenciado, como *10.0.2.4* e *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar a relação de confiança de floresta de entrada no domínio local

O domínio do AD DS local precisa de uma relação de confiança de floresta de entrada para o domínio gerenciado. Essa relação de confiança deve ser criada manualmente no domínio do AD DS local, não pode ser criada no portal do Azure.

Para configurar a relação de confiança de entrada no domínio do AD DS local, conclua as seguintes etapas em uma estação de trabalho de gerenciamento para o domínio do AD DS local:

1. Selecione **Iniciar** > **Ferramentas Administrativas** > **Domínios e Relações de Confiança do Active Directory**.
1. Clique com o botão direito do mouse no domínio, como *onprem.contoso.com*, e escolha **Propriedades**.
1. Escolha a guia **Relações de Confiança** e **Nova Relação de Confiança**.
1. Insira o nome de domínio do Azure AD DS, como *aaddscontoso.com*, e selecione **Avançar**.
1. Selecione a opção para criar uma **Relação de confiança de floresta** e, em seguida, para criar uma relação de confiança **Unidirecional: entrada**.
1. Escolha criar a relação de confiança para **Somente este domínio**. Na próxima etapa, você criará a relação de confiança no portal do Azure para o domínio gerenciado.
1. Escolha usar a **Autenticação em toda a floresta**; em seguida, insira a senha da relação de confiança e confirme-a. Essa mesma senha também é inserida no portal do Azure na próxima seção.
1. Percorra as próximas janelas com as opções padrão e, em seguida, escolha a opção **Não, não confirme a relação de confiança de saída**.
1. Selecione **Concluir**.

Se a relação de confiança de floresta não for mais necessária para um ambiente, conclua as seguintes etapas para removê-la do domínio local:

1. Selecione **Iniciar** > **Ferramentas Administrativas** > **Domínios e Relações de Confiança do Active Directory**.
1. Clique com o botão direito do mouse no domínio, como *onprem.contoso.com*, e escolha **Propriedades**.
1. Escolha a guia **Relações de Confiança** e **Domínios que confiam neste domínio (relações de confiança de entrada)** , clique na relação de confiança a ser removida e em **Remover**.
1. Na guia Relações de Confiança, em **Domínios em que este domínio confia (relações de confiança de saída)** , clique na relação de confiança a ser removida e clique em Remover.
1. Clique em **Não, remover apenas a relação de confiança do domínio local**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Criar uma relação de confiança de floresta de saída no Azure AD DS

Com o domínio do AD DS local configurado para resolver o domínio gerenciado e uma relação de confiança da floresta de entrada criada, agora crie a relação de confiança da floresta de saída. Essa relação de confiança de floresta de saída conclui a relação de confiança entre o domínio do AD DS local e o domínio gerenciado.

Para criar a relação de confiança de saída para o domínio gerenciado no portal do Azure, conclua as seguintes etapas:

1. No portal do Azure, procure e selecione **Azure AD Domain Services** e escolha seu domínio gerenciado, como *aaddscontoso.com*.
1. No menu esquerdo do domínio gerenciado, selecione **Relações de confiança** e, em seguida, escolha para **+ Adicionar** uma relação de confiança.

   > [!NOTE]
   > Se você não vir a opção de menu **Relações de confiança**, verifique em **Propriedades** o *Tipo de floresta*. Somente florestas de *recursos* podem criar relações de confiança. Se o tipo de floresta for *Usuário*, você não poderá criar relações de confiança. No momento, não há como alterar o tipo de floresta de um domínio gerenciado. Você precisa excluir e recriar o domínio gerenciado como uma floresta de recursos.

1. Insira um nome de exibição que identifique sua relação de confiança e o nome DNS da floresta confiável local, como *onprem.contoso.com*.
1. Forneça a mesma senha da relação de confiança que foi usada para configurar a relação de confiança de floresta de entrada para o domínio do AD DS local na seção anterior.
1. Forneça, no mínimo, dois servidores DNS para o domínio do AD DS local, como *10.1.1.4* e *10.1.1.5*.
1. Quando estiver pronto, **Salve** a relação de confiança de floresta de saída.

    ![Criar relação de confiança de floresta de saída no portal do Azure](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

Se a relação de confiança de floresta não for mais necessária para um ambiente, conclua as seguintes etapas para removê-la do Azure AD DS:

1. No portal do Azure, procure e selecione **Azure AD Domain Services** e escolha seu domínio gerenciado, como *aaddscontoso.com*.
1. No menu do lado esquerdo do domínio gerenciado, selecione **Relações de Confiança**, escolha a relação de confiança e clique em **Remover**.
1. Forneça a mesma senha de confiança que foi usada para configurar a relação de confiança de floresta e clique em **OK**.

## <a name="validate-resource-authentication"></a>Validar a autenticação de recurso

Os seguintes cenários comuns permitem que você valide se a relação de confiança de floresta autentica corretamente os usuários e o acesso a recursos:

* [Autenticação de usuário local na floresta de recursos do Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Habilitar compartilhamento de arquivos e impressora](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar um compartilhamento de arquivo para acesso entre florestas](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação entre florestas para um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação de usuário local na floresta de recursos do Azure AD DS

Você deverá ter a máquina virtual do Windows Server ingressada no domínio gerenciado. Use esta máquina virtual para testar se o usuário local pode se autenticar em uma máquina virtual. Se necessário, [crie uma VM do Windows e ingresse-a no domínio gerenciado][join-windows-vm].

1. Conecte-se à VM do Windows Server ingressada na floresta de recursos do Azure AD DS usando o [Azure Bastion](../bastion/bastion-overview.md) e suas credenciais de administrador do Azure AD DS.
1. Abra um prompt de comando e use o comando `whoami` para mostrar o nome diferenciado do usuário autenticado no momento:

    ```console
    whoami /fqdn
    ```

1. Use o comando `runas` para se autenticar como um usuário no domínio local. No comando a seguir, substitua `userUpn@trusteddomain.com` pelo UPN de um usuário do domínio local confiável. O comando solicita a senha do usuário:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for bem-sucedida, um novo prompt de comando será aberto. O título do novo prompt de comando inclui `running as userUpn@trusteddomain.com`.
1. Use `whoami /fqdn` no novo prompt de comando para ver o nome diferenciado do usuário autenticado no Active Directory local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local

Usando o VM do Windows Server ingressado na floresta de recursos do Azure AD DS, você pode testar o cenário em que usuários podem acessar recursos hospedados na floresta de recursos quando eles se autenticam em computadores no domínio local com usuários do domínio local. Os exemplos a seguir mostram como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Habilitar compartilhamento de arquivos e impressora

1. Conecte-se à VM do Windows Server ingressada na floresta de recursos do Azure AD DS usando o [Azure Bastion](../bastion/bastion-overview.md) e suas credenciais de administrador do Azure AD DS.

1. Abra as **Configurações do Windows**, pesquise e selecione **Central de Rede e Compartilhamento**.
1. Escolha a opção para as configurações **Alterar compartilhamento avançado**.
1. No **Perfil de Domínio**, selecione **Ativar compartilhamento de arquivos e impressora** e, em seguida, **Salvar alterações**.
1. Feche a **Central de Rede e Compartilhamento**.

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Abra **Computadores e Usuários do Active Directory**.
1. Selecione com o botão direito do mouse o nome de domínio, escolha **Novo** e, em seguida, selecione **Unidade Organizacional**.
1. Na caixa de nome, digite *LocalObjects* e, em seguida, selecione **OK**.
1. Selecione e clique com o botão direito do mouse em **LocalObjects** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Digite *FileServerAccess* na caixa **Nome do grupo**. Para o **Escopo do Grupo**, selecione **Local do domínio** e, em seguida, **OK**.
1. No painel de conteúdo, clique duas vezes em **FileServerAccess**. Selecione **Membros**, escolha **Adicionar** e selecione **Locais**.
1. Selecione o Active Directory local na exibição **Localização** e escolha **OK**.
1. Digite os *Usuários de Domínio* na caixa **Insira os nomes de objeto a serem selecionados**. Selecione **Verificar Nomes**, forneça credenciais para o Active Directory local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Você deve fornecer credenciais porque a relação de confiança é somente unidirecional. Isso significa que os usuários do domínio gerenciado do Azure AD DS não podem acessar recursos nem pesquisar usuários ou grupos no domínio confiável (local).

1. O grupo **Usuários de Domínio** do seu Active Directory local deve ser membro do grupo **FileServerAccess**. Selecione **OK** para salvar o grupo e fechar a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar um compartilhamento de arquivo para acesso entre florestas

1. Na VM do Windows Server ingressada na floresta de recursos do Azure AD DS, crie uma pasta e forneça um nome como *CrossForestShare*.
1. Selecione com o botão direito do mouse a pasta e escolha **Propriedades**.
1. Na guia **Segurança** e, em seguida, escolha **Editar**.
1. Na caixa de diálogo *Permissões para CrossForestShare*, selecione **Adicionar**.
1. Digite *FileServerAccess* em **Insira os nomes de objeto a serem selecionados** e, em seguida, selecione **OK**.
1. Selecione *FileServerAccess* na lista **Grupos ou nomes de usuário**. Na lista **Permissões para FileServerAccess**, escolha *Permitir* para as permissões **Modificar** e **Gravar**; em seguida, selecione **OK**.
1. Selecione a guia **Compartilhamento** e escolha **Compartilhamento Avançado…** .
1. Escolha **Compartilhar esta pasta** e, em seguida, insira um nome fácil de memorizar para o compartilhamento de arquivo em **Nome do compartilhamento** como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **Permissões para Todos**, escolha **Permitir** para a permissão **Alterar**.
1. Selecione **OK** duas vezes e, em seguida, **Fechar**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação entre florestas para um recurso

1. Entre em um computador Windows ingressado em seu Active Directory local usando uma conta de usuário do Active Directory local.
1. Usando o **Windows Explorer**, conecte-se ao compartilhamento que você criou usando o nome de host totalmente qualificado e o compartilhamento, como `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Para validar a permissão de gravação, selecione com o botão direito do mouse a pasta, escolha **Novo** e, em seguida, selecione **Documento de Texto**. Use o nome padrão **Novo Documento de Texto**.

    Se as permissões de gravação estiverem definidas corretamente, um documento de texto será criado. As etapas a seguir abrirão, editarão e excluirão o arquivo conforme apropriado.
1. Para validar a permissão Ler, abra **Novo Documento de Texto**.
1. Para validar a permissão Modificar, adicione texto ao arquivo e feche o **Bloco de Notas**. Quando for solicitado a salvar as alterações, escolha **Salvar**.
1. Para validar a permissão Excluir, selecione com o botão direito do mouse **Novo Documento de Texto** e escolha **Excluir**. Escolha **Sim** para confirmar a exclusão do arquivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar o DNS em um ambiente do AD DS local para dar suporte à conectividade do Azure AD DS
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente do AD DS local
> * Criar uma relação de confiança de floresta de saída unidirecional no Azure AD DS
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Para obter mais informações conceituais sobre os tipos de floresta no Azure AD DS, confira [O que são florestas de recursos?][concepts-forest] e [Como relações de confiança de floresta funcionam no Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md
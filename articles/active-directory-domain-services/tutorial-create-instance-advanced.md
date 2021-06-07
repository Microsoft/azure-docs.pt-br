---
title: Tutorial – Criar um domínio gerenciado personalizado do Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar um domínio gerenciado personalizado do Azure Active Directory Domain Services e a especificar opções de configuração avançadas usando o portal do Azure.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4d03edac98329d7c47b8bfafbf4d30cfca18863c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618222"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>Tutorial: Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services com opções de configuração avançadas

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

[Crie um domínio gerenciado usando as opções de configuração padrão][tutorial-create-instance] para rede e sincronização ou defina essas configurações manualmente. Este tutorial mostra como definir essas opções de configuração avançadas para criar e configurar um domínio gerenciado do Azure AD DS usando o portal do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar um domínio gerenciado
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Permitir a sincronização de hash de senha

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

Embora não seja obrigatório para o Azure AD DS, é recomendável [configurar a SSPR (redefinição de senha por autoatendimento)][configure-sspr] para o locatário do Azure AD. Os usuários podem alterar sua senha sem a SSPR, mas a SSPR ajudará se eles esquecerem sua senha e precisarem redefini-la.

> [!IMPORTANT]
> Depois de criar um domínio gerenciado, você não poderá mover o domínio gerenciado para outro grupo de recursos, outra rede virtual, outra assinatura etc. Tome cuidado ao selecionar a assinatura, o grupo de recursos, a região e a rede virtual mais apropriados ao implantar o domínio gerenciado.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você cria e configura o domínio gerenciado usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>Criar um domínio gerenciado e definir as configurações básicas

Para iniciar o assistente **Habilitar Azure AD Domain Services**, conclua as seguintes etapas:

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Insira *Domain Services* na barra de pesquisa e, em seguida, escolha *Azure AD Domain Services* nas sugestões de pesquisa.
1. Na página Azure AD Domain Services, selecione **Criar**. O assistente **Habilitar Azure AD Domain Services** é iniciado.
1. Selecione a **Assinatura** do Azure na qual você deseja criar o domínio gerenciado.
1. Selecione o **Grupo de recursos** a que o domínio gerenciado deve pertencer. Escolha **Criar** ou selecione um grupo de recursos existente.

Ao criar um domínio gerenciado, você especifica um nome DNS. Eis algumas considerações a serem feitas ao escolher o nome DNS:

* **Nome de domínio interno:** Por padrão, o nome de domínio interno do diretório é usado (com sufixo *.onmicrosoft.com*). Se quiser habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, nenhuma AC (Autoridade de Certificação) emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, normalmente um que você já tenha e seja roteável. Quando você usa um domínio roteável personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não roteáveis:** De modo geral, recomendamos que você evite sufixos de nome de domínio não roteáveis, tal como *contoso.local*. O sufixo *.local* não é roteável e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. Recomendamos usar um nome de domínio separado de qualquer namespace DNS local ou do Azure existente.
>
> Por exemplo, se você tiver um namespace DNS existente *contoso.com*, crie um domínio gerenciado com o nome de domínio personalizado *aaddscontoso.com*. Caso precise usar o LDAP Seguro, registre e seja o proprietário desse nome de domínio personalizado para gerar os certificados necessários.
>
> Talvez seja necessário criar alguns registros DNS adicionais para outros serviços no ambiente ou encaminhadores DNS condicionais entre os namespaces DNS existentes no ambiente. Por exemplo, se você executar um servidor Web que hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Nestes tutoriais e nestes artigos de instruções, o domínio personalizado *aaddscontoso.com* é usado como um breve exemplo. Em todos os comandos, especifique o próprio nome de domínio.

As seguintes restrições de nome DNS também se aplicam:

* **Restrições do prefixo do domínio:** Você não pode criar um domínio gerenciado com um prefixo de mais de 15 caracteres. O prefixo do nome de domínio especificado (como *aaddscontoso* no nome de domínio *aaddscontoso.com*) precisa conter até 15 caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado não deve já existir na rede virtual. Especificamente, verifique os seguintes cenários que podem resultar em conflitos de nome:
    * Se você já tiver um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.
    * Se você tiver um serviço de nuvem existente do Azure com esse mesmo nome na rede virtual do Azure.

Preencha os campos na janela *Básico* do portal do Azure para criar um domínio gerenciado:

1. Insira um **nome de domínio DNS** para o domínio gerenciado, levando em consideração os pontos anteriores.
1. Escolha o **Local** do Azure no qual o domínio gerenciado deve ser criado. Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos do Azure AD DS serão distribuídos entre as zonas para proporcionar redundância adicional.

    > [!TIP]
    > As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.
    >
    > Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para saber mais e ver a disponibilidade da região, consulte [O que são as Zonas de Disponibilidade no Azure?][availability-zones]

1. O **SKU** determina o desempenho, a frequência de backup e o número máximo de relações de confiança de floresta que você pode criar. Você poderá alterar a SKU depois que o domínio gerenciado tiver sido criado se as demandas ou requisitos empresariais mudarem. Para obter mais informações, confira [Conceitos do SKU do Azure AD DS][concepts-sku].

    Para este tutorial, selecione o SKU *Standard*.
1. Uma *floresta* é um constructo lógico usado pelo Active Directory Domain Services para agrupar um ou mais domínios. Por padrão, um domínio gerenciado é criado como uma floresta de *Usuários*. Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local.

    Uma floresta de *Recursos* sincroniza apenas usuários e grupos criados diretamente no Azure AD. Os hashes de senha dos usuários locais nunca são sincronizados em um domínio gerenciado quando você cria uma floresta de recursos. Para saber mais sobre florestas de *Recursos*, incluindo por que você usaria uma e como criar relações de confiança das floresta com domínios locais do AD DS, confira [Visão geral das florestas de recursos do Azure AD DS][resource-forests].

    Para este tutorial, escolha criar uma floresta de *Usuários*.

    ![Definir as configurações básicas para um domínio gerenciado do Azure AD Domain Services](./media/tutorial-create-instance-advanced/basics-window.png)

1. Para configurar manualmente as opções adicionais, escolha **Avançar – Rede**. Caso contrário, selecione **Examinar + criar** para aceitar as opções de configuração padrão e, em seguida, vá para a seção para [Implantar seu domínio gerenciado](#deploy-the-managed-domain). Os seguintes padrões são configurados quando você escolhe essa opção de criação:

    * Cria uma rede virtual chamada *aadds-vnet* que usa o intervalo de endereços IP *10.0.1.0/24*.
    * Cria uma sub-rede chamada *aadds-subnet* usando o intervalo de endereços IP *10.0.1.0/24*.
    * Sincroniza *Todos* os usuários do Azure AD no domínio gerenciado.

## <a name="create-and-configure-the-virtual-network"></a>Criar e configurar a rede virtual

Para fornecer conectividade, são necessárias uma rede virtual do Azure e uma sub-rede dedicada. O Azure AD DS está habilitado na sub-rede desta rede virtual. Neste tutorial, você criará uma rede virtual, embora possa optar por usar uma rede virtual existente. Em qualquer abordagem, você deve criar uma sub-rede dedicada para uso pelo Azure AD DS.

Algumas considerações para essa sub-rede de rede virtual dedicada incluem as seguintes áreas:

* A sub-rede deve ter pelo menos de 3 a 5 endereços IP disponíveis em seu intervalo de endereços para dar suporte aos recursos do Azure AD DS.
* Não selecione a sub-rede do *Gateway* para implantar o Azure AD DS. Não há suporte para implantar o Azure AD DS em uma sub-rede de *Gateway*.
* Não implante nenhuma outra máquina virtual na sub-rede. Os aplicativos e as VMs geralmente usam grupos de segurança de rede para proteger a conectividade. A execução dessas cargas de trabalho em uma sub-rede separada permite que você aplique esses grupos de segurança de rede sem interromper a conectividade com o domínio gerenciado.
* Você não pode mover o domínio gerenciado para uma rede virtual diferente depois que tiver habilitado o Azure AD DS.

Para obter mais informações sobre como planejar e configurar a rede virtual, confira as [considerações de rede para o Azure Active Directory Domain Services][network-considerations].

Preencha os campos na janela *Rede* da seguinte maneira:

1. Na página **Rede**, escolha uma rede virtual na qual o Azure AD DS será implantado no menu suspenso ou selecione **Criar**.
    1. Se você optar por criar uma rede virtual, insira um nome para a rede virtual, como *myVnet*, em seguida, forneça um intervalo de endereços, como *10.0.1.0/24*.
    1. Crie uma sub-rede dedicada com um nome claro, como *DomainServices*. Forneça um intervalo de endereços, como *10.0.1.0/24*.

    [ ![Criar uma rede virtual e uma sub-rede para uso com o Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Escolha um intervalo de endereço que esteja dentro do seu intervalo de endereços IP privados. Intervalos de endereços IP que não lhe pertencem e estejam no espaço de endereços públicos causam erros no Azure AD DS.

1. Selecione uma sub-rede da rede virtual, como *DomainServices*.
1. Quando estiver pronto, escolha **Avançar – Administração**.

## <a name="configure-an-administrative-group"></a>Configurar um grupo administrativo

Um grupo administrativo especial chamado *Administradores do AAD DC* é usado para o gerenciamento do domínio do Azure AD DS. Os membros desse grupo recebem permissões administrativas nas VMs que estão unidas ao domínio gerenciado. Em VMs unidas ao domínio, esse grupo é adicionado ao grupo de administradores locais. Os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente às VMs unidas ao domínio.

> [!IMPORTANT]
> Você não tem permissões de *Administrador de Domínio* ou de *Administrador Corporativo* em um domínio gerenciado usando o Azure AD DS. Essas permissões são reservadas pelo serviço e não são disponibilizadas aos usuários dentro do locatário.
>
> Em vez disso, o grupo de *Administradores do AAD DC* permite que você execute algumas operações privilegiadas. Essas operações incluem pertencer ao grupo de administração nas VMs ingressadas no domínio e configurar a Política de Grupo.

O assistente cria automaticamente o grupo de *Administradores do AAD DC* no diretório do Azure AD. Se houver um grupo existente com esse nome em seu diretório do Azure AD, o assistente o selecionará. Como alternativa, você pode optar por adicionar outros usuários a esse grupo de *Administradores do AAD DC* durante o processo de implantação. Essas etapas podem ser concluídas posteriormente.

1. Para adicionar outros usuários a esse grupo de *Administradores do AAD DC*, selecione **Gerenciar associação ao grupo**.

    ![Configurar a associação ao grupo do grupo de Administradores do AAD DC](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecione o botão **Adicionar membros** e procure e selecione usuários do diretório do Azure AD. Por exemplo, pesquise sua própria conta e adicione-a ao grupo de *Administradores do AAD DC*.
1. Se desejar, altere ou adicione destinatários adicionais para notificações quando houver alertas no domínio gerenciado que exijam atenção.
1. Quando estiver pronto, escolha **Avançar – Sincronização**.

## <a name="configure-synchronization"></a>Configurar sincronização

O Azure AD DS permite que você sincronize *todos* os usuários e grupos disponíveis no Azure AD ou uma sincronização *com escopo* incluindo apenas grupos específicos. Você pode alterar o escopo da sincronização agora ou após o domínio gerenciado ser implantado. Para saber mais, confira [Sincronização com escopo do Azure AD Domain Services][scoped-sync].

1. Para este tutorial, escolha sincronizar **Todos** os usuários e grupos. Essa opção de sincronização é a opção padrão.

    ![Execute uma sincronização completa de usuários e grupos do Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecione **Examinar + criar**.

## <a name="deploy-the-managed-domain"></a>Implantar o domínio gerenciado

Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar para qualquer etapa do assistente para efetuar alterações. Para reimplantar um domínio gerenciado em outro locatário do Azure AD de maneira consistente usando essas opções de configuração, você também pode **Baixar um modelo para automação**.

1. Para criar o domínio gerenciado, selecione **Criar**. É exibida uma observação indicando que algumas opções de configuração, como o nome DNS ou a rede virtual, não podem ser alteradas depois que o Azure AD DS gerenciado é criado. Para continuar, selecione **OK**.
1. O processo de provisionamento de seu domínio gerenciado pode levar até uma hora. Uma notificação é exibida no portal, mostrando o andamento da sua implantação do Azure AD DS. Selecione a notificação para ver detalhes do andamento da implantação.

    ![Notificação no portal do Azure da implantação em andamento](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecione o grupo de recursos, como *myResourceGroup* e, em seguida, escolha o domínio gerenciado na lista de recursos do Azure, como *aaddscontoso.com*. A guia **Visão Geral** mostra que o domínio gerenciado está sendo *implantado* no momento. Você não pode configurar o domínio gerenciado até que ele esteja totalmente provisionado.

    ![Status do Domain Services durante o estado de provisionamento](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Quando o domínio gerenciado está totalmente provisionado, a guia **Visão Geral** mostra o status do domínio como *Em execução*.

    ![Status do Domain Services depois de provisionado com sucesso](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> O domínio gerenciado está associado ao seu locatário do Azure AD. Durante o processo de provisionamento, o Azure AD DS cria dois aplicativos empresariais denominados *Serviços de Controlador de Domínio* e *AzureActiveDirectoryDomainControllerServices* no locatário do Azure AD. Esses Aplicativos Empresariais são necessários para atender o domínio gerenciado. Não exclua esses aplicativos.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as configurações do DNS para a rede virtual do Azure

Com o Azure AD DS implantado com sucesso, agora configure a rede virtual para permitir que outras VMs e aplicativos conectados usem o domínio gerenciado. Para fornecer essa conectividade, atualize as configurações do servidor DNS da rede virtual para que ela aponte para os dois endereços IP em que o domínio gerenciado está implantado.

1. A guia **Visão geral** do domínio gerenciado mostra algumas **Etapas de configuração obrigatórias**. A primeira etapa de configuração é atualizar as configurações do servidor DNS da rede virtual. Depois que as configurações de DNS forem definidas corretamente, essa etapa não será mais mostrada.

    Os endereços listados são os controladores de domínio para uso na rede virtual. Nesse exemplo, os endereços são *10.0.1.4* e *10.0.1.5*. Posteriormente, você poderá encontrar esses endereços IP na guia **Propriedades**.

    ![Definir as configurações de DNS para sua rede virtual com os endereços IP do Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Para atualizar as configurações do servidor DNS da rede virtual, selecione o botão **Configurar**. As configurações de DNS são configuradas automaticamente para sua rede virtual.

> [!TIP]
> Se você selecionou uma rede virtual existente nas etapas anteriores, todas as VMs conectadas à rede só obterão as novas configurações de DNS após uma reinicialização. Você pode reiniciar as VMs usando o portal do Azure, o Azure PowerShell ou a CLI do Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Habilitar contas de usuário para o Azure AD DS

Para autenticar os usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para a autenticação NTLM (Gerenciador de LAN NT) e Kerberos. O Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

> [!NOTE]
> Uma vez configurado adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerenciado. Se você excluir o domínio gerenciado, todas as hashes de senha armazenadas nesse ponto também serão excluídas.
>
> As informações de credenciais sincronizadas no Azure AD não poderão ser reutilizadas se você criar posteriormente um domínio gerenciado – você precisa reconfigurar a sincronização de hash de senha para armazenar os hashes de senha novamente. As VMs ou os usuários previamente unidos ao domínio não poderão autenticar imediatamente – o Azure AD precisa gerar e armazenar as hashes de senha no novo domínio gerenciado.
>
> Para obter mais informações, confira [Processo de sincronização de hash de senha para o Azure AD DS e o Azure AD Connect][password-hash-sync-process].

As etapas para gerar e armazenar esses hashes de senha são diferentes para as contas de usuário somente em nuvem criadas no Azure AD versus as contas de usuário sincronizadas do seu diretório local usando o Azure AD Connect.

Uma conta de usuário somente na nuvem é uma conta que foi criada no diretório do Azure AD usando o portal do Azure ou os cmdlets do Azure AD PowerShell. Essas contas de usuário não são sincronizadas de um diretório local.

Neste tutorial, vamos trabalhar com uma conta de usuário básica somente de nuvem. Para obter mais informações sobre as etapas adicionais necessárias para usar Azure AD Connect, confira [Sincronizar hashes de senha para contas de usuário sincronizadas do AD local para o domínio gerenciado][on-prem-sync].

> [!TIP]
> Se o locatário do Azure AD tiver uma combinação de usuários somente de nuvem e usuários do AD local, será necessário concluir ambos os conjuntos de etapas.

Para contas de usuário somente de nuvem, os usuários devem alterar suas senhas antes de usar o Azure AD DS. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD. A conta não é sincronizada do Azure AD para o Azure AD DS até que a senha seja alterada. Expire as senhas de todos os usuários do locatário que precisam usar o Azure AD DS (o que forçaria a troca de senha na próxima entrada) ou então instrua-os a alterar as respectivas senhas manualmente. Para este tutorial, vamos alterar manualmente a senha do usuário.

Antes que o usuário possa redefinir a senha, o locatário do Azure AD deve ser [configurado para redefinição de senha self-service][configure-sspr].

Para alterar a senha de um usuário somente de nuvem, o usuário deve concluir as seguintes etapas:

1. Vá para a página do Painel de acesso do Azure AD em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Perfil** no menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na página **Perfil**, selecione **Alterar senha**.
1. Na página **Alterar senha**, insira sua senha existente (antiga) e, em seguida, insira uma nova senha e confirme-a.
1. Selecione **Enviar**.

Leva alguns minutos após você ter alterado sua senha para que a nova senha possa ser usada no Azure AD DS e para que seja possível se conectar com êxito a computadores ingressados no domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar um domínio gerenciado
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Habilitar contas de usuário para o Azure AD DS e gerar hashes de senha

Para ver esse domínio gerenciado em ação, crie e una uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Una uma máquina virtual do Windows Server ao seu domínio gerenciado](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->

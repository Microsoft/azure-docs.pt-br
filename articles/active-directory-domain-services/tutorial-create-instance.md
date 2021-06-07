---
title: Tutorial – Criar um domínio gerenciado do Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar um domínio gerenciado do Azure Active Directory Domain Services usando o portal do Azure.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 945c131394a0a3c6273f79044c8500a2feba70fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618137"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Crie um domínio gerenciado usando as opções de configuração padrão para rede e sincronização ou [defina essas configurações manualmente][tutorial-create-instance-advanced]. Este tutorial mostra como usar opções padrão para criar e configurar um domínio gerenciado do Azure AD DS usando o portal do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Entender os requisitos de DNS para um domínio gerenciado
> * Criar um domínio gerenciado
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

## <a name="create-a-managed-domain"></a>Criar um domínio gerenciado

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
1. Escolha o **Local** do Azure no qual o domínio gerenciado deve ser criado. Se você escolher uma região que dê suporte às Zonas de Disponibilidade do Azure, os recursos do Azure AD DS serão distribuídos entre as zonas para fornecer redundância adicional.

    > [!TIP]
    > As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.
    >
    > Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para saber mais e ver a disponibilidade da região, consulte [O que são as Zonas de Disponibilidade no Azure?][availability-zones]

1. O **SKU** determina o desempenho, a frequência de backup e o número máximo de relações de confiança de floresta que você pode criar. Você poderá alterar a SKU depois que o domínio gerenciado tiver sido criado se as demandas ou requisitos empresariais mudarem. Para obter mais informações, confira [Conceitos do SKU do Azure AD DS][concepts-sku].

    Para este tutorial, selecione o SKU *Standard*.
1. Uma *floresta* é um constructo lógico usado pelo Active Directory Domain Services para agrupar um ou mais domínios. Por padrão, um domínio gerenciado é criado como uma floresta de *Usuários*. Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local.

    Uma floresta de *Recursos* sincroniza apenas usuários e grupos criados diretamente no Azure AD. Para saber mais sobre florestas de *Recursos*, incluindo por que você usaria uma e como criar relações de confiança das floresta com domínios locais do AD DS, confira [Visão geral das florestas de recursos do Azure AD DS][resource-forests].

    Para este tutorial, escolha criar uma floresta de *Usuários*.

    ![Definir as configurações básicas para um domínio gerenciado do Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

Para criar rapidamente um domínio gerenciado, você pode selecionar **Revisar + criar** para aceitar opções de configuração padrão adicionais. Os seguintes padrões são configurados quando você escolhe essa opção de criação:

* Cria uma rede virtual chamada *aadds-vnet* que usa o intervalo de endereços IP *10.0.2.0/24*.
* Cria uma sub-rede chamada *aadds-subnet* usando o intervalo de endereços IP *10.0.2.0/24*.
* Sincroniza *Todos* os usuários do Azure AD no domínio gerenciado.

Selecione **Revisar + criar** para aceitar essas opções de configuração padrão.

## <a name="deploy-the-managed-domain"></a>Implantar o domínio gerenciado

Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar para qualquer etapa do assistente para efetuar alterações. Para reimplantar um domínio gerenciado em outro locatário do Azure AD de maneira consistente usando essas opções de configuração, você também pode **Baixar um modelo para automação**.

1. Para criar o domínio gerenciado, selecione **Criar**. É exibida uma observação indicando que algumas opções de configuração, como o nome DNS ou a rede virtual, não podem ser alteradas depois que o Azure AD DS gerenciado é criado. Para continuar, selecione **OK**.
1. O processo de provisionamento de seu domínio gerenciado pode levar até uma hora. Uma notificação é exibida no portal, mostrando o andamento da sua implantação do Azure AD DS. Selecione a notificação para ver detalhes do andamento da implantação.

    ![Notificação no portal do Azure da implantação em andamento](./media/tutorial-create-instance/deployment-in-progress.png)

1. A página será carregada com atualizações no processo de implantação, incluindo a criação de novos recursos em seu diretório.
1. Selecione o grupo de recursos, como *myResourceGroup* e, em seguida, escolha o domínio gerenciado na lista de recursos do Azure, como *aaddscontoso.com*. A guia **Visão Geral** mostra que o domínio gerenciado está sendo *implantado* no momento. Você não pode configurar o domínio gerenciado até que ele esteja totalmente provisionado.

    ![Status do Domain Services durante o estado de provisionamento](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Quando o domínio gerenciado está totalmente provisionado, a guia **Visão Geral** mostra o status do domínio como *Em execução*.

    ![Status do Domain Services depois de provisionado com sucesso](./media/tutorial-create-instance/successfully-provisioned.png)

> [!IMPORTANT]
> O domínio gerenciado está associado ao seu locatário do Azure AD. Durante o processo de provisionamento, o Azure AD DS cria dois aplicativos empresariais denominados *Serviços de Controlador de Domínio* e *AzureActiveDirectoryDomainControllerServices* no locatário do Azure AD. Esses Aplicativos Empresariais são necessários para atender o domínio gerenciado. Não exclua esses aplicativos.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as configurações do DNS para a rede virtual do Azure

Com o Azure AD DS implantado com sucesso, agora configure a rede virtual para permitir que outras VMs e aplicativos conectados usem o domínio gerenciado. Para fornecer essa conectividade, atualize as configurações do servidor DNS da rede virtual para que ela aponte para os dois endereços IP em que o domínio gerenciado está implantado.

1. A guia **Visão geral** do domínio gerenciado mostra algumas **Etapas de configuração obrigatórias**. A primeira etapa de configuração é atualizar as configurações do servidor DNS da rede virtual. Depois que as configurações de DNS forem definidas corretamente, essa etapa não será mais mostrada.

    Os endereços listados são os controladores de domínio para uso na rede virtual. Nesse exemplo, os endereços são *10.0.2.4* e *10.0.2.5*. Posteriormente, você poderá encontrar esses endereços IP na guia **Propriedades**.

    ![Definir as configurações de DNS para sua rede virtual com os endereços IP do Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

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

> Neste tutorial, vamos trabalhar com uma conta de usuário básica somente de nuvem. Para obter mais informações sobre as etapas adicionais necessárias para usar Azure AD Connect, confira [Sincronizar hashes de senha para contas de usuário sincronizadas do AD local para o domínio gerenciado][on-prem-sync].

> [!TIP]
> Se o locatário do Azure AD tiver uma combinação de usuários somente de nuvem e usuários do AD local, será necessário concluir ambos os conjuntos de etapas.

Para contas de usuário somente de nuvem, os usuários devem alterar suas senhas antes de usar o Azure AD DS. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD. A conta não é sincronizada do Azure AD para o Azure AD DS até que a senha seja alterada. Expire as senhas de todos os usuários do locatário que precisam usar o Azure AD DS (o que forçaria a troca de senha na próxima entrada) ou então instrua-os a alterar as respectivas senhas manualmente. Para este tutorial, vamos alterar manualmente a senha do usuário.

Antes que o usuário possa redefinir a senha, o locatário do Azure AD deve ser [configurado para redefinição de senha self-service][configure-sspr].

Para alterar a senha de um usuário somente de nuvem, o usuário deve concluir as seguintes etapas:

1. Vá para a página do Painel de acesso do Azure AD em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Perfil** no menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance/select-profile.png)

1. Na página **Perfil**, selecione **Alterar senha**.
1. Na página **Alterar senha**, insira sua senha existente (antiga) e, em seguida, insira uma nova senha e confirme-a.
1. Selecione **Enviar**.

Leva alguns minutos após você ter alterado sua senha para que a nova senha possa ser usada no Azure AD DS e para que seja possível se conectar com êxito a computadores ingressados no domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Entender os requisitos de DNS para um domínio gerenciado
> * Criar um domínio gerenciado
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Habilitar contas de usuário para o Azure AD DS e gerar hashes de senha

Antes de ingressar as VMs no domínio e implantar aplicativos que usam o domínio gerenciado, configure uma rede virtual do Azure para cargas de trabalho de aplicativo.

> [!div class="nextstepaction"]
> [Configurar a rede virtual do Azure para cargas de trabalho de aplicativo para usar seu domínio gerenciado](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix

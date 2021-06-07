---
title: Ingressar uma VM do Windows Server em um domínio gerenciado do Azure AD Domain Services   Microsoft Docs
description: Neste tutorial, saiba como ingressar uma máquina virtual do Windows Server em um domínio gerenciado do Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619565"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Ingressar em uma máquina virtual do Windows Server em um domínio gerenciado do Azure Active Directory Domain Services

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Com um domínio gerenciado do Azure AD DS, você pode fornecer recursos de ingresso no domínio e gerenciamento para VMs (máquinas virtuais) no Azure. Este tutorial mostra como criar uma VM do Windows Server e, em seguida, ingressá-la em um domínio gerenciado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar a VM do Windows Server a uma rede virtual do Azure
> * Ingressar a VM no domínio gerenciado

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é parte do domínio gerenciado.
    * Verifique se a sincronização de hash de senha ou a redefinição de senha por autoatendimento do Azure AD Connect foram realizadas de modo que a conta possa entrar no domínio gerenciado.
* Um host do Azure Bastion implantado na rede virtual do Azure AD DS.
    * Se necessário, [crie um host do Azure Bastion][azure-bastion].

Se você já tiver uma VM que quer ingressar em um domínio, pule para a seção de [ingressar a VM no domínio gerenciado](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará uma VM do Windows Server e a ingressará no domínio gerenciado usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Criar uma máquina virtual do Windows Server

Para ver como ingressar um computador em um domínio gerenciado, crie uma VM do Windows Server. Essa VM está conectada a uma rede virtual do Azure que fornece conectividade ao domínio gerenciado. O processo de ingressar em um domínio gerenciado é igual ao de ingressar em um domínio local normal do Active Directory Domain Services.

Se você já tiver uma VM que quer ingressar em um domínio, pule para a seção de [ingressar a VM no domínio gerenciado](#join-the-vm-to-the-managed-domain).

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Em **Começar**, escolha **Windows Server 2016 Datacenter**.

    ![Escolher criar uma VM do Windows Server 2016 Datacenter no portal do Azure](./media/join-windows-vm/select-vm-image.png)

1. Na janela **Básico**, defina as principais configurações da máquina virtual. Deixe as configurações padrão de *Opções de disponibilidade*, *Imagem* e *Tamanho*.

    | Parâmetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Resource group       | Selecione ou crie um grupo de recursos, como *myResourceGroup* |
    | Nome da máquina virtual | Insira um nome para a VM, como *myVM* |
    | Região               | Escolha uma região para criar sua VM, como *Leste dos EUA* |
    | Nome de Usuário             | Insira um nome de usuário para a conta de administrador local para criar na VM, como *azureuser* |
    | Senha             | Insira e confirme uma senha segura para o administrador local criar na VM. Não especifique as credenciais da conta de usuário de um domínio. |

1. Por padrão, as VMs criadas no Azure podem ser acessadas pela Internet usando o RDP. Quando o RDP estiver habilitado, haverá a possibilidade de ataques automatizados de conexão, o que poderá desabilitar contas com nomes comuns, como *admin* ou *administrador* devido a várias tentativas seguidas de logon com falha.

    O protocolo RDP deve ser habilitado somente quando solicitado e limitado a um conjunto de intervalos de IP autorizados. Essa configuração ajuda a aumentar a segurança da VM e reduz a área de possíveis ataques. Ou então, crie e use um host do Azure Bastion que permita o acesso somente por meio do portal do Azure via TLS. Na próxima etapa deste tutorial, você usará um host do Azure Bastion para se conectar com segurança à VM.

    Em **Portas de entrada públicas**, selecione *Nenhum*.

1. Quando terminar, selecione **Avançar: Discos**.
1. No menu suspenso de **Tipo de disco de SO**, escolha *SSD Standard* e **Avançar: Rede**.
1. A VM precisa se conectar a uma sub-rede da rede virtual do Azure que possa se comunicar com a sub-rede em que o domínio gerenciado foi implantado. É recomendável que um domínio gerenciado seja implantado em uma sub-rede própria e dedicada. Não implante a VM na mesma sub-rede do domínio gerenciado.

    Existem duas maneiras principais de implantar a VM e conectar-se a uma sub-rede apropriada da rede virtual:
    
    * Criar ou selecionar uma sub-rede existente na mesma rede virtual em que o domínio gerenciado está implantado.
    * Selecionar uma sub-rede em uma rede virtual do Azure que esteja conectada a ela usando o [emparelhamento de rede virtual do Azure][vnet-peering].
    
    Se você selecionar uma sub-rede de rede virtual que não esteja conectada à sub-rede do domínio gerenciado, não poderá ingressar a VM no domínio gerenciado. Neste tutorial, vamos criar uma nova sub-rede na rede virtual do Azure.

    No painel **Rede**, selecione a rede virtual em que o domínio gerenciado está implantado, como *aaads-vnet*.
1. Neste exemplo, a sub-rede *aaads-subnet* existente mostra que o domínio gerenciado está conectado. Não conecte sua VM a essa sub-rede. Para criar uma sub-rede para a VM, selecione **Gerenciar configuração de sub-rede**.

    ![Escolher gerenciar a configuração de sub-rede no portal do Azure](./media/join-windows-vm/manage-subnet.png)

1. No menu à esquerda da janela da rede virtual, selecione **Espaço de endereço**. A rede virtual é criada com um só espaço de endereço *10.0.2.0/24*, que é usado pela sub-rede padrão. Outras sub-redes, como para *workloads* ou o Azure Bastion, também podem existir.

    Adicione um intervalo de endereços IP adicional à rede virtual. O tamanho desse intervalo de endereços e do intervalo de endereços IP real a ser usado depende de outros recursos de rede já implantados. O intervalo de endereços IP não deve se sobrepor a nenhum intervalo de endereços existente no ambiente local ou do Azure. Dimensione o intervalo de endereços IP com um tamanho grande o suficiente para o número de VMs que espera implantar na sub-rede.

    No exemplo a seguir, um intervalo de endereços IP extra *10.0.5.0/24* é adicionado. Quando estiver pronto, selecione **Salvar**.

    ![Adicionar um intervalo de endereços IP de rede virtual adicional no portal do Azure](./media/join-windows-vm/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela da rede virtual, selecione **Sub-redes** e, em seguida, escolha **+ Sub-rede** para adicionar uma sub-rede.

1. Selecione **+ Sub-rede** e digite um nome para ela, como *gerenciamento*. Forneça um **Intervalo de endereços (bloco CIDR)** , como *10.0.5.0/24*. Verifique se esse intervalo de endereço IP não se sobrepõe a quaisquer outros intervalos de endereços locais ou do Azure. Deixe as outras opções com os valores padrão e clique em **OK**.

    ![Criar uma configuração de sub-rede no portal do Azure](./media/join-windows-vm/create-subnet.png)

1. A sub-rede demora alguns segundos para ser criada. Após a criação, clique no *X* para fechar a janela da sub-rede.
1. Volte ao painel **Rede** para criar uma VM e escolha a sub-rede que você criou no menu suspenso, como *gerenciamento*. Novamente, verifique se você escolheu a sub-rede correta e não implante a VM na mesma sub-rede do domínio gerenciado.
1. Em **IP Público**, selecione *Nenhum* no menu suspenso. Ao usar o Azure Bastion neste tutorial para se conectar ao gerenciamento, você não precisa ter um endereço IP público atribuído à VM.
1. Deixe as outras opções com os valores padrão e clique em **Gerenciamento**.
1. Defina **Diagnóstico de inicialização** como *Desativado*. Deixe as outras opções com os valores padrão e clique em **Revisar + criar**.
1. Revise as configurações da VM e selecione **Criar**.

São necessários alguns minutos para criar a VM. O portal do Azure mostra o status da implantação. Quando a VM estiver pronta, selecione **Ir para o recurso**.

![Vá para o recurso da VM no portal do Azure após a criação bem-sucedida](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Conectar-se à VM do Windows Server

Para se conectar com segurança às suas VMs, use um host do Azure Bastion. Com o Azure Bastion, um host gerenciado é implantado na rede virtual e fornece conexões RDP ou SSH baseadas na Web com as VMs. Nenhum endereço IP público é necessário para as VMs. Você não precisará abrir as regras do grupo de segurança de rede para o tráfego remoto externo. Você se conectará às VMs usando o portal do Azure no navegador da Web. Se necessário, [crie um host do Azure Bastion][azure-bastion].

Para usar um host do Bastion para se conectar à VM, conclua as seguintes etapas:

1. No painel **Visão geral** da VM, selecione **Conectar** e, em seguida **Bastion**.

    ![Conectar-se à uma máquina virtual do Windows usando o Bastion no portal do Azure](./media/join-windows-vm/connect-to-vm.png)

1. Insira as credenciais para a VM especificadas na seção anterior e, em seguida, selecione **Conectar**.

   ![Conectar-se por meio do host do Bastion no portal do Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o navegador da Web abra pop-ups para que a conexão do Bastion seja exibida. São necessários alguns segundos para estabelecer a conexão com a VM.

## <a name="join-the-vm-to-the-managed-domain"></a>Ingressar a VM no domínio gerenciado

Com a VM criada e uma conexão RDP baseada na Web estabelecida usando o Azure Bastion, agora vamos ingressar a máquina virtual do Windows Server no domínio gerenciado. É o mesmo processo quando um computador se conecta a um domínio local normal do Active Directory Domain Services.

1. Se **Gerenciador do Servidor** não abrir por padrão quando você entrar na VM, selecione o menu **Iniciar** e, em seguida, escolha **Gerenciador do Servidor**.
1. No painel esquerdo da janela **Gerenciador do Servidor**, selecione **Servidor Local**. Em **Propriedades**, no painel direito, escolha **Grupo de trabalho**.

    ![Abrir o Gerenciador do Servidor na VM e editar a propriedade do grupo de trabalho](./media/join-windows-vm/server-manager.png)

1. Na janela **Propriedades do Sistema**, selecione **Alterar** para ingressar no domínio gerenciado.

    ![Escolher alterar as propriedades do grupo de trabalho ou do domínio](./media/join-windows-vm/change-domain.png)

1. Na caixa **Domínio**, especifique o nome do domínio gerenciado, como *aaddscontoso.com* e, em seguida, selecione **OK**.

    ![Especificar o domínio gerenciado no qual ingressar](./media/join-windows-vm/join-domain.png)

1. Insira as credenciais do domínio para ingressá-lo. Forneça as credenciais de um usuário que faça parte do domínio gerenciado. A conta deve fazer parte do domínio gerenciado ou do locatário do Azure AD – contas de diretórios externos associadas ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio.

    As credenciais da conta podem ser especificadas em uma das seguintes maneiras:

    * **Formato UPN** (recomendado): insira o sufixo do nome UPN da conta de usuário, conforme configurado no Azure AD. Por exemplo, o sufixo UPN do usuário *contosoadmin* seria `contosoadmin@aaddscontoso.onmicrosoft.com`. Há alguns casos de uso comuns em que o formato UPN pode ser usado de forma confiável para entrar no domínio em vez do formato *SAMAccountName*:
        * Se o prefixo de UPN de um usuário for longo, por exemplo, *deehasareallylongname*, o *SAMAccountName* poderá ser gerado automaticamente.
        * Se vários usuários tiverem o mesmo prefixo UPN no locatário do Azure AD, por exemplo, *dee*, o formato *SAMAccountName* poderá ser gerado automaticamente.
    * **Formato SAMAccountName:** insira o nome da conta no formato *SAMAccountName*. Por exemplo, o *SAMAccountName* do usuário *contosoadmin* seria `AADDSCONTOSO\contosoadmin`.

1. São necessários alguns segundos para ingressar no domínio gerenciado. Quando terminar, a seguinte mensagem lhe dará as boas-vindas ao domínio:

    ![Bem-vindo ao domínio](./media/join-windows-vm/join-domain-successful.png)

    Selecione **OK** para continuar.

1. Para concluir o processo de ingresso no domínio gerenciado, reinicie a VM.

> [!TIP]
> É possível ingressar uma VM no domínio usando o PowerShell com o cmdlet [Add-Computer][add-computer]. O exemplo a seguir ingressa o domínio *AADDSCONTOSO* e, em seguida, reinicia a VM. Quando solicitado, insira as credenciais de um usuário que faça parte do domínio gerenciado:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Para ingressar uma VM no domínio sem se conectar a ela e configurar a conexão manualmente, você pode usar o cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] do Azure PowerShell.

Após a reinicialização da VM do Windows Server, todas as políticas aplicadas ao domínio gerenciado serão enviadas por push à VM. Agora, também é possível entrar na VM do Windows Server usando as credenciais de domínio corretas.

## <a name="clean-up-resources"></a>Limpar os recursos

No próximo tutorial, você usará essa VM do Windows Server para instalar as ferramentas de gerenciamento que permitem administrar o domínio gerenciado. Caso não deseje continuar nesta série de tutoriais, examine as etapas de limpeza a seguir para [excluir a VM](#delete-the-vm). Caso contrário, [prossiga para o próximo tutorial](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>Desfazer o ingresso da VM no domínio gerenciado

Para remover a VM do domínio gerenciado, siga novamente as etapas para [ingressar a VM em um domínio](#join-the-vm-to-the-managed-domain). Em vez de ingressar no domínio gerenciado, escolha ingressar em um grupo de trabalho, como o *WORKGROUP* padrão. Depois que a VM for reinicializada, o objeto de computador será removido do domínio gerenciado.

Se você [excluir a VM](#delete-the-vm) sem deixar o domínio, um objeto de computador órfão será deixado no Azure AD DS.

### <a name="delete-the-vm"></a>Excluir a VM

Se você não for usar essa VM do Windows Server, exclua-a seguindo estas etapas:

1. No menu esquerdo, selecione **Grupos de recursos**
1. Escolha seu grupo de recursos, por exemplo, *myResourceGroup*.
1. Escolha sua VM, por exemplo, *myVM*, e selecione **Excluir**. Selecione **Sim** para confirmar a exclusão do recurso. São necessários alguns minutos para excluir a VM.
1. Quando a VM for excluída, selecione o disco do sistema operacional, o adaptador de rede e quaisquer outros recursos com o prefixo *myVM-* e exclua-os.

## <a name="troubleshoot-domain-join-issues"></a>Solucionar problemas de ingresso no domínio

A VM do Windows Server deve ser ingressada com êxito no domínio gerenciado, do mesmo modo que um computador local normal ingressaria em um domínio do Active Directory Domain Services. Se a VM do Windows Server não puder ingressar no domínio gerenciado, isso indicará a existência de um problema de credencial ou de conectividade. Revise as seguintes seções de solução de problemas para ingressar o domínio gerenciado com êxito.

### <a name="connectivity-issues"></a>Problemas de conectividade

Se você não receber uma solicitação de credenciais para ingressar no domínio, haverá um problema de conectividade. A VM não pode alcançar o domínio gerenciado na rede virtual.

Após tentar cada uma dessas etapas de solução de problemas, tente ingressar a VM do Windows Server no domínio gerenciado novamente.

* Verifique se a VM está conectada à mesma rede virtual em que o Azure AD DS está habilitado ou se há uma conexão de rede emparelhada.
* Tente executar ping do nome de domínio DNS do domínio gerenciado, como `ping aaddscontoso.com`.
    * Se a solicitação de ping falhar, tente executar ping dos endereços IP do domínio gerenciado, por exemplo, `ping 10.0.0.4`. O endereço IP do ambiente é exibido na página *Propriedades* quando você seleciona o domínio gerenciado na lista de recursos do Azure.
    * Se você conseguir executar o ping do endereço IP, mas não do domínio, o DNS poderá estar configurado incorretamente. Verifique se os endereços IP do domínio gerenciado estão configurados como servidores DNS para a rede virtual.
* Tente liberar o cache do resolvedor DNS na máquina virtual usando o comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemas de credenciais

Se você receber uma solicitação de credenciais para ingressar no domínio, mas em seguida um erro após inserir essas credenciais, a VM será capaz de se conectar ao domínio gerenciado. As credenciais fornecidas não permitem que a VM ingresse no domínio gerenciado.

Após tentar cada uma dessas etapas de solução de problemas, tente ingressar a VM do Windows Server no domínio gerenciado novamente.

* Verifique se a conta de usuário especificada pertence ao domínio gerenciado.
* Confirme se a conta faz parte do domínio gerenciado ou do locatário do Azure AD. Contas de diretórios externos associadas ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio.
* Tente usar o formato UPN para especificar as credenciais, como `contosoadmin@aaddscontoso.onmicrosoft.com`. Se houver vários usuários com o mesmo prefixo UPN no seu locatário ou se o prefixo UPN for muito longo, o *SAMAccountName* da sua conta poderá ser gerado automaticamente. Nesses casos, o formato de *SAMAccountName* da sua conta pode ser diferente do que você espera ou usa em seu domínio local.
* Verifique se você [habilitou a sincronização de senhas][password-sync] para o domínio gerenciado. Sem esta etapa de configuração, os hashes de senha necessários não estarão presentes no domínio gerenciado para autenticar corretamente sua tentativa de conexão.
* Aguarde a conclusão da sincronização de senha. Quando a senha de uma conta de usuário é alterada, uma sincronização automática em segundo plano do Azure AD a atualiza no Azure AD DS. Leva algum tempo para que a senha fique disponível para ser usada para ingresso no domínio.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar a VM do Windows Server a uma rede virtual do Azure
> * Ingressar a VM no domínio gerenciado

Para administrar o domínio gerenciado, configure uma VM de gerenciamento usando o ADAC (Centro Administrativo do Active Directory).

> [!div class="nextstepaction"]
> [Instalar ferramentas de administração em uma VM de gerenciamento](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
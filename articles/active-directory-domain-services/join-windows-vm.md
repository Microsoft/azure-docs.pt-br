---
title: 'Azure Active Directory Domain Services: Adicionar uma VM do Windows Server a um domínio gerenciado | Microsoft Docs'
description: Ingressar uma máquina virtual do Windows Server no Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 377e253ef595e933f3ccab76bd053e2b416d3a16
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473177"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Ingressar uma máquina virtual do Windows Server em um domínio gerenciado
Este artigo mostra como implantar uma máquina virtual do Windows Server usando o Portal do Azure. Então, mostra como ingressar a máquina virtual em um domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Etapa 1: Criar uma máquina virtual do Windows Server
Para criar uma máquina virtual do Windows ingressada na rede virtual na qual você habilitou o Azure AD DS, execute estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na parte superior do painel esquerdo, selecione **Novo**.
3. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.

    ![O link do Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. No painel **Noções Básicas** do assistente, defina as configurações básicas da máquina virtual.

    ![O painel Noções Básicas](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > O nome de usuário e a senha que você insere aqui são para uma conta de administrador local usada para fazer logon na máquina virtual. Escolha uma senha forte para proteger a máquina virtual contra ataques de força bruta da senha. Não digite as credenciais de uma conta usuário de domínio aqui.
    >

5. Selecione um **Tamanho** para a máquina virtual. Para exibir mais tamanhos, selecione **Exibir todos** ou altere o filtro **Tipo de disco com suporte**.

    ![O painel “Escolha um tamanho”](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. No painel **Configurações**, selecione a rede virtual na qual o domínio gerenciado do Azure AD DS é implantado. Escolha uma sub-rede diferente daquela na qual o seu domínio gerenciado está implantado. Para as outras configurações, mantenha os padrões e selecione **OK**.

    ![Configurações da rede virtual para a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Escolha a rede virtual e sub-rede certas.**
    >
    > Selecione a rede virtual na qual o domínio gerenciado está implantado ou uma rede virtual que esteja a ele usando emparelhamento de rede virtual. Se você selecionar uma rede virtual desconectada, você não poderá ingressar a máquina virtual no domínio gerenciado.
    >
    > É recomendável implantar o seu domínio gerenciado em uma sub-rede dedicada. Portanto, não selecione a sub-rede na qual você habilitou o seu domínio gerenciado.

7. Para as outras configurações, mantenha os padrões e selecione **OK**.
8. Na página **Compra**, examine as configurações e selecione **OK** para implantar a máquina virtual.
9. A implantação da VM será fixada ao painel do portal do Azure.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Após a implantação ser concluída, você pode exibir informações sobre a VM na página **Visão Geral**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Etapa 2: Conectar a máquina virtual do Windows Server usando a conta de administrador local
Em seguida, conecte-se à máquina virtual do Windows Server recém-criada para ingressar no domínio. Use as credenciais de administrador local especificadas quando você criou a máquina virtual.

Para conectar-se à máquina virtual, execute estas etapas:

1. No painel **Visão geral**, selecione **Conectar**.  
    Um arquivo do Protocolo RDP (.rdp) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
3. Insira as **credenciais de administrador local**que você especificou ao criar a máquina virtual (por exemplo, *localhost\mahesh*).
4. Se você vir um aviso de certificado durante o processo de entrada, selecione **Sim** ou **Continuar** para se conectar.

Neste ponto, você deve estar conectado à máquina virtual do Windows recém-criada com as credenciais de administrador local. A próxima etapa é ingressar a máquina virtual no domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Etapa 3: Ingressar a máquina virtual do Windows Server no domínio gerenciado do Azure AD DS
Para ingressar a máquina virtual do Windows Server no domínio gerenciado do Azure AD DS, execute estas etapas:

1. Conecte-se à VM do Windows Server, como mostrado na “Etapa 2”. Na tela **Iniciar**, abra **Gerenciador do Servidor**.
2. No painel esquerdo da janela **Gerenciador do Servidor**, selecione **Servidor Local**.

    ![A janela Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Em **Propriedades**, selecione **Grupo de Trabalho**.
4. Na janela **Propriedades do Sistema**, selecione **Alterar** para ingressar no domínio.

    ![A janela Propriedades do Sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Na caixa **Domínio**, especifique o nome do domínio gerenciado do Azure AD DS e selecione **OK**.

    ![Especificar o domínio no qual ingressar](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Você recebe uma solicitação para inserir suas credenciais e ingressar no domínio. Use as credenciais de um *usuário que pertença ao grupo de administradores DC do Azure AD*. Somente os membros desse grupo têm privilégios para ingressar computadores no domínio gerenciado.

    ![A janela Segurança do Windows para especificar credenciais](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Você pode especificar as credenciais das seguintes maneiras:

   * **Formato UPN**: (Recomendado) Especifique o sufixo do UPN (nome UPN) da conta de usuário, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do usuário *bob* é *bob\@domainservicespreview.onmicrosoft.com*.

   * **Formato SAMAccountName**: É possível especificar o nome da conta no formato SAMAccountName. Neste exemplo, o usuário *bob* precisaria inserir *CONTOSO100\bob*.

     > [!TIP]
     > **Recomendamos usar o formato UPN para especificar as credenciais.**
     >
     > Se o prefixo de UPN de um usuário for longo demais (por exemplo, *joehasareallylongname*), o SAMAccountName poderá ser gerado automaticamente. Se vários usuários tiverem o mesmo prefixo UPN (por exemplo, *bob*) no seu locatário do Azure AD, o formato SAMAccountName poderá ser gerado automaticamente pelo serviço. Nesses casos, o formato UPN pode ser usado de forma confiável para fazer logon no domínio.
     >

8. Depois que tiver ingressado em um domínio com êxito, a mensagem de erro a seguir receberá você no domínio.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Para concluir o ingresso no domínio, reinicie a máquina virtual.

## <a name="troubleshoot-joining-a-domain"></a>Solucionar problemas de ingresso em um domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual for incapaz de localizar o domínio, experimente estas etapas de solução de problemas:

* Verifique se a máquina virtual está conectada à mesma rede virtual na qual o Azure AD DS está habilitado. Caso contrário, a máquina virtual não poderá se conectar ou ingressar no domínio.

* Verifique se a máquina virtual está em uma rede virtual que, por sua vez, esteja conectada à rede virtual na qual o Azure AD DS está habilitado.

* Tente executar o ping no nome de domínio DNS do domínio gerenciado (por exemplo, *ping contoso100.com*). Se você não puder fazer isso, tente executar o ping dos endereços IP para o domínio exibido na página em que você habilitou o Azure AD DS (por exemplo, *ping 10.0.0.4*). Se você conseguir executar o ping do endereço IP, mas não do domínio, o DNS poderá estar configurado incorretamente. Verifique se os endereços IP do domínio estão configurados como servidores DNS para a rede virtual.

* Tente liberar o cache do resolvedor DNS na máquina virtual (*ipconfig /flushdns*).

Se uma janela for exibida solicitando credenciais para ingressar no domínio, você não terá problemas de conectividade.

### <a name="credentials-related-issues"></a>Problemas de credenciais
Se você estiver enfrentando problemas com as credenciais e não conseguir ingressar no domínio, tente as seguintes etapas de solução de problema:

* Tente usar o formato UPN para especificar as credenciais. Se houver vários usuários com o mesmo prefixo UPN no seu locatário ou se o prefixo UPN for muito longo, o SAMAccountName para sua conta poderá ser gerado automaticamente. Nesses casos, o formato de SAMAccountName para sua conta pode ser diferente do que você espera ou usa em seu domínio local.

* Tente usar credenciais de uma conta de usuário que pertença ao grupo *Administradores do DC do AAD*.

* Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) para o domínio gerenciado.

* Verifique se você usou o UPN do usuário conforme configurado no Azure AD (por exemplo, *bob\@domainservicespreview.onmicrosoft.com*) para entrar.

* Aguarde tempo suficiente até a conclusão da sincronização da senha, conforme especificado no guia de introdução.

## <a name="related-content"></a>Conteúdo relacionado
* [Guia de introdução ao Azure AD DS](create-instance.md)
* [Gerenciar um domínio do Azure AD Domain Services](manage-domain.md)

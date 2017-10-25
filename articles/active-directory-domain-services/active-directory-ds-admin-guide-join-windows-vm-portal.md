---
title: "Active Directory Domain Services: ingressar uma VM do Windows Server em um domínio gerenciado | Microsoft Docs"
description: "Ingressar uma máquina virtual do Windows Server nos Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ce50c678247226b629490a2bd8ba2935ed229f06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Ingressar uma máquina virtual do Windows Server em um domínio gerenciado
> [!div class="op_single_selector"]
> * [Portal do Azure - Windows](active-directory-ds-admin-guide-join-windows-vm-portal.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

Este artigo mostra como implantar uma máquina virtual do Windows Server usando o Portal do Azure. Então, mostra como ingressar a máquina virtual em um domínio gerenciado do Azure AD Domain Services.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Etapa 1: Criar a máquina virtual do Windows Server
Realize as seguintes etapas para criar uma máquina virtual do Windows ingressada na rede virtual na qual você habilitou os Serviços de Domínio do Azure AD.

1. Faça logon no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
3. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.

    ![Selecionar a imagem](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Defina as configurações básicas para a máquina virtual na página **Noções Básicas** do assistente.

    ![Definir as configurações básicas para a VM](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > O nome de usuário e a senha que você insere aqui são para uma conta de administrador local usada para fazer logon na máquina virtual. Escolha uma senha forte para proteger a máquina virtual contra ataques de força bruta da senha. Não digite as credenciais de uma conta usuário de domínio aqui.
    >

5. Selecione um **Tamanho** para a máquina virtual. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**.

    ![Selecione o tamanho da VM](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Na página **Configurações** do assistente, selecione a rede virtual na qual o domínio gerenciado do Azure AD Domain Services é implantado. Escolha uma sub-rede diferente daquela na qual o seu domínio gerenciado está implantado. Para as outras configurações, mantenha os padrões e clique em **OK**.

    ![Selecionar a rede virtual para a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Escolha a rede virtual e sub-rede certas.**
    > Selecione a rede virtual na qual o domínio gerenciado está implantado ou uma rede virtual que esteja a ele usando emparelhamento de rede virtual. Se você selecionar uma rede virtual diferente, não poderá ingressar na rede virtual para o domínio gerenciado.
    > É recomendável implantar o seu domínio gerenciado em uma sub-rede dedicada. Portanto, não selecione a sub-rede na qual você habilitou o seu domínio gerenciado.

7. Na página **Compra**, examine as configurações e clique em **OK** para implantar a máquina virtual.
8. A implantação da VM será fixada ao painel do portal do Azure.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. Após a implantação ser concluída, você pode ver informações sobre a VM na página **Visão Geral**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Etapa 2: Conectar-se à máquina virtual do Windows Server usando a conta de administrador local
Agora, conecte-se à máquina virtual do Windows Server recém-criada para ingressar no domínio. Use as credenciais de administrador local especificadas durante a criação da máquina virtual.

Execute as seguintes etapas para se conectar à máquina virtual.

1. Clique no botão **Conectar** na página **Visão Geral**. Um arquivo do Protocolo RDP (.rdp) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**.
3. No prompt de logon, insira as **credenciais de administrador local**que você especificou ao criar a máquina virtual. Por exemplo, usamos 'localhost\mahesh' neste exemplo.
4. Você pode receber um aviso do certificado durante o processo de logon. Clique em Sim ou em Continuar para prosseguir com a conexão.

Neste ponto, você deve estar conectado à máquina virtual do Windows recém-criada usando as credenciais de administrador local. A próxima etapa é ingressar a máquina virtual no domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Etapa 3: Ingressar a máquina virtual do Windows Server no domínio gerenciado do DS do AAD
Execute as seguintes etapas para ingressar a máquina virtual do Windows Server no domínio gerenciado do DS do AAD.

1. Conecte-se ao Windows Server como mostrado na Etapa 2. Na tela inicial, abra **Gerenciador do Servidor**.
2. Clique em **Servidor Local** no painel esquerdo da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Clique em **GRUPO DE TRABALHO** na seção **PROPRIEDADES**. Na página de propriedade **Propriedades do Sistema**, clique em **Alterar** para ingressar no domínio.

    ![Página Propriedades do Sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Especifique o nome de domínio do seu domínio gerenciado dos Serviços de Domínio do Azure AD na caixa de texto **Domínio** e clique em **OK**.

    ![Especificar o domínio no qual ingressar](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Você é solicitado a inserir suas credenciais para ingressar no domínio. Certifique-se de **especificar as credenciais para um usuário que pertença ao grupo Administradores do DC do AAD** . Somente os membros desse grupo têm privilégios para ingressar computadores no domínio gerenciado.

    ![Especificar credenciais para ingresso no domínio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Você pode especificar as credenciais das seguintes maneiras:

   * **Formato UPN: (recomendado)** especifique o sufixo UPN para a conta de usuário, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do usuário "pedro" é "bob@domainservicespreview.onmicrosoft.com".
   * **Formato SAMAccountName:** você pode especificar o nome da conta no formato SAMAccountName. Neste exemplo, o usuário 'bob' precisaria inserir 'CONTOSO100\bob'.

     > [!TIP]
     > **Recomendamos usar o formato UPN para especificar as credenciais.**
     > Se o prefixo de UPN de um usuário for longo demais (por exemplo, 'joehasareallylongname'), o SAMAccountName poderá ser gerado automaticamente. Se vários usuários tiverem o mesmo prefixo UPN (por exemplo, 'paulo') no seu locatário do Azure AD, o formato SAMAccountName poderá ser gerado automaticamente pelo serviço. Nesses casos, o formato UPN pode ser usado de forma confiável para fazer logon no domínio.
     >

7. Após o ingresso no domínio ser realizado com êxito, você vê a mensagem de boas-vindas ao domínio a seguir. Reinicie a máquina virtual para que a operação de junção de domínio seja concluída.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Solucionando problemas de ingresso no domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual for incapaz de localizar o domínio, consulte as seguintes etapas de solução de problemas:

* Certifique-se de que a máquina virtual esteja conectada à mesma rede virtual na qual você habilitou os Serviços de Domínio. Caso contrário, a máquina virtual não pode se conectar ao domínio e, assim, não é possível ingressar no domínio.
* Verifique se a máquina virtual está em uma rede virtual que, por sua vez, esteja conectada à rede virtual na qual você habilitou os Serviços de Domínio.
* Tente executar o ping no domínio usando o nome do domínio gerenciado (por exemplo, 'ping contoso100.com'). Se você não puder fazer isso, tente executar o ping dos endereços IP para o domínio exibido na página em que você habilitou os Serviços de Domínio do Azure AD (por exemplo, 'ping 10.0.0.4'). Se você conseguir executar o ping do endereço IP, mas não o domínio, o DNS poderá estar configurado incorretamente. Verifique se os endereços IP do domínio estão configurados como servidores DNS para a rede virtual.
* Tente liberar o cache do resolvedor DNS na máquina virtual ('ipconfig /flushdns').

Se você chegar à caixa de diálogo que solicita credenciais para ingressar no domínio, não terá problemas de conectividade.

### <a name="credentials-related-issues"></a>Problemas de credenciais
Consulte as etapas a seguir se você estiver enfrentando problemas com as credenciais e não conseguir ingressar no domínio.

* Tente usar o formato UPN para especificar as credenciais. Se houver vários usuários com o mesmo prefixo UPN no seu locatário ou se o prefixo UPN for muito longo, o SAMAccountName para sua conta poderá ser gerado automaticamente. Portanto, o formato de SAMAccountName para sua conta pode ser diferente do que você espera ou usa em seu domínio local.
* Tente usar as credenciais de uma conta de usuário que pertence ao grupo 'Administradores do controlador de domínio do AAD' para ingressar computadores ao domínio gerenciado.
* Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.
* Use o UPN do usuário conforme configurado no Azure AD (por exemplo, "bob@domainservicespreview.onmicrosoft.com") para entrar.
* Certifique-se de que já esperou tempo suficiente para a sincronização de senha ser concluída conforme especificado no Guia de Introdução.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

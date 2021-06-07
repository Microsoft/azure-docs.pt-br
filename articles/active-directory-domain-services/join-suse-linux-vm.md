---
title: Ingresse em uma VM de EPU para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e ingressar em uma máquina virtual SUSE Linux Enterprise em um Azure AD Domain Services domínio gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619599"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ingressar em uma máquina virtual SUSE Linux Enterprise em um Azure Active Directory Domain Services domínio gerenciado

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM SUSE Linux Enterprise (EPU) a um domínio gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é parte do domínio gerenciado.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Criar e conectar-se a uma VM do Linux de EPU

Se você tiver uma VM do Linux de EPU existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM de EPU do Linux ou desejar criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ao criar a VM, preste atenção às configurações de rede virtual para garantir que a VM possa se comunicar com o domínio gerenciado:

* Implante a VM na mesma rede virtual, ou emparelhada, na qual você habilitou Azure AD Domain Services.
* Implante a VM em uma sub-rede diferente da Azure AD Domain Services domínio gerenciado.

Depois que a VM for implantada, siga as etapas para se conectar à VM usando SSH.

## <a name="configure-the-hosts-file"></a>Configurar o arquivo de hosts

Para certificar-se de que o nome do host da VM esteja configurado corretamente para o domínio gerenciado, edite o arquivo */etc/hosts* e defina o nome do host:

```console
sudo vi /etc/hosts
```

No arquivo *hosts* , atualize o endereço *localhost* . No exemplo a seguir:

* *aaddscontoso.com* é o nome de domínio DNS do seu domínio gerenciado.
* *Linux-q2gr* é o nome do host da sua VM de EPU que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Quando terminar, salve e saia do arquivo de *hosts* usando o `:wq` comando do editor.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Ingressar a VM no domínio gerenciado usando SSSD

Para ingressar no domínio gerenciado usando o **SSSD** e o módulo de *Gerenciamento de logon de usuário* do YaST, conclua as seguintes etapas:

1. Instale o módulo YaST de *Gerenciamento de logon do usuário* :

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Abra o YaST.

1. Para usar a descoberta automática de DNS mais tarde, configure os endereços IP de domínio gerenciado (o *servidor de Active Directory*) como o servidor de nomes para o cliente.

    No YaST, selecione **sistema > configurações de rede**.

1. Selecione a guia *nome de host/DNS* e insira os endereços IP do domínio gerenciado na caixa de texto *nome servidor 1*. Esses endereços IP são mostrados na janela *Propriedades* no portal do Azure para seu domínio gerenciado, como *10.0.2.4* e *10.0.2.5*.

    Adicione seus próprios endereços IP de domínio gerenciado e, em seguida, selecione **OK**.

1. Na janela principal do YaST, escolha *serviços de rede*  >  *Gerenciamento de logon do usuário*.

    O módulo é aberto com uma visão geral que mostra as diferentes propriedades de rede do seu computador e o método de autenticação em uso no momento, conforme mostrado no seguinte exemplo de captura de tela:

    ![Captura de tela de exemplo da janela de gerenciamento de logon de usuário no YaST](./media/join-suse-linux-vm/overview-window.png)

    Para iniciar a edição, selecione **alterar configurações**.

Para ingressar a VM no domínio gerenciado, conclua as seguintes etapas:

1. Na caixa de diálogo, selecione **Adicionar domínio**.

1. Especifique o *nome de domínio* correto, como *aaddscontoso.com*, e especifique os serviços a serem usados para dados de identidade e autenticação. Selecione *Microsoft Active Directory* para ambos.

    Verifique se a opção *habilitar o domínio* está selecionada.

1. Quando estiver pronto, selecione **OK**.

1. Aceite as configurações padrão na caixa de diálogo a seguir e selecione **OK**.

1. A VM instala software adicional conforme necessário e, em seguida, verifica se o domínio gerenciado está disponível.

    Se tudo estiver correto, a caixa de diálogo de exemplo a seguir será mostrada para indicar que a VM descobriu o domínio gerenciado, mas que você *ainda não está inscrito*.

    ![Captura de tela de exemplo da janela de registro de Active Directory no YaST](./media/join-suse-linux-vm/enroll-window.png)

1. Na caixa de diálogo, especifique o *nome* de usuário e a *senha* de usuários que fazem parte do domínio gerenciado. Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Para certificar-se de que o domínio atual está habilitado para o samba, ative *substituir configuração do samba para trabalhar com este anúncio*.

1. Para registrar, selecione **OK**.

1. Uma mensagem é mostrada para confirmar que você foi registrado com êxito. Para concluir, selecione **OK**.

Depois que a VM for inscrita no domínio gerenciado, configure o cliente usando *gerenciar logon de usuário de domínio*, conforme mostrado no seguinte exemplo de captura de tela:

![Captura de tela de exemplo da janela Gerenciar logon do usuário do domínio no YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Para permitir entradas usando os dados fornecidos pelo domínio gerenciado, marque a caixa *Permitir logon de usuário do domínio*.

1. Opcionalmente, em *habilitar fonte de dados de domínio*, verifique as fontes de dados adicionais conforme necessário para seu ambiente. Essas opções incluem quais usuários têm permissão para usar o **sudo** ou quais unidades de rede estão disponíveis.

1. Para permitir que os usuários no domínio gerenciado tenham diretórios base na VM, marque a caixa *criar diretórios base*.

1. Na barra lateral, selecione **Opções de serviço › Switch de nome** e, em seguida, *Opções estendidas*. Nessa janela, selecione *fallback_homedir* ou *override_homedir* e, em seguida, selecione **Adicionar**.

1. Especifique um valor para o local do diretório base. Para que os diretórios base sigam o formato de */home/user_name*, use */Home/%u*. Para obter mais informações sobre variáveis possíveis, consulte a página do Man SSSD. conf ( `man 5 sssd.conf` ), *override_homedir* de seção.

1. Selecione **OK**.

1. Para salvar as alterações, selecione **OK**. Em seguida, verifique se os valores exibidos agora estão corretos. Para sair da caixa de diálogo, selecione **Cancelar**.

1. Se você pretende executar SSSD e Winbind simultaneamente (por exemplo, ao ingressar via SSSD, mas, em seguida, executar um servidor de arquivos do samba), o *método Kerberos* de opção do samba deve ser definido como *segredos e keytab* em SMB. conf. A opção SSSD *ad_update_samba_machine_account_password* também deve ser definida como *true* em SSSD. conf. Essas opções impedem que o sistema keytab saia da sincronização.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Ingressar a VM no domínio gerenciado usando Winbind

Para ingressar no domínio gerenciado usando o **Winbind** e o módulo de *Associação de domínio do Windows* do YaST, conclua as seguintes etapas:

1. No YaST, selecione **serviços de rede > Associação de domínio do Windows**.

1. Insira o domínio para ingressar no *domínio ou grupo de trabalho* na tela *Associação de domínio do Windows* . Insira o nome de domínio gerenciado, como *aaddscontoso.com*.

    ![Captura de tela de exemplo da janela de associação de domínio do Windows no YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Para usar a origem SMB para autenticação do Linux, marque a opção para *usar informações SMB para autenticação do Linux*.

1. Para criar automaticamente um diretório base local para usuários de domínio gerenciado na VM, marque a opção *criar diretório base no logon*.

1. Marque a opção para *autenticação offline* para permitir que os usuários do domínio se conectem mesmo se o domínio gerenciado estiver temporariamente indisponível.

1. Se você quiser alterar os intervalos de UID e GID para os usuários e grupos do samba, selecione *configurações de especialista*.

1. Configure a sincronização de tempo do protocolo NTP (NTP) para seu domínio gerenciado selecionando *configuração de NTP*. Insira os endereços IP do domínio gerenciado. Esses endereços IP são mostrados na janela *Propriedades* no portal do Azure para seu domínio gerenciado, como *10.0.2.4* e *10.0.2.5*.

1. Selecione **OK** e confirme o ingresso no domínio quando solicitado.

1. Forneça a senha para um administrador no domínio gerenciado e selecione **OK**.

    ![Captura de tela de exemplo do prompt da caixa de diálogo de autenticação ao unir uma VM de EPU ao domínio gerenciado](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Depois de ingressar no domínio gerenciado, você pode entrar nele na sua estação de trabalho usando o Gerenciador de exibição da sua área de trabalho ou o console do.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Ingresse a VM no domínio gerenciado usando o Winbind da interface de linha de comando do YaST

Para ingressar no domínio gerenciado usando o **Winbind** e a *interface de linha de comando do YaST*:

* Ingresse no domínio:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Ingressar a VM no domínio gerenciado usando o Winbind do terminal

Para ingressar no domínio gerenciado usando **Winbind** e o *`samba net` comando*:

1. Instalar o cliente Kerberos e o samba-Winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Edite os arquivos de configuração:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Verifique se a data e a hora no Azure AD e no Linux estão em sincronia. Você pode fazer isso adicionando o servidor do Azure AD ao serviço NTP:
   
   1. Adicione a seguinte linha a/etc/ntp.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Reinicie o serviço NTP:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Ingresse no domínio:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Habilite o Winbind como uma fonte de logon nos módulos de autenticação conectável do Linux (PAM):

   ```console
   pam-config --add --winbind
   ```

6. Habilite a criação automática de diretórios base para que os usuários possam fazer logon:

   ```console
   pam-config -a --mkhomedir
   ```

7. Inicie e habilite o serviço Winbind:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>Permitir autenticação de senha para SSH

Por padrão, os usuários só podem entrar em uma VM usando a autenticação baseada em chave pública SSH. Falha na autenticação baseada em senha. Quando você ingressa a VM em um domínio gerenciado, essas contas de domínio precisam usar a autenticação baseada em senha. Atualize a configuração de SSH para permitir a autenticação baseada em senha da seguinte maneira.

1. Abra o arquivo *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualize a linha de *PasswordAuthentication* para *Sim*:

    ```console
    PasswordAuthentication yes
    ```

    Quando terminar, salve e saia do arquivo de *sshd_conf* usando o `:wq` comando do editor.

1. Para aplicar as alterações e permitir que os usuários entrem usando uma senha, reinicie o serviço SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder os privilégios sudo de grupo 'Administradores de controlador de domínio do AAD'

Para conceder aos membros do grupo de *Administradores do AAD DC* privilégios administrativos na VM de EPU, adicione uma entrada ao */etc/sudoers*. Depois de adicionados, os membros do grupo de *Administradores do AAD DC* podem usar o `sudo` comando na VM de EPU.

1. Abra o arquivo do *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao final do arquivo */etc/sudoers* . O grupo de *Administradores do AAD DC* contém espaço em branco no nome, portanto, inclua o caractere de escape de barra invertida no nome do grupo. Adicione seu próprio nome de domínio, como *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, salve e saia do editor usando o `:wq` comando do editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Entrar na VM usando uma conta de domínio

Para verificar se a VM ingressou com êxito no domínio gerenciado, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório base foi criado e se a associação de grupo do domínio foi aplicada.

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o `ssh -l` comando, como `contosoadmin@aaddscontoso.com` e, em seguida, insira o endereço da VM, como *Linux-q2gr.aaddscontoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Quando você se conectou com êxito à VM, verifique se o diretório base foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no diretório */Home* com seu próprio diretório que corresponda à conta de usuário.

3. Agora, verifique se as associações de grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver as associações de grupo do domínio gerenciado.

4. Se você entrou na VM como um membro do grupo de *Administradores do controlador de domínio do AAD* , verifique se você pode usar corretamente o `sudo` comando:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para conectar a VM ao domínio gerenciado ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

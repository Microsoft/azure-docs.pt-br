---
title: 'Azure Active Directory Domain Services: Unir uma VM do Ubuntu a um domínio gerenciado | Microsoft Docs'
description: Adicionar uma máquina virtual Ubuntu Linux aos Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 29a6cb69a818ed11e5f20dddd7299c01fbefbf47
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234012"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Adicionar uma máquina virtual Ubuntu no Azure a um domínio gerenciado
Este artigo mostra como adicionar uma máquina virtual Ubuntu Linux a um domínio gerenciado do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:  
1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua as etapas necessárias para [sincronizar senhas para seu domínio gerenciado do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Provisionar uma máquina virtual Ubuntu Linux
Provisione uma máquina virtual Ubuntu Linux no Azure usando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [PowerShell do Azure](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implante a máquina virtual na **mesma rede virtual em que você habilitou o Azure AD Domain Services**.
> * Escolha uma **sub-rede diferente** daquela em que você habilitou o Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual Ubuntu Linux
A máquina virtual Ubuntu foi provisionada no Azure. A próxima tarefa é conectar-se remotamente à máquina virtual usando a conta de administrador local criada durante o provisionamento da VM.

Siga as instruções no artigo [Como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o arquivo de hosts na máquina virtual Linux
Em seu terminal SSH, edite o arquivo /etc/hosts e atualize o endereço IP e o nome do host do computador.

```console
sudo vi /etc/hosts
```

No arquivo de hosts, digite o seguinte valor:

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

Aqui, “contoso100.com” é o nome de domínio DNS do seu domínio gerenciado. 'contoso-ubuntu' é o nome do host da máquina virtual Ubuntu que você está adicionado ao domínio gerenciado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar os pacotes necessários na máquina virtual do Linux
Em seguida, instale os pacotes necessários para o ingresso no domínio na máquina virtual. Execute as seguintes etapas:

1.  Em seu terminal SSH, digite o seguinte comando para baixar as listas de pacote dos repositórios. Esse comando atualiza as listas de pacote para obter informações sobre as versões mais recentes de pacotes e suas dependências.

    ```console
    sudo apt-get update
    ```

2. Digite o seguinte comando para instalar os pacotes necessários.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante a instalação do Kerberos, você vê uma tela rosa. A instalação do pacote 'krb5-user' solicita o nome de realm (tudo em LETRAS MAIÚSCULAS). A instalação grava as seções [realm] e [domain_realm] em /etc/krb5.conf.

    > [!TIP]
    > Se o nome do seu domínio gerenciado for contoso100.com, digite CONTOSO100.COM como o realm. Lembre-se de que o nome de realm deve ser especificado em LETRAS MAIÚSCULAS.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Definir as configurações do protocolo NTP na máquina virtual Linux
A data e hora em que a VM Ubuntu deve sincronizar-se com o domínio gerenciado. Adicione nome de host NTP do domínio gerenciado no arquivo /etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

No arquivo ntp.conf, digite o seguinte valor e salve o arquivo:

```console
server contoso100.com
```

Aqui, “contoso100.com” é o nome de domínio DNS do seu domínio gerenciado.

Sincronizar agora a data e hora da VM Ubuntu com o servidor NTP e, em seguida, inicie o serviço NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ingressar a máquina virtual do Linux no domínio gerenciado
Agora que os pacotes necessários são instalados na máquina virtual do Linux, a próxima tarefa é ingressar a máquina virtual no domínio gerenciado.

1. Descubra o domínio gerenciado dos Serviços de Domínio do AAD. No terminal SSH, digite o seguinte comando:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Solução de problemas:** Se *realm discover* não puder localizar o domínio gerenciado:
   >   * Verifique se o domínio pode ser acessado da máquina virtual (tente executar o ping).
   >   * Verifique se a máquina virtual, de fato, foi implantada na mesma rede virtual na qual o domínio gerenciado está disponível.
   >   * Verifique se você atualizou as configurações do servidor DNS para a rede virtual para apontar para os controladores de domínio do domínio gerenciado.

2. Inicialize o Kerberos. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > * Certifique-se de especificar um usuário que pertence ao grupo 'Administradores do DC do AAD’.
    > * Especifique o nome de domínio em letras maiúsculas, caso contrário, o kinit falhará.
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Ingresse a máquina no domínio. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > Use a mesma conta de usuário especificada na etapa anterior ('kinit').

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Você deverá receber uma mensagem ("Computador registrado com êxito no realm") quando a máquina for ingressada com êxito no domínio gerenciado.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Atualizar a configuração SSSD e reinicie o serviço
1. No terminal SSH, digite o seguinte comando. Abra o arquivo sssd.conf e faça a seguinte alteração
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Comente a linha **use_fully_qualified_names = True** e salve o arquivo.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Reinicie o serviço SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática do diretório base
Para habilitar a criação automática da pasta base depois de fazer logon de usuários, digite os seguintes comandos em seu terminal PuTTY:

```console
sudo vi /etc/pam.d/common-session
```

Adicione a seguinte linha ao arquivo abaixo da linha 'session optional pam_sss.so' e salve-o:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Verificar o ingresso no domínio
Verifique se o computador ingressou com êxito no domínio gerenciado. Conecte-se á VM Ubuntu ingressada no domínio usando uma conexão SSH diferente. Use uma conta de usuário de domínio e, em seguida, verifique se a conta de usuário é resolvida corretamente.

1. No seu terminal SSH, digite o seguinte comando para se conectar à máquina virtual Ubuntu ingressada no domínio usando SSH. Use uma conta de domínio que pertença ao domínio gerenciado (por exemplo, 'bob@CONTOSO100.COM' neste caso).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. No terminal do SSH, digite o seguinte comando para ver se o diretório base foi inicializado corretamente.
    
    ```console
    pwd
    ```

3. No seu terminal SSH, digite o seguinte comando para ver se as associações de grupo estão sendo resolvidas corretamente.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder os privilégios sudo de grupo 'Administradores de controlador de domínio do AAD'
Você pode conceder aos membros do grupo 'Administradores de controlador de domínio do AAD' privilégios administrativos na VM Ubuntu. O arquivo sudo está localizado em /etc/sudoers. Os membros dos grupos do AD adicionados em sudoers podem executar sudo.

1. Em seu terminal SSH, verifique se que você está conectado com privilégios de superusuário. Você pode usar a conta de administrador local especificada ao criar a VM. Execute o seguinte comando:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Adicione a seguinte entrada ao arquivo /etc/sudoers e salve-o:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Você agora pode fazer logon no como um membro do grupo 'Administradores do controlador de domínio do AAD' e deve ter privilégios administrativos na VM.


## <a name="troubleshooting-domain-join"></a>Solucionando problemas de ingresso no domínio
Consulte o artigo [Troubleshooting domain join](join-windows-vm.md#troubleshoot-joining-a-domain) (Solucionando problemas de ingresso no domínio).


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como fazer logon em uma máquina virtual executando o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

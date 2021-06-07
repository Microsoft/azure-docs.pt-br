---
title: Ingresse em uma VM RHEL para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e ingressar um Red Hat Enterprise Linux máquina virtual em um domínio Azure AD Domain Services gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 285a972936bfdf4b173e2a20223143883cd8b7d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619548"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ingressar uma máquina virtual Red Hat Enterprise Linux em um domínio Azure Active Directory Domain Services gerenciado

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM Red Hat Enterprise Linux (RHEL) a um domínio gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é parte do domínio gerenciado.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Criar e conectar-se a uma VM do RHEL Linux

Se você tiver uma VM do Linux RHEL existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM do RHEL Linux ou desejar criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

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
* *RHEL* é o nome do host da sua VM RHEL que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Quando terminar, salve e saia do arquivo de *hosts* usando o `:wq` comando do editor.

## <a name="install-required-packages"></a>Instalar os pacotes necessários

A VM precisa de alguns pacotes adicionais para unir a VM ao domínio gerenciado. Para instalar e configurar esses pacotes, atualize e instale as ferramentas de ingresso no domínio usando o `yum` . Há algumas diferenças entre o RHEL 7. x e o RHEL 6. x, portanto, use os comandos apropriados para sua versão do distribuição nas seções restantes deste artigo.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Ingressar VM no domínio gerenciado

Agora que os pacotes necessários estão instalados na VM, ingresse a VM no domínio gerenciado. Novamente, use as etapas apropriadas para sua versão distribuição do RHEL.

### <a name="rhel-7"></a>RHEL 7

1. Use o `realm discover` comando para descobrir o domínio gerenciado. O exemplo a seguir descobre o realm *AADDSCONTOSO.com*. Especifique seu próprio nome de domínio gerenciado em letras MAIÚSCULAs:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se o `realm discover` comando não conseguir localizar seu domínio gerenciado, examine as seguintes etapas de solução de problemas:

    * Verifique se o domínio está acessível da VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é retornada.
    * Verifique se a VM está implantada na mesma rede virtual, ou emparelhada, na qual o domínio gerenciado está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado.

1. Agora, inicialize o Kerberos usando o `kinit` comando. Especifique um usuário que faça parte do domínio gerenciado. Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Novamente, o nome de domínio gerenciado deve ser inserido em letras MAIÚSCULAs. No exemplo a seguir, a conta denominada `contosoadmin@aaddscontoso.com` é usada para inicializar o Kerberos. Insira sua própria conta de usuário que faça parte do domínio gerenciado:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, ingresse a VM no domínio gerenciado usando o `realm join` comando. Use a mesma conta de usuário que faz parte do domínio gerenciado que você especificou no comando anterior `kinit` , como `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Leva alguns minutos para unir a VM ao domínio gerenciado. A saída de exemplo a seguir mostra que a VM ingressou com êxito no domínio gerenciado:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Use o `adcli info` comando para descobrir o domínio gerenciado. O exemplo a seguir descobre o realm *ADDDSCONTOSO.com*. Especifique seu próprio nome de domínio gerenciado em letras MAIÚSCULAs:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Se o `adcli info` comando não conseguir localizar seu domínio gerenciado, examine as seguintes etapas de solução de problemas:

    * Verifique se o domínio está acessível da VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é retornada.
    * Verifique se a VM está implantada na mesma rede virtual, ou emparelhada, na qual o domínio gerenciado está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado.

1. Primeiro, ingresse no domínio usando o `adcli join` comando. esse comando também cria o keytab para autenticar o computador. Use uma conta de usuário que faça parte do domínio gerenciado.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Agora, configure o `/ect/krb5.conf` e crie os `/etc/sssd/sssd.conf` arquivos para usar o `aaddscontoso.com` domínio Active Directory.
   Certifique-se de que `AADDSCONTOSO.COM` o seja substituído pelo seu próprio nome de domínio:

    Abra o `/ect/krb5.conf` arquivo com um editor:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Atualize o `krb5.conf` arquivo para corresponder ao seguinte exemplo:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Crie o `/etc/sssd/sssd.conf` arquivo:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Atualize o `sssd.conf` arquivo para corresponder ao seguinte exemplo:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Verifique se `/etc/sssd/sssd.conf` as permissões são 600 e pertencem ao usuário raiz:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Use `authconfig` para instruir a VM sobre a integração do Linux com o AD:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Inicie e habilite o serviço SSSD:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Se sua VM não puder concluir com êxito o processo de ingresso no domínio, verifique se o grupo de segurança de rede da VM permite o tráfego de saída do Kerberos na porta TCP + UDP 464 para a sub-rede da rede virtual para o domínio gerenciado.

Agora, verifique se você pode consultar informações do AD do usuário usando `getent`

```console
sudo getent passwd contosoadmin
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

1. Para aplicar as alterações e permitir que os usuários entrem usando uma senha, reinicie o serviço SSH para a versão distribuição do RHEL:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder os privilégios sudo de grupo 'Administradores de controlador de domínio do AAD'

Para conceder aos membros do grupo de *Administradores do AAD DC* privilégios administrativos na VM RHEL, você adiciona uma entrada ao */etc/sudoers*. Depois de adicionados, os membros do grupo de *Administradores do AAD DC* podem usar o `sudo` comando na VM do RHEL.

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

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o `ssh -l` comando, como `contosoadmin@aaddscontoso.com` e, em seguida, insira o endereço da VM, como *RHEL.aaddscontoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Quando você se conectou com êxito à VM, verifique se o diretório base foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no diretório */Home* com seu próprio diretório que corresponda à conta de usuário.

1. Agora, verifique se as associações de grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver as associações de grupo do domínio gerenciado.

1. Se você entrou na VM como um membro do grupo de *Administradores do controlador de domínio do AAD* , verifique se você pode usar corretamente o `sudo` comando:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para conectar a VM ao domínio gerenciado ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

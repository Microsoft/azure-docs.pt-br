---
title: Ingressar uma VM CoreOS para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e ingressar uma máquina virtual CoreOS em um domínio gerenciado Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619582"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ingressar uma máquina virtual CoreOS em um domínio gerenciado Azure Active Directory Domain Services

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM CoreOS a um domínio gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é parte do domínio gerenciado.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Criar e conectar-se a uma VM do Linux CoreOS

Se você tiver uma VM do CoreOS Linux existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM do CoreOS Linux ou desejar criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

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
* *Coreos* é o nome do host da VM Coreos que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Quando terminar, salve e saia do arquivo de *hosts* usando o `:wq` comando do editor.

## <a name="configure-the-sssd-service"></a>Configurar o serviço SSSD

Atualize a configuração do */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Especifique seu próprio nome de domínio gerenciado para os seguintes parâmetros:

* *domínios* em todas as letras maiúsculas
* *[Domain/AADDSCONTOSO]* em que AADDSCONTOSO está em todas as letras maiúsculas
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* em todas as letras maiúsculas

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Ingressar a VM no domínio gerenciado

Com o arquivo de configuração SSSD atualizado, agora ingresse a máquina virtual no domínio gerenciado.

1. Primeiro, use o `adcli info` comando para verificar se você pode ver informações sobre o domínio gerenciado. O exemplo a seguir obtém informações para o domínio *AADDSCONTOSO.com*. Especifique seu próprio nome de domínio gerenciado em letras MAIÚSCULAs:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Se o `adcli info` comando não conseguir localizar seu domínio gerenciado, examine as seguintes etapas de solução de problemas:

    * Verifique se o domínio está acessível da VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é retornada.
    * Verifique se a VM está implantada na mesma rede virtual, ou emparelhada, na qual o domínio gerenciado está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado.

1. Agora, ingresse a VM no domínio gerenciado usando o `adcli join` comando. Especifique um usuário que faça parte do domínio gerenciado. Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Novamente, o nome de domínio gerenciado deve ser inserido em letras MAIÚSCULAs. No exemplo a seguir, a conta denominada `contosoadmin@aaddscontoso.com` é usada para inicializar o Kerberos. Insira sua própria conta de usuário que faça parte do domínio gerenciado.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    O `adcli join` comando não retorna nenhuma informação quando a VM ingressou com êxito no domínio gerenciado.

1. Para aplicar a configuração de ingresso no domínio, inicie o serviço SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Entrar na VM usando uma conta de domínio

Para verificar se a VM ingressou com êxito no domínio gerenciado, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório base foi criado e se a associação de grupo do domínio foi aplicada.

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o `ssh -l` comando, como `contosoadmin@aaddscontoso.com` e, em seguida, insira o endereço da VM, como *Coreos.aaddscontoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Agora, verifique se as associações de grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver as associações de grupo do domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para conectar a VM ao domínio gerenciado ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

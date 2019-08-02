---
title: 'Azure Active Directory Domain Services: Unir uma VM do CentOS a um domínio gerenciado | Microsoft Docs'
description: Ingressar uma máquina virtual CentOS Linux aos Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c4a04f55f4f69521f00ed450a2d3d1a80b56761c
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234087"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Ingressar uma máquina virtual do CentOS Linux em um domínio gerenciado
Este artigo mostra como adicionar uma máquina virtual CentOS Linux no Azure a um domínio gerenciado do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:
1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua as etapas necessárias para [sincronizar senhas para seu domínio gerenciado do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Provisionar uma máquina virtual CentOS Linux
Provisione uma máquina virtual CentOS no Azure usando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [PowerShell do Azure](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implante a máquina virtual na **mesma rede virtual em que você habilitou o Azure AD Domain Services**.
> * Escolha uma **sub-rede diferente** daquela em que você habilitou o Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual do Linux recém-provisionada
A máquina virtual CentOS foi provisionada no Azure. A próxima tarefa é conectar-se remotamente à máquina virtual usando a conta de administrador local criada durante o provisionamento da VM.

Siga as instruções no artigo [Como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o arquivo de hosts na máquina virtual Linux
Em seu terminal SSH, edite o arquivo /etc/hosts e atualize o endereço IP e o nome do host do computador.

```console
sudo vi /etc/hosts
```

No arquivo de hosts, digite o seguinte valor:

```console
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```

Aqui, “contoso100.com” é o nome de domínio DNS do seu domínio gerenciado. 'contoso-centos' é o nome do host da máquina virtual CentOS que você está adicionado ao domínio gerenciado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar os pacotes necessários na máquina virtual do Linux
Em seguida, instale os pacotes necessários para o ingresso no domínio na máquina virtual. Em seu terminal SSH, digite o seguinte comando para instalar os pacotes necessários:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ingressar a máquina virtual do Linux no domínio gerenciado
Agora que os pacotes necessários são instalados na máquina virtual do Linux, a próxima tarefa é ingressar a máquina virtual no domínio gerenciado.

1. Descubra o domínio gerenciado dos Serviços de Domínio do AAD. No terminal SSH, digite o seguinte comando:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Solução de problemas:** Se *realm discover* não puder localizar o domínio gerenciado:  
   >    * Verifique se o domínio pode ser acessado da máquina virtual (tente executar o ping).  
   >    * Verifique se a máquina virtual, de fato, foi implantada na mesma rede virtual na qual o domínio gerenciado está disponível.
   >    * Verifique se você atualizou as configurações do servidor DNS para a rede virtual para apontar para os controladores de domínio do domínio gerenciado.  

2. Inicialize o Kerberos. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > * Especifique um usuário que pertença ao grupo 'Administradores do DC do AAD’.
    > * Especifique o nome de domínio em letras maiúsculas, caso contrário, o kinit falhará.

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Ingresse a máquina no domínio. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > Use a mesma conta de usuário especificada na etapa anterior ('kinit').

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Você deverá receber uma mensagem ("Computador registrado com êxito no realm") quando a máquina for ingressada com êxito no domínio gerenciado.


## <a name="verify-domain-join"></a>Verificar o ingresso no domínio
Verifique se o computador ingressou com êxito no domínio gerenciado. Conecte-se à VM CentOS ingressada no domínio usando uma conexão SSH diferente. Use uma conta de usuário de domínio e, em seguida, verifique se a conta de usuário é resolvida corretamente.

1. No seu terminal SSH, digite o seguinte comando para se conectar à máquina virtual CentOS ingressada no domínio usando SSH. Use uma conta de domínio que pertença ao domínio gerenciado (por exemplo, 'bob@CONTOSO100.COM' neste caso).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. No terminal do SSH, digite o seguinte comando para ver se o diretório base foi inicializado corretamente.
   
    ```console
    pwd
    ```

3. No seu terminal SSH, digite o seguinte comando para ver se as associações de grupo estão sendo resolvidas corretamente.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Solucionando problemas de ingresso no domínio
Consulte o artigo [Troubleshooting domain join](join-windows-vm.md#troubleshoot-joining-a-domain) (Solucionando problemas de ingresso no domínio).

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como fazer logon em uma máquina virtual executando o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Instalando o Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7: Guia de integração do Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

---
title: Configurar o ADDS LDAP com grupos estendidos para acesso ao volume NFS do Azure NetApp Files | Microsoft Docs
description: Descreve as considerações e as etapas para habilitar o LDAP com grupos estendidos quando você cria um volume NFS com o Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498844"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Configurar o ADDS LDAP com grupos estendidos para acesso ao volume NFS

Ao [criar um volume NFS](azure-netapp-files-create-volumes.md), você tem a opção de habilitar o recurso LDAP com grupos estendidos (a opção **LDAP**) para o volume. O recurso permite que usuários de LDAP do Active Directory e grupos estendidos (até 1.024 grupos) acessem o volume.  

Este artigo explica as considerações e as etapas para habilitar o LDAP com grupos estendidos quando você cria um volume de NFS.  

## <a name="considerations"></a>Considerações

* *Não* habilite o LDAP sobre TLS quando você estiver usando o AADDS (Azure Active Directory Domain Services).  

* Se você habilitar o recurso LDAP com grupos estendidos, os [volumes Kerberos](configure-kerberos-encryption.md) com LDAP habilitado não exibirão corretamente os usuários de LDAP como proprietários de arquivos. Um arquivo ou diretório criado por um usuário de LDAP usará `root` como o proprietário em vez do usuário real. No entanto, a conta `root` pode alterar manualmente a propriedade do arquivo com o comando `chown <username> <filename>`. 

* Não é possível modificar a configuração da opção LDAP (habilitada ou desabilitada) depois de criar o volume.  

* Veja na tabela a seguir as configurações de TTL (vida útil) do cache de LDAP. Você deve aguardar a atualização do cache para acessar um arquivo ou diretório por meio de um cliente. Caso contrário, uma mensagem de acesso negado aparecerá no cliente. 

    |     Condição de erro    |     Resolução    |
    |-|-|
    | Cache |  Tempo limite padrão |
    | Lista de associação de grupo  | TTL de 24 horas  |
    | Grupos do UNIX  | TTL de 24 horas, TTL negativo de 1 minuto  |
    | Usuários do UNIX  | TTL de 24 horas, TTL negativo de 1 minuto  |

    Os caches têm um período de tempo limite específico chamado *vida útil*. Após o período de tempo limite, as entradas envelhecem para que as entradas obsoletas não permaneçam. O valor de *TTL negativo* se aplica a pesquisas com falha, a fim de evitar que consultas de LDAP para objetos inexistentes causem problemas no desempenho.        

## <a name="steps"></a>Etapas

1. O recurso LDAP com grupos estendidos está atualmente na versão prévia. Você precisa registrar o recurso para usá-lo pela primeira vez:  

    1. Registrar o recurso:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Verifique o status do registro do recurso: 

        > [!NOTE]
        > O **RegistrationState** pode ficar no estado `Registering` por até 60 minutos antes da alteração para `Registered`. Aguarde até que o status seja `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Você também pode usar os [comandos da CLI do Azure](/cli/azure/feature) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 

2. Os volumes LDAP exigem uma configuração de Active Directory para as configurações do servidor LDAP. Siga as instruções em [Requisitos para conexões do Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections) e [Criar uma conexão do Active Directory](create-active-directory-connections.md#create-an-active-directory-connection) para configurar conexões do Active Directory no portal do Azure.  

3. Verifique se o servidor LDAP Active Directory está em execução no Active Directory. Para isso, instale e configure a função [Active Directory Lightweight Directory Services (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) no computador do AD.

4. Os usuários do LDAP NFS precisam ter determinados atributos POSIX no servidor LDAP. Siga [Gerenciar atributos de POSIX do LDAP](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) para definir os atributos necessários.  

5. Se você quiser configurar um cliente Linux integrado ao LDAP, consulte [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md).

6.  Siga as etapas em [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md) para criar um volume NFS. Durante a criação do volume, na guia **Protocolo**, habilite a opção **LDAP**.   

    ![Captura de tela que mostra a página Criar um Volume com a opção LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Opcional – Você pode permitir que os usuários do cliente NFS local que não estão presentes no servidor LDAP do Windows acessem um volume NFS com o LDAP com grupos estendidos habilitados. Para fazer isso, habilite a opção **Permitir usuários NFS locais com LDAP** da seguinte maneira:
    1. Clique em **Conexões do Active Directory**.  Em uma conexão do Active Directory atual, clique no menu de contexto (os três pontos `…`) e selecione **Editar**.  
    2. Na janela **Editar configurações do Active Directory** que aparece, selecione a opção **Permitir usuários NFS locais com LDAP**.  

    ![Captura de tela que mostra a opção Permitir usuários NFS locais com LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Próximas etapas  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar e gerenciar conexões do Active Directory](create-active-directory-connections.md)
* [Solucionar problemas de volumes LDAP](troubleshoot-ldap-volumes.md)

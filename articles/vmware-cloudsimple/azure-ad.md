---
title: Solução do Azure VMware por CloudSimple-use o Azure AD como fonte de identidade na nuvem privada
description: Descreve como adicionar o Azure AD como um provedor de identidade em sua nuvem privada do CloudSimple para autenticar usuários que acessam o CloudSimple do Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898599"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Usar o Azure AD como um provedor de identidade para o vCenter na nuvem privada do CloudSimple

Você pode configurar seu vCenter de nuvem privada do CloudSimple para autenticar com o Azure Active Directory (Azure AD) para que seus administradores do VMware acessem o vCenter. Depois que a fonte de identidade de logon único é configurada, o usuário **cloudowner** pode adicionar usuários da origem de identidade ao vCenter.  

Você pode configurar seu Active Directory controladores de domínio e domínio de qualquer uma das seguintes maneiras:

* Active Directory controladores de domínio e domínio em execução no local
* Active Directory controladores de domínio e domínio em execução no Azure como máquinas virtuais em sua assinatura do Azure
* Novo Active Directory controladores de domínio e domínio em execução em sua nuvem privada do CloudSimple
* Serviço de Azure Active Directory

Este guia explica as tarefas necessárias para configurar o Azure AD como uma fonte de identidade.  Para obter informações sobre como usar Active Directory locais ou Active Directory em execução no Azure, consulte [configurar fontes de identidade do vCenter para usar Active Directory](set-vcenter-identity.md) para obter instruções detalhadas sobre como configurar a origem da identidade.

## <a name="about-azure-ad"></a>Sobre o Azure AD

O Azure AD é o serviço de gerenciamento de identidade e diretório baseado em nuvem multilocatário da Microsoft.  O Azure AD fornece um mecanismo de autenticação escalonável, consistente e confiável para os usuários autenticarem e acessarem diferentes serviços no Azure.  Ele também fornece serviços LDAP seguros para qualquer serviço de terceiros usar o Azure AD como uma origem de autenticação/identidade.  O Azure AD combina os principais serviços de diretório, governança avançada de identidade e gerenciamento de acesso a aplicativos, que pode ser usado para conceder acesso à sua nuvem privada para usuários que administram a nuvem privada.

Para usar o Azure AD como uma fonte de identidade com o vCenter, você deve configurar o Azure AD e os serviços de domínio do Azure AD. Siga estas instruções:

1. [Como configurar o Azure AD e os Azure AD Domain Services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Como configurar uma fonte de identidade em seu vCenter de nuvem privada](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Configurar o Azure AD e os Azure AD Domain Services

Antes de começar, você precisará de acesso à sua assinatura do Azure com privilégios de administrador global.  As etapas a seguir fornecem diretrizes gerais. Os detalhes estão contidos na documentação do Azure.

### <a name="azure-ad"></a>AD do Azure

> [!NOTE]
> Se você já tiver o Azure AD, poderá ignorar esta seção.

1. Configure o Azure AD em sua assinatura, conforme descrito na  [documentação do Azure ad](../active-directory/fundamentals/active-directory-whatis.md).
2. Habilite Azure Active Directory Premium em sua assinatura, conforme descrito em [inscrever-se para Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Configure um nome de domínio personalizado e verifique o nome de domínio personalizado conforme descrito em [Adicionar um nome de domínio personalizado para Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Configure um registro DNS no seu registrador de domínio com as informações fornecidas no Azure.
    2. Defina o nome de domínio personalizado como o domínio primário.

Opcionalmente, você pode configurar outros recursos do Azure AD.  Eles não são necessários para habilitar a autenticação do vCenter com o Azure AD.

### <a name="azure-ad-domain-services"></a>Serviços de domínio do Azure AD

> [!NOTE]
> Esta é uma etapa importante para habilitar o Azure AD como uma fonte de identidade para o vCenter.  Para evitar problemas, certifique-se de que todas as etapas sejam executadas corretamente.

1. Habilite os serviços de domínio do Azure AD conforme descrito em [habilitar Azure Active Directory serviços de domínio usando o portal do Azure](../active-directory-domain-services/tutorial-create-instance.md).
2. Configure a rede que será usada pelos serviços de domínio do Azure AD, conforme descrito em [habilitar Azure Active Directory Domain Services usando o portal do Azure](../active-directory-domain-services/tutorial-create-instance.md).
3. Configure o grupo de administradores para gerenciar Azure AD Domain Services conforme descrito em [habilitar Azure Active Directory Domain Services usando o portal do Azure](../active-directory-domain-services/tutorial-create-instance.md).
4. Atualize as configurações de DNS para seu Azure AD Domain Services conforme descrito em [habilitar Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Se você quiser se conectar ao AD pela Internet, configure o registro DNS para o endereço IP público dos serviços de domínio do Azure AD para o nome de domínio.
5. Habilitar a sincronização de hash de senha para usuários.  Esta etapa habilita a sincronização de hashes de senha necessários para a autenticação Kerberos e NTLM (NT LAN Manager) para Azure AD Domain Services. Depois que a sincronização de hashes de senha é configurada, os usuários podem entrar no domínio gerenciado com suas credenciais corporativas. Consulte [habilitar a sincronização de hash de senha para Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Se os usuários somente na nuvem estiverem presentes, eles deverão alterar sua senha usando o <a href="https://myapps.microsoft.com/" target="_blank">painel de acesso do AD do Azure</a> para garantir que os hashes de senha sejam armazenados no formato exigido pelo NTLM ou pelo Kerberos.  Siga as instruções em [habilitar a sincronização de hash de senha para seu domínio gerenciado para contas de usuário somente em nuvem](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Esta etapa deve ser feita para usuários individuais e qualquer novo usuário criado no diretório do AD do Azure usando os cmdlets do PowerShell do portal do Azure ou do Azure AD. Os usuários que precisam de acesso aos serviços de domínio do Azure AD devem usar o <a href="https://myapps.microsoft.com/" target="_blank">painel de acesso do Azure ad</a> e acessar seu perfil para alterar a senha.

        > [!NOTE]
        > Se a sua organização tiver contas de usuário somente na nuvem, todos os usuários que precisarem usar o Azure Active Directory Domain Services deverão alterar suas senhas. Uma conta de usuário somente na nuvem é uma conta que foi criada no diretório do Azure AD usando o portal do Azure ou os cmdlets do Azure AD PowerShell. Essas contas de usuário não são sincronizadas de um diretório local.

    2. Se você estiver sincronizando senhas do seu Active Directory local, siga as etapas na [documentação do Active Directory](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Configure o LDAP seguro em seu Azure Active Directory Domain Services conforme descrito em [Configurar LDAP seguro (LDAPS) para um domínio gerenciado Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Carregue um certificado para uso pelo LDAP seguro, conforme descrito no tópico do Azure [obter um certificado para LDAP seguro](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  O CloudSimple recomenda o uso de um certificado assinado emitido por uma autoridade de certificação para garantir que o vCenter possa confiar no certificado.
    2. Habilite o LDAP seguro conforme descrito [habilitar LDAP seguro (LDAPS) para um domínio gerenciado Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Salve a parte pública do certificado (sem a chave privada) no formato. cer para uso com o vCenter durante a configuração da origem da identidade.
    4. Se o acesso à Internet para os serviços de domínio do Azure AD for necessário, habilite a opção ' permitir acesso seguro ao LDAP pela Internet '.
    5. Adicione a regra de segurança de entrada para os serviços de domínio do Azure AD NSG para a porta TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Configurar uma fonte de identidade em seu vCenter de nuvem privada

1. [Escalonar privilégios](escalate-private-cloud-privileges.md) para seu vCenter de nuvem privada.
2. Colete os parâmetros de configuração necessários para configurar a origem da identidade.

    | **Opção** | **Descrição** |
    |------------|-----------------|
    | **Nome** | Nome da origem da identidade. |
    | **DN de base para usuários** | Nome distinto base para os usuários.  Para o Azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça um endereço IP nesta caixa de texto. |
    | **Alias de domínio** | *(opcional)* O nome NetBIOS do domínio. Adicione o nome NetBIOS do domínio Active Directory como um alias da origem de identidade se você estiver usando autenticações SSPI. |
    | **DN de base para grupos** | O nome distinto base para os grupos. Para o Azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL do servidor primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Use o formato `ldaps://hostname:port`. A porta geralmente é 636 para conexões LDAPs. <br><br>Um certificado que estabelece confiança para o ponto de extremidade LDAPs do servidor Active Directory é necessário quando você usa `ldaps://` o na URL LDAP primária ou secundária. |
    | **URL do servidor secundário** | Endereço de um servidor LDAP do controlador de domínio secundário que é usado para failover. |
    | **Escolher certificado** | Se você quiser usar LDAPs com o servidor LDAP Active Directory ou a origem de identidade do servidor OpenLDAP, um botão escolher certificado será exibido depois que você digitar `ldaps://` na caixa de texto URL. Uma URL secundária não é necessária. |
    | **Nome de usuário** | ID de um usuário no domínio que tem um mínimo de acesso somente leitura ao DN base para usuários e grupos. |
    | **Senha** | Senha do usuário que é especificada pelo nome de usuário. |

3. Entre em sua nuvem privada vCenter após os privilégios serem escalados.
4. Siga as instruções em [Adicionar uma fonte de identidade no vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) usando os valores da etapa anterior para configurar Azure Active Directory como uma origem de identidade.
5. Adicione usuários/grupos do Azure AD aos grupos do vCenter, conforme descrito no tópico VMware [adicionar membros a um grupo único de Sign-On do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente contas de serviço devem ser adicionadas ao grupo *Administradores* .

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o modelo de permissão de nuvem privada](learn-private-cloud-permissions.md)

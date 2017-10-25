---
title: "Arquitetura do Azure HDInsight ingressado no domínio | Microsoft Docs"
description: "Aprenda a planejar o HDInsight associado ao domínio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 08d61f95fb38962402142c0ccd0a5a2f3a7e802a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planejar clusters Hadoop do Azure associados ao domínio no HDInsight

O Hadoop tradicional é um cluster de usuário único. Ele é adequado para a maioria das empresas que têm equipes de aplicativos menores que criam grandes cargas de trabalho de dados. À medida que o Hadoop ganha popularidade, muitas empresas estão mudando para um modelo em que clusters são gerenciados por equipes de TI, e várias equipes de aplicativo compartilham clusters. Assim, as funcionalidades que envolvem clusters multiusuários estão entre as funcionalidades mais solicitadas no Azure HDInsight.

Em vez de criar sua própria autorização e autenticação multiusuário, o HDInsight recorre ao provedor de identidade mais popular – o AD (Active Directory). A funcionalidade de segurança eficiente no AD pode ser usada para gerenciar a autorização multiusuário no HDInsight. Integrando HDInsight com o AD, você pode se comunicar com os clusters, usando suas credenciais do AD. O HDInsight mapeia o usuário do AD para um usuário do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark Thrift e outros) funcionem sem problemas para o usuário autenticado.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight ao Active Directory

Quando você integra o HDInsight ao Active Directory, os nós do cluster HDInsight são ingressados em um domínio do AD. O HDInsight cria entidades de serviço para os serviços do Hadoop em execução no cluster e coloca-as em uma UO (unidade organizacional) especificada no domínio. O HDInsight também cria mapeamentos de DNS reversos no domínio para os endereços IP de nós que ingressaram no domínio.

Há duas opções de implantação para o Active Directory:
* **[Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md):** esse serviço fornece um domínio do Active Directory gerenciado que é totalmente compatível com o Active Directory do Windows Server. A Microsoft cuida do gerenciamento, da aplicação de patch e do monitoramento do domínio do AD. Você pode implantar o cluster sem se preocupar em manter os controladores de domínio. Usuários, grupos e senhas são sincronizados do Azure Active Directory, permitindo que os usuários entrem no cluster usando as respectivas credenciais corporativas.

* **Domínio do Active Directory do Windows Server em VMs de IaaS do Azure:** nessa opção, você implanta e gerencia seu próprio domínio do Active Directory do Windows Server em VMs de IaaS do Azure. 

Você pode obter essa configuração, usando várias arquiteturas. É possível escolher uma das arquiteturas a seguir.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>HDInsight integrado com um domínio do AD gerenciado do Azure AD Domain Services
Você pode implantar um domínio gerenciado do [Azure AD DS](../active-directory-domain-services/active-directory-ds-overview.md) (Azure Active Directory Domain Services). O Azure AD DS fornece um domínio AD gerenciado no Azure, que é gerenciado, atualizado e monitorado pela Microsoft. Ele cria dois controladores de domínio para alta disponibilidade e inclui serviços DNS. Você pode então integrar o cluster HDInsight com esse domínio gerenciado. Com essa opção de implantação, você não precisa se preocupar com o gerenciamento, a aplicação de patches, a atualização e o monitoramento de controladores de domínio.

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para a integração com o Azure AD Domain Services:

* [Provisionar um domínio gerenciado do Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-getting-started.md).
* Crie uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dentro da qual você colocará as VMs do cluster HDInsight e as entidades de serviço usadas pelo cluster.
* Configure o [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) quando você configurar o Azure AD DS. O certificado usado para configurar o LDAPS deve ser emitido por uma autoridade de certificação pública (não um certificado autoassinado).
* Crie zonas DNS reversas no domínio gerenciado para o intervalo de endereços IP da sub-rede HDInsight (por exemplo 10.2.0.0/24 na figura anterior).
* Configure a [sincronização de hashes de senha necessária para a autenticação Kerberos e NTLM](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) do Azure AD para o domínio gerenciado do Azure AD DS.
* Uma conta de serviço ou uma conta de usuário é necessária. Use essa conta para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores no domínio do Azure AD


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight integrado ao AD do Windows Server em execução no Azure IaaS

Você pode implantar a função do Active Directory Domain Services do Windows Server em uma (ou várias) VMs (máquinas virtuais) no Azure e promovê-las a controladores de domínio. Essas VMs do controlador de domínio podem ser implantadas usando o modelo de implantação do Resource Manager na mesma rede virtual que o cluster HDInsight. Se os controladores de domínio forem implantados em uma rede virtual diferente, você precisará emparelhar essas redes virtuais usando o [emparelhamento VNet para VNet](../virtual-network/virtual-network-create-peering.md). 

[Mais informações – implantando o Active Directory do Windows Server em VMs do Azure](../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nessa arquitetura, você não pode usar o Azure Data Lake Store com o cluster HDInsight.


Pré-requisitos para a integração com o Active Directory do Windows Server em VMs do Azure:

* Deve-se criar uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você coloca as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
* [Protocolos de acesso de diretório leve](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) devem ser configurados para se comunicar com o AD. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* As zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDInsight (por exemplo 10.2.0.0/24 na figura anterior).
* Uma conta de serviço ou uma conta de usuário é necessária. Use essa conta para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores no domínio do Active Directory


## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight ingressado em um domínio, confira [Configurar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-configure.md).
* Para gerenciar clusters HDInsight ingressados em um domínio, confira [Gerenciar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight ingressados no domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

---
title: Visão geral dos Azure Active Directory Domain Services | Microsoft Docs
description: Nesta visão geral, saiba o que Azure Active Directory Domain Services oferece e como usá-lo em sua organização para fornecer serviços de identidade a aplicativos e serviços na nuvem.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660986"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é o Azure Active Directory Domain Services?

O AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, política de grupo, protocolo LDAP e autenticação Kerberos/NTLM. Você pode usar esses serviços de domínio sem a necessidade de implantar, gerenciar e aplicar um patch em DCs (controladores de domínio) na nuvem.

Um domínio gerenciado do Azure AD DS permite que você execute aplicativos herdados na nuvem que não podem usar métodos de autenticação modernos ou nos quais você não deseja que as pesquisas de diretório sempre voltem para um ambiente de AD DS local. Você pode realizar lift-and-shift desses aplicativos herdados do seu ambiente local para um domínio gerenciado, sem a necessidade de gerenciar o ambiente de AD DS na nuvem.

O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem em serviços e aplicativos conectados ao domínio gerenciado usando as respectivas credenciais existentes. Você também pode usar grupos e contas de usuário para proteger o acesso aos recursos. Esses recursos fornecem um lift-and-shift mais suave de recursos locais para o Azure.

> [!div class="nextstepaction"]
> [Para começar, crie um domínio gerenciado do Azure AD DS usando o portal do Azure][tutorial-create]

Assista ao nosso vídeo curto para saber mais sobre o Azure AD DS.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Ao criar um domínio gerenciado do Azure AD DS, você define um namespace exclusivo. Esse namespace é o nome de domínio, como *aaddscontoso.com*. Dois DCs (controladores de domínio) do Windows Server são então implantados na sua região do Azure selecionada. Essa implantação de DCs é conhecida como conjunto de réplicas.

Você não precisa gerenciar, configurar nem atualizar esses DCs. A plataforma do Azure manipula os DCs como parte do domínio gerenciado, incluindo backups e a criptografia em repouso usando o Azure Disk Encryption.

Um domínio gerenciado é configurado para realizar uma sincronização unidirecional no Azure AD para dar acesso a um conjunto central de usuários, grupos e credenciais. É possível criar recursos diretamente no domínio gerenciado, mas eles não são sincronizados com o Azure AD. Depois, os aplicativos, os serviços e as VMs no Azure que se conectam ao domínio gerenciado podem usar recursos comuns do AD DS, como ingresso no domínio, política de grupo, LDAP e autenticação Kerberos/NTLM.

Em um ambiente híbrido com um ambiente local do AD DS, o [Azure AD Connect][azure-ad-connect] sincroniza as informações de identidade com o Azure AD, que, por sua vez, é sincronizado com o domínio gerenciado.

![Sincronização no Azure AD Domain Services com o Azure AD e o AD DS local usando o AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

O Azure AD DS replica informações de identidade do Azure AD, portanto ele funciona com locatários do Azure AD que estão somente na nuvem ou sincronizados com um ambiente local do AD DS. O mesmo conjunto de recursos do Azure AD DS existe em ambos os ambientes.

* Se você tiver um ambiente local do AD DS, poderá sincronizar as informações da conta de usuário para fornecer uma identidade consistente aos usuários. Para saber mais, confira [Como os objetos e as credenciais são sincronizados em um domínio gerenciado][synchronization].
* Para ambientes somente na nuvem, não é necessário um ambiente local tradicional do AD DS para usar os serviços de identidade centralizados do Azure AD DS.

Você pode expandir um domínio gerenciado para ter mais de um conjunto de réplicas por locatário do Azure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual emparelhada em qualquer região do Azure com suporte para o Azure AD DS. Conjuntos de réplicas adicionais em diferentes regiões do Azure fornecem recuperação de desastre geográfica para aplicativos herdados se uma região do Azure fica offline. Atualmente, os conjuntos de réplicas estão em versão prévia. Para obter mais informações, confira [Conceitos e recursos dos conjuntos de réplicas para domínios gerenciados][concepts-replica-sets].

O vídeo a seguir mostra uma visão geral de como o Azure AD DS se integra com aplicativos e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Para ver os cenários de implantação do Azure AD DS em ação, você pode explorar os seguintes exemplos:

* [Azure AD DS para organizações híbridas](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS para organizações somente na nuvem](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Características e benefícios do Azure AD DS

Para fornecer serviços de identidade a aplicativos e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente tradicional do AD DS para operações como ingresso em domínio, LDAPS (LDAP Seguro), Política de Grupo, gerenciamento de DNS e associação LDAP e suporte de leitura. O suporte para a gravação de LDAP está disponível para objetos criados no domínio gerenciado, mas não para os recursos sincronizados do Azure AD.

Para saber mais sobre as suas opções de identidade, [compare o Azure AD DS com o Azure AD, o AD DS em VMs do Azure e o AD DS local][compare].

Os recursos a seguir do Azure AD DS simplificam as operações de implantação e o gerenciamento:

* **Experiência de implantação simplificada:** O Azure AD DS é habilitado para o locatário do Azure AD usando um assistente único no portal do Azure.
* **Integrado ao Azure AD:** Contas de usuário, associações de grupo e credenciais estão automaticamente disponíveis no locatário do Azure AD. Novos usuários, grupos ou alterações em atributos no locatário do Azure AD ou no ambiente local do AD DS são automaticamente sincronizados com Azure AD DS.
    * Contas em diretórios externos vinculadas ao Azure AD não estão disponíveis no Azure AD DS. As credenciais não estão disponíveis para esses diretórios externos, portanto, não podem ser sincronizadas em um domínio gerenciado.
* **Use suas senhas/credenciais corporativas:** as senhas de usuários no Azure AD DS são iguais às de seu locatário do Azure AD DS. Os usuários podem usar as credenciais corporativas para ingressar em computadores no domínio, entrarem interativamente ou pela área de trabalho remota e autenticar em relação ao domínio gerenciado.
* **Autenticação Kerberos e NTLM:** Com a compatibilidade com a autenticação NTLM e Kerberos, é possível implantar aplicativos que usam a autenticação integrada do Windows.
* **Alta disponibilidade:** O Azure AD DS tem vários controladores de domínio, que oferecem alta disponibilidade para seu domínio gerenciado. Essa alta disponibilidade garante o tempo de atividade e a resiliência a falhas.
    * Em regiões que dão suporte a [Zonas de Disponibilidade do Azure][availability-zones], esses controladores de domínio também são distribuídos entre zonas para resiliência adicional.
    * [Os conjuntos de réplicas][concepts-replica-sets] também poderão ser usados para fornecer recuperação de desastre geográfico para aplicativos herdados se uma região do Azure ficar offline.

Alguns aspectos importantes de um domínio gerenciado incluem os seguintes:

* O domínio gerenciado é um domínio autônomo. Não é uma extensão de um domínio local.
    * Se necessário, você poderá criar relações de confiança de floresta de saída unidirecionais do Azure AD DS para um ambiente do AD DS local. Para obter mais informações, confira [Conceitos e recursos da floresta de recursos do Azure AD DS][ forest-trusts].
* A equipe de TI não precisa gerenciar, aplicar patch ou monitorar os controladores de domínio para o domínio gerenciado.

Para ambientes híbridos que executam o AD DS local, não é necessário gerenciar a replicação do AD para o domínio gerenciado. Contas de usuário, associações de grupo e credenciais do diretório local são sincronizados com o Azure AD por meio do [Azure AD Connect][azure-ad-connect]. Essas contas de usuário, associações de grupo e credenciais estão automaticamente disponíveis dentro do domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure AD DS se compara a outras soluções de identidade e ver como funciona a sincronização, confira os seguintes artigos:

* [Comparação entre o Azure AD DS e o Azure AD, Active Directory Domain Services nas VMs do Azure e Active Directory Domain Services local][compare]
* [Saiba como o Azure AD Domain Services sincroniza com o diretório do Azure AD][synchronization]
* Para aprender a administrar um domínio gerenciado, confira [conceitos de gerenciamento para contas de usuário, senhas e administração no Azure AD DS][administration-concepts].

Para começar, [crie um domínio gerenciado usando o portal do Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md

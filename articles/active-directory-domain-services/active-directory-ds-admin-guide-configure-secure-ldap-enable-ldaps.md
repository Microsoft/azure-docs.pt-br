---
title: Habilitar o LDAP Seguro (LDAPS) no Azure Active Directory Domain Services|Microsoft Docs
description: Habilitar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 631689d28399c07330c7f57b08952961973d1a92
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474319"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Habilitar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de começar
Completar a [Tarefa 2 – exportar o certificado LDAP seguro para um arquivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tarefa 3: Habilitar o LDAP seguro para o domínio gerenciado usando o portal do Azure
Execute as seguintes etapas de configuração para habilitar o LDAP seguro:

1. Navegue até o **[Portal do Azure](https://portal.azure.com)** .

2. Pesquise por “domain services” na caixa de pesquisa **Pesquisar recursos**. Selecione **Azure AD Domain Services** nos resultados da pesquisa. A página **Azure AD Domain Services** lista seu domínio gerenciado.

    ![Localize o domínio gerenciado que está sendo provisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerenciado (por exemplo, "contoso100.com") para ver mais detalhes sobre ele.

    ![Domain Services – estado de provisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. Clique em **LDAP Seguro** no painel de navegação.

    ![Domain Services – página LDAP Seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Por padrão, o acesso LDAP seguro ao seu domínio gerenciado fica desabilitado. Posicione a tecla de alternância **LDAP Seguro** em **Habilitar**.

    ![Habilitar o LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Por padrão, o acesso LDAP seguro ao seu domínio gerenciado pela Internet fica desabilitado. Posicione a tecla de alternância **Permitir acesso LDAP seguro na Internet** em **Habilitar**, se você precisar.

    > [!WARNING]
    > Quando você habilita o acesso LDAP seguro por meio da Internet, seu domínio fica suscetível a ataques de força bruta de senha por meio da Internet. Portanto, é recomendável configurar um NSG para bloquear o acesso aos intervalos de endereços IP de origem necessários. Consulte as instruções para [bloquear o acesso a LDAPS para seu domínio gerenciado pela Internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Clique no ícone de pasta após **Arquivo .PFX com certificado LDAP seguro**. Especifique o caminho para o arquivo PFX com o certificado para acesso LDAP seguro ao domínio gerenciado.

7. Especifique a **Senha para descriptografar o arquivo PFX**. Insira a mesma senha que você usou ao exportar o certificado para o arquivo PFX.

8. Quando tiver terminado, clique no botão **Salvar**.

9. Você verá uma notificação informando que o LDAP seguro está sendo configurado para o domínio gerenciado. Até que essa operação seja concluída, você não poderá modificar outras configurações do domínio.

    ![Configurando LDAP seguro para o domínio gerenciado](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Demora cerca de 10 a 15 minutos para habilitar o LDAP seguro para seu domínio gerenciado. Se o certificado LDAP seguro fornecido não corresponder aos critérios necessários, LDAP seguro não estará habilitado para o diretório e você verá uma falha. Por exemplo, o nome de domínio está incorreto, o certificado expirou ou expirará em breve. Nesse caso, tente novamente com um certificado válido.
>
>

## <a name="next-step"></a>Próxima etapa
[Tarefa 4: configurar o DNS para acessar o domínio gerenciado pela Internet](active-directory-ds-ldaps-configure-dns.md)

---
title: "Configurar o LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure AD |Microsoft Docs"
description: "Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 245ad4948cf4b8c2d44a0dafb61923b0b4267856
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de começar
Não deixe de concluir a [Tarefa 2 – exportar o certificado de LDAP seguro para um arquivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tarefa 3 – habilitar o LDAP seguro para o domínio gerenciado usando o portal do Azure
Execute as seguintes etapas de configuração para habilitar o LDAP seguro:

1. Navegue até o **[Portal do Azure](https://portal.azure.com)**.

2. Pesquise por “domain services” na caixa de pesquisa **Pesquisar recursos**. Selecione **Azure AD Domain Services** nos resultados da pesquisa. A página **Azure AD Domain Services** lista seu domínio gerenciado.

    ![Localize o domínio gerenciado que está sendo provisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerenciado (por exemplo, "contoso100.com") para ver mais detalhes sobre ele.

    ![Domain Services – estado de provisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. Clique em **LDAP Seguro** no painel de navegação.

    ![Domain Services – página LDAP Seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Por padrão, o acesso LDAP seguro ao seu domínio gerenciado fica desabilitado. Posicione a tecla de alternância **LDAP Seguro** em **Habilitar**.

    ![Habilitar o LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Por padrão, o acesso LDAP seguro ao seu domínio gerenciado pela Internet fica desabilitado. Posicione a tecla de alternância **Permitir acesso LDAP seguro na Internet** em **Habilitar**, se desejar. 

    > [!TIP]
    > Se você habilitar o acesso LDAP seguro pela Internet, é recomendável configurar um NSG para bloquear o acesso aos intervalos de endereços IP de origem necessários. Consulte as instruções para [bloquear o acesso a LDAPS para seu domínio gerenciado pela Internet](#task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet).
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

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 4 – configurar o DNS para acessar o domínio gerenciado pela Internet
> [!NOTE]
> **Tarefa opcional** – Ignore esta tarefa de configuração se você não pretende acessar o domínio gerenciado usando LDAPS pela Internet.
>
>

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Depois que você habilitar o acesso LDAP seguro pela Internet para seu domínio gerenciado, será necessário atualizar o DNS para que os computadores cliente possam localizar esse domínio gerenciado. No final da tarefa 3, um endereço IP externo é exibido na guia **Propriedades** em **ENDEREÇO IP EXTERNO PARA ACESSO a LDAPS**.

Configure seu provedor DNS externo para que o nome DNS do domínio gerenciado (por exemplo, 'ldaps.contoso100.com') aponte para esse endereço IP externo. Em nosso exemplo, precisaremos criar a entrada DNS a seguir:

    ldaps.contoso100.com  -> 52.165.38.113

Isso é tudo - agora você está pronto para se conectar ao domínio gerenciado usando LDAP seguro pela Internet.

> [!WARNING]
> Lembre-se de que os computadores cliente devem confiar no emissor do certificado LDAPS para poderem se conectar com êxito ao domínio gerenciado usando LDAPS. Se estiver usando uma autoridade de certificação confiável publicamente, você não precisará fazer nada, pois os computadores cliente confiarão nesses emissores de certificado. Se estiver usando um certificado autoassinado, instale a parte pública do certificado autoassinado no repositório de certificados confiáveis no computador cliente.
>
>


## <a name="task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Tarefa 5 – bloquear o acesso LDAPS ao domínio gerenciado pela Internet
> [!NOTE]
> **Tarefa opcional** – ignore esta tarefa de configuração se você não tiver habilitado o acesso LDAPS ao domínio gerenciado pela Internet.
>
>

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Expor seu domínio gerenciado para acesso LDAPS pela Internet representa uma ameaça à segurança. O domínio gerenciado é acessível pela Internet na porta usada para LDAP seguro (ou seja, a porta 636). Portanto, você pode optar por restringir o acesso ao domínio gerenciado a endereços IP conhecidos específicos. Para segurança aprimorada, crie um NSG (grupo de segurança de rede) e associe-o à sub-rede na qual você habilitou o Azure AD Domain Services.

A tabela a seguir ilustra um exemplo de NSG que você pode configurar para bloquear o acesso LDAP seguro pela Internet. O NSG contém um conjunto de regras que permitem o acesso LDAPS de entrada pela porta TCP 636 somente de um conjunto especificado de endereços IP. A regra “DenyAll” padrão se aplica a todos os outros tráfegos de entrada da Internet. A regra de NSG para permitir o acesso LDAPS pela Internet de endereços IP especificados tem prioridade mais alta que a regra de NSG DenyAll.

![Exemplo de NSG para acesso LDAPS seguro pela Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mais informações** - [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)
* [Criar um Grupo de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

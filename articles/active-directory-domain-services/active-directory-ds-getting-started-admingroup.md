---
title: 'Azure Active Directory Domain Services: Introdução | Microsoft Docs'
description: Habilite o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: f82fee635dbca4be8f22d9e63dfea40173f472e9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474303"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure

## <a name="task-3-configure-administrative-group"></a>Tarefa 3: configurar um grupo administrativo

Nesta tarefa de configuração, você cria um grupo administrativo em seu diretório do Azure AD. Este grupo administrativo especial é chamado *AAD DC Administrators*. Os membros desse grupo recebem permissões administrativas nos computadores ingressados no domínio gerenciado. Em computadores ingressados no domínio, esse grupo é adicionado ao grupo de administradores. Além disso, os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente a computadores ingressados no domínio.

> [!NOTE]
> Você não tem permissões de Administrador de Domínio nem de Administrador Enterprise no domínio gerenciado criado com o Azure Active Directory Domain Services. Em domínios gerenciados, essas permissões são reservadas pelo serviço e não são disponibilizadas aos usuários dentro do locatário. No entanto, você poderá usar o grupo administrativo especial criado nesta tarefa de configuração para executar algumas operações privilegiadas. Essas operações incluem ingressar os computadores no domínio, pertencer ao grupo de administração em computadores ingressados no domínio e configurar a Política de Grupo.
>

O assistente cria automaticamente o grupo administrativo em seu diretório do Azure AD. Esse grupo se chama "Administradores do AAD DC". Se houver um grupo existente com esse nome em seu diretório do Azure AD, o assistente o selecionará. Você pode configurar a associação ao grupo usando a página do assistente **Grupo de administradores**.

1. Para configurar a associação ao grupo, clique em **Administradores do AAD DC**.

    ![Configurar associação ao grupo](./media/getting-started/domain-services-blade-admingroup.png)

2. Clique no botão **Adicionar membros** para adicionar usuários de seu diretório do Azure AD ao grupo de administradores.

3. Quando terminar, clique em **OK** para ir para a página **Resumo** do assistente.

## <a name="configure-synchronization"></a>Configurar sincronização

Os Serviços de Domínio do Azure AD Domain Services permitem a sincronização completa de todos os usuários e grupos disponíveis no Azure AD, ou você pode selecionar a sincronização com escopo para sincronizar apenas os grupos específicos. Se você escolher a sincronização completa, você **não** poderá escolher a sincronização com escopo em um momento posterior. Para saber mais sobre a sincronização com escopo, visite o [artigo de sincronização com do Azure AD Domain Services](scoped-synchronization.md).

### <a name="full-synchronization"></a>Sincronização total

1. Para a sincronização completa, basta clicar em "OK" na parte inferior da tela, como completo é já escolhido.
    ![Sincronização completa](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Sincronização com escopo

1. Alternar o botão de sincronização para “Com escopo” e uma página de grupos selecionados aparecerá. A partir disso, você pode ver quais grupos já estão selecionados para serem sincronizados com seu domínio gerenciado.
    ![Sincronização com escopo](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Clique em **Selecionar grupos** na barra de navegação superior. A partir daqui, um seletor de grupo será exibido no lado. Use isso para selecionar todos os grupos adicionais para sincronizar com o Azure AD Domain Services. Quando terminar, clique em **Selecionar** para fechar o seletor de grupo e adicionar esses grupos à lista selecionada.
    ![Grupos selecionados de sincronização com escopo](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Clique em **OK** para mover para a página de resumo.

## <a name="deploy-your-managed-domain"></a>Implantar o domínio gerenciado

1. Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar para qualquer etapa do assistente para fazer alterações se for necessário. Quando terminar, clique em **OK** para criar o novo domínio gerenciado.

    ![Resumo](./media/getting-started/domain-services-blade-summary.png)

2. Você verá uma notificação que mostra o andamento da implantação do Azure AD Domain Services. Clique na notificação para ver detalhes do andamento da implantação.

    ![Notificação – implantação em andamento](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Verifique o status de implantação do domínio gerenciado

O processo de provisionamento de seu domínio gerenciado pode levar até uma hora.

1. Enquanto a implantação está em andamento, você pode pesquisar por "domain services" na caixa de pesquisa **Pesquisar recursos**. Selecione **Azure AD Domain Services** nos resultados da pesquisa. A folha **Azure AD Domain Services** lista o domínio gerenciado que está sendo provisionado.

    ![Localize o domínio gerenciado que está sendo provisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerenciado (por exemplo, "contoso100.com") para ver mais detalhes sobre ele.

    ![Domain Services – estado de provisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. A guia **Visão Geral** mostra que o domínio gerenciado está sendo provisionado no momento. Você não pode configurar o domínio gerenciado até que ele esteja totalmente provisionado. Pode levar até uma hora para que o domínio gerenciado seja totalmente provisionado.

    ![Serviços de domínio – guia Visão Geral no estado de provisionamento](./media/getting-started/domain-services-provisioning-state-details.png)

4. Quando o domínio gerenciado está totalmente provisionado, a guia **Visão Geral** mostra o status do domínio como **Em execução**.

    ![Domain Services - guia Visão geral depois de totalmente provisionado](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Durante o processo de provisionamento, o Azure AD Domain Services cria Aplicativos Empresariais nomeados "Serviços do Controlador de Domínio" e "AzureActiveDirectoryDomainControllerServices" no diretório. Esses Aplicativos Empresariais são necessários para atender o domínio gerenciado. É imperativo que não sejam excluídos a qualquer momento.
    >

5. Na guia **Propriedades**, você vê dois endereços IP nos quais controladores de domínio estão disponíveis para a rede virtual.

    ![Serviços de domínio – guia Propriedades após a conclusão do provisionado](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Precisa de ajuda?

Pode levar uma hora ou duas para ambos os controladores de domínio para seu domínio gerenciado serem provisionados. Se a sua implantação tiver falhado ou estiver presa no estado “Pendente” por mais de duas horas, fique à vontade para [contatar a equipe de produto para obter ajuda](contact-us.md).

## <a name="next-step"></a>Próxima etapa

Tarefa 4: [atualizar as configurações do DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)

---
title: "Azure Active Directory Domain Services: introdução | Microsoft Docs"
description: Habilite o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: d86b9e99619dea408e784f32d76ba6296948bdc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure
Este artigo mostra como habilitar o Azure AD DS (Azure Active Directory Domain Services) usando o portal do Azure.

Para iniciar o assistente **Habilitar Azure AD Domain Services**, conclua as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique em **Novo**.
3. Na página **Novo**, digite **Domain Services** na barra de pesquisa.

    ![Pesquisa pelo Domain Services](./media/getting-started/search-domain-services.png)

4. Clique para selecionar o **Azure AD Domain Services** na lista de sugestões de pesquisa. Na página **Azure AD Domain Services**, clique no botão **Criar**.

    ![Exibição de serviços de domínio](./media/getting-started/domain-services-blade.png)

5. O assistente **Habilitar Azure AD Domain Services** é iniciado.


## <a name="task-1-configure-basic-settings"></a>Tarefa 1: definir as configurações básicas
Na página **Básico** do assistente, você pode especificar o nome de domínio DNS para o domínio gerenciado. Você também pode escolher o grupo de recursos e o local do Azure no qual o domínio gerenciado deve ser implantado.

![Configurar os aspectos básicos](./media/getting-started/domain-services-blade-basics.png)

1. Escolha o **nome de domínio DNS** para seu domínio gerenciado.

   * O nome de domínio padrão do diretório (com um sufixo **.onmicrosoft.com**) é selecionado por padrão.

   * Você também pode inserir um nome de domínio personalizado. Neste exemplo, o nome de domínio personalizado é *contoso100.com*.

     > [!WARNING]
     > O prefixo do nome do domínio especificado (por exemplo, *contoso100* no nome de domínio *contoso100.com*) deve conter 15 caracteres ou menos. Você não pode criar um domínio gerenciado com um prefixo de mais de 15 caracteres.
     >
     >

2. Garanta que o nome de domínio DNS escolhido para o domínio gerenciado ainda não exista na rede virtual. Especificamente, verifique se:

   * Você já tiver um domínio com o mesmo nome de domínio DNS na rede virtual.

   * A rede virtual em que você planeja habilitar o domínio gerenciado tem uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.

   * Você tiver um serviço de nuvem existente com esse nome na rede virtual.

3. Escolha o **tipo de rede virtual**. Por padrão, o tipo de rede virtual **Gerenciador de Recursos** está selecionado. Recomendamos o uso desse tipo de rede virtual para todos os domínios gerenciados recém-criados.

    > [!TIP]
    > **Suporte de rede virtual clássica está agendado para ser interrompido.** Selecione o tipo de rede virtual Resource Manager para todas as novas implantações. Redes virtuais clássicas logo não terão mais suporte para novas implantações. Domínios gerenciados existentes implantados em redes virtuais clássicas continuarão tendo suporte.
    >

4. Selecione a **Assinatura** do Azure na qual você deseja criar o domínio gerenciado.

5. Selecione o **Grupo de recursos** a que o domínio gerenciado deve pertencer. Você pode escolher as opções **Criar novo** ou **Usar existente** para selecionar o grupo de recursos.

6. Escolha o **Local** do Azure no qual o domínio gerenciado deve ser criado. Na página **Rede** do assistente, você vê somente redes virtuais que pertencem ao local selecionado.

7. Quando terminar, clique em **OK** para ir para a página **Rede** do assistente.


## <a name="next-step"></a>Próxima etapa
[Tarefa 2: definir as configurações de rede](active-directory-ds-getting-started-network.md)

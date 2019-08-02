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
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: fd6db8a9ac6759b55273940d4a100fc3c4df7692
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473652"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure
Este artigo mostra como habilitar o Azure AD DS (Azure Active Directory Domain Services) usando o portal do Azure.


## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas listadas neste artigo, será necessário ter:

* Uma **assinatura do Azure**válida.
* Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
* A **assinatura do Azure deve estar associada ao diretório do Azure AD**.
* É necessário ter privilégios de **administrador global** no diretório do Azure AD para habilitar o Azure AD Domain Services.


## <a name="enable-azure-ad-domain-services"></a>Habilitar os Serviços de Domínio do Azure AD

Para iniciar o assistente **Habilitar Azure AD Domain Services**, conclua as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique em **Criar um recurso**.
3. Na página **Novo**, digite **Domain Services** na barra de pesquisa.

    ![Pesquisa pelo Domain Services](./media/getting-started/search-domain-services.png)

4. Clique para selecionar o **Azure AD Domain Services** na lista de sugestões de pesquisa. Na página **Azure AD Domain Services**, clique no botão **Criar**.

    ![Exibição de serviços de domínio](./media/getting-started/domain-services-blade.png)

5. O assistente **Habilitar Azure AD Domain Services** é iniciado.


## <a name="task-1-configure-basic-settings"></a>Tarefa 1: definir as configurações básicas
Na página **Básico** do assistente, especifique o nome de domínio DNS para o domínio gerenciado. Você também pode escolher o grupo de recursos e o local do Azure no qual o domínio gerenciado deve ser implantado.

![Configurar os aspectos básicos](./media/getting-started/domain-services-blade-basics.png)

1. Escolha o **nome de domínio DNS** para seu domínio gerenciado.

   > [!NOTE]
   > **Diretrizes para selecionar um nome de domínio DNS**
   > * **Nome de domínio interno:** Por padrão, o assistente especifica o nome de domínio padrão/interno do diretório (com um sufixo **.onmicrosoft.com**) para você. Se você optar por habilitar o acesso LDAP seguro para o domínio gerenciado pela Internet, espere ter problemas ao criar um registro DNS público ou ao obter um certificado LDAP seguro de uma CA pública para esse nome de domínio. A Microsoft possui o domínio *.onmicrosoft.com* e as CAs não emitirão certificados para atestar esse domínio.
   > * **Nomes de domínio personalizados:** Você também pode inserir um nome de domínio personalizado. Neste exemplo, o nome de domínio personalizado é *contoso100.com*.
   > * **Sufixos de domínio não roteáveis:** Geralmente é recomendável evitar um sufixo de nome de domínio que não possa ser roteado. Por exemplo, é melhor evitar a criação de um domínio com o nome de domínio DNS 'contoso.local'. O sufixo DNS '.local' não é roteável e pode causar problemas com a resolução DNS.
   > * **Restrições do prefixo do domínio:** O prefixo do nome do domínio especificado (por exemplo, *contoso100* no nome de domínio *contoso100.com*) deve conter 15 caracteres ou menos. Você não pode criar um domínio gerenciado com um prefixo de mais de 15 caracteres.
   > * **Conflitos de nome de rede:** Garanta que o nome de domínio DNS escolhido para o domínio gerenciado ainda não exista na rede virtual. Especificamente, verifique se:
   >     * Você já tiver um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual.
   >     * A rede virtual em que você planeja habilitar o domínio gerenciado tem uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.
   >     * Você tiver um serviço de nuvem existente com esse nome na rede virtual.

2. Selecione a **Assinatura** do Azure na qual você deseja criar o domínio gerenciado.

3. Selecione o **Grupo de recursos** a que o domínio gerenciado deve pertencer. Escolha as opções **Criar novo** ou **Usar existente** para selecionar o grupo de recursos.

4. Escolha o **Local** do Azure no qual o domínio gerenciado deve ser criado. Na página **Rede** do assistente, você vê somente redes virtuais que pertencem ao local selecionado.

5. Clique em **OK** para acessar a página **Rede** do assistente.


## <a name="next-step"></a>Próxima etapa
[Tarefa 2: definir as configurações de rede](active-directory-ds-getting-started-network.md)

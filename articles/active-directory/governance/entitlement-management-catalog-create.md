---
title: Criar & gerenciar um catálogo de recursos no gerenciamento de direitos-Azure AD
description: Saiba como criar um novo contêiner de recursos e pacotes de acesso no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c6f37a1b5cc16c80af8316cfeae164d58ea9796
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552160"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Criar e gerenciar um catálogo de recursos no gerenciamento de direitos do Azure AD

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contêiner de recursos e pacotes de acesso. Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso. Qualquer pessoa que criar o catálogo se tornará o primeiro proprietário do catálogo. Um proprietário de catálogo pode adicionar proprietários de catálogos adicionais.

**Função de pré-requisito:** Administrador global, administrador de usuário ou criador de catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos**.

    ![Catálogos de gerenciamento de direitos no portal do Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **novo catálogo**.

1. Insira um nome exclusivo para o catálogo e forneça uma descrição.

    Os usuários verão essas informações nos detalhes do pacote de acesso.

1. Se você quiser que os pacotes de acesso nesse catálogo estejam disponíveis para que os usuários solicitem assim que eles forem criados, defina **habilitado** como **Sim**.

1. Se você quiser permitir que os usuários em diretórios externos selecionados possam solicitar pacotes de acesso neste catálogo, defina **habilitado para usuários externos** como **Sim**.

    ![Painel novo catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Clique em **criar** para criar o catálogo.

### <a name="creating-a-catalog-programmatically"></a>Criando um catálogo programaticamente

Você também pode criar um catálogo usando Microsoft Graph.  Um usuário em uma função apropriada com um aplicativo que tem a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para [criar um accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos em um pacote do Access, os recursos devem existir em um catálogo. Os tipos de recursos que você pode adicionar são grupos, aplicativos e sites do SharePoint Online. Os grupos podem ser grupos de Microsoft 365 criados na nuvem ou grupos de segurança do Azure AD criados pela nuvem. Os aplicativos podem ser aplicativos corporativos do Azure AD, incluindo aplicativos SaaS e seus próprios aplicativos federados para o Azure AD. Os sites podem ser sites do SharePoint Online ou coleções de sites do SharePoint Online.

**Função de pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar recursos.

1. No menu à esquerda, clique em **recursos**.

1. Clique em **Adicionar recursos**.

1. Clique em um tipo de recurso: **grupos e equipes**, **aplicativos** ou **sites do SharePoint**.

    Se você não vir um recurso que deseja adicionar ou não puder adicionar um recurso, verifique se você tem a função de gerenciamento de direitos e funções de diretório do Azure AD necessária. Talvez seja necessário ter alguém com as funções necessárias para adicionar o recurso ao seu catálogo. Para obter mais informações, confira [Funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selecione um ou mais recursos do tipo que você deseja adicionar ao catálogo.

    ![Adicionar recursos a um catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Quando terminar, clique em **Adicionar**.

    Esses recursos agora podem ser incluídos em pacotes do Access no catálogo.

### <a name="add-a-multi-geo-sharepoint-site"></a>Adicionar um site do SharePoint de várias áreas geográficas

1. Se você tiver [várias geografias](https://docs.microsoft.com/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) habilitadas para o SharePoint, selecione o ambiente do qual deseja selecionar sites.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Pacote de acesso-adicionar funções de recurso-selecionar sites de várias regiões do SharePoint":::

1. Em seguida, selecione os sites que você deseja adicionar ao catálogo. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adicionando um recurso a um catálogo programaticamente

Você também pode adicionar um recurso a um catálogo usando Microsoft Graph.  Um usuário em uma função apropriada, ou um proprietário de catálogo e recurso, com um aplicativo que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para [criar um accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Você pode remover recursos de um catálogo. Um recurso só poderá ser removido de um catálogo se não estiver sendo usado em nenhum dos pacotes de acesso do catálogo.

**Função de pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo do qual você deseja remover os recursos.

1. No menu à esquerda, clique em **recursos**.

1. Selecione os recursos que você deseja remover.

1. Clique em **remover** (ou clique nas reticências (**...**) e, em seguida, clique em **remover recurso**).


## <a name="add-additional-catalog-owners"></a>Adicionar proprietários de catálogos adicionais

O usuário que criou um catálogo torna-se o primeiro proprietário do catálogo. Para delegar o gerenciamento de um catálogo, você adiciona usuários à função de proprietário do catálogo. Isso ajuda a compartilhar as responsabilidades de gerenciamento de catálogo. 

Siga estas etapas para atribuir um usuário à função de proprietário do catálogo:

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar administradores.

1. No menu à esquerda, clique em **funções e administradores**.

    ![Cataloga funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique em **Adicionar proprietários** para selecionar os membros para essas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Você pode editar o nome e a descrição de um catálogo. Os usuários veem essas informações nos detalhes de um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja editar.

1. Na página **visão geral** do catálogo, clique em **Editar**.

1. Edite o nome do catálogo, a descrição ou as configurações habilitadas.

    ![Editar configurações do catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Clique em **Save** (Salvar).

## <a name="delete-a-catalog"></a>Excluir um catálogo

Você pode excluir um catálogo, mas somente se ele não tiver nenhum pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja excluir.

1. Na **visão geral** do catálogo, clique em **excluir**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

### <a name="deleting-a-catalog-programmatically"></a>Excluindo um catálogo de forma programática

Você também pode excluir um catálogo usando Microsoft Graph.  Um usuário em uma função apropriada com um aplicativo que tem a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a API para [excluir um accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Próximas etapas

- [Delegar governança de acesso para acessar gerenciadores de pacotes](entitlement-management-delegate-managers.md)
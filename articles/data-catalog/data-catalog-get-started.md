---
title: 'Início Rápido: Criar um Catálogo de Dados do Azure'
description: Este início rápido descreve como criar um Catálogo de Dados do Azure usando o portal do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674726"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Início Rápido: Criar um Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

O Catálogo de Dados do Azure é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em ativos de dados da empresa. Para obter uma visão detalhada, confira [O que é o Catálogo de Dados do Azure](overview.md).

Este guia de início rápido ajuda você a começar a criar um Catálogo de Dados do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> [!Note]
> Devido aos requisitos de segurança do Azure, o Catálogo de Dados do Azure gerou o protocolo TLS 1.2. O TLS 1.0 e o TLS 1.1 foram desabilitados. Você poderá encontrar erros ao executar a ferramenta de registro se o computador não estiver atualizado para o TLS 1.2. Confira [Habilitar o protocolo TLS (1.2)](/mem/configmgr/core/plan-design/security/enable-tls-1-2) para atualizar seu computador para o TLS 1.2.

Para começar, você precisa ter:

* Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/).
* Você precisa ter seu próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Para configurar o Catálogo de Dados, você deve ser o proprietário ou o coproprietário de uma assinatura do Azure.

## <a name="create-a-data-catalog"></a>Criar um catálogo de dados

Você somente pode provisionar um catálogo de dados por organização (domínio do Azure Active Directory). Portanto, se o proprietário ou coproprietário de uma assinatura do Azure que pertence a esse domínio do Azure Active Directory já tiver criado um catálogo, você não poderá criar um catálogo novamente, mesmo se tiver várias assinaturas do Azure. Para testar se um catálogo de dados foi criado por um usuário no domínio do Azure Active Directory, navegue até a [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e verifique se você vê o catálogo. Se o catálogo já foi criado para você, ignore o procedimento a seguir e vá para a próxima seção.

1. Vá para [portal do Azure](https://portal.azure.com) > **Criar um recurso** e selecione **Catálogo de Dados**.

    ![Botão Criar Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-create.png)

2. Especifique um **nome** para o catálogo de dados, a **assinatura** que você deseja usar e o **local** do catálogo e o **tipo de preço**. Em seguida, selecione **Criar**.

3. Vá para a [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.

   ![Catálogo de Dados do Azure – botão Publicar Dados](media/data-catalog-get-started/data-catalog-publish-data.png)

   Você também pode acessar a página inicial do Catálogo de Dados da [página do serviço de Catálogo de Dados](https://azure.microsoft.com/services/data-catalog) selecionando **Começar**.

   ![Catálogo de Dados do Azure – página inicial de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Vá para a página **Configurações**.

    ![Catálogo de Dados do Azure – provisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expanda **Preço** e verifique sua **edição** (Gratuita ou Standard) do Catálogo de Dados do Azure.

    ![Catálogo de Dados do Azure – selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Se você escolher a edição *Standard* como seu tipo de preço, você poderá expandir **Grupos de Segurança** e habilitar autorização de grupos de segurança do Active Directory para acessar o Catálogo de Dados e habilitar o ajuste automático de cobrança.

    ![Grupos de Segurança do Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expanda **Usuários do Catálogo** e clique em **Adicionar** para adicionar usuários ao catálogo de dados. Você é adicionado automaticamente a este grupo.

    ![Catálogo de Dados do Azure – usuários](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Se você escolher a edição *Standard* como seu tipo de preço, poderá expandir **Administradores do Glossário** e clicar em **Adicionar** para adicionar usuários administradores do glossário. Você é adicionado automaticamente a este grupo.

    ![Administradores do Glossário do Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. Você é adicionado automaticamente a este grupo.

    ![Catálogo de Dados do Azure – administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expanda **Título do Portal** e adicione mais texto que será exibido no título do portal.

    ![Título do Portal do Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Depois de concluir a página **Configurações**, navegue até a página **Publicar**.

    ![Catálogo de Dados do Azure – criado](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Encontrar um catálogo de dados no portal do Azure

1. Em uma guia separada no navegador da Web ou em uma janela de navegador da Web separada, vá para o [portal do Azure](https://portal.azure.com) e entre com a mesma conta que você usou para criar o catálogo de dados na etapa anterior.

2. Selecione **Todos os Serviços** e, em seguida, clique em **Catálogo de Dados**.

    ![Catálogo de Dados do Azure – procurar no Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Você vê o catálogo de dados que criou.

    ![Catálogo de Dados do Azure – exibir catálogo em lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Clique no catálogo que você criou. Você vê a folha **Catálogo de dados** no portal.

   ![Catálogo de Dados do Azure – folha no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Você pode exibir as propriedades do catálogo de dados e atualizá-las. Por exemplo, clique em **Tipo de preço** e altere a edição.

    ![Catálogo de Dados do Azure – tipo de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um Catálogo de Dados do Azure para sua organização. Agora você pode registrar fontes de dados em seu catálogo de dados.

> [!div class="nextstepaction"]
> [Registrar fontes de dados no Catálogo de Dados do Azure](data-catalog-how-to-register.md)
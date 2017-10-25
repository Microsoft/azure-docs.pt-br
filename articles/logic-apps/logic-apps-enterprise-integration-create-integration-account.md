---
title: "Criar, vincular, excluir ou mover uma conta de integração no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Como criar uma conta de integração e vinculá-la aos seus aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 716e7b5bab8725dea0fd2b760d0e46e8e892c5b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-an-integration-account"></a>O que é uma conta de integração?

Uma conta de integração permite aos aplicativos de integração corporativa gerenciar artefatos, incluindo esquemas, mapas, certificados, parceiros e contratos. Qualquer aplicativo de integração que você criar usará uma conta de integração para acessar esses esquemas, mapas, certificados etc.

## <a name="create-an-integration-account"></a>Criar uma conta de integração

1.  Entre no [portal do Azure](http://portal.azure.com "portal do Azure"). No menu à esquerda, selecione **Mais serviços**.

    ![Escolha “Mais serviços”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Na caixa de pesquisa, digite "Integração" como filtro. Na lista de resultados, selecione **Contas de Integração**.

    ![Filtre por "integração", selecione "Contas de Integração"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Na parte superior da página, escolha **Adicionar**.

    ![Escolha Adicionar](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Nomeie sua conta de integração e selecione a assinatura do Azure que você deseja usar. Você pode criar um novo **grupo de recursos** ou escolher um grupo de recursos existente. Selecione um **Local** para hospedar sua conta de integração e um **Tipo de preço**. 

    Quando você estiver pronto, escolha **Criar**.

    ![Forneça detalhes para sua conta de integração](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    O Azure provisiona sua conta de integração no local selecionado, o que deve ser concluído dentro de 1 minuto.

5. Atualize a página. Você verá a nova conta de integração listada.

    ![A nova conta de integração é exibida](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Em seguida, vincule a conta de integração criada ao seu aplicativo lógico. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vincular uma conta de integração a um aplicativo lógico

Para que seus Aplicativos lógicos possam acessar mapas, esquemas, contratos e outros artefatos em sua conta de integração, vincule a conta de integração ao seu aplicativo lógico.

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta de integração
* Um aplicativo lógico

> [!NOTE] 
> Verifique se sua conta de integração e o aplicativo lógico estão no *mesmo local do Azure* antes de começar.


1. No portal do Azure, selecione seu aplicativo lógico e verifique o local dele.

    ![Selecionar seu aplicativo lógico, verificar o local](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. Em **Configurações**, selecione **Conta de Integração**.

    ![Selecionar “Conta de Integração”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Na lista **Selecione a conta de integração**, selecione a conta de integração que você deseja vincular ao aplicativo lógico. Para concluir a vinculação, escolha **Salvar**.

    ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    Você verá uma notificação mostrando que sua conta de integração foi vinculada ao seu Aplicativo lógico, e que todos os artefatos de sua conta de integração estão disponíveis para seu aplicativo lógico.

    ![Seu aplicativo lógico é vinculado à sua conta de integração](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Agora que sua conta de integração está vinculada ao seu aplicativo lógico, você pode usar os conectores B2B dentro dos aplicativos lógicos. Alguns conectores B2B comuns incluem a validação de XML e codificação/decodificação de arquivo simples.  

## <a name="delete-your-integration-account"></a>Excluir sua conta de integração

1. Escolha **Mais serviços**.

    ![Escolha “Mais serviços”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Na caixa de pesquisa, digite "Integração" como filtro. Na lista de resultados, selecione **Contas de Integração**.

    ![Filtre por "integração", selecione "Contas de Integração"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Selecione a conta de integração que você deseja excluir.

    ![Selecionar conta de integração a ser excluída](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. No menu, escolha **Excluir**.

    ![Clique em “Excluir”](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. Confirme sua escolha para excluir a conta de integração.

## <a name="move-your-integration-account"></a>Selecionar sua conta de integração

Para mover uma conta de integração para outro grupo de recursos ou de assinatura do Azure, siga estas etapas.

> [!IMPORTANT]
> Você tem que atualizar todos os scripts a fim de usar as IDs do novo recurso depois de mover uma conta de integração.

1. Escolha **Mais serviços**.

    ![Escolha “Mais serviços”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Na caixa de pesquisa, digite "Integração" como filtro. Na lista de resultados, selecione **Contas de Integração**.

    ![Filtre por "integração", selecione "Contas de Integração"](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. Selecione a conta de integração que você deseja mover. Em **Configurações**, escolha **Propriedades**.

    ![Selecione a conta de integração a ser movida. Em Configurações, escolha Propriedades](./media/logic-apps-enterprise-integration-accounts/move.png)

5. Altere o grupo de recursos ou a assinatura do Azure associada à sua conta de integração.

    ![Escolher Alterar grupo de recursos ou Alterar assinatura](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  


---
title: "Gerenciar metadados de artefato de contas de integração – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Adicionar ou recuperar metadados de artefato de contas de integração dos Aplicativo Lógico do Azure"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 28bb8296ddd820ec5aa9793dc0928b4b1e67bf6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Gerenciar metadados de artefato nas contas de integração de aplicativos lógicos

É possível definir metadados personalizados para artefatos em contas de integração e recuperar esses metadados durante o tempo de execução do aplicativo lógico. Por exemplo, é possível especificar metadados para artefatos como parceiros, contratos, esquemas e mapas – todos armazenam metadados usando pares chave-valor. Atualmente, os artefatos não podem criar metadados por meio da interface do usuário, mas é possível usar APIs REST para criar metadados. Para adicionar metadados ao criar ou selecionar um parceiro, contrato ou esquema no portal do Azure, escolha **Editar como JSON**. Para recuperar metadados de artefato em aplicativos lógicos, é possível usar o recurso Pesquisa de Artefato da Conta de Integração.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adicionar metadados a artefatos em contas de integração

1. Crie uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md).

2. Adicione um artefato à conta de integração, por exemplo, um [parceiro](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [contrato](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) ou [esquema](logic-apps-enterprise-integration-schemas.md).

3.  Selecione o artefato, escolha **Editar como JSON** e insira os detalhes dos metadados.

    ![Inserir metadados](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperar metadados de artefatos de aplicativos lógicos

1. Crie um [aplicativo lógico](logic-apps-create-a-logic-app.md).

2. Crie um [link do aplicativo lógico para a conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. No Designer de Aplicativos Lógicos, adicione um gatilho como *Solicitação* ou *HTTP* ao aplicativo lógico.

4.  Escolha **Próxima Etapa** > **Adicionar uma ação**. Pesquise *integração* para poder localizá-la e, em seguida, selecione **Conta de Integração – Pesquisa de Artefato da Conta de Integração**.

    ![Selecionar Pesquisa de Artefato da Conta de Integração](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Selecione o **Tipo de Artefato** e forneça o **Nome do Artefato**.

    ![Selecionar tipo de artefato e especificar um nome de artefato](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exemplo: Recuperar metadados do parceiro

Os metadados do parceiro têm estes detalhes de `routingUrl`:

![Encontrar metadados de “routingURL” do parceiro](media/logic-apps-enterprise-integration-metadata/image6.png)

1. No aplicativo lógico, adicione o gatilho, uma ação **Conta de Integração – Pesquisa de Artefato da Conta de Integração** do parceiro e um **HTTP**.

    ![Adicionar gatilho, pesquisa de artefato e “HTTP” ao aplicativo lógico](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Para recuperar o URI, acesse o Modo de Exibição de Código do aplicativo lógico. A definição do aplicativo lógico deve ser parecida com este exemplo:

    ![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  

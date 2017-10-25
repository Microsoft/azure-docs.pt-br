---
title: "Validar XML – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Validar XML com esquemas para o Aplicativo Lógico do Azure e cenários B2B usando o Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Validar XML para integração corporativa

Geralmente, em cenários B2B, os parceiros de um contrato devem garantir que as mensagens trocadas entre eles são válidas antes de iniciar o processamento de dados. É possível validar documentos com um esquema predefinido usando o conector de Validação de XML do Enterprise Integration Pack.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validar um documento com o conector de Validação de XML

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico") que contém o esquema que você deseja usar para validar os dados XML.

2. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. Para adicionar a ação de **Validação de XML**, escolha **Adicionar uma ação**.

4. Para filtrar todas as ações para aquela que você deseja usar, insira *xml* na caixa de pesquisa. Escolha **Validação de XML**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. Para especificar o conteúdo XML que você deseja validar, selecione **CONTEÚDO**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. Selecione a marca body como o conteúdo que você deseja validar.

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. Para especificar o esquema desejado a fim de validar a entrada de *conteúdo* anterior, escolha **NOME DO ESQUEMA**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. Salve seu trabalho   

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

Agora, a configuração do conector de validação foi concluída. Em um aplicativo real, convém armazenar os dados validados em um aplicativo LOB (de linha de negócios), como o SalesForce. Para enviar a saída validada ao Salesforce, adicione uma ação.

Para testar a ação de validação, faça uma solicitação ao ponto de extremidade HTTP.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")   


---
title: Validar XML para Enterprise Integration B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Validar XML com esquemas para soluções B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995835"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Geralmente, em cenários B2B, os parceiros de um contrato devem garantir que as mensagens trocadas entre eles são válidas antes de iniciar o processamento de dados. É possível validar documentos com um esquema predefinido usando o conector de Validação de XML do Enterprise Integration Pack.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validar um documento com o conector de Validação de XML

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico") que contém o esquema que você deseja usar para validar os dados XML.

2. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Para adicionar a ação de **Validação de XML**, escolha **Adicionar uma ação**.

4. Para filtrar todas as ações para aquela que você deseja usar, insira *xml* na caixa de pesquisa. Escolha **Validação de XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Para especificar o conteúdo XML que você deseja validar, selecione **CONTEÚDO**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Selecione a marca body como o conteúdo que você deseja validar.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Para especificar o esquema desejado a fim de validar a entrada de *conteúdo* anterior, escolha **NOME DO ESQUEMA**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Salve seu trabalho   

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Agora, a configuração do conector de validação foi concluída. Em um aplicativo real, convém armazenar os dados validados em um aplicativo LOB (de linha de negócios), como o SalesForce. Para enviar a saída validada ao Salesforce, adicione uma ação.

Para testar a ação de validação, faça uma solicitação ao ponto de extremidade HTTP.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")   


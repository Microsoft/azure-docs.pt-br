---
title: Manipular mensagens EDIFACT com segmentos UNH 2.5 – Aplicativos Lógicos do Azure | Microsoft Docs
description: Resolver documentos EDIFACT com segmentos UNH 2.5 nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681639"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Manipular documentos EDIFACT com segmentos UNH 2.5 nos Aplicativos Lógicos do Azure

Quando está presente no documento EDIFACT, UNH2.5 está sendo usado para pesquisa de esquema. 

Exemplo: É o campo UNH **EAN008** na mensagem EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Etapas a seguir para manipular a mensagem 
1. Atualizar o esquema
2. Verificar as configurações do contrato  

## <a name="update-the-schema"></a>Atualizar o esquema
Para processar a mensagem, você precisa implantar um esquema com o nome do nó raiz UNH2.5.  Para dar um exemplo, o nome raiz do esquema seria **EFACT_D03B_ORDERS_EAN008**  

Para cada D03B_ORDERS com um segmento UNH2.5 diferente, você precisaria implantar um esquema individual.  

## <a name="add-schema-to-the-edifact-agreement"></a>Adicionar o esquema ao contrato de EDIFACT
### <a name="edifact-decode"></a>Decodificação de EDIFACT
Para decodificar a mensagem de entrada, configure o esquema nas definições de recebimento do contrato de EDIFACT
1. Adicionar o esquema à conta de integração    
2. Configure o esquema nas definições de recebimento do contrato de EDIFACT. 
3. Selecione o contrato de EDIFACT e clique em **Editar como JSON**.  Adicionar o valor UNH2.5 no contrato de recebimento **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codificação de EDIFACT
Para codificar a mensagem de entrada, configure o esquema nas definições de envio do contrato de EDIFACT
1. Adicionar o esquema à conta de integração    
2. Configure o esquema nas definições de envio do contrato de EDIFACT. 
3. Selecione o contrato de EDIFACT e clique em **Editar como JSON**.  Adicione o valor UNH2.5 no contrato de envio **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos da conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  
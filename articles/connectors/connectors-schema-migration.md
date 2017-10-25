---
title: "Como migrar aplicativos lógicos para a versão de esquema 2015-08-01-preview | Microsoft Docs"
description: "Você pode facilmente migrar aplicativos lógicos para a última versão do esquema. Basta seguir estas etapas."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Como migrar aplicativos lógicos para a versão de esquema 2015-08-01-preview
Para mover aplicativos lógicos existentes para o novo esquema, faça o seguinte:  

1. Abra o aplicativo lógico no portal do Azure  
2. Clique em Atualizar Esquema:
   
   ![Ícone de API][step1]   
   A página Atualizar Esquema exibe e fornece um link para um documento que fornece detalhes sobre os aperfeiçoamentos no novo esquema: ![Ícone de API][step2]

> [!NOTE]
> Quando você seleciona **Atualizar Esquema**, executamos automaticamente as etapas de migração e lhe fornecemos o código de saída. Você pode usar isso para atualizar sua definição. No entanto, siga as práticas recomendadas de codificação, como as descritas na seção **Práticas recomendadas** abaixo.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Práticas recomendadas ao migrar aplicativos lógicos para a última versão do esquema:
* Copie o script migrado para um novo aplicativo lógico - não substitua o antigo até concluir o teste e confirmar que o aplicativo migrado funciona conforme o esperado.
* Testar o aplicativo lógico **antes** de colocá-lo em produção
* Após a conclusão da migração, comece a atualizar os Aplicativos Lógicos para usar as [APIs gerenciadas](apis-list.md) sempre que possível. Por exemplo, você pode começar a usar o Dropbox v2 em todos os casos em que usa o DropBox v1.

## <a name="whats-next"></a>O que vem a seguir
* [Saiba como migrar manualmente os aplicativos lógicos](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png







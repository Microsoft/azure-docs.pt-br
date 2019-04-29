---
title: Mensagens XML para Enterprise Integration B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Processar, validar, transformar e enriquecer mensagens XML para soluções B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996552"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Mensagens XML e arquivos simples nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Ao utilizar aplicativos lógicos, você tem a capacidade de processar as mensagens XML que você envia e recebe. Esse recurso está incluído no Enterprise Integration Pack. Para os usuários com uma tela de fundo do BizTalk Server, o Enterprise Integration Pack fornece recursos semelhantes para transformar e validar mensagens, trabalhar com arquivos simples e até mesmo usar o XPath para enriquecer ou extrair propriedades específicas de uma mensagem. 

Para os usuários que são novos nesse espaço, esses recursos expandem a forma que você processa mensagens dentro do seu fluxo de trabalho. Por exemplo, caso esteja em um cenário de business-to-business e trabalhar com esquemas XML específicos, é possível usar o Enterprise Integration Pack para aprimorar como sua empresa processa essas mensagens. 

O Enterprise Integration Pack inclui: 

* [Validação de XML](logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a validação de mensagens XML") – Validar uma mensagem XML de entrada ou de saída em relação a um esquema específico.
* [Transformação XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre mapas e transformações de mensagens XML") – Converter ou personalizar uma mensagem XML com base nos seus requisitos ou nos requisitos de um parceiro.
* [Codificação e decodificação de arquivos simples](logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a codificação/decodificação de arquivos simples") Codificar ou decodificar arquivos simples. Por exemplo, o SAP aceita e envia arquivos IDOC no formato de arquivo simples. Muitas plataformas de integração criam mensagens XML, incluindo Aplicativos Lógicos. Portanto, você pode criar um aplicativo lógico que use o codificador de arquivo simples "converter" arquivos XML em arquivos simples. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) – Aprimorar uma mensagem e extrair propriedades específicas dela. Então, será possível usar as propriedades extraídas para rotear a mensagem a um destino ou a um ponto de extremidade intermediário.

## <a name="try-it-out"></a>Experimentar
[Implantar um aplicativo lógico totalmente operacional](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (amostra GitHub), usando os recursos XML em aplicativos lógicos do Azure.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

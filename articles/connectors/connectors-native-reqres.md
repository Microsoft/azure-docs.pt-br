---
title: "Usar ações de solicitação e resposta | Microsoft Docs"
description: "Visão geral do gatilho e da ação da solicitação e da resposta em um aplicativo lógico do Azure"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: e45b07d709927af64cfba28dfb0d8ee9cb8893b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-request-and-response-components"></a>Introdução aos componentes de solicitação e resposta
Com os componentes de solicitação e resposta em um aplicativo lógico, você pode responder em tempo real a eventos.

Por exemplo, você pode:

* Responder a uma solicitação HTTP com dados de um banco de dados local por meio de um aplicativo lógico.
* Dispare um aplicativo lógico de um evento de webhook externo.
* Chamar um aplicativo lógico com uma ação de solicitação e resposta de dentro de outro aplicativo lógico.

Para começar a usar as ações de solicitação e resposta em um aplicativo lógico, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Usar o gatilho de Solicitação HTTP
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar uma solicitação HTTP no Designer de Aplicativo Lógico.

1. Adicione o gatilho **Solicitação — quando uma solicitação HTTP é recebida** no seu aplicativo lógico. Como opção, você pode fornecer um esquema JSON (usando uma ferramenta como [JSONSchema.net](http://jsonschema.net)) para o corpo da solicitação. Isso permite que o designer gere tokens para propriedades na solicitação HTTP.
2. Adicione outra ação para que seja possível salvar o aplicativo lógico.
3. Depois de salvar o aplicativo lógico, você pode obter a URL da solicitação HTTP do cartão de solicitação.
4. Um HTTP POST (você pode usar uma ferramenta como [Postman](https://www.getpostman.com/)) para a URL que dispara o aplicativo lógico.

> [!NOTE]
> Se você não definir uma ação de resposta, uma resposta `202 ACCEPTED` será retornada imediatamente ao chamador. Você pode usar a ação de resposta para personalizar uma resposta.
> 
> 

![Gatilho de resposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Usar a ação de Resposta HTTP
A ação de Resposta HTTP só será válida quando usada em um fluxo de trabalho disparado por uma solicitação HTTP. Se você não definir uma ação de resposta, uma resposta `202 ACCEPTED` será retornada imediatamente ao chamador.  Você pode adicionar uma ação de resposta a qualquer etapa do fluxo de trabalho. O aplicativo lógico só mantém a solicitação de entrada aberta por um minuto para uma resposta.  Após um minuto, se nenhuma resposta for enviada do fluxo de trabalho (e se existir uma ação de resposta na definição), um `504 GATEWAY TIMEOUT` será retornado ao chamador.

Veja como adicionar uma ação de Resposta HTTP:

1. Selecione o botão **Nova Etapa** .
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, digite **resposta** para listar a ação de Resposta.
   
    ![Selecionar a ação de resposta](./media/connectors-native-reqres/using-action-1.png)
4. Adicione todos os parâmetros necessários para a mensagem de resposta HTTP.
   
    ![Concluir a ação de resposta](./media/connectors-native-reqres/using-action-2.png)
5. Clique no canto superior esquerdo da barra de ferramentas para salvar e seu aplicativo lógico será salvo e publicado (ativado).

## <a name="request-trigger"></a>Gatilho de solicitação
Veja os detalhes do gatilho com suporte deste conector. Há um único gatilho de solicitação.

| Gatilho | Descrição |
| --- | --- |
| Solicitação |Ocorre quando uma solicitação HTTP é recebida |

## <a name="response-action"></a>Ação de resposta
Veja os detalhes da ação com suporte deste conector. Há uma única ação de resposta que pode ser usada apenas quando acompanhada por um gatilho de solicitação.

| Ação | Descrição |
| --- | --- |
| resposta |Retorna uma resposta à solicitação HTTP correlacionada |

### <a name="trigger-and-action-details"></a>Detalhes de gatilho e da ação
As tabelas a seguir descrevem os campos de entrada para o gatilho e a ação e os detalhes de saída correspondentes.

#### <a name="request-trigger"></a>Gatilho de solicitação
A seguir, um campo de entrada para o gatilho de uma solicitação HTTP de entrada.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Esquema JSON |schema |O esquema JSON do corpo da solicitação HTTP |

<br>

**Detalhes de saída**

A seguir, os detalhes de saída para a solicitação.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| headers |objeto |Cabeçalhos da solicitação |
| Corpo |objeto |Objeto da solicitação |

#### <a name="response-action"></a>Ação de resposta
Estes são os campos de entrada da ação de Resposta HTTP. Um * significa que é um campo obrigatório.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Código de status* |statusCode |O código de status HTTP |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de cabeçalhos de resposta a serem incluídos |
| Corpo |Corpo |O corpo da resposta |

## <a name="next-steps"></a>Próximas etapas
Agora, experimente a plataforma e [crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).


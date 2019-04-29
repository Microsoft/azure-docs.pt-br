---
title: Mensagens AS2 para integração de empresas B2B – aplicativos lógicos do Azure
description: Troca de mensagens AS2 nos aplicativos de lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760194"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

Para trabalhar com mensagens AS2 nos aplicativos lógicos do Azure, você pode usar o conector do AS2, que fornece os gatilhos e ações para o gerenciamento de comunicação do AS2. Por exemplo, para estabelecer confiabilidade e segurança ao transmitir mensagens, você pode usar essas ações:

* [**Codificar mensagem AS2** ação](#encode) para fornecer criptografia, digital de assinatura e confirmações por meio de notificações de disposição de mensagem (MDN), que ajuda a dar suporte a não-repúdio. Por exemplo, essa ação aplica cabeçalhos HTTP/AS2 e executa essas tarefas quando configurado:

  * Sinaliza mensagens de saída.
  * Criptografa mensagens de saída.
  * Compacta a mensagem.
  * Transmite o nome do arquivo no cabeçalho MIME.

* [**Decodificar mensagem AS2** ação](#decode) para fornecer a descriptografia, assinatura digital e confirmações por meio de notificações de disposição de mensagem (MDN). Por exemplo, essa ação executa estas tarefas: 

  * Processa cabeçalhos HTTP/AS2.
  * Reconcilia MDNs recebidas com as mensagens de saída originais.
  * Atualiza e correlaciona os registros no banco de dados não-repúdio.
  * Grava os registros para o relatório de status do AS2.
  * Conteúdo da carga de saídas como codificada em base64.
  * Determina se os MDNs são necessárias. Com base no AS2 contrato, determina se os MDNs devem ser síncronas ou assíncronas.
  * Gera MDNs síncronas ou assíncronas com base no contrato AS2.
  * Define as propriedades e os tokens de correlação em MDNs.

  Essa ação também executa as seguintes tarefas quando configurado:

  * Verifica a assinatura.
  * Descriptografa as mensagens.
  * Descompacta as mensagens. 
  * Verifique e não permitir duplicatas de ID de mensagem.

Este artigo mostra como adicionar o AS2 de codificação e decodificação de ações para um aplicativo lógico existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja usar o conector AS2 e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. O conector do AS2 fornece ações apenas, não gatilhos. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que tem associado com sua assinatura do Azure e vinculado ao aplicativo lógico em que você planeja usar o conector do AS2. Ambos os seu aplicativo e integração conta lógico deve existir no mesmo local ou na região do Azure.

* Pelo menos duas [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu na sua conta de integração usando o qualificador de identidade do AS2.

* Antes de usar o conector do AS2, você deve criar um AS2 [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) entre seus parceiros comerciais e armazenamento esse contrato em sua conta de integração.

* Se você usar [Azure Key Vault](../key-vault/key-vault-overview.md) para o gerenciamento de certificado, verifique o que permitem que as chaves do cofre a **Encrypt** e **descriptografar** operações. Caso contrário, a codificação e decodificação de ações de falham.

  No portal do Azure, vá até seu Cofre de chaves, exiba sua chave de cofre **operações permitidas**e confirme se o **Encrypt** e **descriptografar** operações são selecionadas.

  ![Verifique as operações de chave de cofre](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. Se você ainda não fez isso, nos [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No designer, adicione uma nova ação para seu aplicativo lógico. 

1. Sob **escolher uma ação** e a pesquisa, escolha **todos os**. Na caixa de pesquisa, digite "codificar as2" e selecione esta ação: **Codificar mensagem AS2**.

   ![Selecione "Codificar mensagem AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Se você não tiver uma conexão existente para sua conta de integração, você for solicitado a criar essa conexão agora. Nomeie sua conexão, selecione a conta de integração que você deseja se conectar e, em seguida, escolha **criar**.

   ![criar a conexão à conta de integração](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Agora fornecem informações sobre essas propriedades:

   | Propriedade | DESCRIÇÃO |
   |----------|-------------|
   | **AS2-de** | O identificador para o remetente da mensagem conforme especificado por seu contrato AS2 |
   | **AS2-para** | O identificador para o destinatário da mensagem conforme especificado por seu contrato AS2 |
   | **body** | A carga da mensagem |
   |||

   Por exemplo: 

   ![Propriedades de codificação de mensagem](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificar mensagens AS2

1. Se você ainda não fez isso, nos [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No designer, adicione uma nova ação para seu aplicativo lógico. 

1. Sob **escolher uma ação** e a pesquisa, escolha **todos os**. Na caixa de pesquisa, digite "decodificar as2" e selecione esta ação: **Decodificar mensagem AS2**

   ![Selecione "Mensagem de decodificação AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Se você não tiver uma conexão existente para sua conta de integração, você for solicitado a criar essa conexão agora. Nomeie sua conexão, selecione a conta de integração que você deseja se conectar e, em seguida, escolha **criar**.

   ![criar a conexão à conta de integração](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Para **corpo** e **cabeçalhos**, selecionar esses valores de saídas de gatilho ou ação anteriores.

   Por exemplo, suponha que seu aplicativo lógico recebe mensagens por meio de um gatilho de solicitação. Você pode selecionar as saídas de que o disparador.

   ![Selecione Corpo e Cabeçalhos de saídas de Solicitação](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Amostra

Para tentar implantar um aplicativo lógico totalmente operacional e o cenário de AS2 de exemplo, confira o[modelo e cenário de aplicativo lógico AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente conhecido como Swagger) de arquivos, consulte o [página de referência do conector](/connectors/as2/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

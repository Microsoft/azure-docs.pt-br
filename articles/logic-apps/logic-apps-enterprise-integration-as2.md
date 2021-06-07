---
title: Enviar e receber mensagens AS2 para B2B
description: Trocar mensagens AS2 para cenários de integração corporativa B2B usando aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91856663"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

> [!IMPORTANT]
> O conector AS2 original está sendo preterido, portanto, a menos que você precise de recursos de rastreamento, use o conector **AS2 (v2)** em vez disso. Exceto para acompanhamento, o conector v2 fornece os mesmos recursos da versão original, é nativo para o tempo de execução dos aplicativos lógicos e tem melhorias significativas de desempenho no tamanho da mensagem, na taxa de transferência e na latência. Além disso, o conector v2 não exige que você crie uma conexão com sua conta de integração. Em vez disso, conforme descrito em pré-requisitos, certifique-se de vincular sua conta de integração ao aplicativo lógico em que você planeja usar o conector.

Para trabalhar com mensagens AS2 em aplicativos lógicos do Azure, você pode usar o conector do AS2, que fornece gatilhos e ações que dão suporte e gerenciam a comunicação AS2 (versão 1,2). Por exemplo, para estabelecer a segurança e a confiabilidade ao transmitir mensagens, você pode usar estas ações:

* [Ação de **codificação AS2**](#encode) para fornecer criptografia, assinatura digital e confirmações por meio de notificações de disposição de mensagem (MDN), que ajudam a dar suporte a não-repúdio. Por exemplo, essa ação aplica cabeçalhos AS2/HTTP e executa essas tarefas quando configurado:

  * Assina mensagens de saída.
  * Criptografa mensagens de saída.
  * Compacta a mensagem.
  * Transmite o nome do arquivo no cabeçalho MIME.

* [Ação de **decodificação AS2**](#decode) para fornecer descriptografia, assinatura digital e confirmações por meio de notificações de disposição de mensagem (MDN). Por exemplo, essa ação executa estas tarefas:

  * Processa cabeçalhos AS2/HTTP.
  * Reconcilia o MDNs recebido com as mensagens de saída originais.
  * Atualiza e correlaciona registros no banco de dados de não-repúdio.
  * Grava registros para relatório de status AS2.
  * Gera o conteúdo da carga como codificado na base64.
  * Determina se MDNs são necessárias. Com base no contrato AS2, determina se MDNs deve ser síncrono ou assíncrono.
  * Gera MDNs síncronos ou assíncronos com base no contrato AS2.
  * Define os tokens de correlação e as propriedades em MDNs.

  Essa ação também executa essas tarefas quando configuradas:

  * Verifica a assinatura.
  * Descriptografa as mensagens.
  * Descompacta a mensagem.
  * Verificar e proibir duplicatas de ID de mensagem.

Este artigo mostra como adicionar as ações de codificação e decodificação AS2 a um aplicativo lógico existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico do qual você deseja usar o conector AS2 e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. O conector do AS2 fornece apenas ações, não gatilhos. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e vinculada ao aplicativo lógico no qual você planeja usar o conector do AS2. O aplicativo lógico e a conta de integração devem existir no mesmo local ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração usando o qualificador de identidade AS2.

* Antes de usar o conector do AS2, você deve criar um [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre seus parceiros comerciais e armazenar esse contrato em sua conta de integração.

* Se você usar [Azure Key Vault](../key-vault/general/overview.md) para o gerenciamento de certificados, verifique se as chaves do cofre permitem as operações **criptografar** e **descriptografar** . Caso contrário, as ações de codificação e decodificação falharão.

  Na portal do Azure, vá para a chave no cofre de chaves, examine as **operações permitidas** da chave e confirme se as operações **criptografar** e **descriptografar** estão selecionadas, por exemplo:

  ![Verificar operações de chave do cofre](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. Se você ainda não fez isso, na [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No designer, adicione uma nova ação ao seu aplicativo lógico.

1. Em **Escolher uma ação** e embaixo da caixa de pesquisa, selecione **Todos**. Na caixa de pesquisa, digite "AS2 Encode" e certifique-se de selecionar a ação AS2 (v2): **codificação AS2**

   ![Selecione "codificação AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Agora, forneça informações para essas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Mensagem a ser codificada** | A carga da mensagem |
   | **AS2 de** | O identificador do remetente da mensagem conforme especificado pelo seu contrato AS2 |
   | **AS2 para** | O identificador do destinatário da mensagem, conforme especificado pelo seu contrato AS2 |
   |||

   Por exemplo:

   ![Propriedades de codificação de mensagem](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Se você tiver problemas ao enviar mensagens assinadas ou criptografadas, considere a possibilidade de experimentar diferentes formatos de algoritmo SHA256. A especificação AS2 não fornece nenhuma informação sobre formatos SHA256, de modo que cada provedor usa sua própria implementação ou formato.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificar mensagens AS2

1. Se você ainda não fez isso, na [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No designer, adicione uma nova ação ao seu aplicativo lógico.

1. Em **Escolher uma ação** e embaixo da caixa de pesquisa, selecione **Todos**. Na caixa de pesquisa, insira "decodificação AS2" e certifique-se de selecionar a ação AS2 (v2): **decodificação AS2**

   ![Selecione "decodificação AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Para a **mensagem a ser codificada** e as propriedades de **cabeçalhos de mensagem** , selecione esses valores de saídas anteriores de gatilho ou ação.

   Por exemplo, suponha que seu aplicativo lógico receba mensagens por meio de um gatilho de solicitação. Você pode selecionar as saídas desse gatilho.

   ![Selecione Corpo e Cabeçalhos de saídas de Solicitação](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Amostra

Para tentar implantar um aplicativo lógico totalmente operacional e o cenário de AS2 de exemplo, confira o[modelo e cenário de aplicativo lógico AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/as2/). 

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE deste conector usa os [limites de mensagem B2B para o ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

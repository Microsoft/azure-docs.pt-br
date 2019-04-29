---
title: Criar Enterprise Integrations B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Receber dados de B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998963"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Receber dados de B2B com os Aplicativos Lógicos do Azure e o Enterprise Integration Pack

Depois de criar uma conta de integração com parceiros e contratos, você estará pronto para criar um fluxo de trabalho entre empresas (B2B) para seu aplicativo lógico com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar as ações AS2 e X12, você precisará de uma conta do Enterprise Integration. Saiba [como criar uma conta do Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Criar um aplicativo lógico com conectores B2B

Siga estas etapas para criar um aplicativo lógico B2B que usa as ações AS2 e X12 para receber dados de um parceiro comercial:

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Adicione a ação **Decodificar AS2** selecionando **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Insira a palavra **as2** na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selecione a ação **AS2 – Decodificar mensagem AS2**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adicione o **corpo** que você deseja usar como entrada. 
   Neste exemplo, selecione o corpo da solicitação HTTP que disparou o Aplicativo Lógico. Ou insira uma expressão que insere os cabeçalhos no campo **CABEÇALHOS**:

    @triggerOutputs()['headers']

7. Adicione os **cabeçalhos** necessários para AS2, que pode ser encontrado nos cabeçalhos de solicitação HTTP. 
   Neste exemplo, selecione o cabeçalho da solicitação HTTP que disparou o Aplicativo Lógico.

8. Agora, adicione a ação de mensagem X12 de decodificação. Escolha **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Insira a palavra **x12** na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selecione a ação **X12 – Decodificar mensagem X12**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Agora, você deve especificar a entrada para esta ação. 
    Essa entrada é a saída da ação AS2 anterior.

    O conteúdo real da mensagem está em um objeto JSON e é codificado em base64, então você deve especificar uma expressão como entrada. 
    Insira a seguinte expressão no campo de entrada **X12 MENSAGEM DO ARQUIVO SIMPLES PARA DECODIFICAÇÃO**:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Adicione etapas para decodificar os dados X12 recebidos de um parceiro comercial e produza itens de saída em um objeto JSON. 
    Para informar o parceiro que os dados foram recebidos, você pode enviar uma resposta que contém a Notificação de Disposição de Mensagem (MDN) do AS2 em uma Ação de Resposta HTTP.

12. Adicione a ação **Resposta** selecionando **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Para filtrar todas as ações para aquela que você deseja usar, insira a palavra **resposta** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selecione a ação **resposta**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Defina o campo **CORPO** usando a expressão a seguir para acessar a MDN da saída da ação **Decodificar mensagem X12**:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Salve seu trabalho.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Você concluiu a configuração de seu aplicativo lógico de B2B. Em um aplicativo real, convém armazenar os dados X12 decodificados em um aplicativo LOB ou no armazenamento de dados. Você pode adicionar mais ações ou gravar APIs personalizadas para se conectar a seus próprios aplicativos LOB e usar essas APIs em seu Aplicativo Lógico.

## <a name="features-and-use-cases"></a>Recursos e casos de uso

* As ações de codificação e decodificação AS2 e X12 permitem que você compartilhe dados entre parceiros comerciais usando protocolos padrão da indústria em aplicativos lógicos.
* Você pode usar as ações X12 e AS2 juntas ou separadamente para compartilhar dados com parceiros comerciais.
* As ações de B2B facilitam a criação de parceiros e acordos na conta do Integration e seu consumo em um Aplicativo Lógico.
* Estendendo o seu Aplicativo Lógico com outras ações, você pode enviar e receber dados de e para outros aplicativos e serviços, como o SalesForce.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

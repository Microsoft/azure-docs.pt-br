---
title: Codificar mensagens X12 - Aplicativos Lógicos do Azure | Microsoft Docs
description: Validar EDI e converter mensagens codificadas em XML com o codificador de mensagens X12 nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106497"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens X12 nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Com o conector de mensagem X12 de codificação você pode validar o EDI e as propriedades específicas de parceiro, converter as mensagens codificadas em XML em conjuntos de transações de EDI no intercâmbio e solicita uma Confirmação Técnica, Confirmação Funcional, ou ambas.
Para usar esse conector, você deve adicionar o conector para um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem X12 de codificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Pelo menos dois [contratos X12](logic-apps-enterprise-integration-x12.md) que já estão definidos em sua conta de integração

## <a name="encode-x12-messages"></a>Codificar mensagens do X12

1. [Criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem X12 de codificação não possui gatilhos, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.  Na caixa de pesquisa, digite "x12" como filtro. Selecione **X12 - Codificar mensagem do X12 pelo nome do contrato** ou **X12 - Codificar mensagem do X12 por identidades**.
   
    ![Procure por “x12”](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 
   
    ![conexão com a conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.

    ![conexão com a conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A conexão foi criada.

    ![detalhes da conexão com a conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar mensagens do X12 pelo nome do contrato

Se você optar por codificar mensagens X12 por nome do contrato, abra a lista **Nome do contrato X12**, insira ou selecione o contrato X12 existente. Insira a mensagem XML para codificar.

![Insira a mensagem XML e o nome do contrato X12 para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Codificar mensagens do X12 por identidades

Caso você opte por codificar as mensagens X12 por identidades, insira o identificador do remetente, o qualificador de remetente, o identificador do destinatário e o qualificador do destinatário conforme configurado no contrato X12. Selecione a mensagem XML para codificar.
   
![Forneça as identidades para o remetente e o destinatário, selecione a mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalhes de codificação do X12

O conector de Codificação X12 executa as seguintes tarefas:

* Resolução de contrato ao corresponder as propriedades de contexto do remetente e destinatário.
* Serializa o intercâmbio de EDI, convertendo mensagens codificadas em XML em conjuntos de transação EDI no intercâmbio.
* Aplica os segmentos de cabeçalho e rodapé do conjunto de transação
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de conteúdo
* Valida o EDI e as propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem
  * Validação de EDI executadas em elementos de dados do conjunto de transação.
  * Validação estendida executada em elementos de dados do conjunto de transação
* Solicita uma confirmação técnica e/ou funcional (se configurado).
  * Uma confirmação técnica é gerada como resultado da validação de cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho e rodapé de intercâmbio pelo destinatário no endereço
  * Uma confirmação técnica é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento de documentos recebidos

## <a name="view-the-swagger"></a>Exibir o swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


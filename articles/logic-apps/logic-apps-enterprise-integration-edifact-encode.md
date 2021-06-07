---
title: Codificar mensagens EDIFACT
description: Validar o EDI e gerar o XML com o codificador de mensagem EDIFACT para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82106518"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Com o conector de mensagem EDIFACT de codificação, você pode validar o EDI e as propriedades específicas de parceiro, gerar um documento XML para cada conjunto de transações e gerar uma Confirmação Técnica, Confirmação Funcional, ou ambas.
Para usar esse conector, você deve adicionar o conector para um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem EDIFACT de codificação. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido em sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

> [!IMPORTANT]
> O conector do EDIFACT dá suporte apenas a caracteres UTF-8.
> Se a saída contiver caracteres inesperados, verifique se suas mensagens EDIFACT usam o conjunto de caracteres UTF-8.

1. [Crie um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem EDIFACT de codificação não possui gatilhos, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.  Na caixa de pesquisa, digite "EDIFACT" como filtro. Selecione **Codificar Mensagem EDIFACT pelo nome do contrato** ou **Codificar mensagem EDIFACT por identidades**.
   
    ![pesquisar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar.

    ![criar conexão com a conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.

    ![detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A conexão foi criada.

    ![conexão com a conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar Mensagem EDIFACT pelo nome do contrato

Se você optar por codificar as mensagens EDIFACT pelo nome do contrato, abra a lista **Nome do contrato EDIFACT** e insira ou selecione o nome do contrato EDIFACT. Insira a mensagem XML para codificar.

![Insira o nome do contrato EDIFACT e a mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar Mensagem EDIFACT por identidades

Caso você opte por codificar as mensagens EDIFACT por identidades, insira o identificador do remetente, o qualificador de remetente, o identificador do destinatário e o qualificador do destinatário conforme configurado no contrato EDIFACT. Selecione a mensagem XML para codificar.

![Forneça as identidades para o remetente e o destinatário, selecione a mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalhes de codificação do EDIFACT

O conector EDIFACT de codificação executa as seguintes tarefas: 

* Resolver o contrato, correspondendo o qualificador de remetente e o identificador e o qualificador do destinatário e o identificador
* Serializa o intercâmbio de EDI, convertendo mensagens codificadas em XML em conjuntos de transação EDI no intercâmbio.
* Aplica os segmentos de cabeçalho e rodapé do conjunto de transação
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de conteúdo
* Valida o EDI e as propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem.
  * Validação de EDI executadas em elementos de dados do conjunto de transação.
  * Validação estendida executada em elementos de dados do conjunto de transação
* Gera um documento XML para cada conjunto de transação.
* Solicita uma confirmação técnica e/ou funcional (se configurado).
  * Como uma confirmação técnica, a mensagem CONTRL indica o recebimento de um intercâmbio.
  * Como uma confirmação funcional, a mensagem CONTRL indica a aceitação ou a rejeição do intercâmbio recebido, do grupo ou da mensagem, com uma lista de erros ou funcionalidade sem suporte

## <a name="view-swagger-file"></a>Exibir o arquivo do Swagger
Para exibir os detalhes de Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


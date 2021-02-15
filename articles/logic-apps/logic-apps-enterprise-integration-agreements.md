---
title: Contratos entre parceiros comerciais
description: Criar e gerenciar contratos entre parceiros comerciais usando aplicativos lógicos do Azure e Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992932"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Criar e gerenciar contratos de parceiros comerciais em Aplicativos Lógicos do Azure

Um acordo entre [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *agreement* ajuda as organizações e as empresas a se comunicarem diretamente entre si, definindo o protocolo específico do setor padrão a ser usado na troca de mensagens B2B (entre empresas). Os contratos fornecem benefícios comuns, por exemplo:

* Permita que as organizações troquem informações usando um formato bem conhecido.
* Melhore a eficiência ao conduzir transações B2B.
* São fáceis de criar, gerenciar e usar para a criação de soluções de integração corporativa.

Este artigo mostra como criar um contrato AS2, EDIFACT ou X12 que você pode usar ao criar soluções de integração corporativa para cenários B2B usando o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md). Depois de criar um contrato, você pode usar os conectores AS2, EDIFACT ou X12 para trocar mensagens B2B.

Para criar contratos para a troca de mensagens RosettaNet, consulte [Exchange RosettaNet messages](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefatos B2B. Esta conta de integração deve ser associada à sua assinatura do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já criou em sua conta de integração. Um contrato requer um parceiro de host e um parceiro convidado. Ambos os parceiros devem usar o mesmo qualificador "identidade de negócios" que o contrato que você deseja criar, como AS2, X12 ou EDIFACT.

* Opcional: o aplicativo lógico no qual você deseja usar seu contrato e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Para criar apenas sua conta de integração e artefatos B2B, você não precisa de um aplicativo lógico. No entanto, antes que seu aplicativo lógico possa usar os artefatos B2B em sua conta de integração, você deve vincular sua conta de integração ao seu aplicativo lógico. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Criar contratos

1. Entre no [portal do Azure](https://portal.azure.com).
No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira "integração" como seu filtro. Nos resultados, selecione este recurso: **contas de integração**

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Em **contas de integração**, selecione a conta de integração na qual você deseja criar o contrato.

   ![Selecione a conta de integração em que o contrato deve ser criado](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. No painel direito, em **componentes**, escolha o bloco **contratos** .

   ![Escolher "contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Em **Contratos**, escolha **Adicionar**. No painel **Adicionar** , forneça informações sobre seu contrato, por exemplo:

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*nome do contrato*> | O nome do seu contrato |
   | **Tipo de contrato** | Yes | **AS2**, **X12** ou **EDIFACT** | O tipo de protocolo para seu contrato. Quando você cria o arquivo de contrato, o conteúdo desse arquivo deve corresponder ao tipo de contrato. | |  
   | **Parceiro de Host** | Yes | <*nome do parceiro do host*> | O parceiro host representa a organização que especifica o contrato |
   | **Identidade do Host** | Yes | <*identificador de parceiro de host*> | O identificador do parceiro do host |
   | **Parceiro Convidado** | Yes | <*convidado-nome do parceiro*> | O parceiro convidado representa a organização que está fazendo negócios com o parceiro host |
   | **Identidade do Convidado** | Yes | <*convidado – identificador de parceiro*> | O identificador do parceiro convidado |
   | **Configurações de Recebimento** | Varia | Varia | Essas propriedades especificam como o parceiro host recebe todas as mensagens de entrada do parceiro convidado no contrato. Para obter mais informações, consulte o respectivo tipo de contrato: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   | **Configurações de Envio** | Varia | Varia | Essas propriedades especificam como o parceiro host envia todas as mensagens de saída para o parceiro convidado no contrato. Para obter mais informações, consulte o respectivo tipo de contrato: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > A resolução de um contrato depende da correspondência desses itens definidos no parceiro e na mensagem de entrada:
   >
   > * O qualificador e o identificador do remetente
   > * O qualificador e o identificador do destinatário
   >
   > Se esses valores mudarem para seu parceiro, certifique-se de atualizar o contrato também.

1. Quando terminar de criar seu contrato, na página **Adicionar** , escolha **OK** e retorne à sua conta de integração.

   A lista de **contratos** agora mostra seu novo contrato.

## <a name="edit-agreements"></a>Editar contratos

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, insira "integração" como seu filtro. Nos resultados, selecione este recurso: **contas de integração**

1. Em **contas de integração**, selecione a conta de integração que tem o contrato que você deseja editar.

1. No painel direito, em **componentes**, escolha o bloco **contratos** .

1. Em **contratos**, selecione seu contrato e escolha **Editar**.

1. Faça e salve suas alterações.

## <a name="delete-agreements"></a>Excluir contratos

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, insira "integração" como seu filtro. Nos resultados, selecione este recurso: **contas de integração**

1. Em **contas de integração**, selecione a conta de integração que tem o contrato que você deseja excluir.

1. No painel direito, em **componentes**, escolha o bloco **contratos** .

1. Em **contratos**, selecione seu contrato e escolha **excluir**.

1. Confirme que você realmente deseja excluir o contrato selecionado.

## <a name="next-steps"></a>Próximas etapas

* [Trocar mensagens AS2](logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Trocar mensagens X12](logic-apps-enterprise-integration-x12.md)

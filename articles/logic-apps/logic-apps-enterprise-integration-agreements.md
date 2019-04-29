---
title: Criar e gerenciar acordos entre parceiros comerciais - aplicativos lógicos do Azure
description: Criar e gerenciar acordos entre parceiros comerciais usando aplicativos lógicos do Azure e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761285"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Criar e gerenciar acordos entre parceiros comerciais usando aplicativos lógicos do Azure e o Enterprise Integration Pack

Um [parceiro comercial](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*contrato* ajuda as organizações e empresas que se comunicam diretamente uns com os outros, definindo o protocolo padrão da indústria específico a ser usado ao trocar mensagens de Business-to-business (B2B). Os contratos fornecem benefícios comuns, por exemplo:

* Permitir que as organizações a troca de informações usando um formato conhecido.
* Melhore a eficiência ao conduzir transações B2B.
* São fáceis de criar, gerenciar e usar para criar soluções de integração de enterprise.

Este artigo mostra como criar um AS2, EDIFACT ou X12 contrato que você pode usar ao criar soluções de integração para cenários de B2B do enterprise usando o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e [aplicativos de lógicos do Azure](../logic-apps/logic-apps-overview.md). Depois de criar um contrato, você pode usar X12, EDIFACT ou AS2 conectores para troca de mensagens de B2B.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefatos B2B. Essa conta de integração deve estar associada com sua assinatura do Azure.

* Pelo menos duas [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já criou sua conta de integração. Um contrato requer um parceiro de host e um parceiro convidado. Ambos os parceiros devem usar o mesmo qualificador "identidade de negócios" como o contrato que você deseja criar, como o AS2, X12 ou EDIFACT.

* Opcional: O aplicativo lógico em que você deseja usar seu contrato e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Para criar sua conta de integração e artefatos B2B, não é necessário um aplicativo lógico. No entanto, antes de seu aplicativo lógico pode usar os artefatos B2B em sua conta de integração, você deve vincular sua conta de integração ao seu aplicativo lógico. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Criar contratos

1. Entre no [Portal do Azure](https://portal.azure.com).
No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite "integração" como filtro. Nos resultados, selecione esse recurso: **Contas de integração**

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Sob **contas de integração**, selecione a conta de integração onde você deseja criar o contrato.

   ![Selecione a conta de integração em que o contrato deve ser criado](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. No painel direito, sob **componentes**, escolha o **contratos** lado a lado.

   ![Escolha "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Em **Contratos**, escolha **Adicionar**. No **adicionar** painel, forneça informações sobre o contrato, por exemplo:

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriedade | Necessário | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*agreement-name*> | O nome para o seu contrato |
   | **Tipo de contrato** | Sim | **AS2**, **X12**, ou **EDIFACT** | O tipo de protocolo para o seu contrato. Quando você cria seu arquivo do contrato, o conteúdo desse arquivo deve corresponder ao tipo de contrato. | |  
   | **Parceiro de host** | Sim | <*host-partner-name*> | O parceiro host representa a organização que especifica o contrato |
   | **Host Identity** | Sim | <*host-partner-identifier*> | Identificador do parceiro de host |
   | **Parceiro convidado** | Sim | <*guest-partner-name*> | O parceiro convidado representa a organização que está fazendo negócios com o parceiro host |
   | **Identidade do convidado** | Sim | <*guest-partner-identifier*> | Identificador do parceiro convidado |
   | **Configurações de recebimento** | Varia | Varia | Essas propriedades especificam como lidar com todas as mensagens de entrada recebidas pelo contrato. Para obter mais informações, consulte o tipo de contrato respectiva: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   | **Configurações de envio** | Varia | Varia | Essas propriedades especificam como lidar com todas as mensagens de saída enviadas pelo contrato. Para obter mais informações, consulte o tipo de contrato respectiva: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Quando você terminar a criação de seu contrato, no **Add** , escolha **Okey**e retornar à sua conta de integração.

   O **contratos** lista agora mostra o novo contrato.

## <a name="edit-agreements"></a>Editar contratos

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, digite "integração" como filtro. Nos resultados, selecione esse recurso: **Contas de integração**

1. Sob **contas de integração**, selecione a conta de integração que contém o contrato que você deseja editar.

1. No painel direito, sob **componentes**, escolha o **contratos** lado a lado.

1. Sob **contratos**, selecione seu contrato e escolha **editar**.

1. Verifique e, em seguida, salve as alterações.

## <a name="delete-agreements"></a>Excluir contratos

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, digite "integração" como filtro. Nos resultados, selecione esse recurso: **Contas de integração**

1. Sob **contas de integração**, selecione a conta de integração que contém o contrato que você deseja excluir.

1. No painel direito, sob **componentes**, escolha o **contratos** lado a lado.

1. Sob **contratos**, selecione seu contrato e escolha **excluir**.

1. Confirme que você realmente deseja excluir o contrato selecionado.

## <a name="next-steps"></a>Próximas etapas

* [Trocar mensagens AS2](logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Troca de mensagens X12](logic-apps-enterprise-integration-x12.md)

---
title: Adicionar parceiros comerciais para integrações do B2B – aplicativos lógicos do Azure
description: Criar parceiros comerciais em sua conta de integração para usar com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330201"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Adicionar parceiros comerciais para contas de integração de aplicativos lógicos do Azure

Na [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você pode criar automatizados business-to-business (B2B) integração os fluxos de trabalho usando uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com seus aplicativos lógicos. Para representar sua organização e outros, você cria e adicionar parceiros comerciais como artefatos à sua conta de integração. Os parceiros são entidades que participem de transações B2B e trocam mensagens entre si.

Antes de criar esses parceiros, certifique-se discutir e compartilhar informações com seus parceiros sobre como identificar e validar as mensagens que o outro envia. Depois que você concordar com esses detalhes, você estará pronto para criar parceiros em sua conta de integração.

## <a name="partner-roles-in-integration-accounts"></a>Funções de parceiro em contas de integração

Para definir os detalhes sobre as mensagens trocadas com seus parceiros, você cria e adiciona [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md) como artefatos à sua conta de integração. Contratos exigem pelo menos dois parceiros em sua conta de integração. Sua organização é sempre o *parceiro host* no contrato. A organização que troca mensagens com a sua organização é o *parceiro convidado*. Um parceiro convidado pode ser outra empresa, ou até mesmo um departamento em sua organização. Depois de adicionar esses parceiros, você pode criar um contrato.

Em um contrato, você deve especificar os detalhes para lidar com mensagens de entrada e saídas do ponto de vista do parceiro de host. Mensagens de entrada, o **configurações de recebimento** especificar como o parceiro host recebe as mensagens do parceiro convidado no contrato. Mensagens de saída, o **configurações de envio** especificar como o parceiro host envia mensagens ao parceiro convidado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seus parceiros, contratos e outros artefatos B2B. Essa conta de integração deve estar associada com sua assinatura do Azure.

## <a name="create-partner"></a>Criar parceiro

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite "integração" e selecione **contas de integração**.

   ![Selecione "Contas de integração"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar seus parceiros.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Em **parceiros**, escolha **Adicionar**. Sob **adicionar parceiro**, forneça os detalhes do parceiro, conforme descrito pela tabela a seguir.

   ![Escolha "Adicionar" e forneça detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do parceiro |
   | **Qualifier** | Sim | O corpo de autenticação que fornece a identidades comerciais exclusivas para as organizações, por exemplo, **D-U-N-S (Dun & Bradstreet)** . <p>Os parceiros podem optar por uma identidade de negócios mutuamente definido. Para esses cenários, selecione **mutuamente definido** para EDIFACT ou **mutuamente definido (X12)** para X12. <p>Para RosettaNet, selecione apenas **DUNS**, que é o padrão. |
   | **Valor** | Sim | Um valor que identifica os documentos que recebem seus aplicativos lógicos. <p>Para RosettaNet, esse valor deve ser um número de nove dígitos que corresponde ao número DUNS. |
   ||||

   > [!NOTE]
   > Para parceiros que utilizam RosettaNet, você pode especificar informações adicionais criando primeiro esses parceiros e, em seguida [editá-los posteriormente](#edit-partner).

1. Quando terminar, escolha **OK**.

   Seu novo parceiro aparece agora na **parceiros** lista. Além disso, o **parceiros** bloco atualiza o número atual de parceiros.

   ![Novo parceiro](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Editar parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração.
Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Sob **parceiros**, selecione o parceiro que você deseja editar e escolha **editar**. Sob **editar**, faça as alterações.

   ![Fazer e salvar suas alterações](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Para RosettaNet, sob **propriedades do parceiro RosettaNet**, você pode especificar essas informações adicionais:

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | **Classificação de parceiro** | Não | Tipo de organização do parceiro |
   | **Código de cadeia de suprimentos** | Não | Código do parceiro suprimentos cadeia, por exemplo, "Tecnologia da informação" ou "Componentes eletrônicos" |
   | **Nome de contato** | Não | Nome de contato do parceiro |
   | **Email** | Não | Endereço de email do parceiro |
   | **Fax** | Não | Número de fax do parceiro |
   | **telefone** | Não | Número de telefone do parceiro |
   ||||

1. Quando terminar, escolha **Okey** para salvar suas alterações.

## <a name="delete-partner"></a>Excluir parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Em **Parceiros**, selecione o parceiro que deseja excluir. Clique em **Excluir**.

   ![Excluir parceiro](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
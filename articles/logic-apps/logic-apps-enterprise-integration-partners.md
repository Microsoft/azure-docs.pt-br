---
title: Adicionar parceiros comerciais para integrações B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar parceiros comerciais para a conta de integração nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 137ed89c276338b534cad8fdf81ec31b5e5610b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845873"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Adicionar parceiros comerciais para contas de integração nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Os parceiros são as entidades que participam de mensagens e transações B2B (entre empresas). Antes de criar os parceiros que representam você e a outra organização nessas transações, é necessário que ambas as partes compartilhem informações que identificam e validam as mensagens enviadas. Após a discussão desses detalhes e o início da relação de negócios, é possível criar parceiros em sua conta de integração para representar as duas partes.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Quais são as funções dos parceiros na conta de integração?

Para definir os detalhes sobre as mensagens trocadas entre parceiros, crie contratos entre tais parceiros. No entanto, antes de criar um contrato, é necessário adicionar pelo menos dois parceiros à sua conta de integração. Sua organização deve fazer parte do contrato como **parceiro do host**. O outro parceiro, ou **parceiro convidado**, representa a organização que troca mensagens com a sua empresa. O parceiro convidado pode ser outra empresa ou até mesmo um departamento dentro da sua organização.

Depois de adicionar esses parceiros, você pode criar um contrato.

As configurações de Recebimento e Envio são orientadas do ponto de vista do Parceiro host. Por exemplo, as configurações de Recebimento em um contrato determinam como o parceiro host recebe as mensagens enviadas de um parceiro convidado. Da mesma forma, as configurações de Envio no contrato indicam como o parceiro host envia mensagens ao parceiro convidado.

## <a name="create-partner"></a>Criar parceiro

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu principal do Azure, selecione **Todos os serviços**. Insira “integração” na caixa de pesquisa e, em seguida, selecione **Contas de Integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar seus parceiros.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Escolha o bloco **Parceiros**.

   ![Escolha "Parceiros"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Em **parceiros**, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Insira um nome para seu parceiro e, em seguida, escolha um **Qualificador**. Insira um **Valor** para identificar documentos que recebem seus aplicativos. Quando terminar, escolha **OK**.

   ![Adicionar detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Escolha o bloco **Parceiros** novamente.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Seu novo parceiro é exibido agora. 

   ![Exibir novo parceiro](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Editar parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Em **Parceiros**, selecione o parceiro que deseja editar.

   ![Selecionar o parceiro a ser excluído](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Em **Atualizar Parceiro**, faça as alterações.
Após terminar, escolha **Salvar**. 

   ![Fazer e salvar suas alterações](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Para cancelar as alterações, selecione **Descartar**.

## <a name="delete-partner"></a>Excluir parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Em **Parceiros**, selecione o parceiro que deseja excluir.
Clique em **Excluir**.

   ![Excluir parceiro](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  


---
title: "Criar parceiros para mensagens B2B (entre empresas) — Aplicativos Lógicos do Azure"
description: "Saiba como adicionar parceiros à sua conta de integração com o Enterprise Integration Pack e os Aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Adicionar ou atualizar parceiros em contratos B2B no seu fluxo de trabalho

Os parceiros são as entidades que participam de mensagens e transações B2B (entre empresas). Antes de criar os parceiros que representam você e a outra organização nessas transações, é necessário que ambas as partes compartilhem informações que identificam e validam as mensagens enviadas. Após a discussão desses detalhes e o início da relação de negócios, é possível criar parceiros em sua conta de integração para representar as duas partes.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>Quais são as funções dos parceiros na conta de integração?

Para definir os detalhes sobre as mensagens trocadas entre parceiros, crie contratos entre tais parceiros. No entanto, antes de criar um contrato, é necessário adicionar pelo menos dois parceiros à sua conta de integração. Sua organização deve fazer parte do contrato como **parceiro do host**. O outro parceiro, ou **parceiro convidado**, representa a organização que troca mensagens com a sua empresa. O parceiro convidado pode ser outra empresa ou até mesmo um departamento dentro da sua organização.

Depois de adicionar esses parceiros, você pode criar um contrato.

As configurações de Recebimento e Envio são orientadas do ponto de vista do Parceiro host. Por exemplo, as configurações de recebimento em um contrato determinam como o parceiro host recebe as mensagens enviadas de um parceiro convidado. Da mesma forma, as configurações de envio no contrato indicam como o parceiro host envia mensagens ao parceiro convidado.

## <a name="how-to-create-a-partner"></a>Como criar um parceiro?

1. No portal do Azure, selecione **Procurar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Insira **integração** na caixa de pesquisa do filtro e, em seguida, selecione **Contas de Integração** na lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Escolha a conta de integração à qual você deseja adicionar parceiros.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Escolha o bloco **Parceiros** .

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Na folha Parceiros, escolha **Adicionar**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Insira um nome para seu parceiro e, em seguida, escolha um **Qualificador**. Por fim, insira um **Valor** para ajudar a identificar os documentos que entram em seus aplicativos.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Para ver o progresso do processo de criação do seu parceiro, selecione o ícone de notificação em forma de *sino*.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Para confirmar que seus novos parceiros foram adicionados com êxito, selecione o bloco **Parceiros**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Depois de selecionar o bloco Parceiros, você também verá os parceiros recém-adicionados na folha Parceiros.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Como editar os parceiros existentes em sua conta de integração

1. Escolha o bloco **Parceiros** .
2. Após a folha Parceiros se abrir, selecione o parceiro que deseja editar.
3. No bloco **Atualizar Parceiro**, faça as alterações.
4. Depois de terminar, escolha **Salvar** ou, para cancelar as alterações, selecione **Descartar**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Como excluir um parceiro

1. Escolha o bloco **Parceiros** .
2. Após a folha Parceiros se abrir, selecione o parceiro que deseja excluir.
3. Clique em **Excluir**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  


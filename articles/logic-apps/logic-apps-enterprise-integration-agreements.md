---
title: "Contratos de comunicação B2B – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie contratos para que parceiros possam se comunicar em cenários B2B no Aplicativo Lógico do Azure e no Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.openlocfilehash: 7ce0860272901f3b4e4cf3d63f7361d539f64741
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Contratos entre parceiros para comunicação B2B com o Aplicativo Lógico do Azure e o Enterprise Integration Pack

Os contratos permitem que entidades de negócios se comuniquem diretamente usando protocolos padrão da indústria e são a base da comunicação B2B (entre empresas). Ao habilitar cenários B2B para aplicativos lógicos com o Enterprise Integration Pack, um contrato é um acordo de comunicação entre parceiros comerciais B2B. Esse contrato se baseia na comunicação que os dois parceiros desejam estabelecer e é específico com relação a transporte ou protocolo.

A integração corporativa dá suporte a estes padrões de protocolo/transporte:

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Por que usar contratos

Estes são alguns benefícios comuns do uso de contratos:

* Permite que diferentes organizações e empresas troquem informações em um formato conhecido.
* Melhora a eficiência ao conduzir transações B2B
* Fácil de criar, gerenciar e usar durante a criação de aplicativos de integração corporativa

## <a name="how-to-create-agreements"></a>Como criar contratos

* [Criar um contrato AS2](logic-apps-enterprise-integration-as2.md)
* [Criar um contrato X12](logic-apps-enterprise-integration-x12.md)
* [Criar um contrato EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Como usar um contrato

É possível criar [aplicativos lógicos](logic-apps-what-are-logic-apps.md "Saiba mais sobre Aplicativos Lógicos") com recursos de B2B usando um contrato criado por você.

## <a name="how-to-edit-an-agreement"></a>Como editar um contrato

Você pode editar qualquer contrato seguindo essas etapas:

1. Selecione a conta de integração que contém o contrato que você deseja atualizar.

2. Escolha o bloco **Contratos**.

3. Na folha **Contratos**, selecione o contrato.

4. Escolha **Editar**. Faça as alterações.

5. Para salvá-las, clique em **OK**.

## <a name="how-to-delete-an-agreement"></a>Como excluir um contrato

É possível excluir qualquer contrato seguindo estas etapas:

1. Selecione a conta de integração que contém o contrato que você deseja excluir.
2. Escolha o bloco **Contratos**.
3. Na folha **Contratos**, selecione o contrato.
4. Clique em **Excluir**.
5. Confirme que você realmente deseja excluir o contrato selecionado.

    A folha Contratos não mostrará o contrato excluído.

## <a name="next-steps"></a>Próximas etapas
* [Criar um contrato AS2](logic-apps-enterprise-integration-as2.md)

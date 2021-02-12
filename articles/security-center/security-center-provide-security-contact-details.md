---
title: Configurar notificações por email para alertas da Central de Segurança do Azure
description: Saiba como ajustar os tipos de emails enviados pela Central de Segurança do Azure para alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
ms.author: memildin
ms.openlocfilehash: 72ded01b141aafb7fd3e4d761882a10eaf0c4b33
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920402"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Configurar notificações por email para alertas de segurança 

Os alertas de segurança precisam acessar as pessoas certas em sua organização. Por padrão, a Central de Segurança envia os proprietários da assinatura sempre que um alerta de alta severidade é disparado para a assinatura deles. Esta página explica como personalizar essas notificações.

Para definir suas preferências para emails de notificação, a página de configurações **Notificações por email** da Central de Segurança do Azure permite que você escolha:

- **_quem_ deve ser notificado** – os emails podem ser enviados a indivíduos selecionados ou a qualquer pessoa com uma função do Azure especificada para uma assinatura. 
- **_sobre o que_ eles devem ser notificados** – modifique os níveis de severidade para os quais a Central de Segurança deve enviar notificações.

Para evitar o excesso de alertas, a Central de Segurança limita o volume de emails enviados. Para cada assinatura, a Central de Segurança envia:

- no máximo, um email a cada **6 horas** (quatro emails por dia) para alertas de **alta severidade**
- no máximo, um email a cada **12 horas** (dois emails por dia) para alertas de **severidade média**
- no máximo, um email a cada **24 horas** para alertas de **baixa severidade**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Como configurar os detalhes do contato que receberá os emails sobre os alertas de segurança." :::
 
## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|**Administrador de Segurança**<br>**Proprietário da assinatura** |
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>Personalizar as notificações por email de alertas de segurança<a name="email"></a>

Você pode enviar notificações por email a indivíduos ou a todos os usuários com funções específicas do Azure.

1. Na área **Preços e configurações** da Central de Segurança, selecione a assinatura relevante e **Notificações por email**.

1. Defina os destinatários para suas notificações com uma opção ou com as duas:

    - Na lista suspensa, selecione uma das funções disponíveis.
    - Insira endereços de email específicos separados por vírgulas. Não há um limite para o número de endereços de email que podem ser inseridos.

1. Para aplicar as informações de contato de segurança à sua assinatura, selecione **Salvar**.


## <a name="see-also"></a>Confira também
Para saber mais sobre alertas de segurança, confira as seguintes páginas:

- [Alertas de segurança – um guia de referência](alerts-reference.md): saiba mais sobre os alertas de segurança que você poderá ver no módulo Proteção contra Ameaças da Central de Segurança do Azure
- [Gerenciar alertas de segurança e responder a eles na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar alertas de segurança e responder a eles
- [Automação de fluxo de trabalho](workflow-automation.md): automatize respostas para alertas com a lógica de notificação personalizada
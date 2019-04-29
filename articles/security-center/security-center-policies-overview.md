---
title: Configurações da Central de Segurança do Azure | Microsoft Docs
description: Definir as configurações na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703516"
---
# <a name="security-center-settings"></a>Configurações da Central de Segurança
Este artigo apresenta uma visão geral das configurações na Central de Segurança.

As configurações a seguir podem ser acessadas em Política de segurança:

- **Coleta de dados**: Determina as configurações do provisionamento de agente e da [coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Política de segurança**: Determina quais controles a Central de Segurança monitora e recomenda. Você pode editar a [política de segurança](tutorial-security-policy.md) na Central de Segurança. Também é possível usar o [Azure Policy](tutorial-security-policy.md) para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gerenciamento. 
- **Notificações por email**: Determina os contatos de segurança e as configurações de [notificação por email](security-center-provide-security-contact-details.md).
- **Tipo de preço**: Define a [seleção de preço](security-center-pricing.md) gratuita ou padrão. A camada que você escolhe determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo. Você pode especificar uma camada para assinaturas e espaços de trabalho.

> [!NOTE]
> Você pode definir tudo isso por assinatura. Para workspaces, você pode definir apenas a coleta de dados e o tipo de preço.
>


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. Que significa que os usuários recebem a função de *proprietário*, *Colaborador*, ou *leitor* à assinatura do recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- **Leitor de segurança**: Tem direitos de exibição na Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não pode fazer alterações.
- **Administrador de segurança**: Tem os mesmos direitos de exibição que o *leitor de segurança* e também pode atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre as políticas de segurança na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, leia os seguintes artigos:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para suas assinaturas do Azure.
* [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): Saiba como as recomendações da Central de Segurança ajudam a proteger seus recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Segurança dos Dados da Central de Segurança do Azure](security-center-data-security.md): Saiba como a Central de Segurança gerencia e protege os dados.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Obtenha respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Obtenha as últimas informações e notícias de segurança do Azure.

---
title: Fornecer detalhes de contato de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como fornecer detalhes de contato de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2018
ms.author: rkarlin
ms.openlocfilehash: b6babf7d5d5a0f5796efa9418044366c6a135ed9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909257"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contato de segurança na Central de segurança do Azure
A Central de Segurança do Azure recomendará que você forneça detalhes de contato de segurança para sua assinatura do Azure se ainda não fez isso. Essas informações serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente têm sido acessados por uma pessoa não autorizada ou ilegal. O MSRC executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros.

Uma notificação por email é enviada na primeira ocorrência diária de um alerta e apenas para alertas de gravidade alta. As preferências de email só podem ser configuradas para políticas de assinatura. Grupos de recursos dentro de uma assinatura herdarão essas configurações. 

Notificações de email de alerta são enviadas:
- Somente para alertas de gravidade alta
- Para um único destinatário de email por tipo de alerta por dia  
- Não mais do que 3 mensagens de email são enviadas para um único destinatário em um único dia
- Cada mensagem de email contém um único alerta, não uma agregação de alertas
 
Por exemplo, se uma mensagem de email já foi enviada para alertá-lo sobre um ataque de RDP, você não receberá outra mensagem de email sobre um ataque de RDP no mesmo dia, mesmo se outro alerta for disparado. 
 

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Em **Recomendações**, selecione **Fornecer detalhes de contato de segurança**.
   ![Fornecer contato de segurança][1]
2. Selecione a assinatura do Azure para fornecer informações de contato em.
3. Isso abre **Notificações por email**.

   ![Fornecer detalhes de contato de segurança][2]

   * Insira o endereço de email de contato de segurança ou endereços separados por vírgulas. Não há um limite para o número de endereços de email que você pode inserir.
   * Insira um número de telefone internacional de contato de segurança.
   * Para receber emails sobre alertas de gravidade alta, ative a opção **Enviar-me emails sobre alertas**.
   * Futuramente, você terá a opção de enviar notificações por email aos proprietários da assinatura. Esta opção está esmaecida no momento.
   * Selecione **Salvar** para aplicar as informações de contato de segurança à sua assinatura.

## <a name="see-also"></a>Consulte também
Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png

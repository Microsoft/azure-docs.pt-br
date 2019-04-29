---
title: Monitorar identidade e acesso na Central de Segurança do Azure | Microsoft Docs
description: Aprenda a usar os recursos de identidade e acesso na Central de Segurança do Azure para monitorar a atividade de acesso e os problemas com identidade do usuário.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cb8d494d32f2a9c192418bfab2d5ca9b679c5fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704859"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitore a identidade e o acesso na Central de Segurança do Azure (Visualizar)
Este artigo ajuda você a usar a Central de Segurança do Azure para monitorar a identidade e a atividade de acesso do usuário.

> [!NOTE]
> O monitoramento de identidade e acesso está em pré-visualização e disponível apenas na camada Padrão da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>

A identidade deve ser o plano de controle de sua empresa e a proteção de sua identidade deve ser sua prioridade. O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e aplicativos se mudando para a nuvem, a identidade se tornou o novo perímetro.

Ao monitorar as atividades de identidade, você poderá agir de forma proativa antes que um incidente local ocorra ou agir de forma reativa para interromper uma tentativa de ataque. O painel Identity & Access fornece recomendações como:

- Habilite a MFA para contas privilegiadas em sua assinatura
- Remova contas externas com permissões de gravação da sua assinatura
- Remova contas externas privilegiadas da sua assinatura

> [!NOTE]
> Se a sua assinatura tiver mais de 600 contas, o Security Center não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações que não são executadas estão listadas em "avaliações indisponíveis", que é discutido abaixo.
A Central de Segurança não pode executar as recomendações de identidade em relação aos agentes de administração de um parceiro do provedor de soluções de nuvem (CSP).
>
>

Ver [recomendações](security-center-identity-access.md#recommendations) para obter uma lista das recomendações de acesso e identidade fornecidas pela Central de segurança.

## <a name="monitoring-security-health"></a>Monitoramento de integridade da segurança
Você pode monitorar o estado de segurança de seus recursos na **Central de segurança – visão geral** painel. A seção **Resources** é um indicador de integridade que mostra as gravidades de cada tipo de recurso.

Você pode visualizar uma lista de todos os problemas selecionando **Recomendações**. Sob **recursos**, você pode exibir uma lista de problemas específicos para computação e aplicativos, segurança de dados, conexões de rede, ou identidade e acesso. Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

Para obter uma lista completa das recomendações de identidade e acesso, consulte [recomendações](security-center-identity-access.md#recommendations).

Para continuar, selecione **identidade e acesso** sob **recursos** ou no menu principal da Central de segurança.

![Painel da Central de Segurança][1]

## <a name="monitor-identity-and-access"></a>Monitorar a identidade e acesso
Em **Identidade e acesso**, há duas guias:

- **Visão geral**: recomendações identificadas pela Central de segurança.
- **Assinaturas**: lista de suas assinaturas e o estado de segurança atual de cada um.

![Identidade e acesso][2]

### <a name="overview-section"></a>Seção de visão geral
Sob **visão geral**, há uma lista de recomendações. A primeira coluna lista a recomendação. A segunda coluna mostra o número total de assinaturas afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema.

1. Selecione uma recomendação. A janela da recomendação é aberta e exibe:

   - Descrição da recomendação
   - Lista de assinaturas insalubres e saudáveis
   - Lista de recursos que não são verificados devido a uma avaliação com falha ou o recurso está sob uma assinatura em execução no nível Gratuito e não é avaliado

   ![Janela da recomendação][3]

1. Selecione uma assinatura na lista para obter mais detalhes.

### <a name="subscriptions-section"></a>Seção de assinaturas
Em **Assinaturas**, há uma lista de assinaturas. A primeira coluna lista as assinaturas. A segunda coluna mostra o número total de recomendações para cada assinatura. A terceira coluna mostra a severidade dos problemas.

![Guia da assinatura][4]

1. Selecione uma assinatura. Abre uma exibição de resumo com três guias:

   - **Recomendações**: com base nas avaliações realizadas pela Central de segurança que falhou.
   - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
   - **Avaliações indisponíveis**: lista de avaliações que falharam devido a um erro ou porque a assinatura tiver mais de 600 contas.

   Sob **recomendações** é uma lista das recomendações para a assinatura selecionada e a gravidade de cada recomendação.

   ![Recomendações para selecionar a assinatura][5]

1. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de assinaturas não íntegras e saudáveis e uma lista de recursos não verificados.

   ![Descrição da recomendação][6]

   Em **Avaliações aprovadas**, há uma lista de avaliações aprovadas.  Gravidade dessas avaliações sempre é verde.

   ![Avaliações aprovadas][7]

1. Selecione uma avaliação aprovada da lista para uma descrição da avaliação e uma lista de assinaturas saudáveis. Existe um separador para subscrições não íntegras que lista todas as subscrições que falharam.

   ![Avaliações aprovadas][8]

## <a name="recommendations"></a>Recomendações
Use a tabela abaixo como referência para ajudá-lo a entender as recomendações de identidade e acesso disponíveis e o que cada uma delas se você aplicá-lo.

|Tipo de recurso|Classificação de segurança|Recomendações|DESCRIÇÃO|
|----|----|----|----|
|Assinatura|50|Habilitar a MFA para contas de Aplicativos de Gerenciamento do Microsoft Azure com permissões de proprietário em sua assinatura|Habilite a MFA (Autenticação Multifator) para todas as contas de assinatura com privilégios de administrador para evitar uma violação de conta ou recursos.|
|Assinatura|50|Habilitar a central de segurança em suas assinaturas |Habilite a central de segurança em todas as suas assinaturas para detecção avançada de ameaças, JIT, lista de permissões de aplicativos e recomendações avançadas |
|Assinatura|50|Habilitar a camada padrão da central de segurança em suas assinaturas |Habilite a camada padrão da central de segurança em todas as suas assinaturas para detecção avançada de ameaças, JIT, lista de permissões de aplicativos e recomendações avançadas.|
|Assinatura|40|Habilitar a MFA para contas de Aplicativos de Gerenciamento do Microsoft Azure com permissões de gravação em sua assinatura|Habilite a MFA (Autenticação Multifator) para todas as contas de assinatura com privilégios de gravação para evitar uma violação de conta ou recursos.|
|Assinatura|30|Remova contas externas com permissões de proprietário da sua assinatura|Remova contas externas com permissões de proprietário da sua assinatura para impedir o acesso não monitorado. |
|Assinatura|30|Habilitar a MFA para contas de Aplicativos de Gerenciamento do Microsoft Azure com permissões de leitura em sua assinatura|Habilite a MFA (Autenticação Multifator) para todas as contas de assinatura com privilégios de leitura para evitar uma violação de conta ou recursos.|
|Assinatura|25|Remova contas externas com permissões de gravação da sua assinatura|Remova contas externas com permissões de gravação da sua assinatura para impedir o acesso não monitorado. |
|Assinatura|20|Remover contas preteridas com permissões de proprietário da sua assinatura|Remova contas preteridas com permissões de proprietário das suas assinaturas.|
|Assinatura|5|Remover contas preteridas da sua assinatura|Remova contas preteridas de suas assinaturas para habilitar o acesso a apenas usuários atuais. |
|Assinatura|5|Designe mais de um proprietário na sua assinatura|Designe mais de um proprietário da assinatura para poder ter redundância de acesso de administrador.|
|Assinatura|5|Designe até três proprietários em sua assinatura|Designe menos de três proprietários de assinaturas para reduzir o potencial de violação por um proprietário comprometido.|
|Cofre de chaves|5|Habilitar logs de diagnóstico no Key Vault|Habilite os logs e retenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|Assinatura|15|Remova contas externas com permissões de leitura da sua assinatura|Remova contas externas com privilégios de leitura da sua assinatura para impedir o acesso não monitorado.|
|Assinatura|1|Fornecer detalhes de contato de segurança|Forneça informações de contato de segurança para cada uma das suas assinaturas. Informações de contato são um número de telefone e um endereço de email. As informações serão usadas para contatá-lo se nossa equipe de segurança acreditar que os recursos estão comprometidos|

> ![OBSERVAÇÃO] Se você criou uma política de acesso condicional que exija MFA, mas com exclusões definidas, a avaliação de recomendação de MFA da Central de Segurança considera a política sem conformidade, porque ela permite que alguns usuários entrem no Azure sem o MFA.
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

- [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger seus dados na Central de segurança do Azure e o serviço do SQL Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:
* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre respostas para perguntas frequentes sobre como usar a Central de Segurança.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png

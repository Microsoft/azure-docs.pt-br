---
title: "Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD | Microsoft Docs"
description: "Como solucionar problemas comuns enfrentados ao não ver os usuários que aparecem em uma galeria de aplicativo do Azure AD que você configurou para o provisionamento do usuário com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: 30db71c0706de1dcc0d48fb72fa5d62475a14ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD

Quando o provisionamento automático foi configurado para um aplicativo (incluindo verificar se as credenciais de aplicativo fornecidas no Azure AD para se conectar ao aplicativo são válidas). Em seguida, usuários e/ou grupos são provisionados para o aplicativo e o seguintes elementos são determinados:

-   Quais usuários e grupos têm sido **atribuídos** para o aplicativo. Para obter mais informações sobre atribuição, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Estejam os **mapeamentos de atributos** habilitados ou não, e configurados para sincronizar atributos válidos do Azure AD para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Se há ou não um **filtro de escopo** presente que está filtrando usuários com base nos valores de atributo específico. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Quando observar que os usuários não são provisionados, consulte os logs de auditoria no Azure AD e procure por entradas de log para um usuário específico.

Os logs de auditoria de provisionamento podem ser acessados no portal do Azure, na guia **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt; Logs de auditoria**. Filtre os logs na categoria de **provisionamento de conta**, para ver apenas os eventos de provisionamento para aquele aplicativo. Você pode procurar por usuários com base na "ID correspondente" que foi configurado para eles nos mapeamentos de atributo. Por exemplo, se você configurou o "nome UPN" ou "endereço de email" como o atributo correspondente no lado do Azure AD e o usuário não sendo provisionado tem um valor de "audrey@contoso.com". Pesquisar os logs de auditoria para "audrey@contoso.com" e rever as entradas retornadas.

Os logs de auditoria do provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo consultas ao Azure AD para usuários que estão no escopo para provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar

Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

* [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
* [Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar

Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrados nessa página depois de recarregamentos subsequentes, é provável que o serviço esteja em execução, mas não concluiu uma sincronização inicial ainda. Verifique os **logs de auditoria** descritos acima para determinar quais operações o serviço está executando, e se existem erros.

>[!NOTE]
>Uma sincronização inicial entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Sincronizações subsequentes após a sincronização inicial são mais rápidas, como o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial. Isso melhora o desempenho de sincronizações subsequentes.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de auditoria, é muito importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

-   **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento ser examinar e configurar os mapeamentos de atributo e fluxos de trabalho que definem quais fluxo propriedades de usuário (ou grupo) do Azure AD para o aplicativo. Isso inclui a configuração de "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade, habilitando ou desabilitando o **Mapeamento** para objetos de grupo mostrados na guia de **provisionamento**. Se o provisionamento de grupos é habilitado, certifique-se de rever os mapeamentos de atributos para garantir que um campo apropriado está sendo usado para a "ID correspondente". Este pode ser o nome de exibição ou alias de email, já que o grupo e seus membros não são provisionados se a propriedade correspondente está vazia ou não está preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Próximas etapas
[Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)


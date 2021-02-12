---
title: Os usuários não estão sendo provisionados no meu aplicativo
description: Como solucionar problemas comuns enfrentados ao não ver os usuários que aparecem em uma Galeria de Aplicativos do Azure AD que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 429f993764273d6b6eb23d75d0132b374d5d0ca0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256347"
---
# <a name="no-users-are-being-provisioned"></a>Nenhum usuário sendo provisionado 
>[!NOTE]
>A partir de 04/16/2020, alteramos o comportamento dos usuários com a função de acesso padrão atribuída. Consulte a seção abaixo para obter detalhes. 
>
Após o provisionamento automático ser configurado para um aplicativo (incluindo verificar se as credenciais de aplicativo fornecidas ao Azure AD para se conectar ao aplicativo são válidas), os usuários e/ou grupos são provisionados no aplicativo. O provisionamento é determinado pelos seguintes fatores:

-   Quais usuários e grupos têm sido **atribuídos** para o aplicativo. Observe que não há suporte para o provisionamento de grupos aninhados. Para obter mais informações sobre atribuição, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Estejam os **mapeamentos de atributos** habilitados ou não, e configurados para sincronizar atributos válidos do Azure AD para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
-   Se há ou não um **filtro de escopo** presente que está filtrando usuários com base nos valores de atributo específico. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Se você observar que os usuários não estão sendo provisionados, consulte os [logs de provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Pesquise por entradas de log referentes a um usuário específico.

Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **logs de provisionamento (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os logs de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo a consulta do Azure AD para usuários atribuídos que estão no escopo para provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar
Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

- [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
- [Logs de provisionamento dizem que os usuários são ignorados e não provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar
Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página após recargas subsequentes, é provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **logs de provisionamento (versão prévia)** descritos acima para determinar quais operações o serviço está executando e se há erros.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Sincronizações subsequentes após o ciclo inicial são mais rápidas, pois o serviço de provisionamento armazena marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial. O ciclo inicial melhora o desempenho das sincronizações subsequentes.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de provisionamento, digamos que os usuários sejam ignorados e não provisionados mesmo que sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de provisionamento, é importante examinar a guia **etapas** do log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre a atribuição, consulte [Atribuir acesso de usuário ou grupo](../manage-apps/assign-user-or-group-access-portal.md).
- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento é examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Essa configuração inclui a definição da "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". Essa ID correspondente pode ser o nome de exibição ou o alias do email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for populada para um grupo no Microsoft Azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Provisionando usuários atribuídos à função de acesso padrão
A função padrão em um aplicativo da galeria é chamada de função de "acesso padrão". Historicamente, os usuários atribuídos a essa função não são provisionados e são marcados como ignorados nos [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) devido a serem "não qualificados de forma efetiva". 

**Comportamento para provisionamento de configurações criadas após 04/16/2020:** Os usuários atribuídos à função de acesso padrão serão avaliados da mesma forma que todas as outras funções. Um usuário ao qual é atribuído o acesso padrão não será ignorado como "não é efetivamente qualificado". 

**Comportamento para provisionamento de configurações criadas antes de 04/16/2020:** Nos próximos 3 meses, o comportamento continuará como está hoje. Os usuários com a função de acesso padrão serão ignorados como não tendo sido efetivamente qualificados. Depois de julho de 2020, o comportamento será uniforme para todos os aplicativos. Não ignoraremos o provisionamento de usuários com a função de acesso padrão, pois "não é efetivamente qualificado". Essa alteração será feita pela Microsoft, sem a necessidade de ação do cliente. Se você quiser garantir que esses usuários continuem sendo ignorados, mesmo após essa alteração, aplique os filtros de escopo apropriados ou cancele a atribuição do usuário do aplicativo para garantir que eles estejam fora do escopo.  

Para perguntas sobre essas alterações, entre em contato com provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Próximas etapas

[Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)

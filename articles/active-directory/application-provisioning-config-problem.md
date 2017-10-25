---
title: "Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD | Microsoft Docs"
description: "Como solucionar problemas comuns enfrentados ao configurar provisionamento do usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD"
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 44e344095352f2bc6b27e389fc8be2cdf3e368d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD

Configurando [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para um aplicativo (onde houver suporte), requer que as instruções específicas ser seguidas para preparar o aplicativo para o provisionamento automático. Em seguida, você pode usar o portal do Azure para configurar o serviço de provisionamento para sincronizar contas de usuário ao aplicativo.

Você sempre deve começar encontrando o tutorial de instalação específica de configuração de provisionamento para seu aplicativo. Siga essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. Uma lista de tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Como saber se o provisionamento está funcionando 

Depois que o serviço estiver configurado, a maioria das informações sobre a operação do serviço podem ser obtidas de dois lugares:

-   **Logs de auditoria** – os logs de auditoria do provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo consultas ao Azure AD para usuários que estão no escopo para provisionamento. O aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema de consulta. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação. Os logs de auditoria de provisionamento podem ser acessados no portal do Azure, na guia **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt; Logs de auditoria**. Filtre os logs na categoria de **provisionamento de conta**, para ver apenas os eventos de provisionamento para aquele aplicativo.

-   **Provisionamento de status –** um resumo do último provisionamento executado para um determinado aplicativo pode ser visto na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** na parte inferior da tela sob as configurações de serviço. Esta seção resume quantos usuários (e/ou grupos) estão sendo sincronizados no momento entre os dois sistemas, e se houve erros. Detalhes do erro estão nos logs de auditoria. Observe que o status de provisionamento não seja preenchido até que uma sincronização inicial completa entre o Azure AD foi concluída e o aplicativo.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar

Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

* [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
* [Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar

Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página depois recarregamentos subsequentes. É provável que o serviço está em execução, mas não concluiu uma sincronização inicial ainda. Verifique os **logs de auditoria** descritos acima para determinar quais operações o serviço está executando, e se existem erros.

>[!NOTE]
>Uma sincronização inicial entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Sincronizações subsequentes após a sincronização inicial são mais rápidas, como o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando

Para o provisionamento funcionar, Azure AD requer credenciais válidas que permitem a conexão com uma API fornecida pelo aplicativo de gerenciamento de usuário. Se essas credenciais não funcionam, ou você não souber o que são, examine o tutorial para configurar esse aplicativo, descrito anteriormente.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de auditoria, é muito importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

-   **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento ser examinar e configurar os mapeamentos de atributo e fluxos de trabalho que definem quais fluxo propriedades de usuário (ou grupo) do Azure AD para o aplicativo. Isso inclui a configuração de "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre esse processo importante, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade, habilitando ou desabilitando o **Mapeamento** para objetos de grupo mostrados na guia de **provisionamento**. Se o provisionamento de grupos é habilitado, certifique-se de rever os mapeamentos de atributos para garantir que um campo apropriado está sendo usado para a "ID correspondente". Este pode ser o nome de exibição ou alias de email, já que o grupo e seus membros não são provisionados se a propriedade correspondente está vazia ou não está preenchida para um grupo no Azure AD.

#<a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)

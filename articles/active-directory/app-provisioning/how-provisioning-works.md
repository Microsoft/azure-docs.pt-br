---
title: Entenda como funciona o provisionamento do Azure Active Directory | Microsoft Docs
description: Entenda como funciona o provisionamento do Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 048adee21d5c2e49ef02f518002a1dc6025c1ecd
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988973"
---
# <a name="how-provisioning-works"></a>Como funciona o provisionamento

O provisionamento automático refere-se à criação de identidades e funções de usuário nos aplicativos de nuvem que os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Antes de iniciar uma implantação, você pode ler este artigo para saber como o provisionamento do Azure Active Directory funciona e obter recomendações de configuração. 

O **serviço de provisionamento do Azure Active Directory** provisiona usuários para aplicativos SaaS e outros sistemas a um ponto de extremidade da API de gerenciamento de usuários do Sistema de Gerenciamento de Usuários entre Domínios (SCIM) 2.0 fornecido pelo fornecedor do aplicativo. Esse ponto de extremidade do SCIM permite que o Azure Active Directory crie, atualize e remova usuários por meio de programação. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. O canal usado para provisionamento entre o Azure Active Directory e o aplicativo usa a criptografia HTTPS TLS 1.2.


![Serviço de provisionamento do Azure Active Directory](./media/how-provisioning-works/provisioning0.PNG)
*Figura 1: Serviço de provisionamento do Azure AD*

![Fluxo de trabalho de saída do provisionamento do usuário](./media/how-provisioning-works/provisioning1.PNG)
*Figura 2: fluxo de trabalho de provisionamento do usuário de "saída" do Azure AD para aplicativos SaaS populares*

![Fluxo de trabalho de entrada do provisionamento de usuário](./media/how-provisioning-works/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento do usuário de "entrada" de aplicativos de HCM (gerenciamento de capital humano) para o Azure Active Directory e o Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisionamento usando o SCIM 2.0

O serviço de provisionamento do Azure Active Directory usa o protocolo [SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) para provisionamento automático. O serviço se conecta ao ponto de extremidade do SCIM do aplicativo e usa o esquema de objeto do usuário do SCIM e as APIs REST para automatizar o provisionamento e desprovisionamento de usuários e grupos. Um conector de provisionamento que usa o SCIM é fornecido para a maioria dos aplicativos na galeria do Azure Active Directory. Ao criar aplicativos para o Azure Active Directory, os desenvolvedores podem usar a API de gerenciamento de usuário do SCIM 2.0 para criar um ponto de extremidade do SCIM que integra o Azure Active Directory para provisionamento. Para mais informações, consulte [Criar um ponto de extremidade SCIM e configurar o provisionamento do usuário](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Para solicitar um conector de provisionamento automático do Azure Active Directory para um aplicativo que não tem um, preencha uma [solicitação de aplicativo do Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorização

As credenciais são necessárias para que o Azure Active Directory se conecte à API de gerenciamento de usuários do aplicativo. Enquanto estiver configurando o provisionamento automático de usuário para um aplicativo, você precisará inserir credenciais válidas. Para aplicativos da galeria, você pode encontrar os requisitos e tipos de credenciais para o aplicativo consultando o tutorial do aplicativo. Para aplicativos que não são da galeria, você pode consultar a documentação do [scim](./use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery) para entender os tipos de credenciais e os requisitos. No portal do Azure, para testar as credenciais, faça com que o Azure Active Directory tente se conectar ao aplicativo de provisionamento de aplicativo com as credenciais fornecidas.

## <a name="mapping-attributes"></a>Atributos de mapeamento

Ao habilitar o provisionamento de usuário para um aplicativo SaaS de terceiros, o portal do Azure controlará os valores de seus atributos por meio do mapeamento de atributos. Os mapeamentos determinam os atributos de usuário que são transmitidos entre o Azure Active Directory e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário do Azure Active Directory e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos em conjunto com os Usuários, como Grupos.

Ao configurar o provisionamento, é importante examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) são transmitidas entre o Azure Active Directory e o aplicativo. Examine e configure a propriedade correspondente (**Corresponder objetos usando este atributo**) usada para identificar e corresponder usuários/grupos com exclusividade entre os dois sistemas.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Sendo assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar mapeamentos. Para mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS](./customize-application-attributes.md).

Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Para esses mapeamentos, você precisa escrever uma expressão semelhante a script que permite transformar os dados de usuários em formatos que são mais aceitáveis para o aplicativo SaaS. Para mais informações, consulte [Escrever expressões para mapeamentos de atributo](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Escopo baseado em atribuição

Para o provisionamento de saída do Azure Active Directory para um aplicativo SaaS, depender de [atribuições de usuário ou grupo](../manage-apps/assign-user-or-group-access-portal.md) é a maneira mais comum de determinar quais usuários estão no escopo para provisionamento. Como as atribuições de usuário também são usadas para habilitar o logon único, o mesmo método pode ser usado para gerenciar o acesso e o provisionamento. O escopo baseado em atribuição não se aplica a cenários de provisionamento de entrada, como o Workday e o Successfactors.

* **Grupos.** Com um plano de licença Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS. Em seguida, quando o escopo de provisionamento estiver definido como **Sincronizar somente usuários e grupos atribuídos**, o serviço de provisionamento do Azure Active Directory provisionará ou desprovisionará os usuários com base em sua associação a um grupo que é atribuído ao aplicativo. O objeto de grupo em si não é provisionado, a menos que o aplicativo dê suporte a objetos de grupo. Verifique se os grupos atribuídos ao seu aplicativo têm a propriedade "SecurityEnabled" definida como "True".

* **Grupos dinâmicos.** O serviço de provisionamento de usuários do Azure Active Directory pode ler e provisionar usuários em [grupos dinâmicos](../enterprise-users/groups-create-rule.md). Observe estas advertências e recomendações:

  * Grupos dinâmicos podem afetar o desempenho do provisionamento de ponta a ponta do Azure Active Directory para aplicativos SaaS.

  * A rapidez em que um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende de quanto tempo o grupo dinâmico pode avaliar as alterações de associação. Para informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [Verificar o status de processamento para uma regra de associação](../enterprise-users/groups-create-rule.md).

  * Um usuário perder a associação no grupo dinâmico é considerado um evento de desprovisionamento. Considere este cenário ao criar regras para grupos dinâmicos.

* **Grupos aninhados.** O serviço de provisionamento de usuário do Azure Active Directory não pode ler ou provisionar usuários em grupos aninhados. O serviço só pode ler e provisionar usuários que são membros imediatos de um grupo explicitamente atribuído. Essa limitação de "atribuições baseadas em grupo a aplicativos" também afeta o logon único (consulte [Usar um grupo para gerenciar o acesso a aplicativos SaaS](../enterprise-users/groups-saasapps.md)). Em vez disso, atribua ou, de outra forma, [coloque em escopo](define-conditional-rules-for-provisioning-user-accounts.md) os grupos que contêm os usuários que precisam ser provisionados.

### <a name="attribute-based-scoping"></a>Escopo baseado em atributo 

Você pode usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários são provisionados para um aplicativo. Esse método é usado normalmente no provisionamento de entrada de aplicativos HCM para o Azure Active Directory e Active Directory. Os filtros de escopo são configurados como parte dos mapeamentos de atributos para cada conector de provisionamento de usuário do Azure AD. Para mais informações para configurar filtros de escopo baseados em atributo, consulte [Provisionamento de aplicativo baseado em atributo com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Usuários B2B (convidado)

É possível usar o provisionamento de usuários do serviço para provisionar B2B (ou convidado) no Azure Active Directory para aplicativos SaaS de usuário do Azure Active Directory. No entanto, para usuários B2B entrarem no aplicativo SaaS usando o Azure Active Directory, o aplicativo de SaaS deve ter sua capacidade de logon único baseada em SAML configurada de maneira específica. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários de B2B, consulte [Configurar aplicativos SaaS para colaboração B2B](../external-identities/configure-saas-apps.md).

Observe que userPrincipalName de um usuário convidado é frequentemente armazenado como "alias#EXT#@domain.com". Quando userPrincipalName é incluído nos mapeamentos de atributo como um atributo de origem, #EXT # é removido do userPrincipalName. Se você precisar que o #EXT # esteja presente, substitua userPrincipalName por originalUserPrincipalName como o atributo de origem. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Ciclos de provisionamento: Inicial e incremental

Quando o Azure Active Directory é o sistema de origem, o serviço de provisionamento utiliza [Usar a consulta delta para controlar as alterações nos dados do Microsoft Graph](/graph/delta-query-overview) para monitorar usuários e grupos. O serviço de provisionamento executa um ciclo inicial com o sistema de origem e o sistema de destino, seguida de ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, o primeiro ciclo vai:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).

2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](../manage-apps/assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties) especificados. Exemplo: Se o nome userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de userNames que correspondam aos valores de nome de Principal no sistema de origem.

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado com os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário e a armazena em cache. Essa ID é usada para executar todas as operações futuras nesse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado com os atributos fornecidos pelo sistema de origem. Depois que há uma correspondência de conta de usuário, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.

7. Mantenha uma marca d'água ao final do ciclo inicial para fornecer o ponto de partida para os ciclos incrementais posteriores.

Alguns aplicativos, como ServiceNow, G Suite e Box, dão suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e seus membros. Nesses casos, se o provisionamento de grupos estiver habilitado nos [mapeamentos](customize-application-attributes.md), o serviço de provisionamento sincronizará os usuários e os grupos e, em seguida, as associações de grupos.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos vão:

1. Consultar o sistema de origem em busca de eventuais usuários e grupos que tenham sido atualizados desde a última marca d'água armazenada.

2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](../manage-apps/assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties) especificados.

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado com os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário e a armazena em cache. Essa ID é usada para executar todas as operações futuras nesse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado com os atributos fornecidos pelo sistema de origem. Se a correspondência for uma conta atribuída recentemente, o serviço de provisionamento detectará e armazenará em cache a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.

7. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo (inclusive sendo desatribuído), o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.

8. Se um usuário que estava anteriormente no escopo para provisionamento for desabilitado ou excluído no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.

9. Se um usuário que estava anteriormente no escopo para provisionamento for excluído no sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure Active Directory, os usuários são excluídos 30 dias depois que forem excluídos pelo software.

10. Mantenha uma nova marca d'água ao final do ciclo incremental para fornecer o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Você também pode desabilitar as operações de **criação**, **atualização** ou **exclusão** operações usando as caixas de seleção **Ações do objeto de destino** na seção [Mapeamentos](customize-application-attributes.md). A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributos em um campo como "accountEnabled".

O serviço de provisionamento continua executando ciclos incrementais back-to-back indefinidamente, em intervalos definidos no tutorial [específico para cada aplicativo](../saas-apps/tutorial-list.md). Os ciclos incrementais continuam até que ocorra um dos seguintes eventos:

- O serviço é interrompido manualmente usando o portal do Azure ou o comando apropriado da API do Microsoft Graph.
- Um novo ciclo inicial é disparado usando a opção **Limpar estado e reiniciar** no portal do Azure ou o comando apropriado da API do Microsoft Graph. Essa ação apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou filtros de escopo. Essa ação também apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
- O processo de provisionamento vai para quarentena (veja abaixo) devido à taxa de erros alta e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um erro no sistema de destino impedir que um usuário individual seja adicionado, atualizado ou excluído no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar falhando, as novas tentativas começarão a ocorrer em uma frequência reduzida, voltando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e executar a ação apropriada. Entre as falhas comuns podem estar:

- Usuários que não tenham um atributo preenchido no sistema de origem necessário no sistema de destino
- Usuários que tenham um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Para resolver essas falhas, ajuste os valores de atributo do usuário afetado no sistema de origem ou os mapeamentos de atributos para que eles não causem conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino falharem regularmente por causa de um erro (por exemplo, credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Esse estado é indicado no [relatório de resumo do provisionamento](./check-status-user-account-provisioning.md) e por email, se notificações por email estiverem configuradas no portal do Azure.

Em quarentena, a frequência de ciclos incrementais é reduzida gradualmente a uma por dia.

O trabalho de provisionamento sai da quarentena depois que todos os erros inválidos são corrigidos e o próximo ciclo de sincronização é iniciado. Se permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado. Saiba mais aqui sobre o status de quarentena [aqui](./application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Quanto tempo demora o provisionamento

O desempenho depende se o seu trabalho de provisionamento está executando um ciclo de provisionamento inicial ou incremental. Para mais informações sobre a duração do provisionamento e como monitorar o status do serviço de provisionamento, consulte [Verificar o status do provisionamento de usuário](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Como posso saber se os usuários estão sendo provisionados corretamente

Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos [Logs de provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) do Azure Active Directory. Os logs incluem todas as operações de gravação feitas para os sistemas de origem e destino, e os dados de usuário que foram lidos ou gravados durante cada operação. Para informações sobre como ler os logs de provisionamento no portal do Azure, consulte o [guia de relatório de provisionamento](./check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Desprovisionamento
O serviço de provisionamento do Azure AD mantém os sistemas de origem e de destino sincronizados por contas de provisionamento quando o acesso do usuário é removido.

O serviço de provisionamento dá suporte à exclusão e à desabilitação (às vezes conhecida como exclusão reversível) dos usuários. A definição exata de Disable e Delete varia de acordo com a implementação do aplicativo de destino, mas, em geral, uma desabilitação indica que o usuário não pode entrar. Uma exclusão indica que o usuário foi removido completamente do aplicativo. Para aplicativos SCIM, uma desabilitação é uma solicitação para definir a propriedade *ativa* como false em um usuário. 

**Configurar seu aplicativo para desabilitar um usuário**

Verifique se você selecionou a caixa de seleção para atualizações.

Verifique se você tem o mapeamento para *ativo* para seu aplicativo. Se você estiver usando um aplicativo da Galeria de aplicativos, o mapeamento poderá ser um pouco diferente. Verifique se você usa o mapeamento padrão/pronto para os aplicativos da galeria.

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="Desabilitar um usuário" lightbox="./media/how-provisioning-works/disable-user.png":::


**Configurar seu aplicativo para excluir um usuário**

Os cenários a seguir dispararão uma desabilitação ou uma exclusão: 
* Um usuário é excluído de maneira reversível no Azure AD (enviado para a propriedade da lixeira/AccountEnabled definida como false).
    Trinta dias após a exclusão de um usuário no Azure Active Directory, eles serão excluídos permanentemente do locatário. Neste ponto, o serviço de provisionamento enviará uma solicitação de EXCLUSÃO para excluir permanentemente o usuário no aplicativo. Durante o período de 30 dias, você pode [excluir manualmente um usuário de forma permanente](../fundamentals/active-directory-users-restore.md), o que envia uma solicitação de exclusão ao aplicativo.
* Um usuário é excluído/removido permanentemente da lixeira no Azure AD.
* Um usuário não é atribuído de um aplicativo.
* Um usuário vai de um escopo para fora do escopo (o não passa mais um filtro de escopo).

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="Excluir um usuário" lightbox="./media/how-provisioning-works/delete-user.png":::

Por padrão, o serviço de provisionamento do Azure Active Directory exclui ou desabilita usuários que saem de escopo. Se você quiser substituir esse comportamento padrão, poderá definir um sinalizador para [ignorar exclusões fora do escopo.](skip-out-of-scope-deletions.md)

Se um dos quatro eventos acima ocorrer e o aplicativo de destino não oferecer suporte a exclusões reversíveis, o serviço de provisionamento enviará uma solicitação de EXCLUSÃO para excluir permanentemente o usuário do aplicativo.

Se você vir um atributo IsSoftDeleted em seus mapeamentos de atributo, ele será usado para determinar o estado do usuário e se deseja enviar uma solicitação de atualização com active = false para excluir a exclusão reversível do usuário.

**Limitações conhecidas**

* Se um usuário que foi gerenciado anteriormente pelo serviço de provisionamento não tiver sido atribuído de um aplicativo ou de um grupo atribuído a um aplicativo, enviaremos uma solicitação de desabilitação. Nesse ponto, o usuário não é gerenciado pelo serviço e não enviaremos uma solicitação de exclusão quando eles forem excluídos do diretório.
* Não há suporte para o provisionamento de um usuário que está desabilitado no Azure AD. Eles devem estar ativos no Azure AD antes de serem provisionados.
* Quando um usuário passa de excluído de forma reversível para ativo, o serviço de provisionamento do Azure AD ativará o usuário no aplicativo de destino, mas não irá restaurar automaticamente as associações de grupo. O aplicativo de destino deve manter as associações de grupo para o usuário no estado inativo. Se o aplicativo de destino não oferecer suporte a isso, você poderá reiniciar o provisionamento para atualizar as associações de grupo. 

**Recomendação**

Ao desenvolver um aplicativo, o sempre dá suporte a exclusões reversível e exclusões rígidas. Ele permite que os clientes se recuperem quando um usuário é desabilitado acidentalmente.


## <a name="next-steps"></a>Próximas etapas

[Planejar uma implantação de provisionamento do usuário automática](../app-provisioning/plan-auto-user-provisioning.md)

[Configurar provisionamento para um aplicativo da galeria](./configure-automatic-user-provisioning-portal.md)

[Criar um ponto de extremidade SCIM e configurar o provisionamento ao criar seu próprio aplicativo](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Solucionar problemas com a configuração e o provisionamento de usuários para um aplicativo](./application-provisioning-config-problem.md).

---
title: Solução de problemas comuns
description: Saiba como solucionar problemas com a criação de definições de política, os vários SDKs e o complemento para kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592610"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Solucionar erros com o uso de Azure Policy

Quando você cria definições de política, trabalha com SDKs ou configura o [Azure Policy para](../concepts/policy-for-kubernetes.md) o complemento kubernetes, você pode encontrar erros. Este artigo descreve vários erros gerais que podem ocorrer e sugere maneiras de resolvê-los.

## <a name="find-error-details"></a>Localizar detalhes do erro

O local dos detalhes do erro depende de qual aspecto do Azure Policy você está trabalhando.

- Se você estiver trabalhando com uma política personalizada, vá para a portal do Azure para obter comentários sobre o esquema ou revisar os [dados de conformidade](../how-to/get-compliance-data.md) resultantes para ver como os recursos foram avaliados.
- Se você estiver trabalhando com qualquer um dos vários SDKs, o SDK fornecerá detalhes sobre o motivo da falha da função.
- Se você estiver trabalhando com o complemento do kubernetes, comece com o [registro em log](../concepts/policy-for-kubernetes.md#logging) no cluster.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-alias-not-found"></a>Cenário: alias não encontrado

#### <a name="issue"></a>Problema

Um alias incorreto ou inexistente é usado em uma definição de política. Azure Policy usa [aliases](../concepts/definition-structure.md#aliases) para mapear para Azure Resource Manager Propriedades.

#### <a name="cause"></a>Causa

Um alias incorreto ou inexistente é usado em uma definição de política.

#### <a name="resolution"></a>Resolução

Primeiro, valide se a propriedade do Gerenciador de recursos tem um alias. Para pesquisar os aliases disponíveis, acesse [Azure Policy extensão para Visual Studio Code](../how-to/extension-for-vscode.md) ou o SDK.
Se o alias de uma propriedade do Gerenciador de recursos não existir, crie um tíquete de suporte.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Cenário: os detalhes de avaliação não estão atualizados

#### <a name="issue"></a>Problema

Um recurso está no estado _não iniciado_ ou os detalhes de conformidade não são atuais.

#### <a name="cause"></a>Causa

Uma nova atribuição de política ou iniciativa leva cerca de 30 minutos para ser aplicada. Recursos novos ou atualizados dentro do escopo de uma atribuição existente são disponibilizados em cerca de 15 minutos. Uma verificação de conformidade padrão ocorre A cada 24 horas. Para obter mais informações, consulte os [gatilhos de avaliação](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Resolução

Primeiro, aguarde um período de tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade fiquem disponíveis no portal do Azure ou no SDK. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou a API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Cenário: a conformidade não é como esperado

#### <a name="issue"></a>Problema

Um recurso não está no estado de avaliação _compatível_ ou _não compatível_ que é esperado para o recurso.

#### <a name="cause"></a>Causa

O recurso não está no escopo correto para a atribuição de política ou a definição de política não funciona conforme o esperado.

#### <a name="resolution"></a>Resolução

Para solucionar problemas de definição de política, faça o seguinte:

1. Primeiro, aguarde o tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade fiquem disponíveis no portal do Azure ou no SDK. 

1. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou a API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o escopo de atribuição estão definidos corretamente.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - O modo deve ser `all` para todos os tipos de recurso.
   - O modo deve ser `indexed` se a definição de política verificar marcas ou local.
1. Certifique-se de que o escopo do recurso não seja [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento](../concepts/exemption-structure.md).
1. Se a conformidade de uma atribuição de política mostrar `0/0` recursos, nenhum recurso foi determinado para ser aplicável dentro do escopo de atribuição. Verifique a definição de política e o escopo de atribuição.
1. Para um recurso não compatível que deveria estar em conformidade, consulte [determinar os motivos de não conformidade](../how-to/determine-non-compliance.md). A comparação da definição com o valor da propriedade avaliada indica por que um recurso não está em conformidade.
   - Se o **valor de destino** estiver errado, revise a definição de política.
   - Se o **valor atual** estiver errado, valide a carga do recurso por meio de `resources.azure.com` .
1. Para outros problemas e soluções comuns, consulte [solucionar problemas: imposição não conforme o esperado](#scenario-enforcement-not-as-expected).

Se você ainda tiver um problema com sua definição de política interna duplicada e personalizada ou definição personalizada, crie um tíquete de suporte sob a **criação de uma política** para encaminhar o problema corretamente.

### <a name="scenario-enforcement-not-as-expected"></a>Cenário: imposição não conforme esperado

#### <a name="issue"></a>Problema

Um recurso que você espera que Azure Policy atue não esteja sendo executado, e não há nenhuma entrada no log de [atividades do Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Causa

A atribuição de política foi configurada para uma configuração de [**imposiçãomode**](../concepts/assignment-structure.md#enforcement-mode) _desabilitada_.
Embora o **imposiçãomode** seja desabilitado, o efeito da política não é imposto e não há nenhuma entrada no log de atividades.

#### <a name="resolution"></a>Resolução

Solucione os problemas de imposição da sua atribuição de política fazendo o seguinte:

1. Primeiro, aguarde o tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade fiquem disponíveis no portal do Azure ou no SDK. 

1. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou a API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o escopo de atribuição estão definidos corretamente e se **imimport** está _habilitado_.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - O modo deve ser `all` para todos os tipos de recurso.
   - O modo deve ser `indexed` se a definição de política verificar marcas ou local.
1. Certifique-se de que o escopo do recurso não seja [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento](../concepts/exemption-structure.md).
1. Verifique se a carga do recurso corresponde à lógica da política. Isso pode ser feito [capturando um rastreamento de arquivo http (Har)](../../../azure-portal/capture-browser-trace.md) ou revisando as propriedades do modelo de Azure Resource Manager (modelo ARM).
1. Para outros problemas e soluções comuns, consulte [solucionar problemas: conformidade não conforme o esperado](#scenario-compliance-isnt-as-expected).

Se você ainda tiver um problema com sua definição de política interna duplicada e personalizada ou definição personalizada, crie um tíquete de suporte sob a **criação de uma política** para encaminhar o problema corretamente.

### <a name="scenario-denied-by-azure-policy"></a>Cenário: negado por Azure Policy

#### <a name="issue"></a>Problema

A criação ou atualização de um recurso foi negada.

#### <a name="cause"></a>Causa

Uma atribuição de política para o escopo do recurso novo ou atualizado atende aos critérios de uma definição de política com um efeito de [negação](../concepts/effects.md#deny) . Os recursos que atendem a essas definições são impedidos de serem criados ou atualizados.

#### <a name="resolution"></a>Resolução

A mensagem de erro de uma atribuição de política de negação inclui a definição de política e as IDs de atribuição de política. Se as informações de erro na mensagem forem perdidas, elas também estarão disponíveis no [log de atividades](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Use essas informações para obter mais detalhes para entender as restrições de recursos e ajustar as propriedades de recurso em sua solicitação para corresponder os valores permitidos.

## <a name="template-errors"></a>Erros de modelo

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Cenário: funções com suporte de política processadas pelo modelo

#### <a name="issue"></a>Problema

O Azure Policy dá suporte a várias funções de modelo ARM e funções que estão disponíveis somente em uma definição de política. O Gerenciador de recursos processa essas funções como parte de uma implantação em vez de como parte de uma definição de política.

#### <a name="cause"></a>Causa

O uso de funções com suporte, como `parameter()` ou `resourceGroup()` , resulta no resultado processado da função no momento da implantação em vez de permitir que a função da definição de política e do mecanismo de Azure Policy seja processada.

#### <a name="resolution"></a>Resolução

Para passar uma função por como parte de uma definição de política, escape toda a cadeia de caracteres com `[` tal aparência da propriedade `[[resourceGroup().tags.myTag]` . O caractere de escape faz com que o Gerenciador de recursos trate o valor como uma cadeia de caracteres ao processar o modelo. Azure Policy, em seguida, coloca a função na definição de política, o que permite que ela seja dinâmica conforme esperado. Para obter mais informações, consulte [sintaxe e expressões em modelos de Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Complemento para erros de instalação do kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Cenário: a instalação usando um gráfico Helm falha devido a um erro de senha

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha e retorna um dos seguintes erros:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

A senha gerada inclui uma vírgula ( `,` ), na qual o gráfico Helm está sendo dividido.

#### <a name="resolution"></a>Resolução

Quando você executar `helm install azure-policy-addon` , escape a vírgula ( `,` ) no valor da senha com uma barra invertida ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Cenário: a instalação usando um gráfico Helm falha porque o nome já existe

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha e retorna o seguinte erro:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

O gráfico Helm com o nome `azure-policy-addon` já foi instalado ou parcialmente instalado.

#### <a name="resolution"></a>Resolução

Siga as instruções para [remover o Azure Policy para o complemento kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)e execute o `helm install azure-policy-addon` comando novamente.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Cenário: as identidades atribuídas ao usuário da máquina virtual do Azure são substituídas por identidades gerenciadas atribuídas pelo sistema

#### <a name="issue"></a>Problema

Depois de atribuir iniciativas de política de configuração de convidado às configurações de auditoria dentro de um computador, as identidades gerenciadas atribuídas pelo usuário que foram atribuídas ao computador não serão mais atribuídas. Somente uma identidade gerenciada atribuída pelo sistema é atribuída.

#### <a name="cause"></a>Causa

As definições de política que foram usadas anteriormente nas definições de DeployIfNotExists de configuração de convidado garantiram que uma identidade atribuída pelo sistema seja atribuída ao computador, mas elas também removeram as atribuições de identidade atribuídas pelo usuário.

#### <a name="resolution"></a>Resolução

As definições que anteriormente causaram esse problema aparecem como _\[ preteridas \]_ e são substituídas por definições de política que gerenciam os pré-requisitos sem remover identidades gerenciadas atribuídas pelo usuário. Uma etapa manual é necessária. Exclua as atribuições de política existentes marcadas como _\[ preteridas \]_ e substitua-as pela iniciativa de política de pré-requisito atualizada e definições de política que têm o mesmo nome que o original.

Para obter uma narração detalhada, consulte a postagem de blog [importante alteração lançada para políticas de auditoria de configuração de convidado](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Complemento para erros gerais do kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Cenário: o complemento não consegue acessar o ponto de extremidade do serviço de Azure Policy devido a restrições de saída

#### <a name="issue"></a>Problema

O complemento não pode acessar o ponto de extremidade do serviço Azure Policy e retorna um dos seguintes erros:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Esse problema ocorre quando uma saída de cluster é bloqueada.

#### <a name="resolution"></a>Resolução

Verifique se os domínios e as portas mencionados nos artigos a seguir estão abertos:

- [Regras de rede de saída necessárias e FQDNs (nomes de domínio totalmente qualificados) para clusters AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instalar o complemento Azure Policy para kubernetes habilitados para arco do Azure (versão prévia)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Cenário: o complemento não consegue acessar o ponto de extremidade do serviço de Azure Policy devido à configuração do AAD-Pod-Identity

#### <a name="issue"></a>Problema

O complemento não pode acessar o ponto de extremidade do serviço Azure Policy e retorna um dos seguintes erros:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Esse erro ocorre quando _Add-Pod-Identity_ está instalado no cluster e os pods _Kube do sistema_ não são excluídos no _AAD-Pod-Identity_.

O pods da NMI (identidade gerenciada de nó de componente) do _AAD-Pod-Identity_ modifica os iptables dos nós para interceptar chamadas para o ponto de extremidade de metadados da instância do Azure. Essa configuração significa que qualquer solicitação feita ao ponto de extremidade de metadados é interceptada por NMI, mesmo que o Pod não use o _AAD-Pod-Identity_.
O CRD ( _AzurePodIdentityException_ CustomResourceDefinition) pode ser configurado para informar ao _AAD-Pod-Identity_ que qualquer solicitação para um ponto de extremidade de metadados originado de um pod correspondente aos rótulos definidos no CRD deve ser proxyada sem nenhum processamento em NMI.

#### <a name="resolution"></a>Resolução

Exclua os pods do sistema que têm o `kubernetes.azure.com/managedby: aks` rótulo no namespace _Kube_ no _AAD-Pod-Identity_ Configurando o _AzurePodIdentityException_ CRD.

Para obter mais informações, consulte [desabilitar a identidade do Pod do Azure Active Directory (Azure AD) para um pod/aplicativo específico](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Para configurar uma exceção, siga este exemplo:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Cenário: o provedor de recursos não está registrado

#### <a name="issue"></a>Problema

O complemento pode alcançar o ponto de extremidade de serviço Azure Policy, mas os logs de complemento exibem um dos seguintes erros:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Causa

O provedor de recursos ' Microsoft. PolicyInsights ' não está registrado. Ele deve ser registrado para que o complemento obtenha as definições de política e retorne os dados de conformidade.

#### <a name="resolution"></a>Resolução

Registre o provedor de recursos ' Microsoft. PolicyInsights ' na assinatura do cluster. Para obter instruções, consulte [registrar um provedor de recursos](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Cenário: a assinatura está desabilitada

#### <a name="issue"></a>Problema

O complemento pode alcançar o ponto de extremidade de serviço Azure Policy, mas o seguinte erro é exibido:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Causa

Esse erro significa que a assinatura foi determinada como problemática e o sinalizador de recurso `Microsoft.PolicyInsights/DataPlaneBlocked` foi adicionado para bloquear a assinatura.

#### <a name="resolution"></a>Resolução

Para investigar e resolver esse problema, [entre em contato com a equipe de recursos](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

Se o problema não estiver listado neste artigo ou se você não puder resolvê-lo, obtenha suporte visitando um dos seguintes canais:

- Obtenha respostas de especialistas por meio [do Microsoft Q&a](/answers/topics/azure-policy.html).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esse recurso oficial de Microsoft Azure no Twitter ajuda a melhorar a experiência do cliente conectando-se à comunidade do Azure com as respostas, suporte e especialistas certos.
- Se você ainda precisar de ajuda, vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Enviar uma solicitação de suporte**.

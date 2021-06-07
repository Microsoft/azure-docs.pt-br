---
title: Impedir acesso de leitura público anônimo a contêineres e blobs
titleSuffix: Azure Storage
description: Saiba como analisar solicitações anônimas em uma conta de armazenamento e como impedir o acesso anônimo para toda a conta de armazenamento ou para um contêiner individual.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 53f29c2b8f7a17ac2a23cc081660e8dcb4b9f387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701851"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Impedir acesso de leitura público anônimo a contêineres e blobs

O acesso de leitura público anônimo a contêineres e blobs no Armazenamento do Azure é uma forma conveniente de compartilhar dados, mas também pode representar um risco de segurança. É importante gerenciar o acesso anônimo criteriosamente e entender como avaliar o acesso anônimo aos seus dados. A complexidade operacional, erro humano ou ataque mal-intencionado contra dados acessíveis publicamente pode resultar em violações de dados dispendiosas. A Microsoft recomenda que você habilite o acesso anônimo somente quando necessário para o cenário do aplicativo.

Por padrão, o acesso público aos dados de blob é sempre proibido. No entanto, a configuração padrão para uma conta de armazenamento permite que um usuário com permissões apropriadas configure o acesso público a contêineres e blobs em uma conta de armazenamento. Para aumentar a segurança, você pode proibir todo o acesso público à conta de armazenamento, independentemente da configuração de acesso público para um contêiner individual. Não permitir o acesso público à conta de armazenamento impede que um usuário habilite o acesso público para um contêiner na conta. A Microsoft recomenda que você não permita o acesso público a uma conta de armazenamento, a menos que seu cenário exija isso. Não permitir o acesso público ajuda a evitar violações de dados causadas por acesso anônimo indesejado.

Quando você não permite acesso de blob público para a conta de armazenamento, o armazenamento do Azure rejeita todas as solicitações anônimas para essa conta. Depois que o acesso público não é permitido para uma conta, os contêineres nessa conta não podem ser subseqüentemente configurados para acesso público. Todos os contêineres que já foram configurados para acesso público não aceitarão mais solicitações anônimas. Para obter mais informações, consulte [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md).

Este artigo descreve como usar uma estrutura de arrastar (detecção-correção-auditoria-governança) para gerenciar continuamente o acesso público para suas contas de armazenamento.

## <a name="detect-anonymous-requests-from-client-applications"></a>Detectar solicitações anônimas de aplicativos cliente

Quando você não permite acesso de leitura público para uma conta de armazenamento, você corre o risco de rejeitar solicitações para contêineres e blobs que estão atualmente configurados para acesso público. A despermissão de acesso público para uma conta de armazenamento substitui as configurações de acesso público para contêineres individuais nessa conta de armazenamento. Quando o acesso público não é permitido para a conta de armazenamento, qualquer solicitação anônima futura para essa conta falhará.

Para entender como a despermissão de acesso público pode afetar os aplicativos cliente, a Microsoft recomenda que você habilite o registro em log e as métricas para essa conta e analise padrões de solicitações anônimas em um intervalo de tempo. Use as métricas para determinar o número de solicitações anônimas para a conta de armazenamento e use logs para determinar quais contêineres estão sendo acessados anonimamente.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitorar solicitações anônimas com Metrics Explorer

Para rastrear solicitações anônimas para uma conta de armazenamento, use o Azure Metrics Explorer no portal do Azure. Para obter mais informações sobre Metrics Explorer, consulte [introdução ao Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md).

Siga estas etapas para criar uma métrica que rastreia solicitações anônimas:

1. Navegue até sua conta de armazenamento no portal do Azure. Na seção **monitoramento** , selecione **métricas**.
1. Selecione **Adicionar métrica**. Na caixa de diálogo **métrica** , especifique os seguintes valores:
    1. Deixe o campo de escopo definido como o nome da conta de armazenamento.
    1. Defina o **namespace de métrica** como *blob*. Essa métrica relatará solicitações apenas ao armazenamento de BLOBs.
    1. Defina o campo de **métrica** para *Transações*.
    1. Defina o campo de **agregação** como *sum*.

    A nova métrica exibirá a soma do número de transações no armazenamento de BLOBs em um determinado intervalo de tempo. A métrica resultante aparece conforme mostrado na imagem a seguir:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Captura de tela mostrando como configurar a métrica para somar transações de BLOB":::

1. Em seguida, selecione o botão **Adicionar filtro** para criar um filtro na métrica para solicitações anônimas.
1. Na caixa de diálogo **filtro** , especifique os seguintes valores:
    1. Defina o valor da **Propriedade** como *autenticação*.
    1. Defina o campo **operador** para o sinal de igual (=).
    1. Defina o campo **valores** como *anônimo*.
1. No canto superior direito, selecione o intervalo de tempo durante o qual você deseja exibir a métrica. Você também pode indicar como a agregação das solicitações deve ser granular, especificando intervalos em qualquer lugar, de 1 minuto a 1 mês.

Depois de configurar a métrica, as solicitações anônimas começarão a aparecer no grafo. A imagem a seguir mostra solicitações anônimas agregadas nos últimos trinta minutos.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Captura de tela mostrando solicitações anônimas agregadas no armazenamento de BLOBs":::

Você também pode configurar uma regra de alerta para notificá-lo quando um determinado número de solicitações anônimas for feito em sua conta de armazenamento. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analisar logs para identificar contêineres que recebem solicitações anônimas

Os logs de armazenamento do Azure capturam detalhes sobre as solicitações feitas na conta de armazenamento, incluindo como uma solicitação foi autorizada. Você pode analisar os logs para determinar quais contêineres estão recebendo solicitações anônimas.

Para registrar solicitações em log para sua conta de armazenamento do Azure para avaliar solicitações anônimas, você pode usar o log de armazenamento do Azure no Azure Monitor (versão prévia). Para obter mais informações, consulte [monitorar o armazenamento do Azure](./monitor-blob-storage.md).

O log de armazenamento do Azure no Azure Monitor dá suporte ao uso de consultas de log para analisar dados de log. Para consultar logs, você pode usar um espaço de trabalho de Log Analytics do Azure. Para saber mais sobre consultas de log, consulte [tutorial: introdução às consultas de log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

> [!NOTE]
> Há suporte para a visualização do log de armazenamento do Azure no Azure Monitor apenas na nuvem pública do Azure. As nuvens governamentais não dão suporte ao registro em log para o armazenamento do Azure com Azure Monitor.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Criar uma configuração de diagnóstico no portal do Azure

Para registrar dados do armazenamento do Azure com Azure Monitor e analisá-los com o Azure Log Analytics, você deve primeiro criar uma configuração de diagnóstico que indica quais tipos de solicitações e para quais serviços de armazenamento você deseja registrar dados. Para criar uma configuração de diagnóstico no portal do Azure, siga estas etapas:

1. Crie um novo espaço de trabalho Log Analytics na assinatura que contém sua conta de armazenamento do Azure. Depois de configurar o log para sua conta de armazenamento, os logs estarão disponíveis no espaço de trabalho Log Analytics. Para obter mais informações, confira [Criar um workspace do Log Analytics no portal do Azure](../../azure-monitor/logs/quick-create-workspace.md).
1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção monitoramento, selecione **configurações de diagnóstico (versão prévia)**.
1. Selecione **blob** para registrar solicitações feitas no armazenamento de BLOBs.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a configuração de diagnóstico.
1. Em **detalhes da categoria**, na seção **log** , escolha quais tipos de solicitações registrar em log. Todas as solicitações anônimas serão solicitações de leitura, portanto, selecione **StorageRead** para capturar solicitações anônimas.
1. Em **detalhes de destino**, selecione **Enviar para log Analytics**. Selecione sua assinatura e o espaço de trabalho Log Analytics criado anteriormente, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Captura de tela mostrando como criar uma configuração de diagnóstico para solicitações de log":::

Depois de criar a configuração de diagnóstico, as solicitações para a conta de armazenamento são registradas subsequentemente de acordo com essa configuração. Para obter mais informações, consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos logs de armazenamento do Azure no Azure Monitor, consulte [logs de recursos (versão prévia)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Logs de consulta para solicitações anônimas

Os logs de armazenamento do Azure no Azure Monitor incluem o tipo de autorização que foi usado para fazer uma solicitação para uma conta de armazenamento. Em sua consulta de log, filtre a propriedade **AuthenticationType** para exibir solicitações anônimas.

Para recuperar os logs dos últimos 7 dias para solicitações anônimas no armazenamento de BLOBs, abra seu espaço de trabalho Log Analytics. Em seguida, Cole a seguinte consulta em uma nova consulta de log e execute-a:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Você também pode configurar uma regra de alerta com base nessa consulta para notificá-lo sobre solicitações anônimas. Para obter mais informações, consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Corrigir acesso público anônimo

Depois de avaliar as solicitações anônimas para contêineres e blobs em sua conta de armazenamento, você pode tomar medidas para limitar ou impedir o acesso público. Se alguns contêineres em sua conta de armazenamento puderem estar disponíveis para acesso público, você poderá configurar a configuração de acesso público para cada contêiner em sua conta de armazenamento. Essa opção fornece o controle mais granular sobre o acesso público. Para obter mais informações, consulte [definir o nível de acesso público para um contêiner](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Para aumentar a segurança, você pode impedir o acesso público para toda a conta de armazenamento. A configuração de acesso público para uma conta de armazenamento substitui as configurações individuais dos contêineres nessa conta. Quando você não permite acesso público para uma conta de armazenamento, os contêineres configurados para permitir acesso público não são mais acessíveis anonimamente. Para obter mais informações, consulte [permitir ou não permitir acesso de leitura público para uma conta de armazenamento](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Se seu cenário exigir que determinados contêineres precisem estar disponíveis para acesso público, pode ser aconselhável mover esses contêineres e seus BLOBs para contas de armazenamento que são reservadas para acesso público. Em seguida, você pode proibir o acesso público para qualquer outra conta de armazenamento.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Verifique se o acesso público a um BLOB não é permitido

Para verificar se o acesso público a um blob específico não é permitido, você pode tentar baixar o blob por meio de sua URL. Se o download for executado com sucesso, o BLOB ainda estará disponível publicamente. Se o BLOB não estiver publicamente acessível porque o acesso público não foi permitido para a conta de armazenamento, você verá uma mensagem de erro indicando que o acesso público não é permitido nessa conta de armazenamento.

O exemplo a seguir mostra como usar o PowerShell para tentar baixar um blob por meio de sua URL. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Verifique se a modificação da configuração de acesso público do contêiner não é permitida

Para verificar se a configuração de acesso público de um contêiner não pode ser modificada após o acesso público não ser permitido para a conta de armazenamento, você pode tentar modificar a configuração. A alteração da configuração de acesso público do contêiner falhará se o acesso público não for permitido para a conta de armazenamento.

O exemplo a seguir mostra como usar o PowerShell para tentar alterar a configuração de acesso público de um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Verifique se a criação de um contêiner com acesso público habilitado não é permitida

Se o acesso público não for permitido para a conta de armazenamento, você não poderá criar um novo contêiner com acesso público habilitado. Para verificar, você pode tentar criar um contêiner com acesso público habilitado.

O exemplo a seguir mostra como usar o PowerShell para tentar criar um contêiner com acesso público habilitado. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Verificar a configuração de acesso público para várias contas

Para verificar a configuração de acesso público em um conjunto de contas de armazenamento com o desempenho ideal, você pode usar o Gerenciador de grafo de recursos do Azure no portal do Azure. Para saber mais sobre como usar o Gerenciador de grafo de recursos, consulte [início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../../governance/resource-graph/first-query-portal.md).

A propriedade **AllowBlobPublicAccess** não é definida para uma conta de armazenamento por padrão e não retorna um valor até que você a defina explicitamente. A conta de armazenamento permite acesso público quando o valor da propriedade é **nulo** ou **verdadeiro**.

A execução da consulta a seguir no Gerenciador de grafo de recursos retorna uma lista de contas de armazenamento e exibe a configuração de acesso público para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

A imagem a seguir mostra os resultados de uma consulta em uma assinatura. Observe que para contas de armazenamento em que a propriedade **AllowBlobPublicAccess** foi definida explicitamente, ela aparece nos resultados como **true** ou **false**. Se a propriedade **AllowBlobPublicAccess** não tiver sido definida para uma conta de armazenamento, ela aparecerá como em branco (ou NULL) nos resultados da consulta.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Captura de tela mostrando resultados da consulta para configuração de acesso público entre contas de armazenamento":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Usar Azure Policy para auditar a conformidade

Se você tiver um grande número de contas de armazenamento, convém realizar uma auditoria para certificar-se de que essas contas estão configuradas para impedir o acesso público. Para auditar um conjunto de contas de armazenamento para sua conformidade, use Azure Policy. Azure Policy é um serviço que você pode usar para criar, atribuir e gerenciar políticas que aplicam regras aos recursos do Azure. Azure Policy ajuda a manter esses recursos em conformidade com seus padrões corporativos e contratos de nível de serviço. Para saber mais, confira [Visão geral do Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Criar uma política com um efeito de auditoria

Azure Policy dá suporte a efeitos que determinam o que acontece quando uma regra de política é avaliada em um recurso. O efeito de auditoria cria um aviso quando um recurso não está em conformidade, mas não interrompe a solicitação. Para obter mais informações sobre efeitos, consulte [entender Azure Policy efeitos](../../governance/policy/concepts/effects.md).

Para criar uma política com um efeito de auditoria para a configuração de acesso público para uma conta de armazenamento com o portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Na seção **criação** , selecione **definições**.
1. Selecione **Adicionar definição de política** para criar uma nova definição de política.
1. Para o campo **local de definição** , selecione o botão **mais** para especificar onde o recurso de política de auditoria está localizado.
1. Especifique um nome para a política. Opcionalmente, você pode especificar uma descrição e uma categoria.
1. Em **regra de política**, adicione a definição de política a seguir à seção **policyRule** .

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Salve a política.

### <a name="assign-the-policy"></a>Atribuir a política

Em seguida, atribua a política a um recurso. O escopo da política corresponde a esse recurso e a todos os recursos abaixo dele. Para obter mais informações sobre atribuição de política, consulte [Azure Policy estrutura de atribuição](../../governance/policy/concepts/assignment-structure.md).

Para atribuir a política com a portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Na seção **criação** , selecione **atribuições**.
1. Selecione **atribuir política** para criar uma nova atribuição de política.
1. Para o campo **escopo** , selecione o escopo da atribuição de política.
1. Para o campo **definição de política** , selecione o botão **mais** e, em seguida, selecione a política que você definiu na seção anterior na lista.
1. Forneça um nome para a atribuição de política. A descrição é opcional.
1. Deixe **imposição de política** definida como *habilitada*. Essa configuração não tem efeito sobre a política de auditoria.
1. Selecione **revisão + criar** para criar a atribuição.

### <a name="view-compliance-report"></a>Exibir relatório de conformidade

Depois de atribuir a política, você poderá exibir o relatório de conformidade. O relatório de conformidade de uma política de auditoria fornece informações sobre quais contas de armazenamento não estão em conformidade com a política. Para obter mais informações, consulte [obter dados de conformidade da política](../../governance/policy/how-to/get-compliance-data.md).

Pode levar vários minutos para que o relatório de conformidade fique disponível depois que a atribuição de política for criada.

Para exibir o relatório de conformidade no portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Selecione **conformidade**.
1. Filtre os resultados para o nome da atribuição de política que você criou na etapa anterior. O relatório mostra quantos recursos não estão em conformidade com a política.
1. Você pode fazer uma busca detalhada no relatório para obter detalhes adicionais, incluindo uma lista de contas de armazenamento que não estão em conformidade.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Captura de tela mostrando o relatório de conformidade para a política de auditoria para acesso público ao blob":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Usar Azure Policy para impor o acesso autorizado

O Azure Policy dá suporte à governança de nuvem, garantindo que os recursos do Azure sigam os requisitos e padrões. Para garantir que as contas de armazenamento em sua organização permitam apenas solicitações autorizadas, você pode criar uma política que impede a criação de uma nova conta de armazenamento com uma configuração de acesso público que permita solicitações anônimas. Essa política também impedirá todas as alterações de configuração em uma conta existente se a configuração de acesso público para essa conta não estiver em conformidade com a política.

A política de imposição usa o efeito de negação para evitar uma solicitação que criaria ou modificaria uma conta de armazenamento para permitir acesso público. Para obter mais informações sobre efeitos, consulte [entender Azure Policy efeitos](../../governance/policy/concepts/effects.md).

Para criar uma política com um efeito de negação para uma configuração de acesso público que permita solicitações anônimas, siga as mesmas etapas descritas em [usar Azure Policy para auditar a conformidade](#use-azure-policy-to-audit-for-compliance), mas forneça o JSON a seguir na seção **policyRule** da definição de política:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Depois de criar a política com o efeito de negação e atribuí-la a um escopo, um usuário não poderá criar uma conta de armazenamento que permita acesso público. Nem um usuário pode fazer alterações de configuração em uma conta de armazenamento existente que atualmente permita acesso público. A tentativa de fazer isso resulta em um erro. A configuração de acesso público para a conta de armazenamento deve ser definida como **false** para continuar com a criação ou configuração da conta.

A imagem a seguir mostra o erro que ocorre se você tentar criar uma conta de armazenamento que permita acesso público (o padrão para uma nova conta) quando uma política com um efeito de negação exigir que o acesso público não seja permitido.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Captura de tela mostrando o erro que ocorre ao criar uma conta de armazenamento em violação de política":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>Permissões para permitir ou não permitir acesso público

Para definir a propriedade **AllowBlobPublicAccess** para a conta de armazenamento, um usuário deve ter permissões para criar e gerenciar contas de armazenamento. As funções do Azure RBAC (controle de acesso baseado em função) que fornecem essas permissões incluem o **Microsoft. Storage/storageAccounts/Write** ou **Microsoft. Storage/ \* storageAccounts/** Action. As funções internas com essa ação incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A função de [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Essas funções não fornecem acesso a dados em uma conta de armazenamento por meio do Azure Active Directory (Azure AD). No entanto, eles incluem a **ação Microsoft. Storage/storageAccounts/listkeys/**, que concede acesso às chaves de acesso da conta. Com essa permissão, um usuário pode usar as chaves de acesso da conta para acessar todos os dados em uma conta de armazenamento.

As atribuições de função devem ser delimitadas ao nível da conta de armazenamento ou superior para permitir que um usuário permita ou proíba o acesso público para a conta de armazenamento. Para obter mais informações sobre o escopo da função, consulte [entender o escopo do RBAC do Azure](../../role-based-access-control/scope-overview.md).

Tenha cuidado para restringir a atribuição dessas funções somente aos que exigem a capacidade de criar uma conta de armazenamento ou atualizar suas propriedades. Use o princípio de privilégios mínimos para garantir que os usuários tenham as menores permissões necessárias para realizar suas tarefas. Para obter mais informações sobre como gerenciar o acesso com o RBAC do Azure, consulte [práticas recomendadas para o RBAC do Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> O administrador de serviço de funções de administrador de assinatura clássica e Co-Administrator incluem o equivalente da função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. A função de **proprietário** inclui todas as ações, de modo que um usuário com uma dessas funções administrativas também pode criar e gerenciar contas de armazenamento. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="next-steps"></a>Próximas etapas

- [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md)
- [Acesse contêineres públicos e blobs anonimamente com o .NET](anonymous-read-access-client.md)
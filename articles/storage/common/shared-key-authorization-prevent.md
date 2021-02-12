---
title: Impedir autorização com chave compartilhada (versão prévia)
titleSuffix: Azure Storage
description: Para exigir que os clientes usem o Azure AD para autorizar solicitações, você pode não permitir solicitações para a conta de armazenamento que são autorizadas com a chave compartilhada (versão prévia).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/21/2021
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: e4a5803b3d04b59316f71e50af24945efc87cb69
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677556"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Impedir a autorização de chave compartilhada para uma conta de armazenamento do Azure (versão prévia)

Todas as solicitações seguras para uma conta de armazenamento do Azure devem ser autorizadas. Por padrão, as solicitações podem ser autorizadas com as credenciais Azure Active Directory (Azure AD) ou usando a chave de acesso da conta para a autorização de chave compartilhada. Desses dois tipos de autorização, o Azure AD fornece segurança superior e facilidade de uso sobre chave compartilhada e é recomendado pela Microsoft. Para exigir que os clientes usem o Azure AD para autorizar solicitações, você pode não permitir solicitações para a conta de armazenamento que são autorizadas com a chave compartilhada (versão prévia).

Quando você não permite a autorização de chave compartilhada para uma conta de armazenamento, o armazenamento do Azure rejeita todas as solicitações subsequentes para essa conta que são autorizadas com as chaves de acesso da conta. Somente as solicitações seguras autorizadas com o Azure AD terão sucesso. Para obter mais informações sobre como usar o Azure AD, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](storage-auth-aad.md).

> [!WARNING]
> O armazenamento do Azure dá suporte à autorização do Azure AD para solicitações de armazenamento de BLOBs e de filas. Se você não permitir a autorização com chave compartilhada para uma conta de armazenamento, as solicitações para os arquivos do Azure ou o armazenamento de tabelas que usam a autorização de chave compartilhada falharão. Como o portal do Azure sempre usa a autorização de chave compartilhada para acessar dados de arquivo e tabela, se você não permitir a autorização com chave compartilhada para a conta de armazenamento, não será possível acessar dados de arquivo ou tabela no portal do Azure.
>
> A Microsoft recomenda que você migre todos os arquivos do Azure ou dados de armazenamento de tabela para uma conta de armazenamento separada antes de não permitir o acesso à conta por meio da chave compartilhada ou que você não aplique essa configuração a contas de armazenamento que dão suporte a arquivos do Azure ou cargas de trabalho de armazenamento de tabelas.
>
> A despermissão de acesso de chave compartilhada para uma conta de armazenamento não afeta as conexões SMB com os arquivos do Azure.

Este artigo descreve como detectar solicitações enviadas com a autorização de chave compartilhada e como corrigir a autorização de chave compartilhada para sua conta de armazenamento. Para saber como registrar-se para a versão prévia, consulte [sobre a versão prévia](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Detectar o tipo de autorização usado por aplicativos cliente

Quando você não permitir a autorização de chave compartilhada para uma conta de armazenamento, as solicitações de clientes que estão usando as chaves de acesso da conta para a autorização de chave compartilhada falharão. Para entender como a despermissão de autorização de chave compartilhada pode afetar os aplicativos cliente antes de fazer essa alteração, habilite o registro em log e as métricas para a conta de armazenamento. Em seguida, você pode analisar padrões de solicitações para sua conta durante um período de tempo para determinar como as solicitações estão sendo autorizadas.

Use as métricas para determinar quantas solicitações a conta de armazenamento está recebendo e que estão autorizadas com uma chave compartilhada ou uma assinatura de acesso compartilhado (SAS). Use logs para determinar quais clientes estão enviando essas solicitações.

Para obter mais informações sobre como interpretar solicitações feitas com uma assinatura de acesso compartilhado durante a versão prévia, consulte [sobre a visualização](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Monitorar quantas solicitações são autorizadas com chave compartilhada

Para controlar como as solicitações para uma conta de armazenamento estão sendo autorizadas, use o Metrics Explorer do Azure no portal do Azure. Para obter mais informações sobre Metrics Explorer, consulte [introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Siga estas etapas para criar uma métrica que controla as solicitações feitas com a chave compartilhada ou SAS:

1. Navegue até sua conta de armazenamento no portal do Azure. Na seção **monitoramento** , selecione **métricas**.
1. Selecione **Adicionar métrica**. Na caixa de diálogo **métrica** , especifique os seguintes valores:
    1. Deixe o campo de **escopo** definido como o nome da conta de armazenamento.
    1. Defina o **namespace de métrica** como *conta*. Essa métrica relatará todas as solicitações em relação à conta de armazenamento.
    1. Defina o campo de **métrica** para *Transações*.
    1. Defina o campo de **agregação** como *sum*.

    A nova métrica exibirá a soma do número de transações em relação à conta de armazenamento em um determinado intervalo de tempo. A métrica resultante aparece conforme mostrado na imagem a seguir:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Captura de tela mostrando como configurar a métrica para somar transações feitas com chave compartilhada ou SAS":::

1. Em seguida, selecione o botão **Adicionar filtro** para criar um filtro na métrica para o tipo de autorização.
1. Na caixa de diálogo **filtro** , especifique os seguintes valores:
    1. Defina o valor da **Propriedade** como *autenticação*.
    1. Defina o campo **operador** para o sinal de igual (=).
    1. No campo **valores** , selecione *chave de conta* e *SAS*.
1. No canto superior direito, selecione o intervalo de tempo para o qual você deseja exibir a métrica. Você também pode indicar como a agregação das solicitações deve ser granular, especificando intervalos em qualquer lugar, de 1 minuto a 1 mês. Por exemplo, defina o **intervalo de tempo** para 30 dias e a **granularidade de tempo** para 1 dia para ver solicitações agregadas por dia nos últimos 30 dias.

Depois de configurar a métrica, as solicitações para sua conta de armazenamento começarão a aparecer no grafo. A imagem a seguir mostra as solicitações que foram autorizadas com a chave compartilhada ou foram feitas com um token SAS. As solicitações são agregadas por dia nos últimos trinta dias.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Captura de tela mostrando solicitações agregadas autorizadas com chave compartilhada":::

Você também pode configurar uma regra de alerta para notificá-lo quando um determinado número de solicitações que são autorizadas com chave compartilhada são feitas em sua conta de armazenamento. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analisar logs para identificar clientes que estão autorizando solicitações com chave compartilhada ou SAS

Os logs de armazenamento do Azure capturam detalhes sobre as solicitações feitas na conta de armazenamento, incluindo como uma solicitação foi autorizada. Você pode analisar os logs para determinar quais clientes estão autorizando solicitações com chave compartilhada ou um token SAS.

Para registrar solicitações em sua conta de armazenamento do Azure para avaliar como elas são autorizadas, você pode usar o log de armazenamento do Azure no Azure Monitor (versão prévia). Para obter mais informações, consulte [monitorar o armazenamento do Azure](../blobs/monitor-blob-storage.md).

O log de armazenamento do Azure no Azure Monitor dá suporte ao uso de consultas de log para analisar dados de log. Para consultar logs, você pode usar um espaço de trabalho de Log Analytics do Azure. Para saber mais sobre consultas de log, consulte [tutorial: introdução às consultas de log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Criar uma configuração de diagnóstico no portal do Azure

Para registrar dados do armazenamento do Azure com Azure Monitor e analisá-los com o Azure Log Analytics, você deve primeiro criar uma configuração de diagnóstico que indica quais tipos de solicitações e para quais serviços de armazenamento você deseja registrar dados. Para criar uma configuração de diagnóstico no portal do Azure, siga estas etapas:

1. Registre-se no [log de armazenamento do Azure na versão prévia do Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Crie um novo espaço de trabalho Log Analytics na assinatura que contém sua conta de armazenamento do Azure ou use um espaço de trabalho Log Analytics existente. Depois de configurar o log para sua conta de armazenamento, os logs estarão disponíveis no espaço de trabalho Log Analytics. Para obter mais informações, confira [Criar um workspace do Log Analytics no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção monitoramento, selecione **configurações de diagnóstico (versão prévia)**.
1. Selecione o serviço de armazenamento do Azure para o qual você deseja registrar solicitações. Por exemplo, escolha **blob** para registrar solicitações ao armazenamento de BLOBs.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a configuração de diagnóstico.
1. Em **detalhes da categoria**, na seção **log** , escolha **StorageRead**, **StorageWrite** e **StorageDelete** para registrar em log todas as solicitações de dados para o serviço selecionado.
1. Em **detalhes de destino**, selecione **Enviar para log Analytics**. Selecione sua assinatura e o espaço de trabalho Log Analytics criado anteriormente, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Captura de tela mostrando como criar uma configuração de diagnóstico para solicitações de log":::

Você pode criar uma configuração de diagnóstico para cada tipo de recurso de armazenamento do Azure em sua conta de armazenamento.

Depois de criar a configuração de diagnóstico, as solicitações para a conta de armazenamento são registradas subsequentemente de acordo com essa configuração. Para obter mais informações, consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../../azure-monitor/platform/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos logs de armazenamento do Azure no Azure Monitor, consulte [logs de recursos (versão prévia)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Logs de consulta para solicitações feitas com chave compartilhada ou SAS

Os logs de armazenamento do Azure no Azure Monitor incluem o tipo de autorização que foi usado para fazer uma solicitação para uma conta de armazenamento. Para recuperar os logs das solicitações feitas nos últimos sete dias que foram autorizados com a chave compartilhada ou SAS, abra seu espaço de trabalho do Log Analytics. Em seguida, Cole a consulta a seguir em uma nova consulta de log e execute-a. Essa consulta exibe os dez endereços IP que mais frequentemente enviaram solicitações que foram autorizadas com a chave compartilhada ou SAS:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Você também pode configurar uma regra de alerta com base nessa consulta para notificá-lo sobre solicitações autorizadas com chave compartilhada ou SAS. Para obter mais informações, consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Corrigir a autorização via chave compartilhada

Depois de analisar como as solicitações para sua conta de armazenamento estão sendo autorizadas, você pode tomar medidas para impedir o acesso via chave compartilhada. Mas primeiro, você precisa atualizar todos os aplicativos que estão usando a autorização de chave compartilhada para usar o Azure AD. Você pode monitorar os logs e as métricas conforme descrito em [detectar o tipo de autorização usado por aplicativos cliente](#detect-the-type-of-authorization-used-by-client-applications) para controlar a transição. Para obter mais informações sobre como usar o Azure AD com dados de BLOB e de fila, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](storage-auth-aad.md).

Quando tiver certeza de que você pode rejeitar com segurança solicitações que são autorizadas com a chave compartilhada, você pode definir a propriedade **AllowSharedKeyAccess** para a conta de armazenamento como **false**.

A propriedade **AllowSharedKeyAccess** não é definida por padrão e não retorna um valor até que você a defina explicitamente. A conta de armazenamento permite solicitações que são autorizadas com a chave compartilhada quando o valor da propriedade é **nulo** ou quando é **verdadeiro**.

> [!WARNING]
> Se algum cliente estiver acessando dados em sua conta de armazenamento com a chave compartilhada, a Microsoft recomendará que você migre esses clientes para o Azure AD antes de não permitir o acesso à chave compartilhada à conta de armazenamento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para não permitir a autorização de chave compartilhada para uma conta de armazenamento no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Localize a definição de **configuração** em **configurações**.
1. Defina **permitir acesso à chave compartilhada** como **desabilitado**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Captura de tela mostrando como não permitir acesso de chave compartilhada para a conta":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para não permitir a autorização de chave compartilhada para uma conta de armazenamento com CLI do Azure, instale CLI do Azure versão 2.9.1 ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Em seguida, configure a propriedade **allowSharedKeyAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir mostra como definir a propriedade **allowSharedKeyAccess** com CLI do Azure. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Depois que você não permitir a autorização de chave compartilhada, a criação de uma solicitação para a conta de armazenamento com a autorização de chave compartilhada falhará com o código de erro 403 (proibido). O armazenamento do Azure retorna um erro indicando que a autorização baseada em chave não é permitida na conta de armazenamento.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Verifique se o acesso à chave compartilhada não é permitido

Para verificar se a autorização de chave compartilhada não é mais permitida, você pode tentar chamar uma operação de dados com a chave de acesso da conta. O exemplo a seguir tenta criar um contêiner usando a chave de acesso. Essa chamada falhará quando a autorização de chave compartilhada não for permitida para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> As solicitações anônimas não são autorizadas e continuarão se você tiver configurado a conta de armazenamento e o contêiner para acesso de leitura público anônimo. Para obter mais informações, consulte [Configurar acesso de leitura público anônimo para contêineres e blobs](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Verificar a configuração de acesso de chave compartilhada para várias contas

Para verificar a configuração de acesso a chaves compartilhadas em um conjunto de contas de armazenamento com o desempenho ideal, você pode usar o Gerenciador de grafo de recursos do Azure no portal do Azure. Para saber mais sobre como usar o Gerenciador de grafo de recursos, consulte [início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../../governance/resource-graph/first-query-portal.md).

A execução da consulta a seguir no Gerenciador de grafo de recursos retorna uma lista de contas de armazenamento e exibe a configuração de acesso a chaves compartilhadas para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>Permissões para permitir ou não permitir acesso à chave compartilhada

Para definir a propriedade **AllowSharedKeyAccess** para a conta de armazenamento, um usuário deve ter permissões para criar e gerenciar contas de armazenamento. As funções do Azure RBAC (controle de acesso baseado em função) que fornecem essas permissões incluem a ação **Microsoft. Storage/storageAccounts/Write** ou **Microsoft. Storage \* /storageAccounts/* _. As funções internas com essa ação incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A função de [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Essas funções não fornecem acesso a dados em uma conta de armazenamento por meio do Azure Active Directory (Azure AD). No entanto, eles incluem o _ * Microsoft. Storage/storageAccounts/listkeys/Action * *, que concede acesso às chaves de acesso da conta. Com essa permissão, um usuário pode usar as chaves de acesso da conta para acessar todos os dados em uma conta de armazenamento.

As atribuições de função devem ser delimitadas ao nível da conta de armazenamento ou superior para permitir que um usuário permita ou proíba o acesso a chaves compartilhadas para a conta de armazenamento. Para obter mais informações sobre o escopo da função, consulte [entender o escopo do RBAC do Azure](../../role-based-access-control/scope-overview.md).

Tenha cuidado para restringir a atribuição dessas funções somente aos que exigem a capacidade de criar uma conta de armazenamento ou atualizar suas propriedades. Use o princípio de privilégios mínimos para garantir que os usuários tenham as menores permissões necessárias para realizar suas tarefas. Para obter mais informações sobre como gerenciar o acesso com o RBAC do Azure, consulte [práticas recomendadas para o RBAC do Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> O administrador de serviço de funções de administrador de assinatura clássica e Co-Administrator incluem o equivalente da função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. A função de **proprietário** inclui todas as ações, de modo que um usuário com uma dessas funções administrativas também pode criar e gerenciar contas de armazenamento. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Entender como a despermissão de chave compartilhada afeta os tokens SAS

Quando o acesso à chave compartilhada não é permitido para a conta de armazenamento, o armazenamento do Azure manipula tokens SAS com base no tipo de SAS e no serviço que é alvo da solicitação. A tabela a seguir mostra como cada tipo de SAS é autorizado e como o armazenamento do Azure tratará essa SAS quando a propriedade **AllowSharedKeyAccess** da conta de armazenamento for **falsa**.

| Tipo de SAS | Tipo de autorização | Comportamento quando AllowSharedKeyAccess é false |
|-|-|-|
| SAS de delegação de usuário (somente armazenamento de BLOBs) | Azure AD | A solicitação é permitida. A Microsoft recomenda usar uma SAS de delegação de usuário quando possível para segurança superior. |
| SAS do serviço | Chave compartilhada | A solicitação é negada para todos os serviços de armazenamento do Azure. |
| SAS da conta | Chave compartilhada | A solicitação é negada para todos os serviços de armazenamento do Azure. |

Para obter mais informações sobre assinaturas de acesso compartilhado, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Considere a compatibilidade com outras ferramentas e serviços do Azure

Vários serviços do Azure usam a autorização de chave compartilhada para se comunicar com o armazenamento do Azure. Se você não permitir a autorização de chave compartilhada para uma conta de armazenamento, esses serviços não poderão acessar os dados nessa conta e seus aplicativos poderão ser afetados negativamente.

Algumas ferramentas do Azure oferecem a opção de usar a autorização do Azure AD para acessar o armazenamento do Azure. A tabela a seguir lista algumas ferramentas populares do Azure e observações se elas podem usar o Azure AD para autorizar solicitações ao armazenamento do Azure.

| Ferramenta do Azure | Autorização do Azure AD para o armazenamento do Azure |
|-|-|
| Portal do Azure | Com suporte. Para obter informações sobre como autorizar com sua conta do Azure AD do portal do Azure, consulte [escolher como autorizar o acesso a dados de blob no portal do Azure](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Com suporte para armazenamento de BLOBs. Para obter informações sobre como autorizar operações de AzCopy, consulte [escolher como você fornecerá credenciais de autorização](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) na documentação do AzCopy. |
| Gerenciador de Armazenamento do Azure | Com suporte somente para armazenamento de BLOBs e Azure Data Lake Storage Gen2. Não há suporte para o acesso do Azure AD ao armazenamento de filas. Certifique-se de selecionar o locatário correto do Azure AD. Para obter mais informações, consulte Introdução [ao Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Com suporte. Para obter informações sobre como autorizar comandos do PowerShell para operações de BLOB ou fila com o Azure AD, consulte [executar comandos do PowerShell com as credenciais do Azure ad para acessar dados de blob](../blobs/authorize-data-operations-powershell.md) ou [executar comandos do PowerShell com as credenciais do Azure ad para acessar os dados da fila](../queues/authorize-data-operations-powershell.md). |
| CLI do Azure | Com suporte. Para obter informações sobre como autorizar comandos de CLI do Azure com o Azure AD para acessar dados de BLOB e de fila, consulte [executar comandos de CLI do Azure com as credenciais do Azure ad para acessar dados de BLOB ou fila](../blobs/authorize-data-operations-cli.md). |
| Hub IoT do Azure | Com suporte. Para obter mais informações, consulte [suporte do Hub IOT para redes virtuais](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell é um shell integrado no portal do Azure. Azure Cloud Shell hospeda arquivos para persistência em um compartilhamento de arquivos do Azure em uma conta de armazenamento. Esses arquivos ficarão inacessíveis se a autorização de chave compartilhada não for permitida para essa conta de armazenamento. Para obter mais informações, consulte [conectar o armazenamento de arquivos de Microsoft Azure](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Para executar comandos no Azure Cloud Shell para gerenciar contas de armazenamento para as quais o acesso à chave compartilhada não é permitido, primeiro certifique-se de ter recebido as permissões necessárias para essas contas por meio do RBAC do Azure. Para obter mais informações, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md). |

## <a name="about-the-preview"></a>Sobre a visualização

A visualização para a despermissão da autorização de chave compartilhada está disponível na nuvem pública do Azure. Há suporte para contas de armazenamento que usam apenas o modelo de implantação Azure Resource Manager. Para obter informações sobre quais contas de armazenamento usam o modelo de implantação Azure Resource Manager, consulte [tipos de contas de armazenamento](storage-account-overview.md#types-of-storage-accounts).

> [!IMPORTANT]
> Esta versão prévia é destinada apenas para uso não produtivo.

A visualização inclui as limitações descritas nas seções a seguir.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Métricas e log relatam todas as solicitações feitas com uma SAS, independentemente de como elas são autorizadas

As métricas do Azure e o log em Azure Monitor não fazem distinção entre diferentes tipos de assinaturas de acesso compartilhado na visualização. O filtro **SAS** no Azure Metrics Explorer e o campo **SAS** no log de armazenamento do Azure em Azure monitor as solicitações de relatório que são autorizadas com qualquer tipo de SAS. No entanto, tipos diferentes de assinaturas de acesso compartilhado são autorizados de forma diferente e se comportam de forma diferente quando o acesso à chave compartilhada não é permitido:

- Um token SAS de serviço ou um token SAS de conta é autorizado com a chave compartilhada e não será permitido em uma solicitação para o armazenamento de blob quando a propriedade **AllowSharedKeyAccess** estiver definida como **false**.
- Uma SAS de delegação de usuário é autorizada com o Azure AD e será permitida em uma solicitação para o armazenamento de blob quando a propriedade **AllowSharedKeyAccess** for definida como **false**.

Quando você estiver avaliando o tráfego para sua conta de armazenamento, tenha em mente que as métricas e os logs conforme descrito em [detectar o tipo de autorização usado por aplicativos cliente](#detect-the-type-of-authorization-used-by-client-applications) podem incluir solicitações feitas com uma SAS de delegação de usuário. Para obter mais informações sobre como o armazenamento do Azure responde a uma SAS quando a propriedade **AllowSharedKeyAccess** é definida como **false**, consulte [entender como a despermissão de chave compartilhada afeta os tokens SAS](#understand-how-disallowing-shared-key-affects-sas-tokens).

## <a name="next-steps"></a>Próximas etapas

- [Autorizando o acesso aos dados no armazenamento do Azure](storage-auth.md)
- [Autorizar o acesso a BLOBs e filas usando Azure Active Directory](storage-auth-aad.md)
- [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key)

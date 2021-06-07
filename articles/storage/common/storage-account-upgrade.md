---
title: Atualizar para uma conta de armazenamento de uso geral v2
titleSuffix: Azure Storage
description: Atualize para contas de armazenamento de uso geral v2 usando o portal do Azure, o PowerShell ou o CLI do Azure. Especifique uma camada de acesso para dados de BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 85520032e9bc63b6296e40dd1c1055e36762dcc8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554999"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Atualizar para uma conta de armazenamento de uso geral v2

As contas de armazenamento para uso geral v2 são compatíveis com os recursos mais recentes do Armazenamento do Azure e incorporam todas as funcionalidades das contas de armazenamento de blobs e para uso geral v1. Contas de uso geral v2 são recomendadas para a maioria dos cenários de armazenamento. As contas de armazenamento para uso geral v2 têm os menores preços de capacidade por gigabyte para o Armazenamento do Azure, bem como os preços de transação competitivos no setor. As contas v2 de uso geral dão suporte a camadas de acesso de conta padrão de camadas de nível quente ou fria e de camada de blob entre Hot, frio ou arquivo morto.

A atualização para uma conta de armazenamento v2 de uso geral de suas contas de armazenamento de blob v1 ou de uso geral é simples. Você pode atualizar usando o portal do Azure, o PowerShell ou a CLI do Azure. Não há nenhum tempo de inatividade ou risco de perda de dados associado à atualização para uma conta de armazenamento v2 de uso geral. A atualização da conta ocorre por meio de uma operação simples de Azure Resource Manager que altera o tipo de conta.

> [!IMPORTANT]
> A atualização de uma conta de armazenamento de blob v1 ou de uso geral para uso geral V2 é permanente e não pode ser desfeita.

> [!NOTE]
> Embora a Microsoft recomende contas de uso geral v2 para a maioria dos cenários, a Microsoft continuará a dar suporte a contas v1 de uso geral para clientes novos e existentes. Você pode criar contas de armazenamento de uso geral v1 em novas regiões sempre que o armazenamento do Azure estiver disponível nessas regiões. Atualmente, a Microsoft não tem um plano para substituir o suporte a contas v1 de uso geral e fornecerá pelo menos um ano de aviso prévio antes de substituir qualquer recurso de armazenamento do Azure. A Microsoft continuará a fornecer atualizações de segurança para contas v1 de uso geral, mas nenhum novo desenvolvimento de recurso é esperado para esse tipo de conta.
>
> A partir de 1º de outubro de 2020, os preços para contas v1 de uso geral em novas regiões de armazenamento do Azure são equivalentes a preços para contas v2 de uso geral nessas regiões. Os preços nas regiões de armazenamento do Azure existentes não foram alterados. Para obter detalhes de preços para contas v1 de uso geral em uma região específica, consulte a página de preços do armazenamento do Azure. Escolha sua região e, em seguida, ao lado de **ofertas de preços**, selecione **outros**.

## <a name="upgrade-an-account"></a>Atualizar uma conta

Para atualizar uma conta de armazenamento de blob v1 ou de uso geral para uma conta v2 de uso geral, use portal do Azure, PowerShell ou CLI do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue para sua conta de armazenamento.
3. Na seção **Configurações**, clique em **Configuração**.
4. Em **tipo de conta**, clique em **Atualizar**.
5. Em **confirmar atualização**, digite o nome da sua conta.
6. Clique em **Atualizar** na parte inferior da folha.

    ![Tipo de conta de atualização](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para atualizar uma conta de uso geral v1 para uma conta de uso geral v2 usando PowerShell, primeiro atualize o PowerShell para usar a última versão do módulo **Az.Storage**. Acesse [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps) para obter informações sobre como instalar o PowerShell.

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e a camada de acesso da conta desejada.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar uma conta de uso geral v1 para uma conta de uso geral v2 usando a CLI do Azure, primeiro instale a versão mais recente da CLI do Azure. Para obter informações sobre como instalar a CLI 2.0 do Azure, veja [Como instalar a CLI do Azure](/cli/azure/install-azure-cli).

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e a camada de acesso da conta desejada.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Especificar uma camada de acesso para dados de blob

As contas de uso geral v2 dão suporte a todos os serviços de armazenamento do Azure e objetos de dados, mas as camadas de acesso estão disponíveis apenas para bloquear BLOBs no armazenamento de BLOBs. Ao atualizar para uma conta de armazenamento v2 de uso geral, você pode especificar uma camada de acesso de conta padrão de frequente ou esporádica, que indica a camada padrão em que os dados de blob serão carregados como se o parâmetro de camada de acesso de blob individual não for especificado.

As camadas de acesso ao blob permitem que você escolha o armazenamento mais econômico com base nos padrões de uso previstos. Blobs de blocos podem ser armazenados em camadas quentes, frias ou de arquivo morto. Para obter mais informações sobre camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de armazenamento quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

Por padrão, uma nova conta de armazenamento é criada na camada de acesso quente e uma conta de armazenamento v1 de uso geral pode ser atualizada para a camada de conta quente ou fria. Se uma camada de acesso à conta não for especificada na atualização, ela será atualizada para ativa por padrão. Se você estiver explorando qual camada de acesso usar para a atualização, considere seu cenário de uso de dados atual. Há dois cenários de usuário comuns para a migração para uma conta de uso geral v2:

* Você tem uma conta de armazenamento de uso geral v1 existente e deseja avaliar uma atualização a uma conta de armazenamento de uso geral v2 com a camada de acesso a armazenamento certa para dados de blob.
* Você decidiu usar uma conta de armazenamento de uso geral v2 ou já tem uma e deseja avaliar se deve usar a camada de acesso de armazenamento quente ou fria para dados de BLOB.

Em ambos os casos, a prioridade é estimar o custo de armazenar, acessar e operar os dados armazenados em uma conta de armazenamento de uso geral v2 e comparar isso com seus custos atuais.

## <a name="pricing-and-billing"></a>Preços e cobrança

A atualização de uma conta de armazenamento v1 para uma conta v2 de uso geral é gratuita. Você pode especificar a camada de conta desejada durante o processo de atualização. Se uma camada de conta não for especificada na atualização, a camada de conta padrão da conta atualizada será `Hot` . No entanto, a alteração da camada de acesso de armazenamento após a atualização pode resultar em alterações na sua fatura para que seja recomendável especificar a nova camada de conta durante a atualização.

Todas as contas de armazenamento usam um modelo de preços para o armazenamento de blobs com base na camada de cada blob. Ao se usar uma conta de armazenamento, as seguintes considerações de cobranças são aplicáveis:

* **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia dependendo da camada de acesso de armazenamento. O custo por gigabyte diminui conforme a camada fica mais esporádica.

* **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados na camada de acesso de armazenamento esporádico e de arquivo, será cobrada uma taxa de acesso a dados por gigabyte.

* **Custos de transações**: há um encargo por transação para todas as camadas que aumenta à medida que a camada fica mais esporádica.

* **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.

* **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.

* **Alterando a camada de acesso de armazenamento**: alterar a camada de acesso de armazenamento de conta de fria para quente incorre em um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de acesso à conta de frequente para esporádico gera uma cobrança igual à gravação de todos os dados na camada de acesso esporádico (somente contas GPv2).

> [!NOTE]
> Para saber mais informações sobre o modelo de preços para contas de armazenamento, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais informações sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar custos para seus padrões de uso atuais

Para estimar o custo de armazenar e acessar dados de blob em uma conta de armazenamento de uso geral v2 em uma camada específica, avalie seu padrão de uso existente ou estime seu padrão de uso esperado. Em geral, você precisa saber:

* Seu consumo de armazenamento de Blobs, em gigabytes, incluindo:
  * Quantos dados estão sendo armazenados na conta de armazenamento?
  * Como o volume de dados é alterado mensalmente; os dados novos substituem constantemente os dados antigos?

* O padrão de acesso primário para seus dados de armazenamento de Blobs, incluindo:
  * Qual é quantidade de dados que está sendo lida e gravada para a conta de armazenamento?
  * Quantas operações de leitura versus operações de gravação ocorrem nos dados na conta de armazenamento?

Para decidir a melhor camada de acesso para suas necessidades, pode ser útil determinar a capacidade de dados de blob e como esses dados estão sendo usados. Isso pode ser feito melhor examinando as métricas de monitoramento para sua conta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorando contas de armazenamento existentes

Para monitorar suas contas de armazenamento existentes e reunir esses dados, você poderá fazer uso da Análise de Armazenamento do Azure, que executa logs e fornece dados de métrica para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações em um serviço de armazenamento para tipos de conta de Armazenamento de GPv1, GPv2 e armazenamento de Blobs. Esses dados são armazenados em tabelas conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, acesse [Sobre métricas de análise de armazenamento](../blobs/monitor-blob-storage.md) e [Esquema de tabela de métricas da análise de armazenamento](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)

> [!NOTE]
> As contas de Armazenamento de Blobs expõem o ponto de extremidade de serviço de tabela apenas para armazenar e acessar dados de métricas dessa conta.

Para monitorar o consumo do Armazenamento de Blobs, você precisa habilitar as métricas de capacidade.
Com isso habilitado, os dados de capacidade são gravados diariamente para o serviço blob de uma conta de armazenamento e registrados como uma entrada de tabela que é gravada na tabela de *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorar os padrões de acesso a dados para o armazenamento de Blobs, você precisa habilitar as métricas de transações por hora da API. Com esse recurso habilitado, as transações por API são agregadas a cada hora e registradas como uma entrada de tabela que é gravada na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* registra as transações para o ponto de extremidade secundário ao usar contas de armazenamento RA-GRS.

> [!NOTE]
> Caso você tenha uma conta de armazenamento para uso geral na qual armazenou blobs de página, discos de máquina virtual, ou filas, arquivos ou tabelas junto com blob de bloco e de acréscimo, esse processo de previsão não se aplica. Isso ocorre porque os dados de capacidade não diferenciam blobs de bloco de outros tipos e não fornecem dados de capacidade para outros tipos de dados. Caso use esses tipos, uma metodologia alternativa é examinar as quantidades na sua conta mais recente.

Para obter uma boa aproximação do consumo de dados e do padrão de acesso, recomendamos escolher um período de retenção para as métricas que representam seu uso regular e extrapolar. Uma opção é manter os dados de métricas por sete dias e coletar os dados de cada semana, para analisá-las no final do mês. Outra opção é manter os dados de métricas dos últimos 30 dias e coletar e analisar os dados ao fim do período de 30 dias.

Para obter detalhes sobre como habilitar, coletar e exibir dados de métricas, consulte [métricas de análise de armazenamento](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> O armazenamento, acesso e download de dados de análise também serão cobrados, assim como os dados de usuário comuns.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizando métricas de uso para estimar custos

#### <a name="capacity-costs"></a>Custos de capacidade

A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida por dados do usuário. A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida por logs de análise.

Essa capacidade total consumida tanto por dados de usuário quanto logs de análise (se habilitado) pode ser usada para estimar o custo de armazenar dados na conta de armazenamento. O mesmo método também pode ser usado para estimar os custos de armazenamento nas contas de armazenamento de GPv1.

#### <a name="transaction-costs"></a>Custos de transação

A soma de *'TotalBillableRequests'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de transações para essa API em particular. *Por exemplo*, o número total de transações *'GetBlob'* em um determinado período pode ser calculado pela soma do total de solicitações faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para estimar os custos de transação para contas de armazenamento de Blobs, você precisa dividir as transações em três grupos, já que elas possuem preços diferentes.

* Transações de gravação, como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de exclusão, como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação para contas de armazenamento de GPv1, você precisa agregar todas as transações, independentemente da operação/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de transferência de dados de acesso aos dados e de replicação geográfica

Embora a análise de armazenamento não forneça a quantidade de dados lida e gravada em uma conta de armazenamento, ela pode ser estimada aproximadamente examinando a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API em particular. Da mesma forma, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para estimar os custos de acesso de dados para as contas de armazenamento de Blobs, você precisa dividir as transações em dois grupos.

* A quantidade de dados recuperados da conta de armazenamento pode ser estimada observando a soma de *'TotalEgress'* basicamente para as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados gravados na conta de armazenamento pode ser estimada observando a soma de *'TotalIngress'* basicamente para as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

O custo da transferência de dados de replicação geográfica para contas de armazenamento de Blobs também pode ser calculado usando a estimativa de quantidade de dados gravados ao usar uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado de como calcular os custos para usar a camada de acesso de armazenamento frequente ou esporádico, dê uma olhada nas Perguntas frequentes intituladas *“O que são as camadas de acesso frequente e esporádico e como devo determinar qual delas usar?”* na [Página de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da conta de armazenamento](storage-account-overview.md)
* [Criar uma conta de armazenamento](storage-account-create.md)
* [Mover uma conta de armazenamento do Azure para outra região](storage-account-move.md)
* [Recuperar uma conta de armazenamento excluída](storage-account-recover.md)
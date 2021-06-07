---
title: Solucionar problemas de falhas de criação de cluster com o Azure HDInsight
description: Saiba como solucionar problemas de criação do cluster do Apache para o Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: f909f5714a049f63032c8a23ca1731777a40d332
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702854"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Solucionar problemas de falhas de criação de cluster com o Azure HDInsight

Os seguintes problemas são causas raiz mais comuns para falhas de criação de cluster:

- Problemas de permissão
- Restrições de política de recurso
- Firewalls
- Bloqueios de recurso
- Versões de componentes sem suporte
- Restrições de nome de conta de armazenamento
- Interrupções de serviço

## <a name="permissions-issues"></a>Problemas de permissões

Se você estiver usando Azure data Lake Storage Gen2 e receber o erro `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", abra o portal do Azure, vá para sua conta de armazenamento e, em controle de acesso (iam), verifique se o **colaborador de armazenamento de dados** ou a função de proprietário de **dados** de blob de armazenamento atribuiu acesso à **identidade gerenciada atribuída pelo usuário** para a assinatura. Consulte [configurar permissões para a identidade gerenciada no data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) para obter instruções detalhadas.

Se você estiver usando Azure Data Lake Storage Gen1, consulte instruções de instalação e configuração [Use Azure data Lake Storage Gen1 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md). Data Lake Storage Gen1 não tem suporte para clusters HBase e não tem suporte no HDInsight versão 4,0.

Se estiver usando o armazenamento do Azure, verifique se o nome da conta de armazenamento é válido durante a criação do cluster.

## <a name="resource-policy-restrictions"></a>Restrições de política de recurso

As políticas do Azure baseadas em assinatura podem negar a criação de endereços IP públicos. A criação do cluster HDInsight exige dois IPs públicos.  

Em geral, as políticas a seguir podem afetar a criação do cluster:

* As políticas que impedem a criação de endereços IP & balanceadores de carga na assinatura.
* Política que impede a criação da conta de armazenamento.
* Política que impede a exclusão de recursos de rede (endereço IP/Load balanceadores de carga).

## <a name="firewalls"></a>Firewalls

Firewalls em sua rede virtual ou conta de armazenamento podem negar comunicação com endereços IP de gerenciamento do HDInsight.

Permita o tráfego dos endereços IP na tabela abaixo.

| Endereço IP de origem | Destino | Direção |
|---|---|---|
| 168.61.49.99 | *: 443 | Entrada |
| 23.99.5.239 | *: 443 | Entrada |
| 168.61.48.131 | *: 443 | Entrada |
| 138.91.141.162 | *: 443 | Entrada |

Além disso, adicione os endereços IP específicos à região em que o cluster é criado. Consulte [endereços IP de gerenciamento do HDInsight](../hdinsight-management-ip-addresses.md) para obter uma lista dos endereços para cada região do Azure.

Se você estiver usando uma rota expressa ou seu próprio servidor DNS personalizado, consulte [planejar uma rede virtual para o Azure HDInsight – Conectando várias redes](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Bloqueios de recursos  

Verifique se não há [bloqueios em sua rede virtual e grupo de recursos](../../azure-resource-manager/management/lock-resources.md). Os clusters não poderão ser criados ou excluídos se o grupo de recursos estiver bloqueado. 

## <a name="unsupported-component-versions"></a>Versões de componentes sem suporte

Verifique se você está usando uma [versão com suporte do Azure HDInsight e o componente Apache Hadoop](../hdinsight-component-versioning.md) em sua solução.  

## <a name="storage-account-name-restrictions"></a>Restrições de nome de conta de armazenamento

Os nomes de conta de armazenamento não podem ter mais de 24 caracteres e não podem conter um caractere especial. Essas restrições também se aplicam ao nome do contêiner padrão na conta de armazenamento.

Outras restrições de nomenclatura também se aplicam à criação do cluster. Consulte [restrições de nome de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)para obter mais informações.

## <a name="service-outages"></a>Interrupções de serviço

Verifique o [status do Azure](https://status.azure.com) quanto a possíveis interrupções ou problemas de serviço.

## <a name="next-steps"></a>Próximas etapas

* [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Usar o Armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)

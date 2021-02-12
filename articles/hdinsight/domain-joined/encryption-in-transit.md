---
title: Criptografia do Azure HDInsight em trânsito
description: Saiba mais sobre os recursos de segurança para fornecer criptografia em trânsito para o cluster HDInsight do Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946853"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Criptografia IPSec em trânsito para o Azure HDInsight

Este artigo aborda a implementação de criptografia em trânsito para a comunicação entre os nós de cluster do Azure HDInsight.

## <a name="background"></a>Tela de fundo

O Azure HDInsight oferece uma variedade de recursos de segurança para proteger seus dados corporativos. Essas soluções são agrupadas sob os pilares de segurança, autenticação, autorização, auditoria, criptografia e conformidade do perímetro. A criptografia pode ser aplicada a dados em repouso e em trânsito.

A criptografia em repouso é coberta pela criptografia do lado do servidor nas contas de armazenamento do Azure, bem como pela criptografia de disco nas VMs do Azure que fazem parte de seu cluster HDInsight.

A criptografia de dados em trânsito no HDInsight é obtida com o protocolo [TLS](../transport-layer-security.md) para acessar os gateways de cluster e a [segurança de protocolo Internet (IPSec)](https://wikipedia.org/wiki/IPsec) entre nós de cluster. O IPSec pode ser opcionalmente habilitado entre todos os nós de cabeçalho, nós de trabalho, nós de borda, nós de Zookeeper, bem como nós de [agente de ID](./identity-broker.md) e gateway.

## <a name="enable-encryption-in-transit"></a>Habilitar criptografia em trânsito

### <a name="azure-portal"></a>Portal do Azure

Para criar um novo cluster com criptografia em trânsito habilitada usando o portal do Azure, execute as seguintes etapas:

1. Inicie o processo normal de criação do cluster. Consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para as etapas de criação de cluster inicial.
1. Conclua as guias **básico** e **armazenamento** . Vá para a guia **segurança + rede** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Crie a guia Segurança do cluster e rede.":::

1. Na guia **segurança + rede** , marque a caixa de seleção **habilitar criptografia em trânsito** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Criar cluster – habilite a criptografia em trânsito.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Crie um cluster com criptografia em trânsito habilitada por meio do CLI do Azure

A criptografia em trânsito é habilitada usando a `isEncryptionInTransitEnabled` propriedade.

Você pode [baixar um modelo de exemplo e um arquivo de parâmetro](https://github.com/Azure-Samples/hdinsight-enterprise-security). Antes de usar o modelo e o trecho de código de CLI do Azure abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Espaço reservado | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | A ID da sua assinatura do Azure |
| `<RESOURCE_GROUP>` | O grupo de recursos em que você deseja criar o novo cluster e a conta de armazenamento. |
| `<STORAGEACCOUNTNAME>` | A conta de armazenamento existente que deve ser usada com o cluster. O nome deve estar no formato `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | Sua senha escolhida para entrar no cluster usando SSH e o painel do Ambari. |
| `<VNET_NAME>` | A rede virtual em que o cluster será implantado. |

O trecho de código abaixo faz as seguintes etapas iniciais:

1. Faz logon em sua conta do Azure.
1. Define a assinatura ativa em que as operações de criação serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Implante o modelo para criar um novo cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da segurança empresarial no Azure HDInsight](hdinsight-security-overview.md)
* [Sincronizar Azure Active Directory usuários com um cluster HDInsight](../disk-encryption.md).

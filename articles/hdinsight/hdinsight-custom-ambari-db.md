---
title: Banco de dados do Apache Ambari personalizado no Azure HDInsight
description: Saiba como criar clusters HDInsight com seu próprio banco de dados do Apache Ambari personalizado.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946054"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurar clusters do HDInsight com um Ambari DB personalizado

O Apache Ambari simplifica o gerenciamento e o monitoramento de um cluster Apache Hadoop. O Ambari fornece uma interface do usuário da Web fácil de usar e API REST. O Ambari está incluído em clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração.

Na criação normal do cluster, conforme descrito em outros artigos, como [Configurar clusters no hdinsight](hdinsight-hadoop-provision-linux-clusters.md), o Ambari é implantado em um [banco de dados SQL do Azure S0](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) que é gerenciado pelo HDInsight e não pode ser acessado pelos usuários.

O recurso personalizado do Ambari DB permite que você implante um novo cluster e configure o Ambari em um banco de dados externo que você gerencia. A implantação é feita com um modelo de Azure Resource Manager. Esse recurso tem os seguintes benefícios:

- Personalização-você escolhe o tamanho e a capacidade de processamento do banco de dados. Se você tiver grandes clusters processando cargas de trabalho intensivas, um banco de dados Ambari com especificações mais baixas poderia se tornar um afunilamento para operações de gerenciamento.
- Flexibilidade-você pode dimensionar o banco de dados conforme necessário para atender às suas necessidades.
- Controle-você pode gerenciar backups e segurança para seu banco de dados de uma maneira que se ajuste aos requisitos de suas organizações.

O restante deste artigo aborda os seguintes pontos:

- requisitos para usar o recurso personalizado do Ambari DB
- as etapas necessárias para provisionar clusters HDInsight usando seu próprio banco de dados externo para o Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisitos personalizados do Ambari DB

Você pode implantar um Ambari DB personalizado com todos os tipos de cluster e versões. Vários clusters não podem usar o mesmo Ambari DB.

O Ambari DB personalizado tem os seguintes requisitos:

- O nome do banco de dados não pode conter hifens ou espaços
- Você deve ter um servidor e banco de dados SQL do Azure existente.
- O banco de dados que você fornece para a instalação do Ambari deve estar vazio. Não deve haver nenhuma tabela no esquema dbo padrão.
- O usuário usado para se conectar ao banco de dados deve ter as permissões SELECT, CREATE TABLE e INSERT no banco de dados.
- Ative a opção para [permitir o acesso aos serviços do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) no servidor em que você hospedará Ambari.
- Os endereços IP de gerenciamento do serviço HDInsight precisam ser permitidos na regra de firewall. Consulte [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md) para obter uma lista dos endereços IP que devem ser adicionados à regra de firewall no nível de servidor.

Ao hospedar o Apache Ambari DB em um banco de dados externo, lembre-se dos seguintes pontos:

- Você é responsável pelos custos adicionais do banco de BD SQL do Azure que mantém o Ambari.
- Faça backup do Ambari DB personalizado periodicamente. O banco de dados SQL do Azure gera backups automaticamente, mas o período de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implantar clusters com um Ambari DB personalizado

Para criar um cluster HDInsight que usa seu próprio banco de dados Ambari externo, use o [modelo de início rápido do BD Ambari personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edite os parâmetros no `azuredeploy.parameters.json` para especificar informações sobre o novo cluster e o banco de dados que conterá o Ambari.

Você pode iniciar a implantação usando o CLI do Azure. Substitua `<RESOURCEGROUPNAME>` pelo grupo de recursos no qual você deseja implantar o cluster.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Dimensionamento do banco de dados

A tabela a seguir fornece diretrizes sobre qual camada do BD SQL do Azure selecionar com base no tamanho do cluster HDInsight.

| Número de nós de trabalho | Camada de banco de BD necessária |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | Contatar o suporte |

## <a name="next-steps"></a>Próximas etapas

- [Usar armazenamentos de metadados externos no Azure HDInsight](hdinsight-use-external-metadata-stores.md)

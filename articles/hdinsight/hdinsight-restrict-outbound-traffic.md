---
title: Configurar a restrição de tráfego de rede de saída – Azure HDInsight
description: Saiba como configurar a restrição de tráfego de rede de saída para clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 297c1d4afca5a1d605a046d69b086a05a9322bc7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872074"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configurar o tráfego de rede de saída para clusters do Azure HDInsight usando o firewall

Este artigo fornece as etapas para proteger o tráfego de saída do seu cluster do HDInsight usando o Firewall do Azure. As etapas a seguir pressupõem que você está configurando um Firewall do Azure para um cluster existente. Se você estiver implantando um novo cluster atrás de um firewall, primeiro crie seu cluster e sub-rede do HDInsight. Depois, execute as etapas deste guia.

## <a name="background"></a>Segundo plano

Os clusters do HDInsight normalmente são implantados em uma rede virtual. O cluster tem dependências em serviços fora dessa rede virtual.

O tráfego de gerenciamento de entrada não pode ser enviado por meio de um firewall. Você pode usar marcas de serviço NSG para o tráfego de entrada conforme documentado [aqui](./hdinsight-service-tags.md). 

As dependências de tráfego de saída do HDInsight são quase totalmente definidas com FQDNs. E elas não têm endereços IP estáticos por trás. A falta de endereços estáticos significa que os NSGs (Grupos de Segurança de Rede) não podem ser usados para bloquear o tráfego de saída de um cluster. Os endereços IP são alterados com frequência suficiente não é possível configurar regras com base na resolução de nome e no uso atuais.

Proteja os endereços de saída com um firewall que pode controlar o tráfego de saída com base em FQDNs. O Firewall do Azure restringe o tráfego de saída com base no FQDN de destino ou nas [marcas de FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Como configurar o Firewall do Azure com o HDInsight

Um resumo das etapas para bloquear a saída do HDInsight existente com o Firewall do Azure:

1. Crie uma sub-rede.
1. Crie um firewall.
1. Adicione regras de aplicativo ao firewall
1. Adicione regras de rede ao firewall.
1. Crie uma tabela de roteamento.

### <a name="create-new-subnet"></a>Criar uma sub-rede

Crie uma sub-rede chamada **AzureFirewallSubnet** na rede virtual em que o cluster existe.

### <a name="create-a-new-firewall-for-your-cluster"></a>Criar um firewall para o cluster

Crie um firewall chamado **Test-FW01** usando as etapas em **Implantar o firewall** em [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configurar o firewall com regras de aplicativo

Crie uma coleção de regras de aplicativo que permita que o cluster envie e receba comunicações importantes.

1. Selecione o novo firewall **Test-FW01** do portal do Azure.

1. Navegue até **Configurações** > **Regras** > **Coleção de regras de aplicativos** >  **+ Adicionar coleção de regras de aplicativos**.

    :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png" alt-text="Título: Adicionar coleção de regras de aplicativos":::

1. Na tela **Adicionar coleção de regras de aplicativos**, forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwAppRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção de marcas de FQDN**

    | Nome | Endereço de origem | Marca FQDN | Observações |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Exigido para serviços HDIs |

    **Seção de FQDNs de destino**

    | Nome | Endereços de origem | Protocolo:Porta | FQDNs de destino | Observações |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Permite a atividade de logon do Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Permite a atividade de logon do Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Substitua `storage_account_name` pelo nome da conta de armazenamento real. Para usar APENAS conexões HTTPS, verifique se ["transferência segura necessária"](../storage/common/storage-require-secure-transfer.md) está habilitada na conta de armazenamento. Se você estiver usando um ponto de extremidade privado para acessar contas de armazenamento, essa etapa não será necessária e o tráfego de armazenamento não será encaminhado para o firewall.|

   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png" alt-text="Título: Entrar nos detalhes da coleção de regras de aplicativo":::

1. Selecione **Adicionar**.

### <a name="configure-the-firewall-with-network-rules"></a>Configurar o firewall com regras de rede

Crie as regras de rede para configurar corretamente o cluster do HDInsight.

1. Continuando da etapa anterior, navegue até **Coleção de regras de rede** >  **+ Adicionar coleção de regras de rede**.

1. Na tela **Adicionar coleção de regras de rede**, forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwNetRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção de marcas de serviço**

    | Nome | Protocolo | Endereços de Origem | Marcas de serviço | Portas de destino | Observações |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433 | Se você estiver usando os SQL Servers padrão fornecidos pelo HDInsight, configure uma regra de rede na seção marcas de serviço para SQL que permitirá que você registre e audite o tráfego do SQL. A menos que você tenha configurado pontos de extremidade de serviço para SQL Server na sub-rede do HDInsight, que ignorará o firewall. Se você estiver usando o SQL Server personalizado para metastores do Ambari, Oozie, Ranger e Hive, você só precisará permitir o tráfego para seus próprios servidores SQL personalizados.|
    | Rule_6 | TCP | * | Azure Monitor | * | (Opcional) Os clientes que planejam usar o recurso de dimensionamento automático devem adicionar essa regra. |
    
   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png" alt-text="Título: Inserir coleção de regras de aplicativos":::

1. Selecione **Adicionar**.

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Crie uma tabela de rotas com as seguintes entradas:

* Todos os endereços IP dos [serviços de integridade e gerenciamento](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) com um tipo de próximo salto de **Internet**. Ele deve incluir 4 IPs de regiões genéricas, bem como 2 IPs para sua região específica. Essa regra só será necessária se o ResourceProviderConnection estiver definido como *entrada*. Se o ResourceProviderConnection for definido como *saída* , esses IPS não serão necessários no UDR. 

* Uma rota da Solução de Virtualização para o endereço IP 0.0.0.0/0 com o próximo salto sendo seu endereço IP privado do Firewall do Azure.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região dos EUA "Leste dos EUA", use as seguintes etapas:

1. Selecione o firewall **Test-FW01** do Azure. Copie o **endereço IP privado** listado na página **Visão geral**. Para este exemplo, usaremos um **endereço de exemplo de 10.0.2.4**.

1. Em seguida, navegue até **Todos os serviços** > **Rede** > **Tabelas de rede** e **Criar Tabela de Rotas**.

1. Em sua nova rota, navegue até **Configurações** > **Rotas** >  **+ Adicionar**. Adicione as seguintes rotas:

| Nome da rota | Prefixo de endereço | Tipo do próximo salto | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Solução de virtualização | 10.0.2.4 |

Conclua a configuração da tabela de rotas:

1. Atribua a tabela de rotas que você criou à sub-rede do HDInsight selecionando **Sub-redes** em **Configurações**.

1. Selecione **+ Associar**.

1. Na tela **Associar sub-rede**, selecione a rede virtual na qual o cluster foi criado. E a **Sub-rede** usada para o cluster do HDInsight.

1. Selecione **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nó de borda ou tráfego de aplicativo personalizado

As etapas acima permitirão que o cluster opere sem problemas. Você ainda precisa configurar dependências para acomodar seus aplicativos personalizados em execução nos nós de borda, se aplicável.

As dependências de aplicativo precisam ser identificadas e adicionadas ao Firewall do Azure ou à tabela de rotas.

As rotas precisam ser criadas para o tráfego de aplicativo a fim de evitar problemas de roteamento assimétrico.

Se os seus aplicativos tiverem outras dependências, eles precisarão ser adicionados ao Firewall do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e regras de Rede para todo o resto.

## <a name="logging-and-scale"></a>Registrar em log e escalar

O Firewall do Azure pode enviar logs para alguns sistemas de armazenamento diferentes. Para obter instruções sobre como configurar o registro em log para o firewall, siga as etapas em [Tutorial: monitorar os logs e as métricas do Firewall do Azure](../firewall/firewall-diagnostics.md).

Depois de concluir a configuração do registro em log, se você estiver usando Log Analytics, poderá exibir o tráfego bloqueado com uma consulta, como:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A integração do Firewall do Azure aos logs do Azure Monitor é útil ao colocar um aplicativo para ser executado pela primeira vez. Especialmente quando você não está ciente de todas as dependências do aplicativo. Saiba mais sobre os logs do Azure Monitor em [Analisar dados de log no Azure Monitor](../azure-monitor/logs/log-query-overview.md)

Para saber mais sobre os limites de escala do Firewall do Azure e solicitar aumentos, confira [este](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento ou consulte as [Perguntas frequentes](../firewall/firewall-faq.yml).

## <a name="access-to-the-cluster"></a>Acessar o cluster

Depois de configurar o firewall com êxito, você poderá usar o ponto de extremidade interno (`https://CLUSTERNAME-int.azurehdinsight.net`) para acessar o Ambari de dentro da rede virtual.

Para usar o ponto de extremidade público (`https://CLUSTERNAME.azurehdinsight.net`) ou o ponto de extremidade SSH (`CLUSTERNAME-ssh.azurehdinsight.net`), verifique se você tem as rotas certas na tabela de rotas e as regras de NSG para evitar o problema de roteamento assimétrico explicado [aqui](../firewall/integrate-lb.md). Especificamente nesse caso, você precisa permitir o endereço IP do cliente nas regras de NSG de entrada e também adicioná-lo à tabela de rotas definida pelo usuário com o próximo salto definido como `internet`. Se o roteamento não estiver configurado corretamente, você verá um erro de tempo limite.

## <a name="next-steps"></a>Próximas etapas

* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurar solução de virtualização de rede](./network-virtual-appliance.md)

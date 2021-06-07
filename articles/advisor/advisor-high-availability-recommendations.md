---
title: Aprimore a confiabilidade do seu aplicativo com o Advisor
description: Use o Azure Advisor para garantir e melhorar a confiabilidade em implantações críticas do Azure para os negócios.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579965"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Melhorar a confiabilidade do seu aplicativo usando o Azure Advisor

O Assistente do Azure ajuda a garantir e melhorar a continuidade de aplicativos críticos aos negócios. Você pode obter recomendações de confiabilidade do Advisor na guia **confiabilidade** do painel do Advisor.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Verifique a versão da imagem da solução de virtualização de rede do Check Point

O Advisor pode identificar se sua máquina virtual está executando uma versão da imagem do Check Point que foi conhecida por perder a conectividade de rede durante as operações de manutenção da plataforma. A recomendação do assistente o ajudará a atualizar para uma versão mais recente da imagem que resolve esse problema. Essa verificação garantirá a continuidade dos negócios por meio de uma melhor conectividade de rede.

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância a falhas de Gateway de Aplicativo

Essa recomendação garante a continuidade de negócios de aplicativos de missão crítica viabilizados por gateways de aplicativo. O Advisor identifica as instâncias do gateway de aplicativo que não estão configuradas para tolerância a falhas. Em seguida, ele sugere ações de correção que podem ser tomadas. O Advisor identifica gateways de aplicativo de instância única médio ou grande e recomenda adicionar pelo menos mais uma instância. Ele também identifica gateways de aplicativo de instância única e de várias instâncias pequenas e recomenda migrá-los para SKUs médias ou grandes. O Advisor recomenda essas ações para garantir que as instâncias do gateway de aplicativo sejam configuradas para atender aos requisitos atuais de SLA para esses recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger seus dados de máquina virtual contra exclusão acidental

Configurar o backup da máquina virtual garante a disponibilidade de seus dados críticos de negócios e oferece proteção contra corrupção ou exclusão acidental. O Advisor identifica as máquinas virtuais em que o backup não está habilitado e recomenda habilitar o backup. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Verifique se você tem acesso aos especialistas do Azure quando precisar

Quando você estiver executando uma carga de trabalho crítica aos negócios, é importante ter acesso ao suporte técnico quando precisar. O Advisor identifica possíveis assinaturas críticas para os negócios que não têm suporte técnico incluído em seus planos de suporte. Ele recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crie alertas de integridade do serviço do Azure para ser notificado quando os problemas do Azure afetarem você

É recomendável configurar alertas de integridade do serviço do Azure para que você seja notificado quando os problemas de serviço do Azure afetarem você. A [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Advisor identifica as assinaturas que não têm alertas configurados e recomenda configurá-los.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos de extremidade do Gerenciador de Tráfego para resiliência

Os perfis do Gerenciador de tráfego do Azure com mais de um ponto de extremidade terão maior disponibilidade se qualquer ponto de extremidade especificado falhar. Colocar pontos de extremidade em diferentes regiões melhora ainda mais a confiabilidade do serviço. O Advisor identifica os perfis do Gerenciador de tráfego em que há apenas um ponto de extremidade e recomenda adicionar pelo menos mais um ponto de extremidade em outra região.

Se todos os pontos de extremidade em um perfil do Gerenciador de tráfego configurado para roteamento de proximidade estiverem na mesma região, os usuários de outras regiões poderão enfrentar atrasos de conexão. Adicionar ou mover um ponto de extremidade para outra região melhorará o desempenho geral e fornecerá maior disponibilidade se todos os pontos de extremidade em uma região falharem. O Assistente identifica os perfis do Gerenciador de Tráfego configurados para roteamento de proximidade onde os pontos de extremidade estão na mesma região. Ele recomenda adicionar ou mover um ponto de extremidade para outra região do Azure.

Se um perfil do Gerenciador de tráfego estiver configurado para roteamento geográfico, o tráfego será roteado para pontos de extremidade com base em regiões definidas. Se uma região falhar, não haverá nenhum failover predefinido. Se você tiver um ponto de extremidade onde o agrupamento regional está configurado para **todos (mundo)**, você pode evitar o descarte do tráfego e melhorar a disponibilidade do serviço. O Advisor identifica os perfis do Gerenciador de tráfego configurados para o roteamento geográfico, onde não há nenhum ponto de extremidade configurado para ter o agrupamento regional como **todos (mundo)**. Recomenda alterar a configuração para criar um ponto de extremidade **(mundo)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Use a exclusão reversível em sua conta de armazenamento do Azure para salvar e recuperar dados após substituição ou exclusão acidental

Habilite a [exclusão reversível](../storage/blobs/soft-delete-blob-overview.md) na conta de armazenamento para que os blobs excluídos passem para um estado de exclusão reversível, em vez de serem excluídos permanentemente. Quando os dados são substituídos, um instantâneo de exclusão reversível é gerado para salvar o estado dos dados substituídos. Usar a exclusão reversível permite que você se recupere de exclusões acidentais ou substituições. O Advisor identifica as contas de armazenamento do Azure que não têm a exclusão reversível habilitada e sugere que você a habilite.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configure seu gateway de VPN como ativo-ativo para ter resiliência de conexão

Na configuração ativo-ativo, ambas as instâncias de um gateway de VPN estabelecem túneis VPN S2S para seu dispositivo VPN local. Quando um evento de manutenção planejada ou um evento não planejado ocorre em uma instância de gateway, o tráfego é alternado automaticamente para o outro túnel IPsec ativo. O Azure Advisor identifica gateways de VPN que não estão configurados como ativo-ativo e sugere que você os configure para alta disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Usar gateways de VPN de produção para executar suas cargas de trabalho de produção

O Azure Advisor verifica se há gateways de VPN que usam uma SKU básica e recomenda que você use uma SKU de produção. O SKU básico foi projetado para desenvolvimento e teste. Oferta de SKUs de produção:
- Mais túneis. 
- Suporte a BGP. 
- Opções de configuração ativo-ativo. 
- Política de IPSec/IKE personalizada. 
- Maior estabilidade e disponibilidade.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Garantir a tolerância a falhas da máquina virtual (temporariamente desabilitada)

Para fornecer redundância para seu aplicativo, recomendamos que você agrupe duas ou mais máquinas virtuais em um conjunto de disponibilidade. O Advisor identifica as máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para uma. Essa configuração garante que, durante a manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA da máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, precisará adicionar pelo menos mais uma máquina virtual a ele. É recomendável agrupar duas ou mais máquinas virtuais em um conjunto de disponibilidade para garantir que pelo menos uma delas esteja disponível durante uma interrupção.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Garantir tolerância a falhas de conjunto de disponibilidade (temporariamente desabilitado)

Para fornecer redundância para seu aplicativo, recomendamos que você agrupe duas ou mais máquinas virtuais em um conjunto de disponibilidade. O Assistente identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais a ele. Essa configuração garante que, durante a manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA da máquina virtual do Azure. Você pode optar por criar uma máquina virtual ou então por adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Usar discos gerenciados para melhorar a confiabilidade dos dados (temporariamente desabilitados)

As máquinas virtuais que estão em um conjunto de disponibilidade com discos que compartilham contas de armazenamento ou unidades de escala de armazenamento não são resilientes a falhas em unidades de escala de armazenamento único durante interrupções. O Advisor identifica esses conjuntos de disponibilidade e recomenda a migração para o Azure Managed disks. Essa migração garantirá que os discos das máquinas virtuais no conjunto de disponibilidade sejam suficientemente isolados para evitar um ponto único de falha. 

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detecta as regras de alerta de log que têm consultas inválidas especificadas na seção de condição. Azure Monitor regras de alerta de log executam consultas na frequência especificada e acionam alertas com base nos resultados. As consultas podem se tornar inválidas ao longo do tempo devido a alterações nos recursos, tabelas ou comandos referenciados. O Advisor recomenda correções para consultas de alerta para impedir que as regras sejam automaticamente desabilitadas e garantir a cobertura de monitoramento. Para obter mais informações, consulte [Solucionando problemas de regras de alerta](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Configurar o modo de indexação consistente na sua coleção de Azure Cosmos DB

A configuração de contêineres de Azure Cosmos DB com modo de indexação lento pode afetar a atualização dos resultados da consulta. O Advisor detecta contêineres configurados dessa forma e recomenda alternar para o modo consistente. [Saiba mais sobre políticas de indexação no Azure Cosmos DB.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contêineres do Azure Cosmos DB com uma chave de partição

O Azure Advisor identifica Azure Cosmos DB coleções não particionadas que estão se aproximando da cota de armazenamento provisionada. Recomenda-se migrar essas coleções para novas coleções com uma definição de chave de partição para que elas possam ser dimensionadas automaticamente pelo serviço. [Saiba mais sobre como escolher uma chave de partição.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK do .NET Azure Cosmos DB para a versão mais recente do NuGet

O Azure Advisor identifica Azure Cosmos DB contas que estão usando versões antigas do SDK do .NET. Ele recomenda que você atualize para a versão mais recente do NuGet para as correções mais recentes, aprimoramentos de desempenho e recursos de recurso. [Saiba mais sobre Azure Cosmos DB SDK do .NET.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK do Java do Azure Cosmos DB para a versão mais recente do Maven

O Azure Advisor identifica Azure Cosmos DB contas que estão usando versões antigas do SDK do Java. Ele recomenda que você atualize para a versão mais recente do Maven para as correções mais recentes, aprimoramentos de desempenho e recursos de recurso. [Saiba mais sobre Azure Cosmos DB SDK do Java.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o conector do Spark Azure Cosmos DB para a versão mais recente do Maven

O Azure Advisor identifica Azure Cosmos DB contas que estão usando versões antigas do conector Azure Cosmos DB Spark. Ele recomenda que você atualize para a versão mais recente do Maven para as correções mais recentes, aprimoramentos de desempenho e recursos de recurso. [Saiba mais sobre o conector do Spark Azure Cosmos DB.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Considere a mudança para o Kafka 2,1 no HDInsight 4,0

A partir de 1º de julho de 2020, você não poderá criar novos clusters Kafka usando o Kafka 1,1 no Azure HDInsight 4,0. Os clusters existentes serão executados como estão, sem suporte da Microsoft. Considere a mudança para o Kafka 2,1 no HDInsight 4,0 até 30 de junho de 2020, para evitar a interrupção potencial do sistema/suporte.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Considere atualizar versões mais antigas do Spark em clusters HDInsight Spark

A partir de 1º de julho de 2020, você não poderá criar novos clusters Spark usando o Spark 2,1 ou 2,2 no HDInsight 3,6. Você não poderá criar novos clusters Spark usando o Spark 2,3 no HDInsight 4,0. Os clusters existentes serão executados como estão, sem suporte da Microsoft. 

## <a name="enable-virtual-machine-replication"></a>Habilitar replicação de máquina virtual
As máquinas virtuais que não têm replicação habilitada para outra região não são resilientes a interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso nos negócios durante interrupções de região do Azure. O Advisor detecta VMs em que a replicação não está habilitada e recomenda habilitá-la. Ao habilitar a replicação, se houver uma interrupção, você poderá rapidamente abrir suas máquinas virtuais em uma região remota do Azure. [Saiba mais sobre a replicação da máquina virtual.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Atualizar para a versão mais recente do agente de Azure Connected Machine
O [agente do computador conectado do Azure](../azure-arc/servers/manage-agent.md) é atualizado regularmente com correções de bugs, aprimoramentos de estabilidade e novas funcionalidades. Identificamos os recursos que não estão funcionando na versão mais recente do agente do computador e essa recomendação do Advisor irá sugerir que você atualize seu agente para a versão mais recente para a melhor experiência de Arc do Azure.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Não substitua o nome do host para garantir a integridade do site
O Advisor recomenda tentar evitar a substituição do nome do host ao configurar o gateway de aplicativo. Ter um domínio diferente no front-end do Gateway de Aplicativo do que o usado para acessar o back-end pode levar a uma interrupção de cookies ou do redirecionamento de URLs. Observe que esse pode não ser o caso em todas as situações, e determinadas categorias de back-ends (como API REST) em geral são menos sensíveis a isso. Verifique se o back-end é capaz de lidar com isso ou atualize a configuração do Gateway de Aplicativo para que o nome do host não precise ser substituído para o back-end. Quando usado com o serviço de aplicativo, anexe um nome de domínio personalizado ao aplicativo Web e evite usar o *nome de host. azurewebsites.net em direção ao back-end.* [Saiba mais sobre o domínio personalizado](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de alta disponibilidade no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  No painel do Advisor, selecione a guia **alta disponibilidade** .

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do supervisor](azure-advisor-score.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
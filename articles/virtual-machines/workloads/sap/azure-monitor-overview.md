---
title: Visão geral e arquitetura do Azure Monitor para soluções SAP | Microsoft Docs
description: Este artigo fornece respostas para as perguntas frequentes sobre o Azure monitor para soluções SAP
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a88ad3930e114bdf9f3c3c340f92f164215d59c1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671989"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure monitor para soluções SAP (versão prévia)

## <a name="overview"></a>Visão geral

Azure Monitor para soluções SAP é um produto de monitoramento nativo do Azure para clientes, executando seus cenários SAP no Azure. O produto funciona com [SAP em máquinas virtuais do Azure](./hana-get-started.md) e [SAP em instâncias grandes do Azure](./hana-overview-architecture.md).
Com o Azure Monitor para soluções SAP, os clientes podem coletar dados de telemetria da infraestrutura do Azure e de bancos de dados em um local central e correlacionar visualmente os dados de telemetria para uma solução de problemas mais rápida.

O Azure Monitor para soluções SAP é oferecido por meio do Azure Marketplace. Ele fornece uma experiência de instalação simples e intuitiva e leva apenas alguns cliques para implantar o recurso para Azure Monitor para soluções SAP (conhecido como **recurso do SAP monitor**).

Os clientes podem monitorar diferentes componentes de uma estrutura SAP, como máquinas virtuais do Azure, cluster de alta disponibilidade, SAP HANA banco de dados e assim por diante, adicionando o **provedor** correspondente para esse componente.

Infraestrutura com suporte:

- Máquina Virtual do Azure
- Instância grande do Azure

Bancos de dados com suporte:
- Banco de dados do SAP HANA
- Microsoft SQL Server

Azure Monitor para soluções SAP usa o poder de recursos de [Azure monitor](../../../azure-monitor/overview.md) existentes, como log Analytics e [pastas de trabalho](../../../azure-monitor/visualize/workbooks-overview.md) , para fornecer mais recursos de monitoramento. Os clientes podem criar [visualizações personalizadas](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) editando as pastas de trabalho padrão fornecidas pelo Azure monitor para soluções SAP, escrever [consultas personalizadas](../../../azure-monitor/logs/log-analytics-tutorial.md) e criar [alertas personalizados](../../../azure-monitor/alerts/tutorial-response.md) usando o espaço de trabalho log Analytics do Azure, aproveitar o período de [retenção flexível](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) e conectar dados de monitoramento com seu sistema de tíquetes.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Quais dados Azure Monitor para soluções SAP coletam?

A coleta de dados no Azure Monitor para soluções SAP depende dos provedores configurados pelos clientes. Durante a visualização pública, os dados a seguir estão sendo coletados.

Teledisponibilidade de cluster do pacemaker de alto nível:
- Status do dispositivo de nó, recurso e SBD
- Restrições de local pacemaker
- Votos de quorum e status de anel
- [Others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetria de SAP HANA:
- Utilização de CPU, memória, disco e rede
- Replicação de sistema do HANA (HSR)
- Backup do HANA
- Status do host HANA
- Servidor de índice e funções de servidor de nome

Telemetria do Microsoft SQL Server:
- CPU, memória, utilização de disco
- Hostname, nome da instância SQL, ID do sistema SAP
- Solicitações em lote, compilações e expectativa de vida da página ao longo do tempo
- 10 principais instruções SQL mais caras ao longo do tempo
- Mais 12 maiores tabelas no sistema SAP
- Problemas registrados no log de erros do SQL Server
- Processos de bloqueio e estatísticas de espera do SQL ao longo do tempo

Telemetria do sistema operacional (Linux) 
- Utilização da CPU, contagem de bifurcação, processos em execução e bloqueados. 
- Utilização de memória e distribuição entre o utilizado, armazenado em cache e em buffer. 
- Troca de utilização, paginação e taxa de permuta. 
- Utilização de sistemas de File, número de bytes lidos e gravados por dispositivo de bloco. 
- Latência de leitura/gravação por dispositivo de bloco. 
- Contagem de e/s contínua, bytes de leitura/gravação de memória persistente. 
- Entrada/saída de pacotes de rede, entrada/saída de bytes de rede 

## <a name="data-sharing-with-microsoft"></a>Compartilhamento de dados com a Microsoft

A Azure Monitor para soluções SAP coleta metadados do sistema para fornecer suporte aprimorado ao nosso SAP em clientes do Azure. Nenhuma PII/EUII é coletada.
Os clientes podem habilitar o compartilhamento de dados com a Microsoft no momento da criação de Azure Monitor para recursos de soluções SAP escolhendo *compartilhar* na lista suspensa.
É altamente recomendável que os clientes habilitem o compartilhamento de dados, pois ele fornece ao suporte da Microsoft e às equipes de engenharia mais informações sobre o ambiente do cliente e fornece suporte aprimorado ao nosso SAP de missão crítica em clientes do Azure.

## <a name="architecture-overview"></a>Visão geral da arquitetura

Em um alto nível, o diagrama a seguir explica como o Azure Monitor para soluções SAP coleta a telemetria do banco de dados SAP HANA. A arquitetura é independente de o SAP HANA ser implantado em máquinas virtuais do Azure ou em instâncias grandes do Azure.

![Azure Monitor para arquitetura de soluções SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

Os principais componentes da arquitetura são:
- Portal do Azure – o ponto de partida para os clientes. Os clientes podem navegar até o Marketplace dentro de portal do Azure e descobrir Azure Monitor para soluções SAP
- Azure Monitor para recursos de soluções SAP – um local de chegada para os clientes exibirem a telemetria de monitoramento
- Grupo de recursos gerenciado – implantado automaticamente como parte do Azure Monitor para a implantação de recursos de soluções SAP. Os recursos implantados dentro da ajuda do grupo de recursos gerenciado na coleção de telemetria. Os principais recursos implantados e suas finalidades são:
   - Máquina virtual do Azure: também conhecida como *VM do coletor*. Esta é uma VM Standard_B2ms. A principal finalidade dessa VM é hospedar a carga de *monitoramento*. A carga de monitoramento refere-se à lógica de coleta de telemetria dos sistemas de origem e da transferência dos dados coletados para a estrutura de monitoramento. No diagrama acima, a carga de monitoramento contém a lógica para se conectar ao banco de dados SAP HANA pela porta do SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): esse recurso é implantado para conter com segurança SAP Hana credenciais de banco de dados e para armazenar informações sobre [provedores](./azure-monitor-providers.md).
   - Espaço de trabalho Log Analytics: o destino onde residem os dados de telemetria.
      - A visualização é criada sobre a telemetria no Log Analytics usando [pastas de trabalho do Azure](../../../azure-monitor/visualize/workbooks-overview.md). Os clientes podem personalizar a visualização. Os clientes também podem fixar suas pastas de trabalho ou visualização específica dentro de pastas de trabalho no painel do Azure para o recurso de atualização manual com menor granularidade de 30 minutos.
      - Os clientes podem usar seu espaço de trabalho existente na mesma assinatura que o recurso de monitoramento do SAP escolhendo essa opção no momento da implantação.
      - Os clientes podem usar a linguagem de consulta Kusto (KQL) para executar [consultas](../../../azure-monitor/logs/log-query-overview.md) em tabelas brutas dentro do espaço de trabalho log Analytics. Examine *os logs personalizados*.

> [!Note]
> Os clientes são responsáveis por aplicar patches e manter a VM, implantada no grupo de recursos gerenciado.

> [!Tip]
> Os clientes podem optar por usar um espaço de trabalho Log Analytics existente para coleta de telemetria, se estiver implantado na mesma assinatura do Azure que o recurso para Azure Monitor para soluções SAP.

### <a name="architecture-highlights"></a>Destaques da arquitetura

Veja a seguir os principais destaques da arquitetura:
 - **Várias** instâncias-os clientes podem criar um monitor para várias ocorrências de um determinado tipo de componente (por exemplo, o Hana DB, o cluster de ha, o Microsoft SQL Server) em vários SIDs SAP em uma VNET com um único recurso de Azure monitor para soluções SAP.
 - **Vários provedores** – o diagrama de arquitetura acima mostra o provedor de SAP Hana como um exemplo. Da mesma forma, os clientes podem configurar mais provedores para componentes correspondentes (por exemplo, o HANA DB, o cluster de HA, o Microsoft SQL Server) para coletar dados desses componentes.
 - Software **livre-o código-fonte** de Azure monitor para soluções SAP está disponível no [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Os clientes podem consultar o código do provedor e saber mais sobre o produto, contribuir ou compartilhar comentários.
 - **Estrutura de consulta extensível** – consultas SQL para coletar dados de telemetria são gravadas em [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Mais consultas SQL para coletar mais dados de telemetria podem ser facilmente adicionadas. Os clientes podem solicitar que dados de telemetria específicos sejam adicionados a Azure Monitor para soluções SAP, deixando comentários por meio do link no final deste documento ou contatando sua equipe de contas.

## <a name="pricing"></a>Preços
Azure Monitor para soluções SAP é um produto gratuito (sem taxa de licença). Os clientes são responsáveis por pagar o custo dos componentes subjacentes no grupo de recursos gerenciado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os provedores e crie seu primeiro Azure Monitor para o recurso de soluções SAP.
 - Saiba mais sobre [provedores](./azure-monitor-providers.md)
 - [Implantar o Azure Monitor para soluções SAP com o Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Você tem dúvidas sobre Azure Monitor para soluções SAP? Verifique a seção de [perguntas frequentes](./azure-monitor-faq.md)

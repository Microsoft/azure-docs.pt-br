---
title: Azure Monitor para provedores de soluções SAP | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre o Azure monitor para provedores de soluções SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 8192d7104daf1474a2123331183edf05e6fa1ada
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007407"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure monitor para provedores de soluções SAP (versão prévia)

## <a name="overview"></a>Visão geral  

No contexto de Azure Monitor para soluções SAP, um *tipo de provedor* se refere a um *provedor* específico. Por exemplo *SAP Hana*, que é configurado para um componente específico dentro do cenário do SAP, como SAP Hana banco de dados. Um provedor contém as informações de conexão para o componente correspondente e ajuda a coletar dados de telemetria desse componente. Um Azure Monitor para o recurso de soluções SAP (também conhecido como recurso do SAP monitor) pode ser configurado com vários provedores do mesmo tipo de provedor ou vários provedores de vários tipos de provedor.
   
Os clientes podem optar por configurar diferentes tipos de provedor para habilitar a coleta de dados do componente correspondente em sua estrutura SAP. Por exemplo, os clientes podem configurar um provedor para SAP HANA tipo de provedor, outro provedor para o tipo de provedor de cluster de alta disponibilidade e assim por diante.  

Os clientes também podem optar por configurar vários provedores de um tipo de provedor específico para reutilizar o mesmo recurso de monitoramento do SAP e o grupo gerenciado associado. Saiba mais sobre o grupo de recursos gerenciado. Para a visualização pública, há suporte para os seguintes tipos de provedor:   
- SAP HANA
- Cluster de alta disponibilidade
- Microsoft SQL Server

![Azure Monitor para provedores de soluções SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Os clientes são recomendados para configurar pelo menos um provedor dos tipos de provedor disponíveis no momento da implantação do recurso de monitor do SAP. Ao configurar um provedor, os clientes iniciam a coleta de dados do componente correspondente para o qual o provedor está configurado.   

Se os clientes não configurarem provedores no momento da implantação do recurso do SAP monitor, embora o recurso do SAP monitor seja implantado com êxito, nenhum dado de telemetria será coletado. Os clientes têm a opção de adicionar provedores após a implantação por meio do recurso de monitoramento do SAP dentro do portal do Azure. Os clientes podem adicionar ou excluir provedores do recurso de monitoramento do SAP a qualquer momento.

> [!Tip]
> Se você quiser que a Microsoft implemente um provedor específico, deixe comentários por meio do link no final deste documento ou entre em contato com sua equipe de conta.  

## <a name="provider-type-sap-hana"></a>Tipo de provedor SAP HANA

Os clientes podem configurar um ou mais provedores do tipo de provedor *SAP Hana* para habilitar a coleta de dados do banco de SAP Hana. O provedor de SAP HANA se conecta ao banco de dados do SAP HANA pela porta do SQL, efetua pull das telemetrias do banco e envia por push para o espaço de trabalho Log Analytics na assinatura do cliente. O provedor de SAP HANA coleta dados a cada 1 minuto a partir do banco de SAP HANA.  

Na visualização pública, os clientes podem esperar ver os seguintes dados com o provedor de SAP HANA: utilização de infraestrutura subjacente, SAP HANA status do host, replicação do sistema SAP HANA e SAP HANA dados de telemetria de backup. Para configurar o provedor de SAP HANA, o endereço IP do host, o número da porta do SQL HANA e o nome de usuário e a senha do SYSTEMDB são necessários. Os clientes são recomendados para configurar o provedor de SAP HANA no SYSTEMDB, no entanto, mais provedores podem ser configurados em outros locatários de banco de dados.

![Azure Monitor para provedores de soluções SAP – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Tipo de provedor cluster de alta disponibilidade
Os clientes podem configurar um ou mais provedores do tipo de provedor *cluster de alta disponibilidade* para habilitar a coleta de dados do cluster pacemaker dentro da estrutura SAP. O provedor de cluster de alta disponibilidade conecta-se ao pacemaker, usando [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) ponto de extremidade, efetua pull dos dados de telemetria do banco e envia-os por push para log Analytics espaço de trabalho na assinatura do cliente. O provedor de cluster de alta disponibilidade coleta dados a cada 60 segundos de pacemaker.  

Na visualização pública, os clientes podem esperar ver os seguintes dados com o provedor de cluster de alta disponibilidade:   
 - Status do cluster representado como acumulação de status do nó e do recurso 
 - [outros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor para provedores de soluções SAP-cluster de alta disponibilidade](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Para configurar um provedor de cluster de alta disponibilidade, duas etapas principais estão envolvidas:

1. Instale [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) em *cada* nó dentro do cluster pacemaker.

   Você tem duas opções para instalar o ha_cluster_exporter:
   
   - Use os scripts de automação do Azure para implantar um cluster de alta disponibilidade. Os scripts instalam [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) em cada nó de cluster.  
   - Faça uma [instalação manual](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Configure um provedor de cluster de alta disponibilidade para *cada* nó dentro do cluster pacemaker.

   Para configurar o provedor de cluster de alta disponibilidade, as seguintes informações são necessárias:
   
   - **Nome**. Um nome para este provedor. Ele deve ser exclusivo para este Azure Monitor para a instância de soluções SAP.
   - **Ponto de extremidade Prometheus**. http \: // \<servername or ip address\> : 9664/métricas.
   - **Sid**. Para sistemas SAP, use o SAP SID. Para outros sistemas (por exemplo, clusters de NFS), use um nome de três caracteres para o cluster. O SID deve ser diferente de outros clusters que são monitorados.   
   - **Nome do cluster**. O nome do cluster usado ao criar o cluster. O nome do cluster pode ser encontrado na Propriedade do cluster `cluster-name` .
   - **Nome do host**. O nome de host do Linux da VM.  


## <a name="provider-type-os-linux"></a>Sistema operacional do tipo de provedor (Linux)
Os clientes podem configurar um ou mais provedores de sistema operacional do tipo de provedor (Linux) para habilitar a coleta de dados do nó BareMetal ou da VM. O provedor do sistema operacional (Linux) conecta-se a nós BareMetal ou VM, usando [Node_Exporter](https://github.com/prometheus/node_exporter)   ponto de extremidade, extrai dados de telemetria dos nós e envia-os para log Analytics espaço de trabalho na assinatura do cliente. O provedor do sistema operacional (Linux) coleta dados a cada 60 segundos para a maioria das métricas de nós. 

Na visualização pública, os clientes podem esperar ver os seguintes dados com o provedor do sistema operacional (Linux): 
   - Uso da CPU, uso da CPU por processo 
   - Utilização de disco, leitura & gravação de e/s 
   - Distribuição de memória, uso de memória, troca de uso de memória 
   - Uso da rede, entrada de rede & detalhes do tráfego de saída. 

Para configurar um provedor de sistema operacional (Linux), duas etapas principais estão envolvidas:
1. Instale [Node_Exporter](https://github.com/prometheus/node_exporter)   em cada um dos nós BareMetal ou VM.
   Você tem duas opções para instalar o [Node_exporter](https://github.com/prometheus/node_exporter): 
      - Para a instalação de automação com Ansible, use [Node_Exporter](https://github.com/prometheus/node_exporter) em cada BareMetal ou nós de VM para instalar o provedor do sistema operacional (Linux).  
      - Faça uma [instalação manual](https://prometheus.io/docs/guides/node-exporter/).

2. Configure um provedor de so (Linux) para cada instância de nó de VM ou BareMetal em seu ambiente. 
   Para configurar o provedor do sistema operacional (Linux), as seguintes informações são necessárias: 
      - Nome. Um nome para este provedor. Ele deve ser exclusivo para este Azure Monitor para a instância de soluções SAP. 
      - Ponto de extremidade do exportador de nó. Geralmente http:// <servername or ip address> : 9100/métricas 

> [!NOTE]
> 9100 é uma porta exposta para o ponto de extremidade Node_Exporter.

> [!Warning]
> Verifique se o exportador de nó continua em execução após a reinicialização do nó. 


## <a name="provider-type-microsoft-sql-server"></a>Tipo de provedor Microsoft SQL Server

Os clientes podem configurar um ou mais provedores de tipo de provedor *Microsoft SQL Server* para habilitar a coleta de dados de [SQL Server em máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/). O provedor de SQL Server se conecta ao Microsoft SQL Server pela porta do SQL, efetua pull dos dados de telemetria do Database e envia-os por push para o espaço de trabalho Log Analytics na assinatura do cliente. O SQL Server deve ser configurado para autenticação SQL e um logon SQL Server, com o SAP DB como o banco de dados padrão para o provedor, deve ser criado. O provedor de SQL Server coleta dados entre cada 60 segundos até a cada hora do SQL Server.  

Na visualização pública, os clientes podem esperar ver os seguintes dados com o provedor de SQL Server: utilização de infraestrutura subjacente, instruções SQL superiores, maior tabela principal, problemas registrados no SQL Server logs de erros, processos de bloqueio e outros.  

Para configurar o provedor de Microsoft SQL Server, a ID do sistema SAP, o endereço IP do host, SQL Server número da porta e o nome de logon e a senha do SQL Server são necessários.

![Azure Monitor para provedores de soluções SAP-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Próximas etapas

- Crie seu primeiro Azure Monitor para o recurso de soluções SAP.
- Você tem dúvidas sobre Azure Monitor para soluções SAP? Verifique a seção de [perguntas frequentes](./azure-monitor-faq.md)

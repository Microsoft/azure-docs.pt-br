---
title: Serviços do Azure no FedRAMP e DoD SRG auditoria escopo
description: Este artigo contém tabelas para o público do Azure e o Azure governamental que ilustram quais FedRAMP (vs moderadas. Alto) e o escopo de auditoria (nível de impacto 2, 4 ou 5) SRG do DoD alcançou um determinado serviço.
author: davib
ms.author: davib
ms.date: 5/17/2019
ms.topic: article
ms.service: security
ms.reviewer: rochiou
ms.openlocfilehash: 922098a67d148d5860145663ea1c5bf543c8aaec
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294839"
---
# <a name="azure-services-by-fedramp-and-dod-cc-srg-audit-scope"></a>Escopo de auditoria de serviços do Azure FedRAMP e DoD CC SRG

Serviços de nuvem do governo da Microsoft atender os requisitos exigentes do EUA Federal Risk & Authorization Management Program (FedRAMP) e do departamento de defesa dos EUA, de 2 a 5 níveis de impacto de informações. Implantando protegido serviços incluindo do Azure governamental dos EUA do Office 365 Governamental e governo do Dynamics 365, federal e agências de defesa podem aproveitar um vasto conjunto de serviços em conformidade.

Este artigo fornece uma lista detalhada dos serviços de nuvem no escopo do Azure e Azure governamental para ofertas de conformidade FedRAMP e DoD CC SRG.

#### <a name="terminologysymbols-used"></a>Terminologia/símbolos usados

* CC SRG do DoD = guia de requisitos de segurança de computação em nuvem do departamento de defesa
* IIL = informações de nível de impacto
* FedRAMP = Federal Risk and Authorization Management Program  
* : heavy_check_mark: = indica que o serviço alcançou a este escopo de auditoria.

## <a name="azure-public-services-by-audit-scope"></a>Serviços públicos do Azure pelo escopo de auditoria
| _Última atualização: Junho de 2019_ |

| Serviço do Azure| DoD CC SRG IIL 2 | FedRAMP Moderado | FedRAMP Alto | 2019 planejada |
| ------------ |:---------------:|:----------------:|:------------:|:------------:|
| [Gerenciamento da API](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Automação](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [O Azure Active Directory (gratuita e básica)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) |  |  |  | :heavy_check_mark: |
| [Serviços de Domínio do Active Directory do Azure](https://azure.microsoft.com/services/active-directory-ds/) | |  |  | :heavy_check_mark: |
| [Proteção Avançada contra Ameaças do Azure](https://azure.microsoft.com/features/azure-advanced-threat-protection/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Assistente do Azure](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/archive/) |  |  |  | :heavy_check_mark: |
| [Serviço de Contêiner do Azure](https://docs.microsoft.com/azure/container-service/) |  |  |  | :heavy_check_mark: |
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Data Box](https://azure.microsoft.com/services/databox/) | |  |  | :heavy_check_mark: |
| [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/) |  |  |  | :heavy_check_mark: |
| [Banco de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Banco de Dados do Azure para MariaDB](https://azure.microsoft.com/services/mariadb/) |  |  |  | :heavy_check_mark: |
| [Serviço de Migração de Banco de Dados do Azure](https://azure.microsoft.com/services/database-migration/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) |  |  |  |  |
| [Proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [HSM Dedicado do Azure](https://azure.microsoft.com/services/azure-dedicated-hsm/) |  |  |  | :heavy_check_mark: |
| [DevOps do Azure (anteriormente conhecido como VSTS)](https://azure.microsoft.com/services/devops/) | |  |  | |
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) |  |  |  | :heavy_check_mark: |
| [DNS do Azure](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Sincronização de Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Porta da frente do Azure](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Proteção de Informações do Azure](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) | |  |  | :heavy_check_mark: |
| [Mapas do Azure](https://azure.microsoft.com/services/azure-maps/) |  |  |  | :heavy_check_mark: |
| [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Search](https://azure.microsoft.com/services/search/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gerenciador de serviço do Azure (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Backup](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Visão personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Face](https://azure.microsoft.com/services/cognitive-services/face/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Compreensão de idioma](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: O QnA Maker](https://azure.microsoft.com/services/cognitive-services/qna-maker/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Serviços de fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Tradução de Texto](https://azure.microsoft.com/services/cognitive-services/speech-translation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços cognitivos: Indexador de vídeo](https://azure.microsoft.com/services/media-services/video-indexer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Instâncias de Contêiner](https://azure.microsoft.com/services/container-instances/) |  |  |  | :heavy_check_mark: |
| [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Rede de distribuição de conteúdo](https://azure.microsoft.com/services/cdn/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Catálogo de Dados](https://azure.microsoft.com/services/data-catalog/) |  |  |  | :heavy_check_mark: |
| [Fábrica de dados](https://azure.microsoft.com/services/data-factory/) |  |  |  | :heavy_check_mark: |
| [Grade de Eventos](https://azure.microsoft.com/services/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Proteção avançada contra ameaças do Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hubs de Evento](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Fluxo](https://docs.microsoft.com/flow/getting-started) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Funções](https://azure.microsoft.com/services/functions/) |  |  |  | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Importação / exportação](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Centro de IoT](https://azure.microsoft.com/services/iot-central/) |  |  |  | :heavy_check_mark: |
| [Hub IoT](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Key Vault](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Balanceador de Carga](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Serviços de Machine Learning](https://azure.microsoft.com/services/machine-learning-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) |  |  |  | :heavy_check_mark: |
| [Serviços de Mídia](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/)| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Genomics](https://azure.microsoft.com/services/genomics/) |  |  |  | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [Autenticação Multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Observador de Rede](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Análise de tráfego do observador de rede](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hubs de Notificação](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cache Redis](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Agendador](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Central de Segurança](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento: Blobs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento: Discos (discos gerenciados de incl.)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento: Arquivos](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento: Filas](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento: Tabelas](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gerenciador de Tráfego](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Conjuntos de Escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Máquinas virtuais (instâncias reservadas de incl.)](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Gateway de VPN](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aplicativos Web (Serviço de Aplicativo)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="azure-government-services-by-audit-scope"></a>Serviços do Azure governamental por escopo de auditoria
| _Última atualização: Junho de 2019_ |

| Serviço do Azure | DoD CC SRG IIL 2 | DoD CC SRG IIL 4 | DoD CC SRG IIL 5 | FedRAMP Alto | 2019 planejada
| ------------- |:---------------:|:---------------:|:---------------:|:------------:|:------------:
| [Gerenciamento da API](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |  |  |  |  | :heavy_check_mark:
| [Automação](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [O Azure Active Directory (gratuita e básica)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Active Directory (Premium P1 + P2)](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Assistente do Azure](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/) |  |  |  |  | :heavy_check_mark:
| [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/) |  |  |  |  | :heavy_check_mark:
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Fábrica de dados](https://azure.microsoft.com/services/data-factory/)  |  |  |  |  | :heavy_check_mark:
| [BD do Azure para MySQL](https://azure.microsoft.com/services/mysql/)| :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Banco de dados do Azure para MariaDB](https://azure.microsoft.com/services/mariadb/)  |  |  |  |  | :heavy_check_mark:
| [Proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/) |  |  |  |  | :heavy_check_mark:
| [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)  |  |  |  |  | :heavy_check_mark:
| [DNS do Azure](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/)  |  |  |  |  | :heavy_check_mark:
| [Sincronização de Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Porta da frente do Azure](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Proteção de Informações do Azure](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Gerenciador de serviço do Azure (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Backup](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Batch](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serviços cognitivos: Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/)  |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivos: Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)  |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivos: Face](https://azure.microsoft.com/services/cognitive-services/face/)  |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivos: Compreensão de idioma](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)  |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivos: Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/)  |  |  |  |  | :heavy_check_mark:
| [Serviços cognitivos: Tradução de Texto](https://azure.microsoft.com/services/cognitive-services/speech-translation/) |  |  |  |  | :heavy_check_mark:
| [Hubs de Evento](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Fluxo](https://docs.microsoft.com/flow/getting-started) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Funções](https://azure.microsoft.com/services/functions/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Importação / exportação](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hub IoT](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Key Vault](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Serviços de Mídia](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Proteção avançada contra ameaças do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  |  |  |  |  | :heavy_check_mark:
| [Microsoft Graph](https://docs.microsoft.com/graph/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Autenticação Multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Observador de Rede](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Hubs de Notificação](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Cache Redis](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Agendador](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento: Blobs](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento: Discos (discos gerenciados de incl.)](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento: Arquivos](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento: Filas](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento: Tabelas](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Gerenciador de Tráfego](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de Escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Aplicativos Web (Serviço de Aplicativo)](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

**&ast;** Alguns serviços, como o portal do Azure e o Azure Resource Manager não armazenar ou processar DoD CC SRG IIL 5 conteúdo, no entanto, ainda herdam o conjunto de controles IIL5.


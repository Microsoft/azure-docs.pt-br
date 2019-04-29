---
title: Introdução ao SAP em VMs do Azure | Microsoft Docs
description: Saiba mais sobre soluções SAP que são executados em máquinas virtuais (VMs) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835305"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usar o Azure para hospedar e executar cenários de carga de trabalho do SAP

Quando você usa o Microsoft Azure, você pode executar de forma confiável seus cenários e cargas de trabalho SAP críticas em uma plataforma escalonável, em conformidade e de eficácia comprovada na empresa. Obtenha a escalabilidade, flexibilidade e economia de custos do Azure. Com a parceria expandida entre a Microsoft e SAP, você pode executar aplicativos do SAP entre cenários de desenvolvimento, teste e produção no Azure e com suporte total. Do SAP NetWeaver ao SAP 4hana/s, BI de SAP no Linux para Windows e o SAP HANA ao SQL, podemos lhe ajudar.

Além de hospedar cenários do SAP NetWeaver com os diferentes DBMS no Azure, você pode hospedar outros cenários de carga de trabalho do SAP, como SAP BI no Azure. 

A exclusividade do Azure para SAP HANA é uma oferta que diferenciam o Azure. Para habilitar a hospedagem de mais memória e CPU mais exigentes de recurso SAP cenários que envolvem o SAP HANA, o Azure oferece o uso de hardware de bare-metal dedicado ao cliente. Use essa solução para executar as implantações do SAP HANA que exigem até 24 TB (scale-out de 120 TB) de memória para 4hana/s ou outra carga de trabalho do SAP HANA. 

Hospedagem de cenários de carga de trabalho do SAP no Azure também pode criar requisitos de integração de identidade e logon único. Essa situação pode ocorrer quando você usa o Azure Active Directory (Azure AD) para se conectar a diferentes componentes da SAP e SAP software-como um serviço (SaaS) ou para plataforma como serviço (PaaS) ofertas. Uma lista de tais integração e cenários de logon únicos com o Azure AD e entidades do SAP é descrita e documentada na seção "integração de identidade do AAD SAP e logon único."

## <a name="latest-changes"></a>Alterações mais recentes

- Versão do [controlar de instâncias grandes do HANA do Azure por meio do portal do Azure](hana-li-portal.md)

- Versão do [alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com arquivos do Azure NetApp para aplicativos SAP](high-availability-guide-suse-netapp-files.md)

- Esclarecimentos sobre **net.ipv4.tcp_timestamps de parâmetro do sistema operacional Linux** balanceador de carga de configurações em conjunto com o Azure

- Versão do [configurações de carga de trabalho do SAP com zonas de disponibilidade do Azure](sap-ha-availability-zones.md)

- Versão da [Lista de verificação de planejamento e implantação de carga de trabalho do SAP](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos orienta você por meio do SAP HANA no Azure (instâncias grandes), ou de forma abreviada, instâncias grandes do HANA. Para obter informações sobre as seguintes áreas de instâncias grandes HANA, consulte:

- [Visão geral do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infraestrutura e conectividade para o SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalar o SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Solucionar problemas e monitorar o SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Próximas etapas:

- Leitura [visão geral e arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais do Azure
Esta seção da documentação aborda diferentes aspectos do SAP HANA. Como pré-requisito, você deve estar familiarizado com os serviços de entidade de segurança do Azure que fornecem serviços elementares de IaaS do Azure. Portanto, você precisa de Conhecimento de computação do Azure, armazenamento e rede. Muitos desses objetos são manipulados no relacionados a SAP NetWeaver [guia de planejamento do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Para obter informações sobre HANA no Azure, consulte os artigos a seguir e seus subarticles:

- [Início Rápido: Instalação manual do SAP HANA de instância única em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Implantar o SAP S/4HANA ou o BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurações de infraestrutura do SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilidade do SAP HANA em uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidade do SAP HANA entre regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup do SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup do Azure do SAP HANA no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup do SAP HANA com base em instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais do Azure
Esta seção lista a documentação de planejamento e implantação do SAP NetWeaver e Business One no Azure. A documentação enfoca os conceitos básicos e o uso de bancos de dados do HANA não com uma carga de trabalho do SAP no Azure. Os documentos e artigos para alta disponibilidade também são a base para alta disponibilidade do HANA no Azure, tais como:

- [SAP Business One em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Implantar SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Executar o SAP NetWeaver em VMs do SUSE Linux do Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteger uma implantação de aplicativo multicamada do SAP NetWeaver usando o Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bancos de dados do HANA não sob uma carga de trabalho do SAP no Azure, consulte:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para NetWeaver do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Implantação do SAP MaxDB, o Cache ao vivo e o servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre bancos de dados do SAP HANA no Azure, consulte a seção "SAP HANA em máquinas virtuais do Azure".

Para obter informações sobre a alta disponibilidade de uma carga de trabalho do SAP no Azure, consulte:

- [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e o cenário. Em documentos de cenário posteriores, são fornecidos links para documentos técnicos detalhados que explicam a implantação e configuração dos diferentes métodos de alta disponibilidade. Os documentos diferentes que mostram como estabelecer e configurar a alta disponibilidade para uma carga de trabalho do SAP NetWeaver abordam sistemas operacionais Windows e Linux.


Para obter informações sobre a integração entre o Azure Active Directory (Azure AD) e serviços SAP e logon único, consulte:

- [Tutorial: Integração do Azure Active Directory com o SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente 4hana/s: Fiori Launchpad SAML SSO com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração de serviços do Azure em componentes da SAP, consulte:

- [Usar SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usar o Conector do SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)





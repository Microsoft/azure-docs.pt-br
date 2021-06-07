---
title: Certificações do Microsoft Azure para SAP | Microsoft Docs
description: Lista atualizada de configurações e certificações atuais do SAP na plataforma do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669686"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações do SAP em execução no Microsoft Azure

O SAP e a Microsoft têm um histórico de longa data de trabalho em conjunto em uma forte parceria que tem benefícios mútuos para seus clientes. A Microsoft está constantemente atualizando sua plataforma e enviando novos detalhes de certificação para o SAP para garantir que o Microsoft Azure seja a melhor plataforma para executar suas cargas de trabalho do SAP. As tabelas a seguir descrevem configurações com suporte do Azure e listam as certificações SAP em extensão. Esta lista é uma lista de visão geral que pode ser desviada e ali das listas oficiais do SAP. Como obter os dados detalhados está documentado no artigo [que software SAP tem suporte para implantações do Azure](./sap-supported-product-on-azure.md)

## <a name="sap-hana-certifications"></a>Certificações de SAP HANA
Referências:

- [Plataformas de IaaS certificadas pelo SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para suporte de SAP HANA para VMs nativas do Azure e Instâncias Grandes do HANA.

| Produto SAP | SO com suporte | Ofertas do Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software cliente HANA formado por drivers SQLODBC, ODBO somente Windows, ODBC e JDBC, HANA Studio e banco de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de VMs D-Series |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5. Suporte completo para M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA no Azure (Instâncias Grandes) [Plataformas IaaS certificadas pelo SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Pacote no HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA no Azure (instâncias grandes) [SAP Hana plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA no Azure (instâncias grandes) [SAP Hana plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA no Azure (instâncias grandes) <br /> [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Esteja ciente de que a SAP usa o termo 'clustering' em [Plataformas IaaS certificadas pelo SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como sinônimo de 'scale-out' e NÃO para 'clustering' de alta disponibilidade

## <a name="sap-netweaver-certifications"></a>Certificações do SAP NetWeaver
O Microsoft Azure está certificado para os produtos SAP a seguir, com total suporte da Microsoft e da SAP.
Referências:

- [1928533 - Aplicativos SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533) para todos os aplicativos baseados no SAP NetWeaver, incluindo SAP TREX, SAP LiveCache e o servidor de conteúdo do SAP. E todos os bancos de dados, exceto o SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de máquina virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, D2as_v4 para D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, D2as_v4 para D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |N/D |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, D2as_v4 para D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, D2as_v4 para D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Outra carga de trabalho do SAP com suporte no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de máquina virtual |
| --- | --- | --- | --- |
| SAP Business One no SQL Server | Windows  | SQL Server | Todos os tipos de VM certificadas pelo NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Todos os tipos de VM certificadas pelo NetWeaver<br /> Nota SAP nº 2451795 |
| Plataforma BI do SAP Business Objects | Windows e Linux | | Nota SAP nº 2145537 |
| SAP Data Services 4.2 | | | Nota SAP nº 2288344 |
| Plataforma SAP Hybris Commerce  | Windows | SQL Server, Oracle | Todos os tipos de VM certificadas pelo NetWeaver <br /> [Documentação do Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Plataforma SAP Hybris Commerce  | SLES 12 ou mais recente | SAP HANA | Todos os tipos de VM certificadas pelo NetWeaver <br /> [Documentação do Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Plataforma SAP Hybris Commerce  | RHEL 7 ou mais recente | SAP HANA | Todos os tipos de VM certificadas pelo NetWeaver <br /> [Documentação do Hybris]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Plataforma de comércio SAP (Hybris) 1811 e posterior  | Windows, SLES ou RHEL | SQL Azure DB | Todos os tipos de VM certificadas pelo NetWeaver <br /> [Documentação do Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |

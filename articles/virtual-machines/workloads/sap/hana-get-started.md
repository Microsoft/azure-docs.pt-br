---
title: Instalação do SAP HANA em máquinas virtuais do Azure | Microsoft Docs '
description: Guia para a instalação de SAP HANA em VMs do Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 83c149e92276265b5b5236ae56c475d7307c18e4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093903"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalação do SAP HANA em máquinas virtuais do Azure
## <a name="introduction"></a>Introdução
Este guia ajuda você a apontar para os recursos certos para implantar o HANA em máquinas virtuais do Azure com êxito. Este guia vai apontar para os recursos de documentação que você precisa verificar antes de instalar SAP HANA em uma VM do Azure. Portanto, você pode executar as etapas corretas para terminar com uma configuração com suporte de SAP HANA em VMs do Azure.  

> [!NOTE]
> Esta guia descreve as implantações do SAP HANA em VMs do Azure. Para obter informações sobre como implantar SAP HANA em instâncias grandes do HANA, consulte [como instalar e configurar SAP Hana (instâncias grandes) no Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia também pressupõe que você esteja familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar SAP HANA e instâncias de aplicativo SAP no Azure.
* Os conceitos e procedimentos documentados em:
   * Planejando a implantação do SAP no Azure, que inclui o planejamento de rede virtual do Azure e o uso do armazenamento do Azure. Confira [SAP NetWeaver em máquinas virtuais do Azure – guia de planejamento e implementação](./planning-guide.md)
   * Princípios de implantação e maneiras de implantar VMs no Azure. Confira [implantação de máquinas virtuais do Azure para SAP](./deployment-guide.md)
   * Conceitos de alta disponibilidade para SAP HANA, conforme documentado em [SAP Hana alta disponibilidade para máquinas virtuais do Azure](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Passo a passo antes de implantar
Nesta seção, são listadas as diferentes etapas que você precisa executar antes de começar com a instalação do SAP HANA em uma máquina virtual do Azure. O pedido é enumerado e, como tal, deve ser seguido como enumerado:

1. Nem todos os cenários de implantação possíveis têm suporte no Azure. Portanto, você deve verificar o documento [carga de trabalho SAP nos cenários de máquina virtual do Azure com suporte](./sap-planning-supported-configurations.md) para o cenário que você tem em mente com sua implantação de SAP Hana. Se o cenário não estiver listado, você precisará pressupor que ele não foi testado e, como resultado, não tem suporte
2. Supondo que você tenha uma ideia aproximada do seu requisito de memória para sua implantação de SAP HANA, você precisa encontrar uma VM de ajuste do Azure. Nem todas as VMs certificadas para o SAP NetWeaver, conforme documentado em [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533), são SAP Hana certificados. A fonte de verdade para VMs SAP HANA certificadas do Azure é o site [SAP Hana o diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). As unidades que começam com **S** são unidades de [instâncias grandes do Hana](./hana-overview-architecture.md) e não VMs do Azure.
3. Diferentes tipos de VM do Azure têm versões de sistema operacional mínimas diferentes para SUSE Linux ou Red Hat Linux. No site [SAP Hana o diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), você precisa clicar em uma entrada na lista de unidades SAP Hana certificados para obter dados detalhados dessa unidade. Além da carga de trabalho do HANA com suporte, as versões do sistema operacional com suporte nessas unidades para SAP HANA estão listadas
4. A partir das versões do sistema operacional, você precisa considerar certas versões mínimas do kernel. Essas versões mínimas estão documentadas nestas notas de suporte do SAP:
    - [Nota de suporte SAP #2814271 SAP HANA falha no backup no Azure com erro de soma de verificação](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota de suporte da SAP #2753418 degradação potencial de desempenho devido ao fallback do timer](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota de suporte da SAP #2791572 degradação do desempenho devido ao suporte VDSO ausente para o Hyper-V no Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Com base na versão do sistema operacional com suporte para o tipo de máquina virtual de sua escolha, você precisa verificar se a versão de SAP HANA desejada tem suporte com essa versão do sistema operacional. Leia [#2235581 de nota de suporte SAP](https://launchpad.support.sap.com/#/notes/2235581) para obter uma matriz de suporte de SAP Hana versões com as diferentes versões do sistema operacional.
5. Como você pode ter encontrado uma combinação válida de tipo de VM do Azure, versão do sistema operacional e versão do SAP HANA, você precisa fazer check-in da matriz de disponibilidade do produto SAP. Na matriz de disponibilidade do SAP, você pode descobrir se o produto SAP que você deseja executar em seu banco de dados SAP HANA tem suporte.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Considerações de implantação de VM passo a passo e sistema operacional convidado
Nesta fase, você precisa percorrer as etapas de implantação das VMs para instalar o HANA e, eventualmente, otimizar o sistema operacional escolhido após a instalação.

1. Escolha a imagem base da galeria do Azure. Se desejar criar sua própria imagem do sistema operacional para SAP HANA, você precisará saber todos os pacotes diferentes necessários para uma instalação bem-sucedida do SAP HANA. Caso contrário, é recomendável usar as imagens SUSE e Red Hat para SAP ou SAP HANA da Galeria de imagens do Azure. Essas imagens incluem os pacotes necessários para uma instalação bem-sucedida do HANA. Com base em seu contrato de suporte com o provedor do sistema operacional, você precisa escolher uma imagem na qual você traga sua própria licença. Ou você escolhe uma imagem do sistema operacional que inclui suporte
2. Se você escolher uma imagem do sistema operacional convidado que exige a ativação de sua própria licença, será necessário registrar a imagem do sistema operacional com sua assinatura, para que você possa baixar e aplicar os patches mais recentes. Esta etapa exigirá acesso público à Internet. A menos que você configure sua instância privada do, por exemplo, um servidor SMT no Azure.
3. Decida a configuração de rede da VM. Você pode ler mais informações no documento [SAP Hana configurações de infraestrutura e operações no Azure](./hana-vm-operations.md). Tenha em mente que não há cotas de taxa de transferência de rede que você pode atribuir a placas de rede virtual no Azure. Como resultado, a única finalidade de direcionar o tráfego por meio de diferentes vNICs é baseada em considerações de segurança. Confiamos em você para encontrar um comprometimento com suporte entre a complexidade do roteamento de tráfego por meio de vários vNICs e os requisitos impostos por aspectos de segurança.
3. Aplique os patches mais recentes ao sistema operacional depois que a VM for implantada e registrada. Registrado com sua própria assinatura. Ou, no caso de você escolher uma imagem que inclui suporte ao sistema operacional, a VM já deve ter acesso aos patches. 
4. Aplique as ajustadas necessárias para SAP HANA. Essas músicas estão listadas nas seguintes notas de suporte do SAP:

    - [Nota de suporte da SAP #2694118 Red Hat Enterprise Linux HA Add-On no Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota de suporte SAP #1984787-SUSE LINUX Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota de suporte SAP #2578899-SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota de suporte SAP #2002167-Red Hat Enterprise Linux 7. x: instalação e atualização](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Nota de suporte SAP n° 2292690 – Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Nota de suporte SAP #2772999-Red Hat Enterprise Linux 8. x: instalação e configuração](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota de suporte da SAP #2777782-SAP HANA DB: configurações de so recomendadas para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota de suporte SAP #2455582-Linux: executando aplicativos SAP compilados com GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota de suporte da SAP #2382421-otimizando a configuração de rede no HANA e no nível do sistema operacional](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecione o tipo de armazenamento do Azure para SAP HANA. Nesta etapa, você precisa decidir sobre o layout de armazenamento para SAP HANA instalação. Você vai usar discos do Azure anexados ou compartilhamentos NFS nativos do Azure. Os tipos de armazenamento do Azure que ou com suporte e combinações de diferentes tipos de armazenamento do Azure que podem ser usados, são documentados em [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md). Use as configurações documentadas como ponto de partida. Para sistemas que não são de produção, você pode configurar a taxa de transferência ou o IOPS mais baixos. Para fins de produção, talvez seja necessário configurar um pouco mais de taxa de transferência e IOPS.
2. Certifique-se de que você configurou o [Azure acelerador de gravação](../../how-to-enable-write-accelerator.md) para seus volumes que contêm os logs de transação do DBMS ou logs de refazer quando estiver usando as VMs da série M ou Mv2-Series. Esteja atento às limitações de Acelerador de Gravação conforme documentado.
2. Verifique se a [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs implantadas.

> [!NOTE]
> Nem todos os comandos nos diferentes perfis de ajuste do SAP ou conforme descrito nas anotações podem ser executados com êxito no Azure. Comandos que manipulariam o modo de energia das VMs geralmente retornam com um erro, pois o modo de energia do hardware de host do Azure subjacente não pode ser manipulado.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparação passo a passo específica para máquinas virtuais do Azure
Uma das especificações do Azure é a instalação de uma extensão de VM do Azure que fornece dados de monitoramento para o agente de host do SAP. Os detalhes sobre a instalação dessa extensão de monitoramento estão documentados em:

-  A [Nota sap 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) aborda o monitoramento aprimorado do SAP com VMs do Linux no Azure 
-  A [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informações sobre o SAPOSCOL no Linux 
-  A [Observação da sap 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) aborda as principais métricas de monitoramento para SAP no Microsoft Azure
-  [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalação do SAP HANA
Com as máquinas virtuais do Azure implantadas e os sistemas operacionais registrados e configurados, você pode instalar o SAP HANA de acordo com a instalação do SAP. Como um bom começo para chegar a esta documentação, comece com este site da SAP [recursos do Hana](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)

Para SAP HANA configurações de expansão usando discos conectados diretamente do armazenamento Premium do Azure ou ultra Disk, leia as especificações no documento [SAP Hana configurações de infraestrutura e operações no Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Recursos adicionais para backup de SAP HANA
Para obter informações sobre como fazer backup de bancos de dados do SAP HANA em VMs do Azure, consulte:
* [Guia de backup para SAP HANA em máquinas virtuais do Azure](./sap-hana-backup-guide.md)
* [Backup do Azure do SAP HANA no nível do arquivo](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Próximas etapas
Leia a documentação:

- [Configurações de infraestrutura do SAP HANA e operações no Azure](./hana-vm-operations.md)
- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md)

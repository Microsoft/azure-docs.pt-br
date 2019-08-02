---
title: Passo a passo da infraestrutura do Azure de exemplo | Microsoft Docs
description: Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de uma infraestrutura de exemplo no Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ff98079c6156783442078546a4783a367863057
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722596"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Passo a passo da infraestrutura do Azure de exemplo para VMs Windows
Este artigo explica como criar uma infraestrutura de aplicativo de exemplo. Fornecemos detalhes de como projetar uma infraestrutura para um repositório online simples que reúne todas as diretrizes e decisões sobre convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e balanceadores de carga, bem como a implantação de fato das VMs (máquinas virtuais).

## <a name="example-workload"></a>Carga de trabalho de exemplo
A Adventure Works Cycles deseja criar um aplicativo do repositório online no Azure que consiste em:

* Dois servidores IIS que executam o front-end do cliente em uma camada da Web
* Dois servidores IIS que processam dados e pedidos em uma camada de aplicativo
* Duas instâncias do Microsoft SQL Server com grupos de disponibilidade AlwaysOn (dois SQL Servers e uma testemunha do nó principal) para armazenar dados de produtos e pedidos em uma camada de banco de dados
* Dois controladores de domínio do Active Directory para contas de clientes e fornecedores em uma camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede de front-end para os servidores Web 
  * uma sub-rede de back-end para os servidores de aplicativos, cluster do SQL e controladores de domínio

![Diagrama de níveis diferentes de infraestrutura de aplicativos](./media/infrastructure-example/example-tiers.png)

O tráfego seguro da Web de entrada deve ter balanceamento de carga entre os servidores Web enquanto os clientes navegam pelo repositório online. O tráfego de processamento de pedidos na forma de solicitações HTTP dos servidores Web deve ser balanceado entre os servidores de aplicativos. Além disso, a infraestrutura deve ser projetada para alta disponibilidade.

O projeto resultante deve incorporar:

* Uma assinatura e uma conta do Azure
* Um único grupo de recursos
* Azure Managed Disks
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com funções semelhantes
* Máquinas virtuais

Todos os itens acima seguem estas convenções de nomenclatura:

* A Adventure Works Cycles usa **[carga de trabalho de TI]-[localização]-[recurso do Azure]** como prefixo
  * Neste exemplo, “**azos**” (Repositório Online do Azure) é o nome da carga de trabalho de TI e “**use**” (Leste dos EUA 2) é a localização
* As redes virtuais usam AZOS-USE-VN **[número]**
* Os conjuntos de disponibilidade usam azos-use-as- **[função]**
* Os nomes de máquina virtual usam azos-use-vm- **[nomevm]**

## <a name="azure-subscriptions-and-accounts"></a>Assinaturas e contas do Azure
A Adventure Works Cycles usa sua assinatura Enterprise, chamada Adventure Works Enterprise Subscription, para fornecer cobrança para essa carga de trabalho de TI.

## <a name="storage"></a>Armazenamento
A Adventure Works Cycles determinou que deverá usar o Azure Managed Disks. Durante a criação de VMs, ambas as camadas de armazenamento disponíveis são usadas:

* **Armazenamento padrão** para os servidores Web, servidores de aplicativos, controladores de domínio e seus discos de dados.
* **Armazenamento premium** para VMs do SQL Server e seus discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Como a rede virtual não precisa de conectividade contínua com a rede local da Adventure Work Cycles, ela decidiu usar uma rede virtual somente em nuvem.

Criaram uma rede virtual somente em nuvem com as seguintes configurações usando o portal do Azure:

* Nome: AZOS-USE-VN01
* Localização: Leste dos EUA 2
* Espaço de endereço da rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: FrontEnd
  * Espaço de endereço: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: BackEnd
  * Espaço de endereço: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a alta disponibilidade de todas as quatro camadas de seu repositório online, a Adventure Works Cycles decidiu usar quatro conjuntos de disponibilidade:

* **azos-use-as-web** para os servidores Web
* **azos-use-as-app** para os servidores de aplicativos
* **azos-use-as-sql** para os SQL Servers
* **azos-use-as-dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
A Adventure Works Cycles decidiu usar os seguintes nomes para suas VMs do Azure:

* **azos-use-vm-web01** para o primeiro servidor Web
* **azos-use-vm-web02** para o segundo servidor Web
* **azos-use-vm-app01** para o primeiro servidor de aplicativos
* **azos-use-vm-app02** para o segundo servidor de aplicativos
* **azos-use-vm-sql01** para o primeiro SQL Server no cluster
* **azos-use-vm-sql02** para o segundo SQL Server no cluster
* **azos-use-vm-dc01** para o primeiro controlador de domínio
* **azos-use-vm-dc02** para o segundo controlador de domínio

Veja abaixo a configuração resultante.

![Infraestrutura final do aplicativo implantada no Azure](./media/infrastructure-example/example-config.png)

Essa configuração inclui:

* Uma rede virtual somente em nuvem com duas sub-redes (front-end e back-end)
* Azure Managed Disks com discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada camada do repositório online
* As máquinas virtuais das quatro camadas
* Um conjunto de balanceamento de carga externo para tráfego da Web baseado em HTTPS da Internet para os servidores Web
* Um conjunto de balanceamento de carga interno para tráfego da Web dos servidores Web para os servidores de aplicativos
* Um único grupo de recursos

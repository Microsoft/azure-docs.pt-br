---
title: "Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Docs"
description: "Entenda os requisitos para criar e implantar uma imagem de máquina virtual no Azure Marketplace para outras pessoas comprarem."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace
Leia o processo completamente antes de começar e compreender onde e por que cada etapa é executada. O quanto for possível, você deve preparar informações da sua empresa e outros dados, baixar as ferramentas necessárias e criar componentes técnicos antes de iniciar o processo de criação de oferta. Esses itens devem estar claros com a leitura deste artigo.  

## <a name="download-needed-tools--applications"></a>Baixe as ferramentas e aplicativos necessários
Você deve ter os seguintes itens prontos antes de iniciar o processo:

* Dependendo de qual sistema operacional você está buscando, instale os [cmdlets do Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou uma [ferramenta de interface de linha de comando do Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) na página [Downloads do Azure](https://azure.microsoft.com/downloads/).
* Instale o Gerenciador do Armazenamento do Azure no CodePlex.
* Baixe e instale a Ferramenta do teste de certificação para o Certificado Azure:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Você precisará de um computador baseado no Windows para executar a ferramenta de certificação. Se você não tiver um computador baseado no Windows disponível, você pode executar a ferramenta usando uma VM com base em Windows no Azure.

## <a name="platforms-supported"></a>Plataformas com suporte
Você pode desenvolver VMs do Azure baseadas em Windows ou Linux. Alguns elementos do processo de publicação — como a criação de um VHD (disco rígido virtual) compatível com o Azure — usam diversas ferramentas e etapas dependendo de qual sistema operacional você está usando.  

* Se você estiver usando o Linux, consulte a seção "Criar um VHD compatível com o Azure (baseado em Linux)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).
* Se você estiver usando o Windows, consulte a seção "Criar um VHD compatível com o Azure (baseado em Windows)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Você precisa ter acesso a um computador baseado em Windows para:
> 
> * Executar a ferramenta de validação de certificação.
> * Criar a URL da assinatura de acesso compartilhado do VHD para o envio da certificação do VHD.
> 
> 

## <a name="develop-your-vhd"></a>Desenvolver seu VHD
Você pode desenvolver VHDs do Azure na nuvem ou no local:

* Desenvolvimento baseado em nuvem significa que todas as etapas de desenvolvimento são executadas remotamente em um VHD residente no Azure.
* O desenvolvimento local requer o download de um VHD e desenvolvê-lo usando a infraestrutura local. Embora isso seja possível, não é recomendável. Observe que desenvolver para o Windows ou SQL localmente requer que você tenha as chaves de licença locais relevante. Você não pode incluir ou instalar o SQL Server depois de criar uma VM. Você também deve basear sua oferta em uma imagem SQL aprovada no portal do Azure. Se você optar por desenvolver localmente, execute algumas etapas diferentes do que se você estivesse desenvolvendo na nuvem. Você pode encontrar informações relevantes em [Criar uma imagem de VM local](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md

---
title: Usar a Automação do Azure para escalar verticalmente as máquinas virtuais do Windows | Microsoft Docs
description: Escalar verticalmente uma máquina virtual do Windows em resposta aos alertas de monitoramento com a Automação do Azure
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44243f97b6c431578185fcdeb1ddcabc548d927f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580948"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Escalar verticalmente as VMs do Windows com a Automação do Azure

A escala vertical é o processo de aumentar ou diminuir os recursos de um computador em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina virtual. Isso pode ajudar nos cenários a seguir

* Se a máquina virtual não está sendo usado com frequência, você pode redimensioná-la para um tamanho menor, a fim de reduzir os custos mensais
* Se a máquina virtual está enfrentando um pico de carga, ela pode ser redimensionada para um tamanho maior, a fim de aumentar sua capacidade

Veja abaixo a descrição das etapas para fazer isso

1. Configurar a Automação do Azure para acessar suas máquinas virtuais
2. Importar os runbooks de Escala Vertical da Automação do Azure para sua assinatura
3. Adicionar um webhook ao seu Runbook
4. Adicionar um alerta à sua máquina virtual

> [!NOTE]
> Devido ao tamanho da primeira máquina virtual, os tamanhos para expansão podem ser limitados devido à disponibilidade de outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos Runbooks de automação publicados usados neste artigo, levamos isso em conta e dimensionamos apenas dentro dos pares de tamanhos da VM abaixo. Isso significa que uma máquina virtual Standard_D1v2 não será expandida repentinamente para Standard_G5 ou reduzida para Basic_A0. Também não há suporte para a máquina de Virtual restrita tamanhos reduzir/escalar verticalmente. Você pode optar por dimensionar entre os seguintes pares de tamanhos:
> 
> | Par de dimensionamento de tamanhos de VM |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a Automação do Azure para acessar suas máquinas virtuais
A primeira coisa que você precisa fazer é criar uma conta de Automação do Azure que hospedará os runbooks usados para dimensionar uma Máquina Virtual. O serviço de Automação introduziu recentemente o recurso "Executar como conta" que facilita a configuração da Entidade de Serviço para execução automática de Runbooks em nome do usuário. Leia mais sobre isso no artigo abaixo:

* [Autenticar Runbooks com uma conta Executar como do Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar os runbooks de Escala Vertical da Automação do Azure para sua assinatura
Os Runbooks necessários para dimensionar verticalmente a sua máquina virtual já estão publicados na Galeria de Runbook da Automação do Azure. Você precisará importá-los para sua assinatura. Saiba como importar Runbooks lendo o seguinte artigo:

* [Galerias de runbook e de módulos para a Automação do Azure](../../automation/automation-runbook-gallery.md)

Os Runbooks que precisam ser importados são mostrados na imagem abaixo

![Importar Runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu Runbook
Depois de ter importado os Runbooks, você precisará adicionar um webhook ao Runbook para que ele possa ser disparado por um alerta de uma máquina virtual. Os detalhes da criação de um webhook para seu Runbook podem ser lidos aqui

* [Webhooks da Automação do Azure](../../automation/automation-webhooks.md)

Copie o webhook antes de fechar a caixa de diálogo do webhook, pois você precisará disso na próxima seção.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta à sua máquina virtual
1. Selecione Configurações da Máquina Virtual
2. Selecione “Regras de alerta”
3. Selecione “Adicionar alerta”
4. Selecione uma métrica na qual acionar o alerta
5. Selecione uma condição que, quando cumprida, fará com que o alerta seja acionado
6. Selecione um limite para a condição na Etapa 5. a ser cumprida
7. Selecione um período no qual o serviço de monitoramento verificará a condição e o limite nas Etapas 5 e 6
8. Cole o webhook que você copiou da seção anterior.

![Adicionar alerta à máquina virtual 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar alerta à máquina virtual 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)


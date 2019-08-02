---
title: Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure | Microsoft Docs
description: Como dimensionar verticalmente uma Máquina Virtual do Linux em resposta a alertas de monitoramento com a Automação do Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e0317344fd8ee1eb415b61d4f5035219e649b18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695471"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Dimensionar verticalmente uma máquina virtual Linux do Azure com a Automação do Azure
A escala vertical é o processo de aumentar ou diminuir os recursos de um computador em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina virtual. Isso pode ajudar nos cenários a seguir

* Se a máquina virtual não está sendo usado com frequência, você pode redimensioná-la para um tamanho menor, a fim de reduzir os custos mensais
* Se a máquina virtual está enfrentando um pico de carga, ela pode ser redimensionada para um tamanho maior, a fim de aumentar sua capacidade

Veja abaixo a descrição das etapas para fazer isso

1. Configurar a Automação do Azure para acessar suas máquinas virtuais
2. Importar os runbooks de Escala Vertical da Automação do Azure para sua assinatura
3. Adicionar um webhook ao seu Runbook
4. Adicionar um alerta à sua máquina virtual

## <a name="scale-limitations"></a>Limitações de escala

Devido ao tamanho da primeira máquina virtual, os tamanhos para expansão podem ser limitados devido à disponibilidade de outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos Runbooks de automação publicados usados neste artigo, levamos isso em conta e dimensionamos apenas dentro dos pares de tamanhos da VM abaixo. Isso significa que uma máquina virtual Standard_D1v2 não será expandida repentinamente para Standard_G5 ou reduzida para Basic_A0. Também não há suporte para a máquina de Virtual restrita tamanhos reduzir/escalar verticalmente. 

Você pode optar por dimensionar entre os seguintes pares de tamanhos:

* [A-Series](#a-series)
* [Série B](#b-series)
* [D-Series](#d-series)
* [Série E](#e-series)
* [F-Series](#f-series)
* [G-Series](#g-series)
* [Série H](#h-series)
* [L-Series](#l-series)
* [Série M](#m-series)
* [Série N](#n-series)

### <a name="a-series"></a>A-Series

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>Série B

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D-série

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Série E

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Série F

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G-Series

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>Série H

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L-Series

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Série M

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Série N

| Tamanho inicial | Escalar verticalmente o tamanho | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a Automação do Azure para acessar suas máquinas virtuais
A primeira coisa que você precisa fazer é criar uma conta de Automação do Azure que hospedará os Runbooks usados para dimensionar as instâncias do Conjunto de Escala de VM. O serviço de Automação introduziu recentemente o recurso "Executar como conta" que facilita a configuração da Entidade de Serviço para execução automática de Runbooks em nome do usuário. Leia mais sobre isso no artigo abaixo:

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


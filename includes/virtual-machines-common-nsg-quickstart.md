---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67171892"
---
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou adaptador de rede de VMs. Coloque os filtros, que controlam o tráfego de entrada e de saída, em um grupo de segurança de rede anexado ao recurso que recebe o tráfego.

O exemplo neste artigo demonstra como criar um filtro de rede que usa a porta TCP 80 padrão (supõe-se que você já iniciou os serviços adequados e abriu quaisquer regras de firewall do sistema operacional na VM).

Após criar uma VM configurada para servir solicitações da Web na porta TCP 80 padrão, é possível:

1. Crie um grupo de segurança de rede.

2. Criar uma regra de segurança de entrada que permite o tráfego e atribuir valores às seguintes configurações:

   - **Intervalos de portas de destino**: 80

   - **Intervalos de porta de origem**: * (permite qualquer porta de origem)

   - **Valor de prioridade**: Insira um valor menor que 65.500 e maior em prioridade do que a regra de entrada de negação padrão que captura tudo.

3. Associe o grupo de segurança de rede ao adaptador de rede de VMs ou à sub-rede.

Embora esse exemplo use uma regra simples para permitir o tráfego HTTP, também é possível usar regras e grupos de segurança de rede para criar configurações de rede mais complexas. 





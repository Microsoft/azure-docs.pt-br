---
title: Emparelhamento de Internet-perguntas frequentes
titleSuffix: Azure
description: Emparelhamento de Internet-perguntas frequentes
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775492"
---
# <a name="internet-peering---faqs"></a>Emparelhamento de Internet-perguntas frequentes

Você pode examinar as informações abaixo para perguntas gerais.

**Qual é a diferença entre o serviço de emparelhamento e emparelhamento da Internet?**

O serviço de emparelhamento é um serviço que pretende fornecer conectividade IP pública de nível empresarial à Microsoft para seus clientes corporativos. A Internet de nível empresarial inclui conectividade por meio de ISPs que têm conectividade de alta taxa de transferência à Microsoft e redundância para uma conectividade de alta disponibilidade. Além disso, o tráfego do usuário é otimizado para latência para o Microsoft Edge mais próximo. O serviço de emparelhamento se baseia na conectividade de emparelhamento com a operadora do parceiro. A conectividade de emparelhamento com o parceiro deve ser o emparelhamento direto em oposição ao emparelhamento do Exchange. O emparelhamento direto deve ter redundância local e geográfica.

**O que é o emparelhamento herdado?**

A conexão de emparelhamento configurada usando Azure PowerShell é gerenciada como um recurso do Azure. As conexões de emparelhamento configuradas no passado são armazenadas em nosso sistema como emparelhamento herdado, que você pode optar por converter para gerenciar como um recurso do Azure.

**Quando New-AzPeeringDirectConnectionObject é chamado, quais endereços IP são fornecidos à Microsoft e aos dispositivos de mesmo nível?**

Ao chamar New-AzPeeringDirectConnectionObject cmdlet, um endereço/31 (a. b. c. d/31) ou um endereço/30 (a. b. c. d/30) é inserido. O primeiro endereço IP (a. b. c. d + 0) é fornecido ao dispositivo do par e o segundo endereço IP (a. b. c. d + 1) é fornecido ao dispositivo da Microsoft.

**O que são os parâmetros MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 no cmdlet New-AzPeeringDirectConnectionObject?**

Os parâmetros MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 representam o número máximo de prefixos IPv4 e IPv6 que o ponto deseja que a Microsoft aceite. Esses parâmetros podem ser modificados a qualquer momento.
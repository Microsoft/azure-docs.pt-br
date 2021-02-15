---
title: Suporte ao trabalho remoto do firewall do Azure
description: Este artigo mostra como o Firewall do Azure pode dar suporte aos seus requisitos de força de trabalho remoto.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658308"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte ao trabalho remoto do firewall do Azure

O Firewall do Azure é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Suporte à implantação do Virtual Desktop Infrastructure (VDI)

O trabalho de políticas domésticas exige que muitas organizações de ti resolvam as alterações fundamentais na capacidade, na rede, na segurança e no controle. Os funcionários não são protegidos pelas políticas de segurança em camadas associadas aos serviços locais enquanto trabalham em casa. As implantações de VDI (Virtual Desktop Infrastructure) no Azure podem ajudar as organizações a responderem rapidamente a esse ambiente em constante mudança. No entanto, você precisa de uma maneira de proteger o acesso à Internet de entrada/saída para e a partir dessas implantações de VDI. Você pode usar [as regras de DNAT](rule-processing.md) do firewall do Azure junto com seus recursos de filtragem baseados em [inteligência contra ameaças](threat-intel.md) para proteger suas implantações de VDI.

## <a name="azure-windows-virtual-desktop-support"></a>Suporte de área de trabalho virtual do Windows do Azure

A área de trabalho virtual do Windows é um serviço abrangente de virtualização de desktops e aplicativos em execução no Azure. É a única VDI (Virtual Desktop Infrastructure) que fornece gerenciamento simplificado, várias sessões do Windows 10, otimizações para aplicativos Microsoft 365 para empresas e suporte para ambientes de Serviços de Área de Trabalho Remota (RDS). Você pode implantar e dimensionar suas áreas de trabalho e aplicativos do Windows no Azure em minutos e obter recursos internos de segurança e conformidade. A área de trabalho virtual do Windows não exige que você abra nenhum acesso de entrada à sua rede virtual. No entanto, você deve permitir um conjunto de conexões de rede de saída para as máquinas virtuais da área de trabalho virtual do Windows que são executadas em sua rede virtual. Para obter mais informações, confira [Usar o Firewall do Azure para proteger implantações da Área de Trabalho Virtual do Windows](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [área de trabalho virtual do Windows](../virtual-desktop/index.yml).
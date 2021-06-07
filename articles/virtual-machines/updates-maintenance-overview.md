---
title: Visão geral de atualizações e manutenção
description: Saiba mais sobre as atualizações e as opções de manutenção disponíveis com as máquinas virtuais no Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022176"
---
# <a name="updates-and-maintenance-overview"></a>Visão geral de atualizações e manutenção
Este artigo fornece uma visão geral das várias opções de atualização e manutenção para VMs (máquinas virtuais) do Azure.

## <a name="automatic-os-image-upgrade"></a>Atualização automática de imagem do sistema operacional

Habilitar as [atualizações automáticas de imagem do sistema operacional](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) em seu conjunto de dimensionamento ajuda a facilitar o gerenciamento de atualizações, atualizando de modo automático e seguro o disco do sistema operacional para todas as instâncias no conjunto de dimensionamento.

A [atualização automática de sistema operacional](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) tem as seguintes características:

- Depois de configurada, a imagem mais recente do sistema operacional publicada pelos editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de maneira ininterrupta sempre que uma nova imagem é publicada pelo editor.
- Integra-se com as investigações de integridade do aplicativo e a [extensão de Integridade do Aplicativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Funciona para todos os tamanhos de VM e para imagens Windows e Linux.
- É possível recusar os upgrades automáticos a qualquer momento (os upgrades do sistema operacional podem ser iniciados manualmente também).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos.
- O [sequenciamento de extensões](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) é compatível.
- A atualização automática da imagem do sistema operacional pode ser habilitada em um conjunto de dimensionamento de qualquer tamanho.


## <a name="automatic-vm-guest-patching-preview"></a>Aplicação automática de patch de convidado de VM (versão prévia)

A habilitação da [aplicação automática de patch de convidado de VM](automatic-vm-guest-patching.md) para suas VMs do Azure ajuda a facilitar o gerenciamento de atualizações, aplicando patches em máquinas virtuais automaticamente e com segurança, a fim de manter a conformidade de segurança.

A [aplicação automática de patches de convidado de VM](automatic-vm-guest-patching.md) tem as seguintes características:
- Os patches classificados como *Críticos* ou de *Segurança* são baixados e aplicados automaticamente à VM.
- Os patches são aplicados fora do horário de pico no fuso horário da VM.
- A orquestração de patches é gerenciada pelo Azure e os patches são aplicados seguindo os [princípios de priorização de disponibilidade](automatic-vm-guest-patching.md#availability-first-patching).
- A integridade da máquina virtual, conforme determinado por sinais de integridade da plataforma, é monitorada para detectar falhas de patch.
- Funciona para todos os tamanhos de VM.


## <a name="automatic-extension-upgrade-preview"></a>Atualização Automática de Extensão (Versão prévia)

A [Atualização Automática de Extensão](automatic-extension-upgrade.md) está disponível em versão prévia para VMs do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Quando a Atualização Automática de Extensão está habilitada em uma VM ou conjunto de dimensionamento, a extensão é atualizada automaticamente sempre que o editor de extensão libera uma nova versão para essa extensão.

 A Atualização Automática de Extensão tem os seguintes recursos:
- É compatível com VMs do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. No momento, não é compatível com Conjuntos de Dimensionamento de Máquinas Virtuais do Service Fabric.
- As atualizações são aplicadas em um modelo de implantação com priorização de disponibilidade.
- Para um Conjunto de Dimensionamento de Máquinas Virtuais, não mais do que 20% das máquinas virtuais do conjunto de dimensionamento serão atualizadas cada lote. O tamanho mínimo do lote é uma máquina virtual.
- Funciona para todos os tamanhos de VM e para extensões Windows e Linux.
- Você pode recusar as atualizações automáticas a qualquer momento.
- A atualização de extensão automática pode ser habilitada em um Conjuntos de Dimensionamento de Máquinas Virtuais de qualquer tamanho.
- Cada extensão compatível é inscrita individualmente e você pode escolher quais extensões serão atualizadas automaticamente.
- É compatível com todas as regiões de nuvem pública.

## <a name="hotpatch"></a>Patch dinâmico 

A [aplicação de patch dinâmica](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) é uma nova maneira de instalar atualizações em novas VMs (máquinas virtuais) do Windows Server Azure Edition que não exigem uma reinicialização após a instalação. O patch dinâmico para VMs do Windows Server Azure Edition, tem os seguintes benefícios:

- Menor impacto na carga de trabalho com menos reinicializações
- Implantação mais rápida de atualizações à medida que os pacotes são menores, são instalados mais rapidamente e facilitam a orquestração de patch com o Gerenciador de Atualizações do Azure
- Melhor proteção, pois os pacotes de atualização de patch dinâmico têm como escopo as atualizações de segurança do Windows que são instaladas mais rapidamente sem reinicialização


## <a name="azure-update-management"></a>Gerenciamento de Atualizações do Azure

Você pode usar o [Gerenciamento de Atualizações na Automação do Azure](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) para gerenciar atualizações do sistema operacional para suas máquinas virtuais do Windows e do Linux no Azure, em ambientes locais e em outros ambientes de nuvem. Você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação das atualizações necessárias para os servidores.

## <a name="maintenance-control"></a>Controle de manutenção

Gerencie atualizações de plataforma (que não exigem uma reinicialização) usando o [controle de manutenção](maintenance-control.md). O Azure atualiza frequentemente a própria infraestrutura para aprimorar a confiabilidade, o desempenho, a segurança ou lançar novos recursos. A maioria das atualizações é transparente para os usuários. Algumas cargas de trabalho confidenciais, como jogos, streaming de mídia e transações financeiras, não podem tolerar nem mesmo poucos segundos de congelamento ou desconexão para manutenção em uma VM. O controle de manutenção oferece a opção de aguardar as atualizações de plataforma e aplicá-las em uma janela sem interrupção de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às VMs isoladas e aos hosts dedicados do Azure.

Com o [controle de manutenção](maintenance-control.md), é possível:
- Agrupar atualizações em lote em um pacote de atualização.
- Aguardar até 35 dias para aplicar atualizações. 
- Automatizar as atualizações de plataforma, configurando um agendamento de manutenção ou usando o [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 


## <a name="scheduled-events"></a>Eventos agendados

Os eventos agendados são um serviço de metadados do Azure que dá ao aplicativo tempo para se preparar para a manutenção de máquinas virtuais (VM). Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicializar) para que seu aplicativo possa se preparar e limitar a interrupção. Ele está disponível para todos os tipos de Máquinas Virtuais do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para obter informações sobre Eventos Agendados, confira [Eventos Agendados para VMs do Windows](./windows/scheduled-events.md) e [Eventos Agendados para Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de [Disponibilidade e escala](availability.md) para conhecer mais maneiras de aumentar o tempo de atividade de seus aplicativos e serviços. 
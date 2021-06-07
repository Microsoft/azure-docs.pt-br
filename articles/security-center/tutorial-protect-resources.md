---
title: Tutorial de controles de aplicativo e de acesso – Central de Segurança do Azure
description: Este tutorial mostra como configurar uma política de acesso Just-In-Time de VM e uma política de controle de aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: cc88561f8882b1712648a261d1f0208a18bab26d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095469"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: Proteger seus recursos com a Central de Segurança do Azure
A Central de Segurança limita a exposição a ameaças por meio de controles de acesso e de aplicativo a fim de bloquear atividades mal-intencionadas. O acesso de VM (máquina virtual) JIT (Just-In-Time) reduz a exposição a ataques permitindo que você negue o acesso persistente às VMs. Em vez disso, você fornece acesso controlado e auditado às VMs somente quando for necessário. Controles de aplicativo adaptáveis ajudam a proteger VMs contra malware, controlando quais aplicativos podem ser executados em suas VMs. A Central de Segurança usa o machine learning para analisar os processos em execução na VM e ajuda a aplicar regras de listas de permissões usando essa inteligência.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar uma política de acesso de VM Just-In-Time
> * Configurar uma política de controle de aplicativo

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar os recursos abordados neste tutorial, você deve ter o Azure Defender habilitado. Há uma avaliação gratuita disponível. Para atualizar, confira [Habilitar o Azure Defender](enable-azure-defender.md).

## <a name="manage-vm-access"></a>Gerenciar acesso à VM
O acesso JIT à VM pode ser usado para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição aos ataques, fornecendo acesso fácil para conectar às VMs quando necessário.

As portas de gerenciamento não precisam ficar abertas o tempo todo. Elas só precisam ser abertas enquanto você estiver conectado à VM, por exemplo, para realizar tarefas de manutenção ou gerenciamento. Quando o Just-In-Time está habilitado, a Central de Segurança usa as regras do NSG (Grupo de Segurança de Rede), que restringem o acesso às portas de gerenciamento, de modo que elas não se tornem alvo de invasores.

Siga as diretrizes encontradas em [Proteger suas portas de gerenciamento com acesso just-in-time](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Proteger VMs contra malware
Os controles de aplicativo adaptáveis ajudam você a definir um conjunto de aplicativos que podem ser executados em grupos de recursos configurados que, entre outros benefícios, ajuda você a proteger suas VMs contra malware. A Central de Segurança usa o machine learning para analisar os processos em execução na VM e ajuda a aplicar regras de listas de permissões usando essa inteligência.

Siga as diretrizes encontradas em [Usar controles de aplicativos adaptáveis para reduzir as superfícies de ataque dos computadores](security-center-adaptive-application.md).

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a limitar sua exposição a ameaças fazendo o seguinte:

> [!div class="checklist"]
> * Configurando uma política de acesso Just-In-Time à VM para fornecer acesso controlado e auditado às VMs somente quando for necessário
> * Configurando uma política de controles de aplicativo adaptável para controlar quais aplicativos podem ser executados em suas VMs

Avance para o próximo tutorial para saber mais sobre como responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: Responder a alertas de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png

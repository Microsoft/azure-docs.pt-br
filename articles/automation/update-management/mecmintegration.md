---
title: Integrar o Gerenciamento de Atualizações de automação do Azure com o Microsoft Endpoint Configuration Manager
description: Este artigo descreve como configurar o Microsoft Endpoint Configuration Manager com Gerenciamento de Atualizações para implantar atualizações de software em clientes do gerenciador.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575769"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integrar Gerenciamento de Atualizações com o Microsoft Endpoint Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade dele no gerenciamento de atualizações de software como parte do ciclo de gerenciamento de atualizações de software (SUM).

Você pode relatar e atualizar servidores Windows gerenciados criando e pré-preparando implantações de atualização de software no Microsoft Endpoint Configuration Manager e obter o status detalhado das implantações de atualização concluídas usando [Gerenciamento de atualizações](overview.md). Se você usar o Microsoft Endpoint Configuration Manager para relatórios de conformidade de atualização, mas não para gerenciar implantações de atualização com seus servidores Windows, poderá continuar relatando para o Microsoft Endpoint Configuration Manager enquanto as atualizações de segurança são gerenciadas com a Gerenciamento de Atualizações de automação do Azure.

>[!NOTE]
>Embora Gerenciamento de Atualizações ofereça suporte à avaliação de atualização e aplicação de patches do Windows Server 2008 R2, ele não dá suporte a clientes gerenciados pelo Microsoft Endpoint Configuration Manager executando este sistema operacional.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário adicionar o [Gerenciamento de Atualizações da Automação do Azure](overview.md) à conta de Automação.
* Os servidores do Windows atualmente gerenciados pelo ambiente do Microsoft Endpoint Configuration Manager também precisam ser relatados para o espaço de trabalho Log Analytics que também tem Gerenciamento de Atualizações habilitado.
* Esse recurso está habilitado no Microsoft Endpoint Configuration Manager Branch atual versão 1606 e superior. Para integrar o site de administração central do Microsoft Endpoint Configuration Manager ou um site primário autônomo com Azure Monitor logs e importar coleções, examine [conectar Configuration Manager para Azure monitor logs](../../azure-monitor/logs/collect-sccm.md).  
* Os agentes do Windows devem ser configurados para se comunicar com um servidor Windows Server Update Services (WSUS) ou ter acesso a Microsoft Update se eles não receberem atualizações de segurança do Microsoft Endpoint Configuration Manager.

A maneira como você gerencia clientes hospedados no Azure IaaS com seu ambiente existente do Microsoft Endpoint Configuration Manager depende principalmente da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você pode precisar fazer em seu ponto de extremidade da Microsoft Configuration Manager infraestrutura e o custo relacionado para dar suporte a essas alterações necessárias. Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Gerenciar atualizações de software do Microsoft Endpoint Configuration Manager

Execute as etapas a seguir se você pretende continuar Gerenciando implantações de atualização do Microsoft Endpoint Configuration Manager. A automação do Azure se conecta ao Microsoft Endpoint Configuration Manager para aplicar atualizações aos computadores cliente conectados ao seu espaço de trabalho Log Analytics. O conteúdo de atualização está disponível no cache do computador cliente como se a implantação fosse gerenciada pelo Microsoft Endpoint Configuration Manager.

1. Crie uma implantação de atualização de software do site de nível superior no seu ponto de extremidade da Microsoft Configuration Manager hierarquia usando o processo descrito em [implantar atualizações de software](/configmgr/sum/deploy-use/deploy-software-updates). A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado pelo Gerenciamento de Atualizações através da criação de uma implantação de atualização agendada na próxima etapa.

2. Na Automação do Azure, selecione **Gerenciamento de Atualizações**. Crie uma nova implantação seguindo as etapas descritas em [criando uma implantação de atualização](deploy-updates.md#schedule-an-update-deployment) e selecione **grupos importados** na lista suspensa **tipo** para selecionar o Microsoft Endpoint Configuration Manager coleção apropriado. Tenha em mente os seguintes pontos importantes:

    a. Se uma janela de manutenção for definida na coleção de dispositivos do Microsoft Endpoint Configuration Manager selecionado, os membros da coleção honrarão isso, em vez da configuração **duração** definida na implantação agendada.

    b. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do gateway do Log Analytics).

Após concluir a implantação de atualização através da Automação do Azure, os computadores de destino membros do grupo de computadores selecionado irão instalar as atualizações no horário agendado a partir do cache local do cliente. Você pode [exibir o status de implantação de atualização](deploy-updates.md#check-deployment-status) para monitorar os resultados de sua implantação.

## <a name="manage-software-updates-from-azure-automation"></a>Gerenciar atualizações de software da Automação do Azure

Para gerenciar atualizações para VMs do Windows Server que são clientes do Microsoft Endpoint Configuration Manager, você precisa configurar a política do cliente para desabilitar o recurso Gerenciamento de Atualização de Software para todos os clientes gerenciados pelo Gerenciamento de Atualizações. Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia. Para obter mais informações sobre essa configuração de política e como configurá-la, veja [Como definir as configurações de cliente no Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Depois de executar essa alteração de configuração, você cria uma nova implantação seguindo as etapas descritas em [criando uma implantação de atualização](deploy-updates.md#schedule-an-update-deployment) e seleciona **grupos importados** na lista suspensa **tipo** para selecionar a coleção apropriada de Configuration Manager de ponto de extremidade da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Para configurar um agendamento de integração, veja [Agendamento de uma implantação de atualização](deploy-updates.md#schedule-an-update-deployment).

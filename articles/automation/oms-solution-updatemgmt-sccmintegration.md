---
title: Direcionar atualizações usando coleções do SCCM na Automação do Azure - Gerenciamento de Atualizações
description: Este artigo destina-se a ajudá-lo a configurar o System Center Configuration Manager com esta solução para gerenciar atualizações de computadores gerenciados pelo SCCM.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 92a93982cdd042a92b006cab7052ad4a6fee6fff
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478195"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integrar o System Center Configuration Manager com o Gerenciamento de Atualizações

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade dele no gerenciamento de atualizações de software como parte do ciclo de SUM (gerenciamento de atualização de software) deles.

Você pode relatar e atualizar servidores Windows gerenciados criando e preparando previamente implantações de atualização de software no Gerenciador de Configurações, bem como obter o status detalhado de implantações de atualização concluídas, usando a [solução de Gerenciamento de Atualizações](automation-update-management.md). Se você usar o Configuration Manager para relatórios de conformidade de atualizações, mas não para gerenciar implantações de atualizações com os servidores Windows, você pode continuar a relatar para o Configuration Manager, enquanto as atualizações de segurança são gerenciadas com a solução de Gerenciamento de Atualizações.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter a [solução de Gerenciamento de Atualizações](automation-update-management.md) adicionada à sua conta de Automação.
* Servidores Windows atualmente gerenciados pelo seu ambiente do System Center Configuration Manager também precisam relatar para o espaço de trabalho do Log Analytics, que também tem a solução de Gerenciamento de Atualizações habilitada.
* Esse recurso está habilitado no System Center Configuration Manager versão do branch atual 1606 e superior. Para integrar seu site de administração central do Configuration Manager ou um site primário autônomo com logs do Azure Monitor e importar coleções, examine [conectar o Configuration Manager para o Azure Monitor registra](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows deverão ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update se eles não receberem atualizações de segurança do Configuration Manager.   

Como gerenciar clientes hospedados no IaaS do Azure com seu ambiente existente do Configuration Manager é algo que depende da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você talvez precise fazer à sua infraestrutura do Configuration Manager, bem como o custo relacionado para dar suporte a essas alterações necessárias. Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerenciar atualizações de software do Configuration Manager 

Se você pretende continuar gerenciando implantações de atualização do Configuration Manager, execute as etapas a seguir. A Automação do Azure conecta-se ao Gerenciador de Configurações para aplicar atualizações aos computadores clientes conectados ao espaço de trabalho do Log Analytics. O conteúdo de atualização está disponível do cache do computador cliente, como se a implantação fosse gerenciada pelo Configuration Manager.

1. Crie uma implantação de atualização de software do site de nível superior na hierarquia do Configuration Manager usando o processo descrito em [processo de implantação de atualização de software](/sccm/sum/deploy-use/deploy-software-updates). A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado pela solução de Gerenciamento de Atualizações por meio da criação de uma implantação de atualização agendada na próxima etapa.

1. Na Automação do Azure, selecione **Gerenciamento de Atualizações**. Crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos Importados** no menu suspenso **Tipo** para selecionar a coleção apropriada do Gerenciador de Configurações. Tenha em mente os seguintes pontos importantes: a. Se uma janela de manutenção for definida na coleção de dispositivos do Gerenciador de Configurações selecionado, os membros da coleção irão respeitá-la, em vez da configuração **Duração** definida na implantação agendada.
    b. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do gateway do Log Analytics).

Após concluir a implantação de atualização através da Automação do Azure, os computadores de destino membros do grupo de computadores selecionado irão instalar as atualizações no horário agendado a partir do cache local do cliente. Você pode [exibir o status de implantação de atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorar os resultados de sua implantação.

### <a name="manage-software-updates-from-azure-automation"></a>Gerenciar atualizações de software da Automação do Azure

Para gerenciar atualizações para as VMs do Windows Server que são clientes do Configuration Manager, você precisa configurar a política de cliente para desabilitar o recurso de Gerenciamento de Atualizações de Software para todos os clientes gerenciados por essa solução. Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia. Para obter mais informações sobre essa configuração de política e como configurá-la, examine [como definir as configurações de cliente no System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Após essa alteração de configuração, crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos importados** no menu suspenso **Tipo** para selecionar a coleção adequada do Gerenciador de Configurações.

## <a name="next-steps"></a>Próximas etapas


---
title: "Direcionar para atualizações usando coleções do SCCM no Gerenciamento de Atualizações do OMS | Microsoft Docs"
description: "Este artigo destina-se a ajudá-lo a configurar o System Center Configuration Manager com esta solução para gerenciar atualizações de computadores gerenciados pelo SCCM."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: c3ae8da65e03fe9e11b5657a6a40d02de0567da6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management-preview"></a>Integrar o System Center Configuration Manager com o Gerenciamento de Atualizações do OMS [versão prévia]

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade dele no gerenciamento de atualizações de software como parte do ciclo de SUM (gerenciamento de atualização de software) deles.  

Aproveitando a integração existente atualmente entre a OMS e do Configuration Manager, você pode relatar e atualizar servidores Windows gerenciados criando e preparando previamente implantações de atualização de software no Configuration Manager, bem como obter o status detalhado de implantações de atualização concluídas usando a [solução de Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md). Se você usar o Configuration Manager para relatórios de conformidade de atualizações, mas não para gerenciar implantações de atualizações com os servidores Windows, você pode continuar a relatar para o Configuration Manager, enquanto as atualizações de segurança são gerenciadas com a solução de Gerenciamento de Atualizações do OMS.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve adicionar a [solução de Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md) ao seu espaço de trabalho do Log Analytics e vinculá-la junto à sua conta de Automação no mesmo grupo de recursos e região.   
* Servidores Windows atualmente gerenciados pelo seu ambiente do System Center Configuration Manager também precisam relatar para o espaço de trabalho do Log Analytics, que também tem a solução de Gerenciamento de Atualizações habilitada.  
* Esse recurso dá suporte à ramificação atual do System Center Configuration Manager, versão 1606 e superior.  Para integrar o seu site de administração central do Configuration Manager ou um site primário autônomo ao Log Analytics e importar coleções, examine [Conectar o Configuration Manager ao Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Os agentes do Windows deverão ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update se eles não receberem atualizações de segurança do Configuration Manager.   

Como gerenciar clientes hospedados no IaaS do Azure com seu ambiente existente do Configuration Manager é algo que depende da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você talvez precise fazer à sua infraestrutura do Configuration Manager, bem como o custo relacionado para dar suporte a essas alterações necessárias.  Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerenciar atualizações de software do Configuration Manager 

Se você pretende continuar gerenciando implantações de atualização do Configuration Manager, execute as etapas a seguir.  O OMS se conecta ao Configuration Manager para aplicar as atualizações aos computadores cliente conectados ao seu espaço de trabalho do Log Analytics. O conteúdo de atualização está disponível do cache do computador cliente, como se a implantação fosse gerenciada pelo Configuration Manager.  

1. Crie uma implantação de atualização de software do site de nível superior na hierarquia do Configuration Manager usando o processo descrito em [processo de implantação de atualização de software](https://docs.microsoft.com/en-us/sccm/sum/deploy-use/deploy-software-updates).  A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado pela solução de Gerenciamento de Atualizações do OMS por meio da criação de uma implantação de atualização agendada na próxima etapa.  
2. No Portal do Azure, selecione sua conta de Automação na tela **Conta de Automação** e, em seguida, crie uma variável de tipo booliano denominada **UseOMSForSCCMUpdates** com um valor de **true**, seguindo [Para criar uma nova variável com o Portal do Azure](../automation/automation-variables.md#to-create-a-new-variable-with-the-azure-portal).
3. No portal do OMS, abra o painel de Gerenciamento de Atualizações.  Crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e, na lista suspensa, selecione a coleção apropriada do Configuration Manager representada como um grupo de computadores do OMS.  Tenha em mente os seguintes pontos importantes:
    1. Se uma janela de manutenção for definida na coleção de dispositivos do Configuration Manager selecionada, os membros da coleção respeitarão a ela em vez da configuração **Duração** definida na implantação agendada no OMS.
    2. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do Gateway de OMS).  

Depois de concluir a implantação de atualização com a solução do OMS, os computadores de destino que são membros do grupo de computadores selecionado instalarão atualizações no horário agendado, do cache local do cliente deles.  Você pode [exibir o status de implantação de atualização](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) para monitorar os resultados de sua implantação.  

### <a name="manage-software-updates-from-oms"></a>Gerenciar atualizações de software do OMS

Para gerenciar atualizações para as VMs do Windows Server que são clientes do Configuration Manager, você precisa configurar a política de cliente para desabilitar o recurso de Gerenciamento de Atualizações de Software para todos os clientes gerenciados por essa solução.  Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia.  Para obter mais informações sobre essa configuração de política e como configurá-la, examine [como definir as configurações de cliente no System Center Configuration Manager](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings).  

Depois de realizar essa alteração de configuração, crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e, na lista suspensa, selecione a coleção apropriada do Configuration Manager representada como um grupo de computadores do OMS. 


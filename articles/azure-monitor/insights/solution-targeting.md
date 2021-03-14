---
title: Direcionamento de soluções de monitoramento no Azure Monitor | Microsoft Docs
description: O direcionamento de soluções de monitoramento permite limitar as soluções de monitoramento a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de escopo e aplicá-la a uma solução.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728562"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Direcionamento de soluções de monitoramento no Azure Monitor (versão prévia)
Quando você adiciona uma solução de monitoramento à sua assinatura, ela é implantada automaticamente por padrão em todos os agentes do Windows e do Linux conectados ao seu espaço de trabalho do Log Analytics.  Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes.  Este artigo descreve como usar o **Direcionamento de Solução**, que é um recurso que permite aplicar um escopo às suas soluções.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Como direcionar uma solução
Há três etapas para direcionar uma solução, conforme descrito nas próximas seções. 


### <a name="1-create-a-computer-group"></a>1. criar um grupo de computadores
Especifique os computadores que deseja incluir no escopo criando um [grupo de computadores](../logs/computer-groups.md) no Azure Monitor.  O grupo de computadores pode se basear em uma consulta de logs ou pode ser importado de outras fontes, como grupos do Active Directory ou do WSUS. Conforme [descrito abaixo](#solutions-and-agents-that-cant-be-targeted), somente computadores conectados diretamente ao Azure Monitor serão incluídos no escopo.

Depois de criar o grupo de computadores em seu workspace, você o incluirá em uma configuração de escopo que pode ser aplicada a uma ou mais soluções.
 
 
### <a name="2-create-a-scope-configuration"></a>2. criar uma configuração de escopo
 Uma **Configuração de Escopo** inclui um ou mais grupos de computadores e pode ser aplicada a uma ou mais soluções. 
 
 Crie uma configuração de escopo usando o processo a seguir.  

 1. No portal do Azure, navegue até **Workspaces do Log Analytics** e selecione seu workspace.
 2. Nas propriedades do workspace em **Fontes de Dados do Workspace**, selecione **Configurações de Escopo**.
 3. Clique em **Adicionar** para criar uma nova configuração de escopo.
 4. Digite um **Nome** para a configuração de escopo.
 5. Clique em **Selecionar Grupos de Computadores**.
 6. Selecione o grupo de computadores criado e, opcionalmente, todos os outros grupos a serem adicionados à configuração.  Clique em **Selecionar**.  
 6. Clique em **OK** para criar a configuração de escopo. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. aplicar a configuração de escopo a uma solução.
Depois que você tiver uma configuração de escopo, poderá aplicá-la a uma ou mais soluções.  Observe que, embora uma única configuração de escopo possa ser usada com várias soluções, cada solução só poderá usar uma configuração de escopo.

Aplique uma configuração de escopo usando o processo a seguir.  

 1. No portal do Azure, navegue até **Workspaces do Log Analytics** e selecione seu workspace.
 2. Nas propriedades do workspace, selecione **Soluções**.
 3. Clique na solução que você deseja definir como escopo.
 4. Nas propriedades da solução em **Fontes de Dados do Workspace**, selecione **Direcionamento de Solução**.  Se a opção não estiver disponível, isso significa que [essa solução não poderá ser direcionada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique em **Adicionar configuração de escopo**.  Se você já tiver uma configuração aplicada a essa solução, essa opção não ficará disponível.  É necessário remover a configuração existente antes de adicionar outra.
 6. Clique na configuração de escopo criada.
 7. Observe o **Status** da configuração para garantir que ela mostra **Com Êxito**.  Se o status indicar um erro, clique na elipse à direita da configuração e selecione **Editar configuração de escopo** para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e agentes que não podem ser direcionados
Veja a seguir os critérios para agentes e soluções que não podem ser usados com o direcionamento de solução.

- O direcionamento de solução se aplica somente às soluções implantadas em agentes.
- O direcionamento de solução se aplica somente às soluções fornecidas pela Microsoft.  Ele não se aplica às soluções [criadas por você ou por parceiros](./solutions.md).
- Você pode filtrar somente os agentes que se conectam diretamente ao Azure Monitor.  As soluções serão implantadas automaticamente em todos os agentes que fazem parte de um grupo de gerenciamento conectado do Operations Manager, independentemente de estarem incluídas ou não em uma configuração de escopo.

### <a name="exceptions"></a>Exceções
O direcionamento de solução não pode ser usado com as soluções descritas a seguir, mesmo que elas se enquadrem nos critérios indicados.

- Avaliação de Integridade do Agente

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as soluções de monitoramento, incluindo as soluções disponíveis para instalação em seu ambiente, em [Adicionar soluções de monitoramento do Azure Log Analytics a seu workspace](solutions.md).
- Saiba mais sobre como criar grupos de computadores em [Grupos de computadores em consultas de logs do Azure Monitor](../logs/computer-groups.md).

---
title: "Direcionamento de solução no OMS | Microsoft Docs"
description: "O Direcionamento de Solução é um recurso do OMS (Operations Management Suite) que permite limitar as soluções de gerenciamento a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de escopo e aplicá-la a uma solução."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: cb73a2d7ae57a5a11869259dbe913ae83ffb2b01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-solution-targeting-in-operations-management-suite-oms-to-scope-management-solutions-to-specific-agents-preview"></a>Usar o direcionamento de solução do OMS (Operations Management Suite) para definir o escopo das soluções de gerenciamento para agentes específicos (Visualização)
Quando você adiciona uma solução ao OMS, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics.  Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes.  Este artigo descreve como usar o **Direcionamento de Solução**, que é um recurso do OMS que permite aplicar um escopo às suas soluções.

## <a name="how-to-target-a-solution"></a>Como direcionar uma solução
Há três etapas para direcionar uma solução, conforme descrito nas próximas seções.  Observe que você precisará do portal do OMS e do portal do Azure em etapas diferentes.


### <a name="1-create-a-computer-group"></a>1. Criar um grupo de computadores
Especifique os computadores que você deseja incluir em um escopo criando um [grupo de computadores](../log-analytics/log-analytics-computer-groups.md) no Log Analytics.  O grupo de computadores pode se basear em uma pesquisa de logs ou ser importado de outras fontes, como grupos do Active Directory ou do WSUS. Conforme [descrito abaixo](#solutions-and-agents-that-cant-be-targeted), somente os computadores que estão diretamente conectados ao Log Analytics serão incluídos no escopo.

Depois de criar o grupo de computadores em seu espaço de trabalho, você o incluirá em uma configuração de escopo que pode ser aplicada a uma ou mais soluções.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Criar uma configuração de escopo
 Uma **Configuração de Escopo** inclui um ou mais grupos de computadores e pode ser aplicada a uma ou mais soluções. 
 
 Crie uma configuração de escopo usando o processo a seguir.  

 1. No portal do Azure, navegue para **Log Analytics** e selecione seu espaço de trabalho.
 2. Nas propriedades do espaço de trabalho em **Fontes de Dados do Espaço de Trabalho**, selecione **Configurações de Escopo**.
 3. Clique em **Adicionar** para criar uma nova configuração de escopo.
 4. Digite um **Nome** para a configuração de escopo.
 5. Clique em **Selecionar Grupos de Computadores**.
 6. Selecione o grupo de computadores criado e, opcionalmente, todos os outros grupos a serem adicionados à configuração.  Clique em **Selecionar**.  
 6. Clique em **OK** para criar a configuração de escopo. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Aplicar a configuração de escopo a uma solução.
Depois que você tiver uma configuração de escopo, poderá aplicá-la a uma ou mais soluções.  Observe que, embora uma única configuração de escopo possa ser usada com várias soluções, cada solução só poderá usar uma configuração de escopo.

Aplique uma configuração de escopo usando o processo a seguir.  

 1. No portal do Azure, navegue para **Log Analytics** e selecione seu espaço de trabalho.
 2. Nas propriedades do espaço de trabalho, selecione **Soluções**.
 3. Clique na solução que você deseja definir como escopo.
 4. Nas propriedades da solução em **Fontes de Dados do Espaço de Trabalho**, selecione **Direcionamento de Solução**.  Se a opção não estiver disponível, isso significa que [essa solução não poderá ser direcionada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique em **Adicionar configuração de escopo**.  Se você já tiver uma configuração aplicada a essa solução, essa opção não ficará disponível.  É necessário remover a configuração existente antes de adicionar outra.
 6. Clique na configuração de escopo criada.
 7. Observe o **Status** da configuração para garantir que ela mostra **Com Êxito**.  Se o status indicar um erro, clique na elipse à direita da configuração e selecione **Editar configuração de escopo** para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e agentes que não podem ser direcionados
Veja a seguir os critérios para agentes e soluções que não podem ser usados com o direcionamento de solução.

- O direcionamento de solução se aplica somente às soluções implantadas em agentes.
- O direcionamento de solução se aplica somente às soluções fornecidas pela Microsoft.  Ele não se aplica às soluções [criadas por você ou por parceiros](operations-management-suite-solutions-creating.md).
- Você pode filtrar os agentes que se conectam diretamente ao Log Analytics.  As soluções serão implantadas automaticamente em todos os agentes que fazem parte de um grupo de gerenciamento conectado do Operations Manager, independentemente de estarem incluídas ou não em uma configuração de escopo.

### <a name="exceptions"></a>Exceções
O direcionamento de solução não pode ser usado com as soluções descritas a seguir, mesmo que elas se enquadrem nos critérios indicados.

- Avaliação de Integridade do Agente

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as soluções de gerenciamento, incluindo as soluções disponíveis para instalação em seu ambiente em [Adicionar soluções de gerenciamento do Azure Log Analytics a seu espaço de trabalho](../log-analytics/log-analytics-add-solutions.md).
- Saiba mais sobre como criar grupos de computadores em [Grupos de computadores em pesquisas de logs do Log Analytics](../log-analytics/log-analytics-computer-groups.md).
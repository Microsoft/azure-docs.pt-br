---
title: Início rápido – implantar e gerenciar logs de fluxo de NSG usando Azure Policy
titleSuffix: Azure Network Watcher
description: Este artigo explica como usar as políticas internas para gerenciar a implantação de logs de fluxo do NSG
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011094"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Início rápido: implantar e gerenciar logs de fluxo de NSG usando Azure Policy 

## <a name="overview"></a>Visão geral
O Azure Policy ajuda a impor padrões organizacionais e a avaliar a conformidade em escala. Casos de uso comuns do Azure Policy incluem implementar a governança para consistência de recursos, conformidade regulatória, segurança, custo e gerenciamento. Neste artigo, usaremos duas políticas internas disponíveis para logs de fluxo de NSG para gerenciar sua configuração de logs de fluxo. A primeira política sinaliza qualquer NSGs sem logs de fluxo habilitados. A segunda política implanta automaticamente os logs de fluxo para NSGs sem os logs de fluxo habilitados. 

Se você estiver criando uma política do Azure pela primeira vez, poderá ler: 
- [Visão geral da política do Azure](../governance/policy/overview.md) 
- [Tutorial para a criação de política](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Localizar as políticas
1. Vá para o portal do Azure – [Portal.Azure.com](https://portal.azure.com) 

Navegue até a página Azure Policy pesquisando política na página inicial de política da barra de pesquisa ![](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Vá para a guia **atribuições** no painel esquerdo

![Guia atribuições](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Clique no botão **atribuir política** 

![Botão atribuir política](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Clique no menu de três pontos em "definições de política" para ver as políticas disponíveis

5. Use o filtro de tipo e escolha "interno". Em seguida, pesquise "log de fluxo"

Você deve ver as duas políticas internas para a lista de políticas de logs de fluxo ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Escolha a política que você deseja atribuir

- *"O log de fluxo deve ser configurado para cada grupo de segurança de rede"* é a política de auditoria que sinaliza NSGs não compatível, que é NSGs sem o log de fluxo habilitado
- *"Implantar um recurso de log de fluxo com o grupo de segurança de rede de destino"* é a política com uma ação de implantação, permite que os logs de fluxo em todos os NSGs sem logs de fluxo

Há instruções separadas para cada política abaixo.  

## <a name="audit-policy"></a>Política de Auditoria 

### <a name="how-the-policy-works"></a>Como a política funciona

A política verifica todos os objetos ARM existentes do tipo "Microsoft. Network/networkSecurityGroups", que está examinando todos os NSGs em um determinado escopo e verifica a existência de logs de fluxo vinculados por meio da propriedade logs de fluxo do NSG. Se a propriedade não existir, o NSG será sinalizado.

Se você quiser ver a definição completa da política, poderá visitar a [guia definições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e procurar "logs de fluxo" para localizar a política

### <a name="assignment"></a>Atribuição

1. Preencha os detalhes da política

- Escopo: uma assinatura é a opção comum, você também pode escolher um grupo de gerenciamento ou grupo de recursos como relevante para você.  
- Definição de política: deve ser escolhida conforme mostrado na seção "localizar as políticas".
- Atribuiçãoname: escolha um nome descritivo 

2. Clique em "revisar + criar" para revisar sua atribuição

A política não requer parâmetros. Conforme você atribui uma política de auditoria, não é necessário preencher os detalhes na guia "correção".  

![Análise da política de auditoria](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Resultados

Para verificar os resultados, abra a guia conformidade e procure o nome da sua atribuição.
Você deverá ver algo semelhante à captura de tela a seguir quando a política for executada. Caso sua política não seja executada, aguarde algum tempo. 

![Resultados da política de auditoria](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Política de implantação-IF-NOT-EXISTS 

### <a name="policy-structure"></a>Estrutura de política

A política verifica todos os objetos ARM existentes do tipo "Microsoft. Network/networkSecurityGroups", que está examinando todos os NSGs em um determinado escopo e verifica a existência de logs de fluxo vinculados por meio da propriedade logs de fluxo do NSG. Se a propriedade não existir, a política implantará um log de fluxo. 

Se você quiser ver a definição completa da política, poderá visitar a [guia definições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e procurar "logs de fluxo" para localizar a política. 

### <a name="assignment"></a>Atribuição

1. Preencha os detalhes da política

- Escopo: uma assinatura é a opção comum, você também pode escolher um grupo de gerenciamento ou grupo de recursos como relevante para você.  
- Definição de política: deve ser escolhida conforme mostrado na seção "localizar as políticas".
- Atribuiçãoname: escolha um nome descritivo 

2. Adicionar parâmetros de política 

O serviço do observador de rede é um serviço regional. Esses parâmetros permitem que a ação da política de implantação de logs de fluxo seja executada. 
- Região NSG: regiões do Azure nas quais a política é direcionada
- ID de armazenamento: ID de recurso completo da conta de armazenamento. Observação: essa conta de armazenamento deve estar na mesma região que o NSG. 
- Observadores de rede RG: nome do grupo de recursos que contém o recurso do observador de rede. Se você não o tiver renomeado, poderá inserir ' NetworkWatcherRG ', que é o padrão.
- Nome do observador de rede: nome do serviço do observador de rede regional. Formato: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centralus. Consulte todas a lista completa.

![Parâmetros de política restaurante](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Adicionar detalhes da correção

- Marca de seleção "criar tarefa de correção" se você quiser que a política afete os recursos existentes 
- "Criar uma identidade gerenciada" já deve estar marcado
- Selecionou o mesmo local que o anterior para sua identidade gerenciada 
- Você precisará de permissões de colaborador ou de proprietário para usar essa política. Se você tiver essas permissões, não verá nenhum erro.

![Correção de política de restaurante](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Clique em "revisar + criar" para revisar sua atribuição. você deve ver algo semelhante à captura de tela a seguir.

![Revisão da política de restaurante](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Resultados

Para verificar os resultados, abra a guia conformidade e procure o nome da sua atribuição.
Você deve ver algo como a captura de tela a seguir depois de sua política. Caso sua política não seja executada, aguarde algum tempo.

![Resultados da política de restaurante](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Próximas etapas 

-   Use este [tutorial](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) para aprofundar-se usando modelos ARM para implantar logs de fluxo e análise de tráfego.
-   Saiba mais sobre o [observador de rede](./index.yml)
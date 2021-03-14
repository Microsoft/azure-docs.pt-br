---
title: Migrar para o monitor de conexão do Monitor de Desempenho de Rede
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o monitor de conexão do Monitor de Desempenho de Rede.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 998b0cb04d465f675423e2472a7ca8c6441b1fed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010398"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrar para o monitor de conexão do Monitor de Desempenho de Rede

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho com Monitor de Desempenho de Rede. Você pode continuar a usar os testes criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, migre seus testes de Monitor de Desempenho de Rede para o novo monitor de conexão no observador de rede do Azure antes de 29 de fevereiro de 2024.

Você pode migrar testes de Monitor de Desempenho de Rede (NPM) para um monitor de conexão novo e aprimorado com um único clique e sem tempo de inatividade. Para saber mais sobre os benefícios, consulte [Monitor de conexão](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Pontos-chave a serem observados

A migração ajuda a produzir os seguintes resultados:

* Os agentes locais e as configurações de firewall funcionam como estão. Nenhuma alteração é necessária. Os agentes de Log Analytics instalados em máquinas virtuais do Azure precisam ser substituídos pela [extensão do observador de rede](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).
* Os testes existentes são mapeados para o monitor de conexão > grupo de teste > formato de teste. Ao selecionar **Editar**, você pode exibir e modificar as propriedades do novo monitor de conexão, baixar um modelo para fazer alterações e enviar o modelo por meio de Azure Resource Manager.
* Os agentes enviam dados para o espaço de trabalho Log Analytics e as métricas.
* Monitoramento de dados:
   * **Dados em log Analytics**: antes da migração, os dados permanecem no espaço de trabalho no qual o NPM está configurado na tabela NetworkMonitoring. Após a migração, os dados vão para a tabela NetworkMonitoring, a tabela NWConnectionMonitorTestResult e a tabela NWConnectionMonitorPathResult no mesmo espaço de trabalho. Depois que os testes são desabilitados no NPM, os dados são armazenados somente na tabela NWConnectionMonitorTestResult e na tabela NWConnectionMonitorPathResult.
   * **Alertas, painéis e integrações baseados em log**: você deve editar manualmente as consultas com base na nova tabela NWConnectionMonitorTestResult e na tabela NWConnectionMonitorPathResult. Para recriar os alertas em métricas, consulte [monitoramento de conectividade de rede com o monitor de conexão](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Para o monitoramento do ExpressRoute:
    * **Perda e latência de ponta a ponta**: o monitor de conexão ativará isso e será mais fácil do que NPM, pois os usuários não precisam configurar quais circuitos e emparelhamentos monitorar. Os circuitos no caminho serão automaticamente descobertos, os dados estarão disponíveis em métricas (mais rápido que LA, onde NPM armazenou os resultados). A topologia funcionará como também.
    * **Medidas de largura de banda**: com o lançamento de métricas relacionadas à largura de banda, a abordagem baseada em log Analytics do NPM não era eficaz no monitoramento de largura de banda para clientes do ExpressRoute. Essa funcionalidade agora não está disponível no monitor de conexão.
    
## <a name="prerequisites"></a>Pré-requisitos

* Verifique se o observador de rede está habilitado na sua assinatura e na região do espaço de trabalho Log Analytics. 
* Caso a VM do Azure que pertence a uma região/assinatura diferente da do Log Analytics espaço de trabalho seja usada como um ponto de extremidade, verifique se o observador de rede está habilitado para essa assinatura e região.   
* As máquinas virtuais do Azure com agentes de Log Analytics instalados devem ser habilitadas com a extensão do observador de rede.

## <a name="migrate-the-tests"></a>Migrar os testes

Para migrar os testes de Monitor de Desempenho de Rede para o monitor de conexão, faça o seguinte:

1. Em observador de rede, selecione **Monitor de conexão** e, em seguida, selecione a guia **migrar testes de NPM** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrar testes de Monitor de Desempenho de Rede para o monitor de conexão" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Nas listas suspensas, selecione sua assinatura e espaço de trabalho e, em seguida, selecione o recurso NPM que você deseja migrar. 
1. Selecione **importar** para migrar os testes.

Após o início da migração, as seguintes alterações ocorrem: 
* Um novo recurso de monitor de conexão é criado.
   * Um monitor de conexão por região e assinatura é criado. Para testes com agentes locais, o novo nome do monitor de conexão é formatado como `<workspaceName>_"workspace_region_name"` . Para testes com agentes do Azure, o novo nome do monitor de conexão é formatado como `<workspaceName>_<Azure_region_name>` .
   * Os dados de monitoramento agora são armazenados no mesmo espaço de trabalho Log Analytics no qual o NPM está habilitado, em novas tabelas chamadas tabela NWConnectionMonitorTestResult e tabela NWConnectionMonitorPathResult. 
   * O nome do teste é postergado como o nome do grupo de teste. A descrição do teste não é migrada.
   * Os pontos de extremidade de origem e destino são criados e usados no novo grupo de teste. Para agentes locais, os pontos de extremidade são formatados como `<workspaceName>_<FQDN of on-premises machine>` . A descrição do agente não é migrada.
   * A porta de destino e o intervalo de investigação são movidos para uma configuração de teste chamada `TC_<protocol>_<port>` e `TC_<protocol>_<port>_AppThresholds` . O protocolo é definido com base nos valores de porta. Para o ICMP, as configurações de teste são nomeadas como `TC_<protocol>` e `TC_<protocol>_AppThresholds` . Limites de êxito e outras propriedades opcionais, se definido forem migrados, caso contrário, serão deixados em branco.
   * Se os testes de migração contiverem agentes que não estão em execução, você precisará habilitar os agentes e migrar novamente.
* O NPM não está desabilitado, portanto, os testes migrados podem continuar a enviar dados para a tabela NetworkMonitoring, tabela NWConnectionMonitorTestResult e tabela NWConnectionMonitorPathResult. Essa abordagem garante que os alertas baseados em log existentes e as integrações não sejam afetados.
* O monitor de conexão criado recentemente é visível no monitor de conexão.

Após a migração, certifique-se de:
* Desabilite manualmente os testes no NPM. Até fazer isso, você continuará a ser cobrado por eles. 
* Enquanto estiver desabilitando o NPM, recrie seus alertas nas tabelas NWConnectionMonitorTestResult e NWConnectionMonitorPathResult ou use métricas. 
* Migre todas as integrações externas para as tabelas NWConnectionMonitorTestResult e NWConnectionMonitorPathResult. Exemplos de integrações externas são painéis em Power BI e Grafana e integrações com sistemas SIEM (gerenciamento de eventos e informações de segurança).


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitor de conexão, consulte:
* [Migrar do monitor de conexão (clássico) para o monitor de conexão](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Criar monitor de conexão usando o portal do Azure](./connection-monitor-create-using-portal.md)

---
title: Tipos de recurso com suporte por meio do Azure Resource Health | Microsoft Docs
description: Tipos de recurso com suporte por meio do Azure Resource Health
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: e8ce1f9b7129e272a7714b82ec1a91a928917d6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de integridade no Azure Resource Health
Abaixo, temos uma lista completa de todas as verificações executadas por meio do Resource Health segundo o tipo do recurso.

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Verificações executadas|
|---|
|<ul><li>Todos os nós do Cache estão em funcionamento?</li><li>É possível alcançar o Cache de dentro do datacenter?</li><li>O Cache atingiu o número máximo de conexões?</li><li> O Cache esgotou a memória disponível? </li><li>O Cache está apresentando um alto número de falhas de página?</li><li>O Cache está sob uma carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>Algum ponto de extremidade foi interrompido, removido ou configurado incorretamente?</li><li>O portal complementar pode ser acessado para operações de configuração da CDN?</li><li>Há problemas contínuos de entrega com os pontos de extremidade da CDN?</li><li>Os usuários podem alterar a configuração de seus recursos da CDN?</li><li>Alterações de configuração estão sendo propagadas com a velocidade esperada?</li><li>Os usuários podem gerenciar a configuração da CDN usando o PowerShell, a API ou o Portal do Azure?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Verificações executadas|
|---|
|<ul><li>É possível alcançar a conta de dentro do datacenter?</li><li>O provedor de recursos dos Serviços Cognitivos está disponível?</li><li>O Serviço Cognitivo está disponível na região apropriada?</li><li>Operações de leitura podem ser executadas na conta de armazenamento que contém os metadados do recurso?</li><li>A cota de chamadas à API foi atingida?</li><li>O limite de leitura de chamadas à API foi atingido?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor que hospeda a máquina virtual está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários podem enviar trabalhos para o Data Lake Analytics na região?</li><li>Trabalhos básicos são executados e concluídos com êxito na região?</li><li>Os usuários podem listar itens de catálogo na região?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários podem carregar dados para o Data Lake Store na região?</li><li>Os usuários podem baixar dados do Data Lake Store na região?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Verificações executadas|
|---|
|<ul><li>O hub IoT está em execução?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Alguma solicitação do banco de dados ou da coleção deixou de ser atendida devido a uma indisponibilidade de serviço do DocumentDB?</li><li>Alguma solicitação de documento deixou de ser atendida devido a uma indisponibilidade de serviço do DocumentDB?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está conectado?</li><li>Há conflitos de configuração na conexão?</li><li>As chaves pré-compartilhadas estão configuradas corretamente?</li><li>O dispositivo de VPN local é acessível?</li><li>Há incompatibilidades na política de segurança IPsec/IKE?</li><li>A conexão VPN S2S está provisionada corretamente ou em um estado de falha?</li><li>A conexão VNET para VNET está provisionada corretamente ou em um estado de falha?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O Gateway de VPN pode ser acessado pela Internet?</li><li>O Gateway de VPN está em modo de espera?</li><li>O serviço de VPN está em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li> Operações de tempo de execução como o registro, a instalação ou o envio podem ser executadas no namespace?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Verificações executadas|
|---|
|<ul><li>O SO host está em funcionamento?</li><li>O workspaceCollection pode ser acessado de fora do datacenter?</li><li>O provedor de recursos do PowerBI está disponível?</li><li>O serviço do PowerBI está disponível na região apropriada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>Operações de diagnóstico podem ser executadas no cluster?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Verificações executadas|
|---|
|<ul><li> Houve logons no banco de dados?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>Todos os hosts em que o trabalho está sendo executado estão em funcionamento?</li><li>Não foi possível iniciar o trabalho?</li><li>Há atualizações de tempo de execução em andamento?</li><li>O trabalho está em um estado esperado (por exemplo, em execução ou parado pelo cliente)?</li><li>O trabalho encontrou exceções de memória insuficiente?</li><li>Há atualizações de computação agendadas em andamento?</li><li>O Gerenciador de Execução (plano de controle) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os Serviços de Informações da Internet estão em execução?</li><li>O balanceador de carga está em execução?</li><li>É possível alcançar o Plano do Serviço Web de dentro do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo dos sites para o serverFarm está disponível?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os servidor de Informações da Internet está em execução?</li><li>O balanceador de carga está em execução?</li><li>É possível alcançar o aplicativo Web de dentro do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo do site está disponível?</li></ul>|

# <a name="next-steps"></a>Próximas etapas
-  Consulte [Introdução ao Azure Service Health](service-health-overview.md) e [Introdução ao Azure Resource Health](resource-health-overview.md) para entender mais sobre eles. 
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)
- Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [Configurar alertas do Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
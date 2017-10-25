---
title: "Diagnóstico e monitoramento de contêineres do Azure Service Fabric | Microsoft Docs"
description: "Saiba como monitorar e diagnosticar contêineres orquestrados no Microsoft Azure Service Fabric com a solução de contêineres do OMS."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: dekapur
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Monitorando contêineres do Windows Server com o OMS

## <a name="oms-containers-solution"></a>Solução de Contêineres do OMS

A equipe do OMS (Operations Management Suite) publicou uma solução de Contêineres para diagnóstico e monitoramento de contêineres. Juntamente com sua solução Service Fabric, essa solução é uma excelente ferramenta para monitorar implantações de contêiner orquestradas no Service Fabric. Aqui está um exemplo simples da aparência do painel na solução:

![Painel do OMS básico](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Ele também coleta diferentes tipos de logs que podem ser consultados na ferramenta Log Analytics do OMS e pode ser usado para visualizar todas as métricas ou eventos sendo gerados. Os tipos de log coletados são:

1. ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
2. ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
3. ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
4. ContainerServiceLog: comandos de daemon do docker que foram executados
5. Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner

Este artigo aborda as etapas necessárias para configurar o monitoramento de contêiner para seu cluster. Para saber mais sobre a solução de Contêineres do OMS, consulte sua [documentação](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Configurar um cluster do Service Fabric

Crie um cluster usando o modelo do Azure Resource Manager encontrado [aqui](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample). Certifique-se de adicionar um nome exclusivo de espaço de trabalho do OMS. Esse modelo também padroniza para implantar um cluster no build de versão prévia do Service Fabric (v255.255), o que significa que ele não pode ser usado na produção e não pode ser atualizado para uma versão diferente do Service Fabric. Se você decidir utilizar este modelo para o uso em longo prazo ou em produção, altere a versão para um número de versão estável.

Depois que o cluster for configurado, confirme se você instalou o certificado apropriado e verifique se consegue se conectar ao cluster.

Confirme se o grupo de recursos está configurado corretamente acessando o [Portal do Azure](https://portal.azure.com/) e localizando a implantação. O grupo de recursos deve conter todos os recursos do Service Fabric e também ter uma solução Log Analytics e uma solução Service Fabric.

Para modificar um cluster existente do Service Fabric:
* Confirme se o Diagnóstico está habilitado (se não estiver, habilite-o por meio da [atualização do conjunto de dimensionamento de máquinas virtuais](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Adicione um Espaço de Trabalho do OMS criando uma solução de "Análise do Service Fabric" usando o Azure Marketplace
* Edite as fontes de dados da solução Service Fabric para pegara dados das tabelas apropriadas do Armazenamento do Azure (definidas por WAD) no Grupo de Recursos no qual o cluster está
* Adicione o agente como uma [extensão para o conjunto de dimensionamento de máquinas virtuais](/powershell/module/azurerm.compute/add-azurermvmssextension) por meio do PowerShell ou atualizando o conjunto de dimensionamento de máquinas virtuais (mesmo link acima, para modificar o modelo do Resource Manager)

## <a name="2-deploy-a-container"></a>2. Implantar um contêiner

Depois que o cluster estiver pronto e você confirmar que pode acessá-lo, implante um contêiner nele. Se você optar por usar a versão prévia conforme definido pelo modelo, também poderá explorar a nova funcionalidade Docker Compose do Service Fabric. Tenha em mente que na primeira vez que uma imagem de contêiner é implantada em um cluster, ela leva vários minutos para baixar a imagem, dependendo de seu tamanho.

## <a name="3-add-the-containers-solution"></a>3. Adicionar a solução de Contêineres

No Portal do Azure, crie um recurso de Contêineres (sob a categoria Monitoramento+Gerenciamento) por meio do Azure Marketplace. 

![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

Na etapa de criação, é solicitado um espaço de trabalho do OMS. Selecione o que foi criado com a implantação acima. Esta etapa adiciona uma solução de Contêineres dentro de seu espaço de trabalho do OMS e é detectada automaticamente pelo agente do OMS implantado pelo modelo. O agente começará a coletar dados sobre os processos de contêineres no cluster e, em cerca de 10 a 15 minutos, você deverá ver a solução se iluminar com os dados como a imagem do painel acima.

## <a name="4-next-steps"></a>4. Próximas etapas

O OMS oferece várias ferramentas no espaço de trabalho para torná-lo mais útil para você. Explore as seguintes opções para personalizar a solução para suas necessidades:
- Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
- Configure o Agente do OMS para coletar contadores de desempenho específicos (acesse a Página Inicial > Configurações > Dados > Contadores de Desempenho do Windows do espaço de trabalho)
- Configure o OMS para configurar regras de [alerta automatizado](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e diagnóstico
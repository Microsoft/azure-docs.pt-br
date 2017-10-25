---
title: "Monitorar tempo de execução de integração no Azure Data Factory | Microsoft Docs"
description: "Saiba como monitorar diferentes tipos de tempo de execução de integração no Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: spelluru
ms.openlocfilehash: f2e1957a02d72d79a245ec3be705ba46dcc41e2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorar um tempo de execução de integração no Azure Data Factory  
O **tempo de execução de integração** é a infraestrutura de computação usada pelo Azure Data Factory para fornecer diversas funcionalidades de integração de dados entre diferentes ambientes de rede. São oferecidos três tipos de tempos de execução de integração pelo Data Factory:

- Integration Runtime do Azure
- Tempo de execução de integração auto-hospedado
- Tempo de execução de integração do Azure-SSIS

Para obter o status de uma instância de tempo de execução de integração (IR), execute o seguinte comando do PowerShell: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet retorna diferentes informações para diferentes tipos de tempo de execução de integração. Este artigo explica as propriedades e os status para cada tipo de tempo de execução de integração.  

## <a name="azure-integration-runtime"></a>Integration Runtime do Azure
O recurso de computação para um tempo de execução de integração do Azure é totalmente gerenciado com elasticidade no Azure. A tabela a seguir fornece descrições para propriedades retornadas pelo comando **Get-AzureRmDataFactoryV2IntegrationRuntime**:

### <a name="properties"></a>Propriedades
A tabela a seguir fornece descrições de propriedades retornadas pelo cmdlet para um tempo de execução de integração:

| Propriedade | Descrição |
-------- | ------------- | 
| Nome | Nome do tempo de execução de integração do Azure. |  
| Estado | Status do tempo de execução de integração do Azure. | 
| Local | Local do tempo de execução de integração do Azure. Para obter detalhes sobre o local de um tempo de execução de integração do Azure, consulte [Introdução ao tempo de execução de integração](concepts-integration-runtime.md). |
| DataFactoryName | Nome do Data Factory ao qual o tempo de execução de integração do Azure pertence. | 
| ResourceGroupName | Nome do grupo de recursos ao qual o data factory pertence.  |
| Descrição | Descrição do tempo de execução de integração.  |

### <a name="status"></a>Status
A tabela a seguir fornece os possíveis status de um tempo de execução de integração do Azure:

| Status | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O tempo de execução de integração do Azure está online e pronto para ser usado. | 
| Off-line | O tempo de execução de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Tempo de execução de integração auto-hospedado
Esta seção fornece descrições para propriedades retornadas pelo cmdlet Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> As propriedades retornadas e o status contêm informações sobre o tempo de execução de integração auto-hospedado geral e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

A tabela a seguir apresenta descrições das Propriedades de monitoramento para **cada nó**:

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do tempo de execução de integração auto-hospedado e nós associados a ele. O nó é um computador local do Windows em que o tempo de execução de integração auto-hospedado está instalado. |  
| Status | O status do tempo de execução de integração auto-hospedado geral e de cada nó. Exemplo: Online/Offline/Limitado/etc. Para obter informações sobre esses status, consulte a próxima seção. | 
| Versão | A versão do tempo de execução de integração auto-hospedado geral e de cada nó. A versão do tempo de execução de integração auto-hospedado é determinada com base na versão da maioria dos nós no grupo. Se há nós com versões diferentes na configuração do tempo de execução de integração auto-hospedado, somente os nós com o mesmo número de versão que o tempo de execução de integração auto-hospedado funcionam corretamente. Os outros estão no modo limitado e precisam ser atualizados manualmente (somente caso a atualização automática falhe). | 
| Memória disponível | Memória disponível em um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. |
| Rede (Entrada/Saída) | Utilização da rede de um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. | 
| Trabalhos Simultâneos (Executando/Limite) | Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. Limite significa a máxima de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho do computador. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que memória/CPU/rede são subutilizados, mas as atividades estão atingindo o tempo limite. Essa funcionalidade também está disponível com tempo de execução de integração auto-hospedado de nó único. |
| Função | Há dois tipos de funções em um tempo de execução de integração auto-hospedado com vários nós – dispatcher e de trabalho. Todos os nós são de trabalho, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e distribuí-los para nós de trabalho diferentes. O nó dispatcher também é um nó de trabalho. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nós (cenário de expansão) no tempo de execução de integração auto-hospedado. 
  
### <a name="status-per-node"></a>Status (por nó)
A tabela a seguir fornece os possíveis status de um nó de tempo de execução de integração auto-hospedado:

| Status | Descrição |
| ------ | ------------------ | 
| Online | O nó está conectado ao serviço Data Factory. |
| Off-line | O nó está offline. |
| Atualizando | O nó está sendo atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido a um problema de HTTP da porta 8050, problema de conectividade do barramento de serviço ou problema de sincronização de credenciais. |
| Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria. |

Um nó pode ficar inativo quando ele não pode se conectar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (tempo de execução de integração auto-hospedado geral)
A tabela a seguir fornece os possíveis status de um tempo de execução de integração auto-hospedado. Esse status depende dos status de todos os nós que pertencem ao tempo de execução. 

| Status | Descrição |
| ------ | ----------- | 
| Precisa de registro | Nenhum nó está registrado a esse tempo de execução de integração auto-hospedado. |
| Online | Todos os nós estão online. |
| Off-line | Nenhum nó está online. |
| Limitado | Nem todos os nós no tempo de execução integração auto-hospedado estão em um estado íntegro. Esse status é um aviso de que alguns nós podem estar inativos. Esse status pode ser devido a um problema de sincronização de credenciais no nó dispatcher/de trabalho. |

Use o cmdlet **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** para buscar o conteúdo JSON que contenha as propriedades detalhadas do tempo de execução de integração auto-hospedado e seus valores de instantâneo durante a execução do cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Exemplo de saída (supõe que há dois nós associados a esse tempo de execução de integração auto-hospedado):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Tempo de execução de integração do Azure-SSIS
O tempo de execução de integração do SSIS do Azure é um cluster totalmente gerenciado das máquinas virtuais (ou nós) do Azure dedicado a executar os pacotes de SSIS. Ele não executa nenhuma outra atividade do Azure Data Factory. Depois de provisionado, você pode consultar suas propriedades e monitorar os status geral/de nós específicos.

### <a name="properties"></a>Propriedades

| Propriedade/Status | Descrição |
| --------------- | ----------- |
| CreateTime | A hora UTC em que o tempo de execução de integração do Azure-SSIS foi criado. |
| Nós | Os nós alocados/disponíveis de seu tempo de execução de integração do Azure-SSIS com status específicos dos nós (iniciando/disponível/reciclando/indisponível) e erros acionáveis. |
| OtherErrors | Os erros acionáveis não específicos dos nós em seu tempo de execução de integração do Azure-SSIS. |
| LastOperation | O resultado da última operação de iniciar/parar em seu tempo de execução de integração do Azure-SSIS com erros acionáveis se houve falha. |
| Estado | O status geral (inicial/iniciando/iniciado/parando/parado) de seu tempo de execução de integração do Azure-SSIS. |
| Local | O local do seu tempo de execução de integração do Azure-SSIS. |
| NodeSize | O tamanho de cada nó do seu tempo de execução de integração do Azure-SSIS. |
| NodeCount | O número de nós em seu tempo de execução de integração do Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó em seu tempo de execução de integração do Azure-SSIS. |
| CatalogServerEndpoint | O ponto de extremidade de seu servidor existente de Banco de dados SQL do Azure/Instância Gerenciada para hospedar o SSISDB. |
| CatalogAdminUserName | O nome de usuário do administrador do seu servidor existente de Banco de dados SQL do Azure/Instância Gerenciada. O serviço Data Factory usa essas informações para preparar e gerenciar o SSISDB por você. |
| CatalogAdminPassword | A senha do administrador do seu servidor existente de Banco de dados SQL do Azure/Instância Gerenciada. |
| CatalogPricingTier | O tipo de preço do SSISDB hospedado por seu servidor existente de Banco de dados SQL do Azure.  Não se aplica à Instância Gerenciada de SQL do Azure hospedando o SSISDB. |
| VNetId | A ID de recursos da rede virtual (VNet) para o tempo de execução de integração do Azure-SSIS ingressar. |
| Sub-rede | O nome da sub-rede para o tempo de execução de integração do Azure-SSIS ingressar. |
| ID | A ID de recursos do seu tempo de execução de integração do Azure-SSIS. |
| Tipo | O tipo (Gerenciado/Auto-hospedado) de seu tempo de execução de integração do Azure-SSIS. |
| ResourceGroupName | O nome do seu grupo de recursos do Azure, no qual seu data factory e o tempo de execução de integração do Azure-SSIS foram criados. |
| DataFactoryName | O nome de seu data factory do Azure. |
| Nome | O nome do seu tempo de execução de integração do Azure-SSIS. |
| Descrição | A descrição do seu tempo de execução de integração do Azure-SSIS. |

  
### <a name="status-per-node"></a>Status (por nó)

| Status | Descrição |
| ------ | ----------- | 
| Iniciando | Este nó está sendo preparado. |
| Disponível | Este nó está pronto para implantar/executar pacotes do SSIS. |
| Reciclando | Este nó está sendo reparado/está reiniciando. |
| Indisponível | Este nó não está pronto para implantar/executar pacotes do SSIS e tem problemas/erros acionáveis que você pode resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (tempo de execução de integração do Azure-SSIS geral)

| Status geral | Descrição | 
| -------------- | ----------- | 
| Inicial | Os nós de seu tempo de execução de integração do Azure-SSIS não foram alocados/preparados. | 
| Iniciando | Os nós de seu tempo de execução de integração do Azure-SSIS estão sendo alocados/preparados e a cobrança foi iniciada. |
| Iniciado | Os nós de seu tempo de execução de integração do Azure-SSIS foram alocados/preparados e estão prontos para implantar/executar pacotes do SSIS. |
| Parando  | Os nós de seu tempo de execução de integração do Azure-SSIS estão sendo liberados. |
| Parada | Os nós de seu tempo de execução de integração do Azure-SSIS foram liberados e a cobrança parou. |

Consulte os artigos a seguir para saber mais sobre o tempo de execução de integração do Azure-SSIS:

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma VNet](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual (VNet) do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a VNet para que o IR do Azure-SSIS possa unir-se à VNet. 

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para monitorar os pipelines de maneiras diferentes: 

- [Início rápido: criar um data factory](quickstart-create-data-factory-dot-net.md).
- [Usar o Azure Monitor para monitorar os pipelines de Data Factory](monitor-using-azure-monitor.md)
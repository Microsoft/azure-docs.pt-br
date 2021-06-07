---
title: Monitorar o tempo de execução de integração no Azure Data Factory
description: Saiba como monitorar diferentes tipos de runtime de integração no Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740011"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorar um runtime de integração no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
O **Integration Runtime** é a infraestrutura de computação usada pelo Azure data Factory (ADF) para fornecer vários recursos de integração de dados em diferentes ambientes de rede. São oferecidos três tipos de runtimes de integração pelo Data Factory:

- Azure Integration runtime
- runtime de integração auto-hospedada
- Tempo de execução de integração do SSIS (Azure-SQL Server Integration Services)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o status de uma instância de runtime de integração (IR), execute o seguinte comando do PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet retorna diferentes informações para diferentes tipos de runtime de integração. Este artigo explica as propriedades e os status para cada tipo de runtime de integração.  

## <a name="azure-integration-runtime"></a>Azure Integration runtime

O recurso de computação para um Azure Integration runtime é totalmente gerenciado com elasticidade no Azure. A tabela a seguir fornece descrições das propriedades retornadas pelo comando **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Propriedades

A tabela a seguir fornece descrições de propriedades retornadas pelo cmdlet para um Azure Integration runtime:

| Propriedade | Descrição |
-------- | ------------- | 
| Nome | Nome do Azure Integration runtime. |  
| Estado | Status do Azure Integration runtime. | 
| Location | Local do Azure Integration runtime. Para obter detalhes sobre o local de Azure Integration runtime, consulte [Introdução ao runtime de integração](concepts-integration-runtime.md). |
| DataFactoryName | Nome do Data Factory ao qual o Azure Integration runtime pertence. | 
| ResourceGroupName | Nome do grupo de recursos ao qual o data factory pertence.  |
| Descrição | Descrição do runtime de integração.  |

### <a name="status"></a>Status

A tabela a seguir fornece os possíveis status do Azure Integration runtime:

| Status | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O Azure Integration runtime está online e pronto para ser usado. | 
| Offline | O Azure Integration runtime está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>runtime de integração auto-hospedada

Esta seção fornece descrições para propriedades retornadas pelo cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> As propriedades retornadas e o status contêm informações sobre o runtime de integração auto-hospedada geral e cada nó no runtime.  

### <a name="properties"></a>Propriedades

A tabela a seguir apresenta descrições das Propriedades de monitoramento para **cada nó**:

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do runtime de integração auto-hospedada e nós associados a ele. O nó é um computador local do Windows em que o runtime de integração auto-hospedada está instalado. |  
| Status | O status do runtime de integração auto-hospedada geral e de cada nó. Exemplo: online/offline/Limited/etc. Para obter informações sobre esses status, consulte a próxima seção. | 
| Versão | A versão do runtime de integração auto-hospedada geral e de cada nó. A versão do runtime de integração auto-hospedada é determinada com base na versão da maioria dos nós no grupo. Se há nós com versões diferentes na configuração do runtime de integração auto-hospedada, somente os nós com o mesmo número de versão que o runtime de integração auto-hospedada funcionam corretamente. Os outros estão no modo limitado e precisam ser atualizados manualmente (somente caso a atualização automática falhe). | 
| Memória disponível | Memória disponível em um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. |
| Rede (Entrada/Saída) | Utilização da rede de um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. | 
| Trabalhos Simultâneos (Executando/Limite) | **Em execução**. Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. <br/><br/>**Limite**. Limite significa a máxima de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho do computador. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que as atividades estão atingindo o tempo limite mesmo quando há subutilização de memória, CPU ou rede. Essa funcionalidade também está disponível com runtime de integração auto-hospedada de nó único. |
| Função | Há dois tipos de funções em um runtime de integração auto-hospedada com vários nós – dispatcher e de trabalho. Todos os nós são de trabalho, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e distribuí-los para nós de trabalho diferentes. O nó dispatcher também é um nó de trabalho. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nós no runtime de integração auto-hospedada (ou seja, no cenário de expansão).

#### <a name="concurrent-jobs-limit"></a>Limite de trabalhos simultâneos

O valor padrão do limite de trabalhos simultâneos é definido com base no tamanho do computador. Os fatores usados para calcular esse valor dependem da quantidade de RAM e do número de núcleos de CPU do computador. Portanto, quanto mais núcleos e mais memória, maior será o limite padrão de trabalhos simultâneos.

Você escala horizontalmente aumentando o número de nós. Quando você aumenta o número de nós, o limite de trabalhos simultâneos é a soma dos valores de limite de trabalhos simultâneos de todos os nós disponíveis.  Por exemplo, se um nó permitir que você execute um máximo de 12 trabalhos simultâneos, adicionar mais três nós semelhantes permitirá que você execute um máximo de 48 trabalhos simultâneos (ou seja, 4 × 12). É recomendável aumentar o limite de trabalhos simultâneos apenas quando você observar um baixo uso de recursos com os valores padrão em cada nó.

Você pode substituir o valor padrão calculado no portal do Azure. Selecione Autor > Conexões > Integration Runtimes > Editar > Nós > Modificar valor de trabalhos simultâneos por nó. Você também pode usar o comando [Update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) do PowerShell.
  
### <a name="status-per-node"></a>Status (por nó)

A tabela a seguir fornece os possíveis status de um nó de runtime de integração auto-hospedada:

| Status | Descrição |
| ------ | ------------------ | 
| Online | O nó está conectado ao serviço Data Factory. |
| Offline | O nó está offline. |
| Atualizando | O nó está sendo atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido ao problema de porta HTTP 8060, problema de conectividade do barramento de serviço ou um problema de sincronização de credencial. |
| Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria. |

Um nó pode ficar inativo quando ele não pode se conectar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (runtime de integração auto-hospedada geral)

A tabela a seguir fornece os possíveis status de um runtime de integração auto-hospedada. Esse status depende dos status de todos os nós que pertencem ao runtime. 

| Status | Descrição |
| ------ | ----------- | 
| Precisa de registro | Nenhum nó está registrado a esse runtime de integração auto-hospedada. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós no runtime de integração auto-hospedada estão em um estado íntegro. Esse status é um aviso de que alguns nós podem estar inativos. Esse status pode ser devido a um problema de sincronização de credenciais no nó dispatcher/de trabalho. |

Use o cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** para buscar o conteúdo JSON que contém as propriedades detalhadas do tempo de execução de integração auto-hospedado e seus valores de instantâneo durante o tempo de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exemplo de saída (supõe que há dois nós associados a esse runtime de integração auto-hospedada):

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

## <a name="azure-ssis-integration-runtime"></a>runtime de integração do Azure-SSIS

Azure-SSIS IR é um cluster totalmente gerenciado de máquinas virtuais do Azure (VMs ou nós) dedicado para executar seus pacotes SSIS. Você pode invocar execuções de pacote SSIS em Azure-SSIS IR usando vários métodos, por exemplo, por meio de SQL Server Data Tools habilitados para o Azure (SSDT), utilitário de linha de comando AzureDTExec, T-SQL no SQL Server Management Studio (SSMS)/SQL Server Agent e executar atividades de pacote SSIS em pipelines do ADF. Azure-SSIS IR não executa nenhuma outra atividade do ADF. Depois de provisionado, você pode monitorar suas propriedades e status gerais/específicas do nó por meio de Azure PowerShell, portal do Azure e Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Monitorar o tempo de execução de integração do Azure-SSIS com o Azure PowerShell

Use o cmdlet Azure PowerShell a seguir para monitorar as propriedades e os status gerais/específicas do nó de Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Propriedades

A tabela a seguir fornece descrições das propriedades retornadas pelo cmdlet acima para um Azure-SSIS IR.

| Propriedade/Status              | Descrição                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | A hora UTC em que o Azure-SSIS IR foi criado. |
| Nós                        | Os nós alocados/disponíveis de seu Azure-SSIS IR com status específicos de nó (iniciando/disponível/reciclando/indisponível) e erros acionáveis. |
| OtherErrors                  | Os erros acionáveis não específicos do nó em seu Azure-SSIS IR. |
| LastOperation                | O resultado da última operação de iniciar/parar no seu Azure-SSIS IR com erros acionáveis, caso tenha falhado. |
| Estado                        | O status geral (inicial/iniciando/iniciado/parando/parado) do seu Azure-SSIS IR. |
| Location                     | O local do seu Azure-SSIS IR. |
| NodeSize                     | O tamanho de cada nó em seu Azure-SSIS IR. |
| NodeCount                    | O número de nós em seu Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | O número máximo de execuções paralelas por nó em seu Azure-SSIS IR. |
| CatalogServerEndpoint        | O ponto de extremidade do seu servidor de banco de dados SQL do Azure existente ou instância gerenciada para hospedar o catálogo do SSIS (SSISDB). |
| CatalogAdminUserName         | O nome de usuário do administrador para seu servidor de banco de dados SQL do Azure existente ou instância gerenciada. O ADF usa essas informações para preparar e gerenciar o SSISDB em seu nome. |
| CatalogAdminPassword         | A senha de administrador para seu servidor de banco de dados SQL do Azure existente ou instância gerenciada. |
| CatalogPricingTier           | O tipo de preço para SSISDB hospedado pelo servidor de banco de dados SQL do Azure.  Não se aplica à Instância Gerenciada de SQL do Azure hospedando o SSISDB. |
| VNetId                       | A ID de recurso de rede virtual para seu Azure-SSIS IR ingressar. |
| Sub-rede                       | O nome da sub-rede para o Azure-SSIS IR ingressar. |
| ID                           | A ID de recurso do seu Azure-SSIS IR. |
| Type                         | O tipo de IR (gerenciado/auto-hospedado) do seu Azure-SSIS IR. |
| ResourceGroupName            | O nome do grupo de recursos do Azure, no qual o ADF e o Azure-SSIS IR foram criados. |
| DataFactoryName              | O nome do ADF. |
| Nome                         | O nome do seu Azure-SSIS IR. |
| Descrição                  | A descrição do seu Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Status (por nó de Azure-SSIS IR)

A tabela a seguir fornece os possíveis status de um nó de Azure-SSIS IR:

| Status específico do nó | Descrição |
| -------------------- | ----------- | 
| Iniciando             | Este nó está sendo preparado. |
| Disponível            | Este nó está pronto para implantar/executar pacotes do SSIS. |
| Reciclando            | Este nó está sendo reparado/está reiniciando. |
| Indisponível          | Este nó não está pronto para implantar/executar pacotes do SSIS e tem erros/problemas acionáveis que você pode resolver. |

#### <a name="status-overall-azure-ssis-ir"></a>Status (geral Azure-SSIS IR)

A tabela a seguir fornece possíveis status gerais de um Azure-SSIS IR. O status geral, por sua vez, depende dos status combinados de todos os nós que pertencem ao Azure-SSIS IR. 

| Status geral | Descrição | 
| -------------- | ----------- | 
| Inicial        | Os nós de seu Azure-SSIS IR não foram alocados/preparados. | 
| Iniciando       | Os nós de seu Azure-SSIS IR estão sendo alocados/preparados e a cobrança foi iniciada. |
| Iniciado        | Os nós de seu Azure-SSIS IR foram alocados/preparados e estão prontos para você implantar/executar pacotes do SSIS. |
| Parando       | Os nós de seu Azure-SSIS IR estão sendo liberados. |
| Parado        | Os nós de seu Azure-SSIS IR foram liberados e a cobrança foi interrompida. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Monitorar o tempo de execução de integração do Azure-SSIS no portal do Azure

Para monitorar seus Azure-SSIS IR no portal do Azure, acesse a página **tempos de execução de integração** do hub de **Monitor** na interface do usuário do ADF, onde você pode ver todos os seus tempos de execução de integração.

![Monitorar todos os tempos de execução de integração](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Em seguida, selecione o nome do seu Azure-SSIS IR para abrir sua página de monitoramento, onde você pode ver suas propriedades e status gerais/específicas do nó. Nessa página, dependendo de como você configura as configurações geral, implantação e avançado de seu Azure-SSIS IR, você encontrará vários blocos informativos/funcionais.

Os blocos informativos de **tipo** e **região** mostram o tipo e a região de seu Azure-SSIS ir, respectivamente.

O bloco informativo de **tamanho de nó** mostra o SKU (SSIS edition_VM série tier_VM), o número de núcleos de CPU e o tamanho de RAM por nó para sua Azure-SSIS ir. 

O bloco informativo de **nó (S) em execução/solicitado** compara o número de nós em execução no momento para o número total de nós solicitados anteriormente para sua Azure-SSIS ir.

O bloco informativo de **função/par de espera dupla** mostra o nome do seu par de Azure-SSIS ir em espera duplo que funciona em sincronia com o grupo de failover do banco de dados SQL/instância gerenciada do Azure para continuidade dos negócios e recuperação de desastres (BCDR) e a função primária/secundária atual de seu Azure-SSIS ir. Quando ocorre o failover do SSISDB, seu IRs do Azure-SSIS primário e secundário trocarão funções (consulte [configurando seu Azure-SSIS ir para BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)).

Os blocos funcionais são descritos em mais detalhes abaixo.

![Monitorar seu Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Bloco STATUS

No bloco **status** da sua página de monitoramento de Azure-SSIS ir, você pode ver seu status geral, por exemplo, **em execução** ou **parado**. A seleção do status de **execução** abre uma janela com o botão **parar** ao vivo para parar o Azure-SSIS ir. A seleção do status **parado** exibe uma janela com o botão **Iniciar** ao vivo para iniciar o Azure-SSIS ir. A janela pop-up também tem um botão **Executar Pacote SSIS** para gerar automaticamente um pipeline do ADF com a atividade executar pacote SSIS que é executada no seu Azure-SSIS ir (consulte [executando pacotes do SSIS como executar atividades de pacote do SSIS em pipelines do ADF](./how-to-invoke-ssis-package-ssis-activity.md)) e uma caixa de texto **ID do recurso** , na qual você pode copiar sua Azure-SSIS ir ID do recurso ( `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` ). O sufixo de sua ID de recurso de Azure-SSIS IR que contém os nomes do ADF e do Azure-SSIS IR forma uma ID de cluster que pode ser usada para comprar componentes adicionais do SSIS Premium/licenciado de fornecedores de software independentes (ISVs) e associá-los ao seu Azure-SSIS IR (consulte [instalando componentes premium/licenciados em seu Azure-SSIS ir](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![Monitore seu bloco Azure-SSIS IR STATUS](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Bloco de ponto de extremidade do servidor SSISDB

Se você usar o modelo de implantação de projeto em que os pacotes são armazenados no SSISDB hospedado por seu servidor de banco de dados SQL do Azure ou instância gerenciada, você verá o bloco de **ponto de extremidade do servidor ssisdb** na sua página de monitoramento de Azure-SSIS ir (consulte [definindo suas configurações de implantação de Azure-SSIS ir](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Nesse bloco, você pode selecionar um link designando o servidor do banco de dados SQL do Azure ou a instância gerenciada para exibir uma janela, na qual é possível copiar o ponto de extremidade do servidor de uma caixa de texto e usá-lo ao se conectar do SSMS para implantar, configurar, executar e gerenciar seus pacotes. Na janela pop-up, você também pode selecionar o link **ver seu banco de dados SQL do Azure ou as configurações de instância gerenciada** para reconfigurar/redimensionar seu SSISDB no portal do Azure.

![Monitore seu bloco Azure-SSIS IR SSISDB](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Bloco PROXY/preparo

Se você baixar, instalar e configurar Self-Hosted IR (SHIR) como um proxy para o Azure-SSIS IR acessar dados no local, você verá o bloco **proxy/preparo** na sua página de monitoramento de Azure-SSIS ir (consulte [Configurando SHIR como um proxy para o Azure-SSIS ir](./self-hosted-integration-runtime-proxy-ssis.md)). Nesse bloco, você pode selecionar um link que designa seu SHIR para abrir sua página de monitoramento. Você também pode selecionar outro link designando o armazenamento de BLOBs do Azure para preparo para reconfigurar seu serviço vinculado.

#### <a name="validate-vnet--subnet-tile"></a>VALIDAR bloco de VNET/sub-rede

Se você ingressar seu Azure-SSIS IR em uma VNet, verá o bloco **validar VNet/sub-rede** na sua página de monitoramento de Azure-SSIS ir (consulte [unindo seu Azure-SSIS ir a uma VNet](./join-azure-ssis-integration-runtime-virtual-network.md)). Nesse bloco, você pode selecionar um link que designa sua VNet e sub-rede para exibir uma janela, onde você pode copiar sua ID de recurso de VNet ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) e o nome da sub-rede de caixas de texto, bem como validar suas configurações de vnet e sub-rede para garantir que os tráfegos de rede de entrada/saída necessários e o gerenciamento de seus Azure-SSIS ir não estejam obstruídos.

![Monitorar seu bloco Azure-SSIS IR-VALIDATE](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Bloco DIAGNOSTICAr conectividade

No bloco **diagnosticar conectividade** da sua página de monitoramento de Azure-SSIS ir, você pode selecionar o link **testar conexão** para exibir uma janela, na qual você pode verificar as conexões entre seu Azure-SSIS ir e os armazenamentos de pacote/configuração/dados relevantes, bem como serviços de gerenciamento, por meio de seu endereço de FQDN (nome de domínio totalmente qualificado) e porta designada (consulte [testando conexões de seu Azure-SSIS ir](./ssis-integration-runtime-diagnose-connectivity-faq.md))

![Monitore seu bloco Azure-SSIS IR-DIAGNOSTICAr](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Bloco de endereços IP públicos ESTÁTICOs

Se você colocar seus próprios endereços IP públicos estáticos para Azure-SSIS IR, verá o bloco **endereços IP públicos estáticos** na sua página de monitoramento de Azure-SSIS ir (consulte [trazendo seus próprios endereços IP públicos estáticos para Azure-SSIS ir](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). Nesse bloco, você pode selecionar links que designam seus primeiros/segundo endereços IP públicos estáticos para Azure-SSIS IR para exibir uma janela, onde você pode copiar sua ID de recurso ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) de uma caixa de texto. Na janela pop-up, você também pode selecionar o link **ver seu primeiro/segundo configurações de endereço IP público estático** para gerenciar seu primeiro/segundo endereço IP público estático no portal do Azure.

![Monitorar seu bloco Azure-SSIS IR-estático](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Bloco de REPOSITÓRIOs de pacotes

Se você usar o modelo de implantação de pacote em que os pacotes são armazenados no sistema de arquivos/arquivos do Azure/SQL Server do banco de dados (MSDB) hospedados por seu Instância Gerenciada do Azure SQL e gerenciados por meio de repositórios de pacotes do Azure-SSIS IR, você verá o bloco **armazenamentos de pacotes** na sua página de monitoramento de Azure-SSIS ir (consulte [definindo suas configurações Azure-SSIS ir de implantação](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page) Nesse bloco, você pode selecionar um link que designa o número de repositórios de pacotes anexados à sua Azure-SSIS IR para exibir uma janela, na qual é possível reconfigurar os serviços vinculados relevantes para seus armazenamentos de pacotes do Azure-SSIS IR sobre o sistema de arquivos/arquivos do Azure/MSDB hospedado pelo seu Instância Gerenciada SQL do Azure.

![Monitorar seu bloco Azure-SSIS IR-PACKAGE](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Bloco de erro (S)

Se houver problemas com a inicialização/interrupção/manutenção/atualização do seu Azure-SSIS IR, você verá um bloco adicional de **erros** na sua página de monitoramento de Azure-SSIS ir. Nesse bloco, você pode selecionar um link que designa o número de erros gerados pelo seu Azure-SSIS IR para exibir uma janela, em que você pode ver esses erros em mais detalhes e copiá-los para encontrar as soluções recomendadas em nosso guia de solução de problemas (consulte [Solucionando problemas de seu Azure-SSIS ir](./ssis-integration-runtime-management-troubleshoot.md)).

![Monitore seu bloco Azure-SSIS IR-erro](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Monitorar o tempo de execução de integração do Azure-SSIS com o Azure Monitor

Para monitorar seu Azure-SSIS IR com Azure Monitor, consulte [monitorando operações do SSIS com o Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o runtime de integração do Azure-SSIS

Consulte os artigos a seguir para saber mais sobre o runtime de integração do Azure-SSIS:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar seu Azure-SSIS IR e usar o banco de dados SQL do Azure para hospedar o arquivo de catálogo do SSIS (SSISDB). 
- [Como: Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o Azure SQL Instância Gerenciada para hospedar o SSISDB. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como iniciar, parar ou excluir seu Azure-SSIS IR. Ele também mostra como dimensioná-lo adicionando mais nós. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece instruções sobre como unir seu Azure-SSIS IR a uma rede virtual.

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para monitorar os pipelines de maneiras diferentes: 

- [Início rápido: criar um data Factory](quickstart-create-data-factory-dot-net.md).
- [Usar o Azure Monitor para monitorar os pipelines de Data Factory](monitor-using-azure-monitor.md)

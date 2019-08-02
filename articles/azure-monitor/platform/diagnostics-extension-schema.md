---
title: Histórico de versões de esquema de configuração da extensão do Diagnóstico do Azure
description: Relevante para a coleta de contadores de desempenho em máquinas virtuais do Azure, conjuntos de dimensionamento de VM, Service Fabric e Serviços de Nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 1230a9bcea01ef394a6299c50b8d5537850cfee5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60527308"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Versões de esquema de configuração da extensão do Diagnóstico do Azure
Esta página indexa versões de esquema de extensão do Diagnóstico do Azure fornecidas como parte do SDK do Microsoft Azure.  

> [!NOTE]
> A extensão do Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric
> - Serviços de Nuvem
> - Grupos de segurança de rede
>
> Esta página só é relevante se você estiver usando um desses serviços.

A extensão de Diagnóstico do Azure é usada com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics. Para saber mais, veja [Visão geral das ferramentas de monitoramento da Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Gráfico de envio de versões do SDK e Diagnóstico do Azure  

|Versão do SDK do Azure | Versão da extensão do Diagnóstico | Modelo|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |extensão|  
|2.6               |1,3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2,9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 O Diagnóstico do Azure versão 1.0 foi enviado pela primeira vez em um modelo de plug-in, o que significa que quando você instalou o SDK do Azure, recebeu a versão do Diagnóstico do Azure fornecida com ele.  

 A partir do SDK 2.5 (versão de diagnóstico 1.2), o diagnóstico do Azure passou para um modelo de extensão. As ferramentas para utilizar os novos recursos só foram disponibilizadas em SDKs mais recentes do Azure, mas qualquer serviço que usasse o Diagnóstico do Azure escolheria a versão mais recente diretamente do Azure. Por exemplo, alguém que ainda usasse o SDK 2.5 carregaria a versão mais recente mostrada na tabela anterior, independentemente de os recursos mais recentes estarem sendo usados.  

## <a name="schemas-index"></a>Índice de esquemas  
Versões diferentes do Diagnóstico do Azure usam esquemas de configuração diferentes.

[Esquema de Configuração do Diagnóstico 1.0](diagnostics-extension-schema-1dot0.md)  

[Esquema de Configuração do Diagnóstico 1.2](diagnostics-extension-schema-1dot2.md)  

[Esquema de Configuração do Diagnóstico 1.3 e posterior](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Histórico de versão

### <a name="diagnostics-extension-111"></a>Extensão de diagnóstico 1.11
Suporte adicionado para o coletor Azure Monitor. Esse coletor só é aplicável aos contadores de desempenho. Permite o envio de contadores de desempenho coletados em sua VM, VMSS ou serviço de nuvem para o Azure Monitor como métricas personalizadas. O coletor do Azure Monitor dá suporte a:
* Recuperação de todos os contadores de desempenho enviados para o Azure Monitor por meio de [APIs de métrica do Azure Monitor.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alertas de todos os contadores de desempenho enviados para o Azure Monitor por meio da nova [experiência unificada de alertas](../../azure-monitor/platform/alerts-overview.md) no Azure Monitor
* Tratamento do operador curinga em contadores de desempenho como a dimensão de "Instância" na sua métrica. Por exemplo, se você tiver coletado o contador "LogicalDisk(\*)/DiskWrites/sec", será capaz de filtrar e dividir na dimensão "Instância" para gráfico ou alerta sobre as gravações de disco/s para cada disco lógico (C:, D: etc.)

Definir o Azure Monitor como um novo coletor em sua configuração de extensão de diagnóstico
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> A configuração do coletor do Azure Monitor para VMs clássicas e serviço de nuvem clássico exige mais parâmetros a serem definidos na configuração privada da extensão de diagnóstico.
>
> Para obter mais detalhes, veja a [documentação do esquema de extensão de diagnóstico detalhado.](diagnostics-extension-schema-1dot3.md)

Em seguida, você pode configurar seus contadores de desempenho a serem roteados para o coletor do Azure Monitor.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Extensão do Diagnóstico 1.9
Adicionar suporte ao Docker.


### <a name="diagnostics-extension-181"></a>Extensão do Diagnóstico 1.8.1
Pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração privada. Se um token SAS for fornecido, a chave da conta de armazenamento será ignorada.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Extensão do Diagnóstico 1.8
Tipo de armazenamento adicionado a PublicConfig. StorageType pode ser *Table*, *Blob*, *TableAndBlob*. *Table* é o padrão.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Extensão do Diagnóstico 1.7
Adição da habilidade para rotear ao Hub de Eventos.

### <a name="diagnostics-extension-15"></a>Extensão do diagnóstico 1.5
Adição do elemento de coleta e a capacidade de enviar dados de diagnósticos ao [Application Insights](../../azure-monitor/app/cloudservices.md), facilitando o diagnóstico de problemas em seu aplicativo, bem como no nível do sistema e da infraestrutura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>SDK 2.6 do Azure e esquema de extensão 1.3
Para projetos do Serviço de Nuvem no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam a versões mais recentes do SDK do Azure).

* O emulador local agora dá suporte a diagnósticos. Essa alteração significa que você pode coletar dados de diagnóstico e garantir que seu aplicativo está criando os rastreamentos corretos enquanto está desenvolvendo e testando no Visual Studio. A cadeia de conexão `UseDevelopmentStorage=true` habilita a coleta de dados de diagnóstico durante a execução do seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletados na conta de armazenamento (armazenamento de desenvolvimento).
* A cadeia de conexão da conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada novamente no arquivo de configuração de serviço (.cscfg). No SDK do Azure 2.5, a conta de armazenamento de diagnóstico foi especificada no arquivo diagnostics.wadcfgx.

Há algumas diferenças perceptíveis entre como a cadeia de conexão funcionava no SDK do Azure 2.4 e anteriores e como funciona no SDK do Azure 2.6 e posteriores.

* No SDK do Azure 2.4 e anteriores, a cadeia de conexão era usada como um tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os logs de diagnóstico.
* No SDK do Azure 2.6 e posteriores, o Visual Studio usa a cadeia de conexão de diagnóstico para configurar a extensão de diagnóstico com as informações da conta de armazenamento apropriadas durante a publicação. A cadeia de conexão permite definir contas de armazenamento diferentes para diferentes configurações de serviço que o Visual Studio usará ao publicar. No entanto, como o plug-in de diagnóstico não está mais disponível (após o SDK do Azure 2.5), o arquivo .cscfg sozinho por si só não é capaz de habilitar a extensão de diagnóstico. Você precisa habilitar a extensão separadamente por meio de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída do pacote do Visual Studio também contém o XML de configuração pública para a extensão de diagnóstico para cada função. O Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento presentes na configuração pública. Os arquivos de configuração públicos são criados na pasta extensões e seguem o padrão `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Todas as implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.
* A cadeia de conexão no arquivo .cscfg também é usada pelo portal do Azure para acessar os dados de diagnóstico para que possam aparecer na guia **monitoramento** . A cadeia de conexão é necessária para configurar o serviço para mostrar dados do monitoramento detalhado no portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos para o SDK do Azure 2.6 e posteriores
Ao migrar do SDK do Azure 2.5 para SDK do Azure 2.6 ou posteriores, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo de .wadcfgx, lá ela permanecerá. Para aproveitar a flexibilidade de usar diferentes contas de armazenamento para diferentes configurações de armazenamento, você terá de adicionar manualmente a cadeia de conexão ao seu projeto. Se estiver migrando um projeto do SDK 2.4 ou anterior do Azure para o SDK 2.6 do Azure, as cadeias de conexão de diagnóstico serão preservadas. No entanto, observe as alterações na forma como as cadeias de conexão são tratadas no SDK do Azure 2.6 conforme especificado na seção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* Se nenhuma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio voltará a usar a conta de armazenamento especificada no arquivo .wadcfgx para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* A cadeia de conexão de diagnóstico no arquivo .cscfg tem precedência sobre a conta de armazenamento no arquivo .wadcfgx. Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa e ignora a conta de armazenamento em .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz a caixa de seleção "Atualizar cadeias de conexão do armazenamento de desenvolvimento…"?
A caixa de seleção **Atualizar cadeias de conexão do armazenamento de desenvolvimento para Diagnóstico e cache com credenciais de conta de armazenamento do Microsoft Azure durante a publicação no Microsoft Azure** oferece uma maneira conveniente para atualizar qualquer cadeia de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que você selecione esta caixa de seleção e a cadeia de conexão de diagnóstico especifique `UseDevelopmentStorage=true`. Quando você publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de conexão de diagnóstico com a conta de armazenamento que você especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de conexão de diagnóstico, então essa conta será usada.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre SDK do Azure 2.4 e anteriores e SDK do Azure 2.5 e posteriores
Se você estiver atualizando seu projeto do SDK do Azure 2.4 para o SDK do Azure 2.5 ou posterior, considere as seguintes diferenças de funcionalidade de diagnóstico.

* **APIs de configuração estão preteridos** – A configuração programática de diagnóstico está disponível no SDK do Azure 2.4 ou anteriores, mas foi preterida no SDK do Azure 2.5 e posteriores. Se sua configuração de diagnóstico está definida atualmente no código, você precisará reconfigurar as configurações do zero no projeto migrado para que o diagnóstico continue a funcionar. O arquivo de configuração de diagnóstico do SDK do Azure 2.4 é diagnostics.wadcfg e para o SDK do Azure 2.5 e posteriores, diagnostics.wadcfgx.
* **Diagnóstico para aplicativos de serviço de nuvem só podem ser configurados no nível de função, não no nível de instância.**
* **Sempre que você implanta seu aplicativo, a configuração de diagnóstico é atualizada** – isso pode causar problemas de paridade, se você altera a configuração de diagnóstico do Gerenciador de Servidores e, em seguida, reimplanta o aplicativo.
* **No SDK do Azure 2.5 e posteriores, despejos de memória são configurados no arquivo de configuração de diagnóstico, não no código** – se você tiver despejos de memória configurados no código, precisará transferir manualmente a configuração de código para o arquivo de configuração, pois os despejos de memória não são transferidos durante a migração para o SDK do Azure 2.6.


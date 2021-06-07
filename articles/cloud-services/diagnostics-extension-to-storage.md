---
title: Armazenar e Exibir Dados de Diagnóstico no Armazenamento do Azure
description: Saiba como coletar dados de diagnóstico do Azure em uma conta de armazenamento do Azure para visualizar com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740067"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e exibir dados de diagnóstico no Armazenamento do Azure

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Os dados de diagnóstico não são armazenados permanentemente, a menos que você o transfira para o Emulador de Armazenamento do Microsoft Azure ou para o armazenamento do Azure. Quando estiverem no armazenamento, eles poderão ser exibidos com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento
Especifique a conta de armazenamento que você deseja usar no arquivo ServiceConfiguration.cscfg. As informações da conta são definidas como uma cadeia de conexão em uma definição de configuração. O exemplo a seguir mostra a cadeia de conexão padrão criada para um novo projeto do serviço de nuvem no Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Você pode alterar essa cadeia de caracteres de conexão para fornecer informações de conta para uma conta de armazenamento do Azure.

Dependendo do tipo de dados de diagnóstico que estejam sendo coletados, o diagnóstico do Azure usará o serviço Blob ou o serviço Tabela. A tabela a seguir mostra as fontes de dados persistentes e seu formato.

| Fonte de dados | Formato de armazenamento |
| --- | --- |
| Logs do Azure |Tabela |
| Logs do IIS 7.0 |Blob |
| Logs de infraestrutura do Diagnóstico do Azure |Tabela |
| Logs de Rastreamento de Solicitação com Falha |Blob |
| Log de eventos do Windows |Tabela |
| Contadores de desempenho |Tabela |
| Despejos de memória |Blob |
| Logs de erros personalizados |Blob |

## <a name="transfer-diagnostic-data"></a>Transferir dados de diagnóstico
Para o SDK 2.5 e posterior, a solicitação para transferir dados de diagnóstico pode ocorrer por meio do arquivo de configuração. Você pode transferir os dados de diagnóstico em intervalos agendados, como especificado na configuração.

Para o SDK 2.4 e anterior, você pode solicitar a transferência dos dados de diagnóstico por meio do arquivo de configuração, bem como programaticamente. A abordagem programática também permite fazer transferências sob demanda.

> [!IMPORTANT]
> Quando você transfere dados de diagnóstico para uma conta de armazenamento do Azure, incorre em custos para os recursos de armazenamento usados pelos dados de diagnóstico.
> 
> 

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico
Os dados de log são armazenados no armazenamento de Blob ou de Tabela com os seguintes nomes:

**Tabelas**

* **WadLogsTable** - logs escritos em código usando o ouvinte de rastreamento.
* **WADDiagnosticInfrastructureLogsTable** - monitor de diagnóstico e alterações de configuração.
* **WADDirectoriesTable** – diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, logs de solicitação do IIS com falha e diretórios personalizados.  O local do arquivo de log de blob é especificado no campo Container e o nome do blob está no campo RelativePath.  O campo AbsolutePath indica o local e o nome do arquivo como existia na máquina virtual do Azure.
* **WADPerformanceCountersTable** – contadores de desempenho.
* **WADWindowsEventLogsTable** – logs de Eventos do Windows.

**Blobs**

* **wad-control-container** – (somente para o SDK 2.4 e anteriores) contém os arquivos de configuração XML que controlam o diagnóstico do Azure.
* **wad-iis-failedreqlogfiles** – Contém informações de logs de solicitação com falha do IIS.
* **wad-iis-logfiles** – Contém informações sobre logs do IIS.
* **"custom"** – um contêiner personalizado com base na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois de serem transferidos para o armazenamento. Por exemplo:

* Gerenciador de Servidores no Visual Studio – Se tiver instalado as Ferramentas do Azure para o Microsoft Visual Studio, será possível usar o nó do Armazenamento do Azure no Gerenciador de Servidores para exibir os dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, veja [Procurando e gerenciando recursos de armazenamento com o Gerenciador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Gerenciamento de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite trabalhar facilmente com os dados de Armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de Diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnósticos coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Próximas etapas
[Rastrear o fluxo em um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)



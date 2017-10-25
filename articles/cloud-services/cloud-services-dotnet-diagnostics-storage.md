---
title: "Armazenar e exibir dados de diagnóstico no Armazenamento do Azure | Microsoft Docs"
description: "Obter dados de diagnóstico do Azure no Armazenamento do Azure e exibi-los"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e exibir dados de diagnóstico no Armazenamento do Azure
Os dados de diagnóstico não são armazenados permanentemente, a menos que sejam transferidos para o emulador de armazenamento do Microsoft Azure ou para o armazenamento do Azure. Quando estiverem no armazenamento, eles poderão ser exibidos com uma das várias ferramentas disponíveis.

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
| Despejos de falhas |Blob |
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
* **wad-iis-failedreqlogfiles** – contém informações de logs de solicitação com falha do IIS.
* **wad-iis-logfiles** – contém informações sobre logs do IIS.
* **"custom"** – um contêiner personalizado com base na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois de serem transferidos para o armazenamento. Por exemplo:

* Gerenciador de Servidores no Visual Studio – Se tiver instalado as Ferramentas do Azure para o Microsoft Visual Studio, será possível usar o nó do Armazenamento do Azure no Gerenciador de Servidores para exibir os dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, veja [Procurando e gerenciando recursos de armazenamento com o Gerenciador de Servidores](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Gerenciamento de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite trabalhar facilmente com os dados de Armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de Diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnósticos coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Próximas etapas
[Rastrear o fluxo em um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](cloud-services-dotnet-diagnostics-trace-flow.md)


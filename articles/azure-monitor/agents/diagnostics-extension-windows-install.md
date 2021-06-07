---
title: Instalar e configurar a extensão WAD (Diagnóstico do Azure para Windows)
description: Saiba mais sobre como instalar e configurar a extensão de diagnóstico do Windows. Além disso, saiba como uma descrição de como os dados são armazenados no e na conta de armazenamento do Azure.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 174f372f9dbe8dc0449c7f9b9f5b34c6206f92de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708553"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalar e configurar a extensão WAD (Diagnóstico do Azure para Windows)
A [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) é um agente no Azure Monitor que coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Este artigo fornece detalhes sobre como instalar e configurar a extensão de diagnóstico do Windows e uma descrição de como os dados são armazenados na conta de armazenamento do Azure.

A extensão de diagnóstico é implementada como uma [extensão da máquina virtual](../../virtual-machines/extensions/overview.md) no Azure. Portanto, dá suporte às mesmas opções de instalação usando modelos do Resource Manager, o PowerShell e a CLI. Confira [Extensões da máquina virtual e recursos do Windows](../../virtual-machines/extensions/features-windows.md) para obter detalhes sobre como instalar e manter extensões da máquina virtual.

## <a name="overview"></a>Visão geral
Ao configurar a extensão de diagnóstico do Windows Azure, você deverá especificar uma conta de armazenamento na qual todos os dados especificados serão enviados. Opcionalmente, você pode adicionar um ou mais *coletores de dados* para enviar os dados para locais diferentes.

- Coletor do Azure Monitor – Envia dados de desempenho do convidado para as métricas do Azure Monitor.
- Coletor do hub de eventos – Envia dados de log e desempenho de convidado para os hubs de eventos do Azure para encaminhar fora do Azure. Esse coletor pode ser configurado no portal do Azure.


## <a name="install-with-azure-portal"></a>Instalação com o portal do Azure
Você pode instalar e configurar a extensão de diagnóstico em uma máquina virtual individual no portal do Azure, que fornece uma interface em oposição a trabalhar diretamente com a configuração. Quando você habilita a extensão de diagnóstico, usa automaticamente uma configuração padrão com os contadores de desempenho e eventos mais comuns. Você pode modificar esta configuração padrão de acordo com seus requisitos específicos.

> [!NOTE]
> O seguinte descreve as configurações mais comuns para a extensão de diagnóstico. Para obter detalhes sobre as opções de configuração, confira [Esquema de extensão de diagnóstico do Windows](diagnostics-extension-schema-windows.md).

1. Abra o menu de uma máquina virtual no portal do Azure.

2. Clique em **Configurações de diagnóstico** na seção **Monitoramento** do menu da VM.

3. Clique em **Habilitar monitoramento em nível de convidado** se a extensão de diagnóstico ainda não tiver sido habilitada.

   ![Habilitar o monitoramento](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Uma nova conta de armazenamento do Azure será criada para a VM com o nome baseado no nome do grupo de recursos para a VM, e um conjunto padrão de contadores e logs de desempenho de convidado será selecionado.

   ![Configurações de Diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Na guia **Contadores de desempenho**, selecione as métricas de convidado que você deseja coletar desta máquina virtual. Use a configuração **Personalizado** para uma seleção mais avançada.

   ![Contadores de desempenho](media/diagnostics-extension-windows-install/performance-counters.png)

6. Na guia **Logs**, selecione os logs para coletar da máquina virtual. Os logs podem ser enviados para os hubs de eventos ou armazenamento, mas não para o Azure Monitor. Use o [agente de análise de logs](../agents/log-analytics-agent.md) para coletar logs de convidado para o Azure Monitor.

   ![Captura de tela mostra a guia logs com diferentes logs selecionados para uma máquina virtual.](media/diagnostics-extension-windows-install/logs.png)

7. Na guia **Despejos de memória**, especifique todos os processos para coletar os despejos das memórias após uma falha. Os dados serão gravados na conta de armazenamento para a configuração de diagnóstico, e você poderá especificar um contêiner de BLOB.

   ![Despejos de memória](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Na guia **Coletores**, especifique se deseja enviar os dados para locais diferentes do armazenamento do Azure. Se você selecionar **Azure Monitor**, os dados de desempenho do convidado serão enviados para as métricas do Azure Monitor. Você não pode configurar o coletor dos hubs de eventos usando o portal do Azure.

   ![Captura de tela mostra a guia coletores com a opção enviar dados de diagnóstico para Azure Monitor habilitada.](media/diagnostics-extension-windows-install/sinks.png)
   
   Se você não tiver habilitado uma identidade atribuída ao sistema configurada para sua máquina virtual, poderá ver o aviso abaixo ao salvar uma configuração com o coletor do Azure Monitor. Clique na faixa para habilitar a identidade atribuída ao sistema.
   
   ![Entidade gerenciada](media/diagnostics-extension-windows-install/managed-entity.png)

9. No **Agente**, você pode alterar a conta de armazenamento, definir a cota de disco e especificar se os logs de infraestrutura de diagnóstico devem ser coletados.  

   ![Captura de tela mostra a guia agente com a opção de definir a conta de armazenamento.](media/diagnostics-extension-windows-install/agent.png)

10. Clique em **Salvar** para salvar a configuração. 

> [!NOTE]
> Embora a configuração da extensão de diagnóstico possa ser formatada em JSON ou XML, qualquer configuração feita na portal do Azure sempre será armazenada como JSON. Se você usar XML com outro método de configuração e alterar sua configuração com o portal do Azure, as configurações serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Confira [Use monitoramento e diagnóstico com uma VM do Windows e modelos do Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) sobre como implantar a extensão de diagnóstico com os modelos do Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure
A CLI do Azure pode ser usado para implantar a extensão de Diagnóstico do Azure para uma máquina virtual existente usando [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) como no exemplo a seguir. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

As configurações protegidas são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) do esquema de configuração. A seguir, um exemplo mínimo de um arquivo de configurações protegido que define a conta de armazenamento. Confira [Exemplo de configuração](diagnostics-extension-schema-windows.md#privateconfig-element) para obter detalhes completos das configurações particulares.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

As configurações públicas são definidas no [elemento Public](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. A seguir, um exemplo mínimo de um arquivo de configurações públicas que habilita a coleta de logs de infraestrutura de diagnóstico, um único contador de desempenho e um único log de eventos. Confira [Exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações públicas.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Implantação do PowerShell
O PowerShell pode ser usado para implantar a extensão de Diagnóstico do Azure para uma máquina virtual existente usando [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) como no exemplo a seguir. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

As configurações particulares são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), enquanto as configurações públicas são definidas no [elemento Public](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Você também pode optar por especificar os detalhes da conta de armazenamento como parâmetros do cmdlet Set-AzVMDiagnosticsExtension em vez de incluir nas configurações privadas.

A seguir está um exemplo mínimo de um arquivo de configuração que habilita a coleta de logs de infraestrutura de diagnóstico, um único contador de desempenho e um único log de eventos. Confira [Exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações particulares e públicas. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Consulte também [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Armazenamento de dados
A tabela a seguir lista os diferentes tipos de dados coletados da extensão de diagnóstico e se são armazenados como uma tabela ou um blob. Os dados armazenados em tabelas também podem ser armazenados em blobs dependendo da [configuração StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) em sua configuração pública.


| Dados | Tipo de armazenamento | Descrição |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Alterações da configuração e do monitor de diagnóstico. |
| WADDirectoriesTable | Tabela | Diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, logs de solicitação do IIS com falha e diretórios personalizados.  O local do arquivo de log de blob é especificado no campo Container e o nome do blob está no campo RelativePath.  O campo AbsolutePath indica o local e o nome do arquivo como existia na máquina virtual do Azure. |
| WADLogsTable | Tabela | Logs escritos em código usando o ouvinte de rastreamento. |
| WADPerformanceCountersTable | Tabela | Contadores de desempenho. |
| WADWindowsEventLogsTable | Tabela | Logs de eventos do Windows. |
| wad-iis-failedreqlogfiles | Blob | Contém informações de logs de solicitação com falha do IIS. |
| wad-iis-logfiles | Blob | Contém informações sobre logs do IIS. |
| "custom" | Blob | Um contêiner personalizado com base na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois de serem transferidos para o armazenamento. Por exemplo:

* Gerenciador de Servidores no Visual Studio – Se tiver instalado as Ferramentas do Azure para o Microsoft Visual Studio, será possível usar o nó do Armazenamento do Azure no Gerenciador de Servidores para exibir os dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, veja [Procurando e gerenciando recursos de armazenamento com o Gerenciador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Gerenciamento de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite trabalhar facilmente com os dados de Armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de Diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnósticos coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Próximas etapas
- Confira [Enviar dados da extensão de diagnóstico do Windows Azure para hubs de eventos](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre como encaminhar os dados de monitoramento para os hubs de eventos do Azure.

---
title: Registrar em log o fluxo de tráfego de rede bidirecionalmente em uma VM – tutorial – portal do Azure | Microsoft Docs
description: Saiba como registrar em log o fluxo de tráfego de rede bidirecionalmente em uma VM usando a funcionalidade de logs de fluxo do NSG do Observador de Rede.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 76d57990fcdd82bfe382fa2fb4ab75fb037caf4f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063909"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Tutorial: Registrar em log o tráfego de rede bidirecionalmente em uma máquina virtual usando o portal do Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)

Um NSG (grupo de segurança de rede) permite filtrar o tráfego de entrada e o tráfego de saída bidirecionalmente em uma VM (máquina virtual). Você pode registrar em log o tráfego de rede que flui por um NSG com a funcionalidade de log de fluxo do NSG do Observador de Rede. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM com um grupo de segurança de rede
> * Habilitar o Observador de Rede e registrar o provedor Microsoft.Insights
> * Habilitar um log de fluxo de tráfego para um NSG, usando a funcionalidade de log de fluxo do NSG do Observador de Rede
> * Baixar os dados registrados em log
> * Exibir os dados registrados em log

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, **Windows Server 2016 Datacenter** ou uma versão do **Ubuntu Server**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome|myVm|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Selecione sua assinatura.|
    |Resource group| Selecione **Criar novo** e insira **myResourceGroup**.|
    |Location| Selecione **Leste dos EUA**|

4. Selecione um tamanho para a VM e selecione **Selecionar**.
5. Em **Configurações**, aceite todos os padrões e selecione **OK**.
6. Em **Criar** do **Resumo**, selecione **Criar** para iniciar a implantação da VM. A VM demora alguns minutos para implantar. Aguarde até que a VM conclua a implantação antes de continuar com as etapas restantes.

A VM demora alguns minutos para criar. Não continue com as etapas restantes até que a VM seja criada. Embora o portal crie a VM, ele também cria um grupo de segurança de rede com o nome **myVm-nsg** e o associa ao adaptador de rede da VM.

## <a name="enable-network-watcher"></a>Habilitar o Observador de Rede

Se você já tiver um observador de rede habilitado na região Leste dos EUA, vá para [Registrar provedor Insights](#register-insights-provider).

1. No portal, selecione **Todos os serviços**. Na **caixa Filtro**, insira *Observador de Rede*. Quando o **Observador de Rede** for exibido nos resultados, selecione-o.
2. Selecione **Regiões** para expandi-la e, em seguida, selecione **...** à direita de **Leste dos EUA**, conforme mostrado na seguinte imagem:

    ![Habilitar o Observador de Rede](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Selecione **Habilitar o Observador de Rede**.

## <a name="register-insights-provider"></a>Registrar o provedor Insights

O log de fluxo do NSG exige o provedor **Microsoft.Insights**. Para registrar o provedor, conclua as seguintes etapas:

1. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa Filtro, digite *Assinaturas*. Quando a opção **Assinaturas** for exibida nos resultados da pesquisa, selecione-a.
2. Na lista de assinaturas, selecione a assinatura na qual deseja habilitar o provedor.
3. Selecione **Provedores de recursos**, em **CONFIGURAÇÕES**.
4. Confirme se o **STATUS** do provedor **microsoft.insights** é **Registrado**, conforme mostrado na imagem a seguir. Se o status for **Não Registrado**, selecione **Registrar**, à direita do provedor.

    ![Registrar provedor](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Habilitar o log de fluxo do NSG

1. Os dados do log de fluxo do NSG são gravados em uma conta do Armazenamento do Azure. Para criar uma conta do Armazenamento do Azure, selecione **+ Criar um recurso** na parte superior esquerda do portal.
2. Selecione **Armazenamento** e, em seguida, **Conta de armazenamento – blob, arquivo, tabela, fila**.
3. Insira ou selecione as informações a seguir, aceite os padrões restantes e, em seguida, selecione **Criar**.

    | Configuração        | Valor                                                        |
    | ---            | ---   |
    | Nome           | 3 a 24 caracteres, pode conter apenas letras minúsculas e números e deve ser exclusivo em todas as contas do Armazenamento do Azure.                                                               |
    | Location       | Selecione **Leste dos EUA**                                           |
    | Resource group | Clique em **Usar existente** e selecione **myResourceGroup** |

    A conta de armazenamento pode levar cerca de um minuto para ser criada. Não continue com as etapas restantes até que a conta de armazenamento seja criada. Em todos os incidentes, a conta de armazenamento deve estar na mesma região do NSG.
4. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa **Filtro**, digite *Observador de Rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
5. Em **LOGS**, selecione **Logs de fluxo do NSG**, conforme mostrado na seguinte imagem:

    ![A captura de tela mostra os logs de fluxo do NSG do Observador de Rede.](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Na lista de NSGs, selecione o NSG chamado **myVm-nsg**.
7. Em **Configurações dos logs de fluxo**, selecione **Ativado**.
8. Selecione a versão de registro em log do fluxo. A versão 2 contém estatísticas de sessão de fluxo (bytes e pacotes)

   ![Selecione a versão Logs de fluxo](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Selecione a conta de armazenamento criada na etapa 3.
   > [!NOTE]
   > Os logs do Fluxo do NSG não funcionam com contas de armazenamento que têm [namespace hierárquico](../storage/blobs/data-lake-storage-namespace.md) habilitado.
1. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa **Filtro**, digite *Observador de Rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
10. Defina **Retenção (dias)** como 5 e, em seguida, selecione **Salvar**.

## <a name="download-flow-log"></a>Baixar log de fluxo

1. No Observador de Rede, no portal, selecione **Logs de fluxo do NSG** em **LOGS**.
2. Selecione **Você pode baixar os logs de fluxo das contas de armazenamento configuradas**, conforme mostrado na seguinte imagem:

   ![Baixar logs de fluxo](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Selecione a conta de armazenamento que você configurou na etapa 2 de [Habilitar o log de fluxo do NSG](#enable-nsg-flow-log).
4. Em **Serviço Blob**, selecione **Contêineres** e, em seguida, selecione o contêiner **insights-logs-networksecuritygroupflowevent**.
5. No contêiner, navegue na hierarquia de pastas até chegar a um arquivo PT1H.json, conforme mostrado na figura a seguir. Arquivos de log são gravados em uma hierarquia de pastas que segue a seguinte convenção de nomenclatura: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

   ![Log de fluxo](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Selecione **...** à direita do arquivo PT1H.json e selecione **Baixar**.

## <a name="view-flow-log"></a>Exibir log de fluxo

O seguinte json é um exemplo do que você verá no arquivo PT1H.json de cada fluxo no qual os dados são registrados:

### <a name="version-1-flow-log-event"></a>Evento de log de fluxo versão 1
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Evento de log de fluxo versão 2
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

O valor de **mac** na saída anterior é o endereço MAC do adaptador de rede que foi criado no momento da criação da VM. As informações separadas por vírgula de **flowTuples** são as seguintes:

| Dados de exemplo | O que os dados representam   | Explicação                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Carimbo de data/hora             | O carimbo de data/hora de quando ocorreu o fluxo, no formato UNIX EPOCH. No exemplo anterior, a data é convertida em 1º de maio de 2018 14h59min05s GMT.                                                                                    |
| 10.0.0.4  | Endereço IP de origem      | O endereço IP de origem do qual se originou o fluxo. 10.0.0.4 é o endereço IP privado da VM criada em [Criar uma VM](#create-a-vm).
| 13.67.143.118     | Endereço IP de destino | O endereço IP de destino ao qual o fluxo foi destinado.                                                                                  |
| 44931        | Porta de origem            | A porta de origem da qual se originou o fluxo.                                           |
| 443         | Porta de destino       | A porta de destino à qual o fluxo foi destinado. Como o tráfego era destinado à porta 443, a regra nomeada **UserRule_default-allow-rdp** no arquivo de log processou o fluxo.                                                |
| T            | Protocolo               | Indica se o protocolo do fluxo era TCP (T) ou UDP (U).                                  |
| O            | Direção              | Indica se o tráfego era de entrada (I) ou de saída (O).                                     |
| Um            | Ação                 | Indica se o tráfego foi permitido (A) ou negado (D).  
| C            | Estado de fluxo da **Versão 2 somente** | Captura o estado do fluxo. Os possíveis estados são **B**: Iniciar, quando um fluxo é criado. As estatísticas não são fornecidas. **C**: Continuando um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Encerrar, quando um fluxo é encerrado. Estatísticas são fornecidas. |
| 30 | Pacotes enviados – Origem ao destino **Versão 2 somente** | A quantidade total de pacotes TCP ou UDP enviados da origem ao destino desde a última atualização. |
| 16978 | Bytes enviados – Origem ao destino **Versão 2 somente** | A quantidade total de bytes de pacote TCP ou UDP enviados da origem para o destino desde a última atualização. O número total de bytes de pacote TCP e UDP enviados da origem para o destino desde os últimos bytes updatePacket inclui o cabeçalho e a carga útil do pacote. |
| 24 | Pacotes enviados – Destino para origem **Versão 2 somente** | A quantidade total de pacotes TCP ou UDP enviados do destino para a origem desde a última atualização. |
| 14008| Bytes enviados – Destino para origem **Versão 2 somente** | Os bytes de pacote incluem cabeçalho de pacote e carga útil. Os bytes de pacote incluem cabeçalho de pacote e carga útil.|

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a habilitar o log de fluxo de um NSG. Você também aprendeu a baixar e exibir em um arquivo os dados registrados em log. Os dados brutos no arquivo json podem ser difíceis de serem interpretados. Para visualizar os dados dos Logs de Fluxo, você pode usar a [Análise de Tráfego do Azure](traffic-analytics.md), o [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) e outras ferramentas. Você pode tentar métodos alternativos de habilitar Logs de Fluxo NSG como [PowerShell](network-watcher-nsg-flow-logging-powershell.md), [CLI do Azure](network-watcher-nsg-flow-logging-cli.md), [API REST](network-watcher-nsg-flow-logging-rest.md) e [modelos do ARM](network-watcher-nsg-flow-logging-azure-resource-manager.md).
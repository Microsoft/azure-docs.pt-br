---
title: Pool de criação de evento Azure Batch | Microsoft Docs
description: Referência para o pool do lote criar um evento.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: a86ce329a19272eb83c431af395b330f75111361
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323305"
---
# <a name="pool-create-event"></a>Evento de criação de pool

 Esse evento é emitido quando um pool é criado. O conteúdo do log expõe as informações gerais do pool. Observe que, se o tamanho de destino do pool for maior que 0 nós de computação, um evento inicial de redimensionamento do pool seguirá imediatamente esse evento.

 O exemplo a seguir mostra o corpo de um evento de criação de pool para um pool criado usando a propriedade CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|id|Cadeia de caracteres|A ID do pool.|
|displayName|Cadeia de caracteres|O nome de exibição do pool.|
|vmSize|Cadeia de caracteres|O tamanho das máquinas virtuais no pool. Todas as máquinas virtuais em um pool têm o mesmo tamanho. <br/><br/> Para obter informações sobre tamanhos disponíveis de máquinas virtuais para pools de serviços de nuvem (pools criados com cloudServiceConfiguration), consulte [Tamanhos para serviços de nuvem](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). O lote dá suporte a todos os tamanhos de VM de serviços de nuvem, exceto `ExtraSmall`.<br/><br/> Para obter informações sobre tamanhos de VM disponíveis para pools usando imagens do Marketplace de máquinas virtuais (pools criados com virtualMachineConfiguration) consulte [Tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) ou [Tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). O Lote dá suporte a todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e aqueles com armazenamento premium (série `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Tipo complexo|A configuração do serviço de nuvem para o pool.|
|[virtualMachineConfiguration](#bk_vmconf)|Tipo complexo|A configuração da máquina virtual para o pool.|
|[networkConfiguration](#bk_netconf)|Tipo complexo|A configuração de rede para o pool.|
|resizeTimeout|Time|O tempo limite de alocação de nós de computação para o pool especificado para a última operação de redimensionamento no pool.  (O dimensionamento inicial quando o pool é criado conta como um redimensionamento.)|
|targetDedicated|Int32|O número de nós de computação solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|enableInterNodeCommunication|Bool|Especifica se o pool é configurado para comunicação direta entre nós.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
|maxTasksPerNode|Int32|O número máximo de tarefas que podem ser executadas simultaneamente em um único nó de computação no pool.|
|vmFillType|Cadeia de caracteres|Define como o serviço em lotes distribui tarefas entre nós de computação no pool. Os valores válidos são Difundir ou Empacotar.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|osFamily|Cadeia de caracteres|A família do SO convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> Os valores possíveis são:<br /><br /> **2** – SO Família 2, equivalente ao Windows Server 2008 R2 SP1.<br /><br /> **3** – SO Família 3, equivalente ao Windows Server 2012.<br /><br /> **4** – SO Família 4, equivalente ao Windows Server 2012 R2.<br /><br /> Para obter mais informações, consulte [Lançamentos do SO convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Cadeia de caracteres|A versão do SO convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> O valor padrão é **\*** que especifica a última versão do sistema operacional da família especificada.<br /><br /> Para outros valores permitidos, consulte [Lançamentos do SO convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Tipo complexo|Especifica informações sobre a plataforma ou imagem do Marketplace a ser usada.|
|nodeAgentSKUId|Cadeia de caracteres|O SKU do agente do nó do lote provisionado em nós de computação.|
|[windowsConfiguration](#bk_winconf)|Tipo complexo|Especifica as configurações de sistema operacional do Windows na máquina virtual. Essa propriedade não deverá ser especificada se imageReference fizer referência a uma imagem do SO do Linux.|

###  <a name="bk_imgref"></a> imageReference

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|publisher|Cadeia de caracteres|Especifica o editor da imagem.|
|oferta|Cadeia de caracteres|A oferta da imagem.|
|sku|Cadeia de caracteres|A SKU da imagem.|
|version|Cadeia de caracteres|A versão da imagem.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolean|Indica se a máquina virtual está habilitada para atualizações automáticas. Se essa propriedade não for especificada, o valor padrão será verdadeiro.|

###  <a name="bk_netconf"></a> networkConfiguration

|Nome do elemento|Tipo|Observações|
|------------------|--------------|----------|
|subnetId|Cadeia de caracteres|Especifica o identificador do recurso da sub-rede em que nós de computação do pool são criados.|

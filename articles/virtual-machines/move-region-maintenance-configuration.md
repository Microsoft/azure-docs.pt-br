---
title: Mover uma configuração de manutenção para outra região do Azure
description: Saiba como mover uma configuração de manutenção de VM para outra região do Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202905"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Mover uma configuração de controle de manutenção para outra região

Siga este artigo para mover uma configuração de controle de manutenção para uma região diferente do Azure. Talvez você queira mover uma configuração por vários motivos. Por exemplo, para aproveitar uma nova região, implantar recursos ou serviços disponíveis em uma região específica, para atender aos requisitos internos de políticas e governança, ou em resposta ao planejamento de capacidade.

O [controle de manutenção](maintenance-control.md), com configurações de manutenção personalizadas, permite que você controle como as atualizações de plataforma são aplicadas às VMs e aos hosts dedicados do Azure. Há alguns cenários para mover o controle de manutenção entre regiões:

- Para mover sua configuração de controle de manutenção, mas não os recursos associados à configuração, siga as instruções neste artigo.
- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga [estas instruções](move-region-maintenance-configuration-resources.md).
- Para mover a configuração de manutenção e os recursos associados a ela, primeiro siga as instruções neste artigo. Em seguida, siga [estas instruções](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover uma configuração de controle de manutenção:

- As configurações de manutenção são associadas às VMs do Azure ou aos hosts dedicados do Azure. Verifique se os recursos de VM/host existem na nova região antes de começar.
- Identify: 
    - Configurações de controle de manutenção existentes.
    - Os grupos de recursos nos quais as configurações existentes residem atualmente. 
    - Os grupos de recursos aos quais as configurações serão adicionadas após a mudança para a nova região. 
    - Os recursos associados à configuração de manutenção que você deseja mover.
    - Verifique se os recursos na nova região são os mesmos associados às configurações de manutenção atuais. As configurações podem ter os mesmos nomes na nova região que faziam no antigo, mas isso não é necessário.

## <a name="prepare-and-move"></a>Preparar e mover 

1. Recupere todas as configurações de manutenção em cada assinatura. Execute o comando da lista de configuração da CLI [AZ Maintenance](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) para fazer isso, substituindo $subId pela sua ID de assinatura.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Examine a lista de tabelas retornada de registros de configuração na assinatura. Veja um exemplo. Sua lista conterá valores para seu ambiente específico.

    **Nome** | **Localização** | **Grupo de recursos**
    --- | --- | ---
    Ignorar manutenção | eastus2 | configuração-grupo de recursos
    IgniteDemoConfig | eastus2 | configuração-grupo de recursos
    defaultMaintenanceConfiguration-lesteus | eastus | teste-configuração
    

3. Salve sua lista para referência. À medida que você move as configurações, ele ajuda a verificar se tudo foi movido.
4. Como referência, mapeie cada grupo de recursos/configuração para o novo grupo de recursos na nova região.
5. Crie novas configurações de manutenção na nova região usando o [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)ou a [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associe as configurações aos recursos na nova região, usando o [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)ou a [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verificar a movimentação

Depois de mover as configurações, compare as configurações e os recursos na nova região com a lista de tabelas que você preparou.


## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Após a movimentação, considere excluir as configurações de manutenção movidas na região de origem, no [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)ou na [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Próximas etapas

Siga [estas instruções](move-region-maintenance-configuration-resources.md) se você precisar mover os recursos associados às configurações de manutenção. 

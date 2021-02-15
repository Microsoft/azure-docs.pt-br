---
title: Alterar dinamicamente o nível de serviço de um volume para Azure NetApp Files | Microsoft Docs
description: Descreve como alterar dinamicamente o nível de serviço de um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 78cc68d2be600cec78c433ae3eae1de09d31ac94
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251804"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Alterar dinamicamente o nível de serviço de um volume

> [!IMPORTANT] 
> Atualmente, não há suporte para a alteração dinâmica do nível de serviço de um volume de destino de replicação.

Você pode alterar o nível de serviço de um volume existente movendo o volume para outro pool de capacidade que usa o [nível de serviço](azure-netapp-files-service-levels.md) desejado para o volume. Essa alteração no nível de serviço in-loco para o volume não exige a migração de dados. Ele também não afeta o acesso ao volume.  

Essa funcionalidade permite que você atenda às suas necessidades de carga de trabalho sob demanda.  É possível alterar um volume existente para usar um nível de serviço mais alto e melhorar o desempenho. Outra opção é usar um nível de serviço inferior para a otimizar os custos. Por exemplo, se o volume estiver atualmente em um pool de capacidade que usa o nível de serviço *Standard* e você quiser que o volume use o nível de serviço *Premium* , você poderá mover o volume dinamicamente para um pool de capacidade que usa o nível de serviço *Premium* .  

O pool de capacidade para o qual você deseja mover o volume já deve existir. O pool de capacidade pode conter outros volumes.  Se desejar mover o volume para um pool de capacidade totalmente novo, você precisará [criar o pool de capacidade](azure-netapp-files-set-up-capacity-pool.md) antes de mover o volume.  

## <a name="considerations"></a>Considerações

* Depois que o volume for movido para outro pool de capacidade, você não terá mais acesso aos logs de atividade do volume anterior e às métricas de volume. O volume será iniciado com novos logs de atividade e métricas no novo pool de capacidade.

* Se você mover um volume para um pool de capacidade de um nível de serviço mais alto (por exemplo, mudando de *padrão* para *Premium* ou *ultra* nível de serviço), deverá aguardar pelo menos sete dias antes de poder mover esse volume *novamente* para um pool de capacidade de um nível de serviço inferior (por exemplo, mudando de *ultra* para *Premium* ou *Standard*).  

## <a name="register-the-feature"></a>Registrar o recurso

O recurso para mover um volume para outro pool de capacidade está atualmente em versão prévia. Se você estiver usando esse recurso pela primeira vez, é necessário registrar o recurso primeiro.

1. Registre o recurso: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Verifique o status do registro do recurso: 

    > [!NOTE]
    > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Você também pode usar [comandos de CLI do Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Mover um volume para outro pool de capacidade

1.  Na página volumes, clique com o botão direito do mouse no volume cujo nível de serviço você deseja alterar. Selecione **alterar pool**.

    ![Clique com o botão direito do mouse em volume](../media/azure-netapp-files/right-click-volume.png)

2. Na janela alterar pool, selecione o pool de capacidade para o qual você deseja mover o volume. 

    ![Alterar pool](../media/azure-netapp-files/change-pool.png)

3.  Clique em **OK**.


## <a name="next-steps"></a>Próximas etapas  

* [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Solucionar problemas de alteração do pool de capacidade de um volume](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)

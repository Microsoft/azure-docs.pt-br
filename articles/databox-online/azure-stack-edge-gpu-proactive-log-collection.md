---
title: Entender a coleta de logs proativo no dispositivo pro Azure Stack Edge
description: Descreve como a coleta de logs proativo é feita em um dispositivo Azure Stack Edge pro e como desabilitá-la.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443005"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Coleção de log proativo em seu dispositivo de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A coleta de log proativo reúne indicadores de integridade do sistema em seu Azure Stack dispositivo de borda para ajudá-lo a solucionar problemas de qualquer problema de dispositivo com eficiência. A coleta de log proativo está habilitada por padrão. Este artigo descreve o que está registrado em log, como a Microsoft trata os dados e como desabilitar ou habilitar a coleta proativa de logs. 

As informações neste artigo aplicam-se a Azure Stack Edge pro GPU, Azure Stack Edge pro R e Azure Stack dispositivos mini R do Edge.

## <a name="about-proactive-log-collection"></a>Sobre a coleta de log proativo

As equipes de atendimento ao cliente e de engenharia da Microsoft usam logs do sistema de seu Azure Stack Edge para identificar e corrigir com eficiência os problemas que podem surgir durante a operação. A coleta de log proativo é um método que alerta a Microsoft de que um problema/evento foi detectado pelo dispositivo de borda de Azure Stack do cliente. Consulte [indicadores de coleta de log proativo](#proactive-log-collection-indicators) para eventos que são rastreados. Os logs de suporte referentes ao problema são automaticamente carregados para uma conta de armazenamento do Azure que a Microsoft gerencia e controla. Suporte da Microsoft e engenheiros da Microsoft examinam esses logs de suporte para determinar o melhor curso de ação para resolver o problema com o cliente.

> [!NOTE]
> Esses logs são usados apenas para fins de depuração e para fornecer suporte aos clientes em caso de problemas.


## <a name="enabling-proactive-log-collection"></a>Habilitando a coleta de logs proativo

A coleta de log proativo está habilitada por padrão. Você pode desabilitar a coleta de logs proativo ao tentar ativar o dispositivo por meio da interface do usuário local. 

1. Na interface do usuário da Web local do dispositivo, vá para **a página de introdução.**

2. No bloco **Ativação**, selecione **Ativar**. 

    ![Página "Detalhes de nuvem" da IU da Web local 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. No painel **Ativar**:

   1. Insira a **Chave de ativação** que você recebeu em [Obter chave de ativação para o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Depois de ativada, a coleta de log proativo é habilitada por padrão, permitindo que a Microsoft colete logs com base no status de integridade do dispositivo. Esses logs são carregados em uma conta de armazenamento do Azure. 

      Você pode desabilitar a coleta proativa de logs para impedir que a Microsoft colete logs.

   1. Se você quiser desabilitar a coleta de logs proativo no dispositivo, selecione **desabilitar**.

   1. Selecione **Ativar**.

   ![Página "Detalhes de nuvem" da IU da Web local 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Indicadores de coleta de log proativo

Depois que a coleção de logs proativo estiver habilitada, os logs serão carregados automaticamente quando um dos seguintes eventos for detectado no dispositivo:  


|Alerta/erro/condição  |Descrição  |
|---------|---------|
|AcsUnhealthyCondition     |Os serviços consistentes do Azure não estão íntegros.         |
|IOTEdgeAgentNotRunningCondition      |O agente de IoT Edge não está em execução.         |
|UpdateInstallFailedEvent | Não foi possível instalar a atualização.        |

 
A Microsoft continuará a adicionar novos eventos à lista anterior. Nenhum consentimento adicional é necessário para novos eventos. Consulte esta página para saber mais sobre os indicadores de coleta de log proativo mais atualizados.    
 

## <a name="other-log-collection-methods"></a>Outros métodos de coleta de log

Além da coleção de logs proativo, que coleta logs específicos referentes a um problema específico detectado, outras coleções de logs podem dar uma compreensão muito mais profunda da integridade e do comportamento do sistema. Normalmente, esses outros logs podem ser coletados durante uma solicitação de suporte ou disparados pela Microsoft com base nos dados de telemetria do dispositivo.

## <a name="handling-data"></a>Manipulando dados

Quando a coleta de logs proativo está habilitada, o cliente concorda com a Microsoft coletando logs do dispositivo Azure Stack Edge, conforme descrito aqui. O cliente também reconhece e concede ao upload e à retenção desses logs em uma conta de armazenamento do Azure gerenciada e controlada pela Microsoft.

A Microsoft usa os dados para solucionar problemas de integridade do sistema e problemas apenas. Os dados não são usados para marketing, propaganda ou outras finalidades comerciais sem o consentimento do cliente. 

Os dados que a Microsoft coleta são tratados de acordo com nossas práticas de privacidade padrão. Se um cliente decidir revogar seu consentimento para a coleta de logs proativo, todos os dados coletados com consentimento antes da revogação não serão afetados.

## <a name="next-steps"></a>Próximas etapas

Saiba como [reunir um pacote de suporte](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).
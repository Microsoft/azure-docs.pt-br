---
title: Entender a coleta de logs proativo no dispositivo pro Azure Stack Edge
description: Descreve como a coleta de logs proativo é feita em um dispositivo pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465917"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Coleção de log proativo em seu dispositivo de Azure Stack Edge

Você pode habilitar a coleta de log proativo em seu dispositivo Azure Stack Edge com base nos indicadores de integridade do sistema para ajudar a solucionar com eficiência qualquer problema de dispositivo. Este artigo descreve o que é a coleta de logs proativo, como habilitá-la e como os dados são manipulados quando a coleta de logs proativo está habilitada.
   
As informações neste artigo aplicam-se a Azure Stack Edge pro GPU, Azure Stack Edge pro R e Azure Stack dispositivos mini R do Edge.

## <a name="about-proactive-log-collection"></a>Sobre a coleta de log proativo

As equipes de atendimento ao cliente e de engenharia da Microsoft usam logs do sistema de seu Azure Stack Edge para identificar e corrigir com eficiência os problemas que podem surgir durante a operação. A coleta de log proativo é um método que alerta a Microsoft de que um problema/evento (consulte a seção indicadores de coleção de logs proativo para eventos que estão sendo controlados) foi detectado pelo dispositivo de borda de Azure Stack do cliente. Os logs de suporte referentes ao problema são automaticamente carregados para uma conta de armazenamento do Azure gerenciada e controlada pela Microsoft. Suporte da Microsoft e engenheiros da Microsoft examinam esses logs de suporte para determinar o melhor curso de ação para resolver o problema com o cliente.    

> [!NOTE]
> Esses logs são usados apenas para fins de depuração e fornecem suporte aos clientes em caso de problemas. 


## <a name="enabling-proactive-log-collection"></a>Habilitando a coleta de logs proativo

Você pode habilitar a coleção de logs proativo ao tentar ativar o dispositivo por meio da interface do usuário local. 

1. Na IU da Web local do dispositivo, acesse a página **Introdução**.
2. No bloco **Ativação**, selecione **Ativar**. 

    ![Interface do usuário da Web local "detalhes da nuvem", página 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. No painel **Ativar** :
    1. Insira a **chave de ativação** que você obteve em [obter a chave de ativação para o Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Você pode habilitar a coleta de logs proativo para permitir que a Microsoft colete logs com base no status de integridade do dispositivo. Os logs coletados dessa maneira são carregados em uma conta de armazenamento do Azure.
    
    1. Selecione **Aplicar**.

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

Além da coleção de log proativo, que coleta logs específicos referentes a um problema específico detectado, há outras coleções de log que podem fornecer uma compreensão muito mais profunda da integridade e do comportamento do sistema. Geralmente, essas outras coleções de log podem ser executadas durante uma solicitação de suporte ou disparadas pela Microsoft com base nos dados de telemetria que o dispositivo fornece.  

## <a name="handling-data"></a>Manipulando dados

Se um cliente habilitar a coleta de logs proativo, eles concordarão com a Microsoft coletando logs do dispositivo Azure Stack Edge, conforme descrito aqui. O cliente também reconhece e concede ao upload e à retenção desses logs em uma conta de armazenamento do Azure gerenciada e controlada pela Microsoft.

A Microsoft usa os dados para solucionar problemas de integridade do sistema e problemas apenas. Os dados não são usados para marketing, propaganda ou outras finalidades comerciais sem o consentimento do cliente. 

Os dados que a Microsoft coleta são tratados de acordo com nossas práticas de privacidade padrão. Se um cliente decidir revogar seu consentimento para a coleta de logs proativo, todos os dados coletados com consentimento antes da revogação não serão afetados.

## <a name="next-steps"></a>Próximas etapas

Saiba como [reunir um pacote de suporte](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).
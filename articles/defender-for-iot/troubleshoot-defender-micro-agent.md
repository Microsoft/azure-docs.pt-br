---
title: Solução de problemas do agente do defender IoT micro
titleSuffix: Azure Defender for IoT
description: Saiba como lidar com erros inesperados ou não explicados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: dade0d0d5dc4d690ea94f20deaf956b1e079bad7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124169"
---
# <a name="defender-iot-micro-agent-troubleshooting"></a>Solução de problemas do agente do defender IoT micro 

Caso você tenha erros inesperados ou não explicados, use os seguintes métodos de solução de problemas para tentar resolver seus problemas. Você também pode entrar em contato com a equipe de produto do Azure defender para IoT para obter assistência, conforme necessário.   

## <a name="service-status"></a>Status de serviço 

Para exibir o status do serviço: 

1. Executar o seguinte comando

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Verifique se o serviço está estável verificando se ele está `active` e se o tempo de atividade no processo é apropriado.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Verifique se o serviço está estável verificando se ele está ativo e se o tempo de atividade é apropriado.":::

Se o serviço estiver listado como `inactive` , use o seguinte comando para iniciar o serviço:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Você saberá que o serviço está falhando se o tempo de atividade do processo for muito curto. Para resolver esse problema, você deve examinar os logs.

## <a name="review-logs"></a>Examinar logs 

Use o comando a seguir para verificar se o serviço defender IoT micro Agent está sendo executado com privilégios de raiz.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verifique se o serviço defender para IoT micro Agent está sendo executado com privilégios de raiz.":::

Para exibir os logs, use o seguinte comando:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Para reiniciar o serviço, use o seguinte comando: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Próximas etapas

Confira o [suporte e](edge-security-module-deprecation.md)a desativação do recurso.

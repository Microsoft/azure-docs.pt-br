---
title: Autenticação do agente de segurança (versão prévia)
description: Execute a autenticação do micro Agent com dois métodos possíveis.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779232"
---
# <a name="micro-agent-authentication-methods-preview"></a>Métodos de autenticação do micro Agent (versão prévia)

Há duas opções de autenticação com o defender para IoT micro Agent: 

- Cadeia de conexão 

- Certificado 

## <a name="authentication-using-a-connection-string"></a>Autenticação usando uma cadeia de conexão 

Para usar uma cadeia de conexão, você precisa adicionar um arquivo que usa a cadeia de conexão codificada em UTF-8 no diretório do agente do defender em um arquivo chamado `connection_string.txt` . Por exemplo,

```azurecli
echo “<connection string>” > connection_string.txt 
```

Depois de fazer isso, você deve reiniciar o serviço usando esse comando.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Autenticação usando um certificado 


Para executar a autenticação usando um certificado: 

1. Coloque a parte pública codificada por PEM de um certificado no diretório do agente do defender, em um arquivo chamado `certificate_public.pem` .
1. Coloque a chave privada codificada por PEM no diretório do agente do defender, em um arquivo chamado `certificate_private.pem` .
1. Coloque a cadeia de conexão apropriada em um arquivo chamado `connection_string.txt` . Por exemplo,

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Essa ação indica que o agente do defender esperará que um certificado seja fornecido para autenticação. 

1. Reinicie o serviço usando o código a seguir: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Verifique se o micro Agent está sendo executado corretamente 

1. Execute o comando a seguir: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Verifique se o serviço está estável verificando se ele está **ativo** e se o tempo de atividade do processo é apropriado: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Verifique se o serviço está estável verificando se ele está ativo.":::

## <a name="next-steps"></a>Próximas etapas

Verifique sua [postura de segurança – benchmark de CIS](concept-security-posture.md).

---
title: Instalar o microagente do Defender para IoT (versão prévia)
titleSuffix: Azure Defender for IoT
description: Saiba como instalar e autenticar o microagente do Defender.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8d7e6dffcc40ba1e34a4a84ecccccc1f8b181393
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703041"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Instalar o microagente do Defender para IoT (versão prévia)

Este artigo fornece uma explicação de como instalar e autenticar o microagente do Defender.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o módulo do Defender para IoT, você precisará criar uma identidade do módulo no Hub IoT. Para obter mais informações sobre como criar uma identidade do módulo, confira [Criar um módulo gêmeo azureiotsecurity](quickstart-create-security-twin.md)

## <a name="install-the-package"></a>Instalar o pacote

Instale e configure o repositório de pacotes da Microsoft seguindo [estas instruções](/windows-server/administration/linux-package-repository-for-microsoft-software). 

Para o Debian 9, as instruções não incluem o repositório que precisa ser adicionado. Use os seguintes comandos para adicionar o repositório: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Para instalar o pacote do microagente do Defender no Debian e nas distribuições do Linux baseadas em Ubuntu, use o seguinte comando:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Métodos de autenticação do microagente 

As duas opções usadas para autenticar o microagente do Defender para IoT são: 

- Cadeia de conexão. 

- Certificado.

### <a name="authenticate-using-a-connection-string"></a>Autenticar usando uma cadeia de conexão

Para se autenticar usando uma cadeia de conexão:

1. Coloque um arquivo chamado `connection_string.txt` que contenha a cadeia de conexão codificada em UTF-8 no caminho `/var/defender_iot_micro_agent` do diretório do agente do Defender inserindo o seguinte comando:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    O `connection_string.txt` agora estará na localização do caminho `/var/defender_iot_micro_agent/connection_string.txt`.

1. Reinicie o serviço usando este comando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Autenticar-se usando um certificado

Para se autenticar usando um certificado:

1. Adquira um certificado seguindo [estas instruções](../iot-hub/iot-hub-security-x509-get-started.md).

1. Coloque a parte pública codificada por PEM do certificado e a chave privada no diretório do agente do Defender e nos arquivos `certificate_public.pem` e `certificate_private.pem`. 

1. Coloque a cadeia de conexão apropriada no arquivo `connection_string.txt`. a cadeia de conexão terá a seguinte aparência: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Essa cadeia de caracteres alerta o agente do Defender para esperar que um certificado seja fornecido para autenticação. 

1. Reinicie o serviço usando o seguinte comando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Validar a instalação

Para validar a instalação:

1. Verifique se o microagente está sendo executado corretamente com o seguinte comando:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Veja se o serviço está estável verificando se ele está `active` e se o tempo de atividade do processo é apropriado

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Verificar se o serviço está estável e ativo.":::
 
## <a name="testing-the-system-end-to-end"></a>Como testar o sistema de ponta a ponta 

Você pode testar o sistema de ponta a ponta criando um arquivo de gatilho no dispositivo. O arquivo de gatilho fará com que a verificação de linha de base no agente detecte o arquivo como uma violação de linha de base. 

Crie um arquivo no sistema de arquivos com o seguinte comando:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Uma recomendação de falha de validação de linha de base ocorrerá no hub, com uma `CceId` igual a CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="A recomendação de falha de validação de linha de base que ocorre no hub." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Aguarde até uma hora para que a recomendação seja exibida no hub. 

## <a name="micro-agent-versioning"></a>Controle de versão do microagente 

Para instalar uma versão específica do microagente do Defender para IoT, execute o seguinte comando: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Próximas etapas

[Como criar o microagente do Defender com base no código-fonte](quickstart-building-the-defender-micro-agent-from-source.md)
---
title: 'Guia de início rápido: Instalar o microagente do Defender para IoT (versão prévia)'
description: Neste guia de início rápido, saiba como instalar e autenticar o microagente do Defender.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: a153b640a1d1e86f9b761817d05fda7d3e47da98
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384401"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Guia de início rápido: Instalar o microagente do Defender para IoT (versão prévia)

Este artigo fornece uma explicação de como instalar e autenticar o microagente do Defender.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o módulo do Defender para IoT, você precisará criar uma identidade do módulo no Hub IoT. Para obter mais informações sobre como criar uma identidade do módulo, confira [Criar um módulo gêmeo do microagente do Defender para IoT (versão prévia)](quickstart-create-micro-agent-module-twin.md).

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

- Cadeia de conexão de identidade do módulo. 

- Certificado.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Autenticar-se usando uma cadeia de conexão de identidade do módulo

Verifique se os [pré-requisitos](#prerequisites) deste artigo foram atendidos e se você criou uma identidade do módulo antes de iniciar estas etapas. 

#### <a name="get-the-module-identity-connection-string"></a>Obter a cadeia de conexão de identidade do módulo

Para obter a cadeia de conexão de identidade do módulo do Hub IoT: 

1. Navegue até o Hub IoT e selecione o seu hub.

1. No menu à esquerda, na seção **Gerenciadores**, selecione **Dispositivos IoT**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Selecione os dispositivos IoT no menu à esquerda.":::

1. Selecione um dispositivo na lista de IDs do dispositivo para exibir a página de **Detalhes do dispositivo**.

1. Selecione a guia  **Identidades do módulo**  e, em seguida, selecione o módulo  **DefenderIotMicroAgent**  na lista de identidades do módulo associadas ao dispositivo.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Selecione as identidades do módulo na guia.":::

1. Na página **Detalhes da Identidade do Módulo**, copie a chave primária selecionando o botão **copiar**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Selecione o botão copiar para copiar a chave primária.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Configurar a autenticação usando uma cadeia de conexão de identidade do módulo

Para configurar o agente para autenticação usando uma cadeia de conexão de identidade do módulo:

1. Coloque um arquivo chamado `connection_string.txt` que contenha a cadeia de conexão codificada em UTF-8 no caminho `/var/defender_iot_micro_agent` do diretório do agente do Defender inserindo o seguinte comando:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    O `connection_string.txt` agora estará na localização de caminho `/var/defender_iot_micro_agent/connection_string.txt`.

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

> [!div class="nextstepaction"]
> [Como criar o microagente do Defender com base no código-fonte](quickstart-building-the-defender-micro-agent-from-source.md)

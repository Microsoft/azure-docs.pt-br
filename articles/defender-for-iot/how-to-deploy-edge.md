---
title: Implantar IoT Edge módulo de segurança
description: Saiba mais sobre como implantar um agente de segurança do defender para IoT no IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 9ac283721526488f587fcabc68348dafac1835db
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033346"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implantar um módulo de segurança em seu dispositivo IoT Edge

O módulo **do defender for IOT** fornece uma solução de segurança abrangente para seus dispositivos IOT Edge.
O módulo de segurança coleta, agrega e analisa dados brutos de segurança do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.
Para saber mais, consulte [módulo de segurança para IOT Edge](security-edge-architecture.md).

Neste artigo, você aprenderá a implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar o módulo de segurança

Use as etapas a seguir para implantar um módulo de segurança do defender para IoT para IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. No Hub IoT, verifique se o dispositivo está [registrado como um dispositivo IOT Edge](../iot-edge/how-to-manual-provision-symmetric-key.md#register-a-new-device).

1. O módulo do defender for IoT Edge requer que a [estrutura auditada](https://linux.die.net/man/8/auditd) esteja instalada no dispositivo IOT Edge.

    - Instale a estrutura executando o seguinte comando em seu dispositivo de IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Verifique se a auditoria está ativa executando o seguinte comando:

    `sudo systemctl status auditd`<br>
    - A resposta esperada é: `active (running)`

### <a name="deployment-using-azure-portal"></a>Implantação usando portal do Azure

1. No portal do Azure, abra o **Marketplace**.

1. Selecione **Internet das coisas**, em seguida, pesquise a **central de segurança do Azure para IOT** e selecione-a.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Selecionar defender para IoT":::

1. Selecione **criar** para configurar a implantação.

1. Escolha a **assinatura** do Azure do Hub IOT e selecione o **Hub IOT**.<br>Selecione **implantar em um dispositivo** para direcionar um único dispositivo ou selecione **implantar em escala** para vários dispositivos de destino e selecione **criar**. Para obter mais informações sobre a implantação em escala, consulte [como implantar](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Se você selecionou **implantar em escala**, adicione o nome do dispositivo e os detalhes antes de continuar para a guia **Adicionar módulos** nas instruções a seguir.

Conclua cada etapa para concluir a implantação do IoT Edge para o defender para IoT.

#### <a name="step-1-modules"></a>Etapa 1: módulos

1. Selecione o módulo **AzureSecurityCenterforIoT** .
1. Na guia **configurações do módulo** , altere o **nome** para **azureiotsecurity**.
1. Na guia **variáveis de ambiente** , adicione uma variável, se necessário (por exemplo, você pode adicionar o *nível de depuração* e defini-la como um dos seguintes valores: "fatal", "erro", "aviso" ou "informações").
1. Na guia **Opções de criação do contêiner** , adicione a seguinte configuração:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Na guia **configurações de configuração do módulo** , adicione a seguinte configuração:

   Propriedade de módulo "My":
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Conteúdo da propriedade bificada do módulo: 

   ```json
     {

     }
   ```
    
   Para obter mais informações sobre como configurar o agente, consulte [Configurar agentes de segurança](./how-to-agent-configuration.md).

1. Selecione **Atualização**.

#### <a name="step-2-runtime-settings"></a>Etapa 2: configurações de tempo de execução

1. Selecione **configurações de tempo de execução**.
2. Em **Hub do Edge**, altere a **imagem** para **MCR.Microsoft.com/azureiotedge-Hub:1.0.8.3**.

    >[!Note]
    > Atualmente, há suporte para a versão 1.0.8.3 ou anterior.

3. Verifique se a **opção criar opções** está definida com a seguinte configuração:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. Selecione **Salvar**.

5. Selecione **Avançar**.

#### <a name="step-3-specify-routes"></a>Etapa 3: especificar rotas

1. Na guia **especificar rotas** , verifique se você tem uma rota (explícita ou implícita) que encaminhará as mensagens do módulo **azureiotsecurity** para **$upstream** de acordo com os exemplos a seguir. Somente quando a rota estiver em vigor, selecione **Avançar**.

   Roteiros de exemplo:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selecione **Avançar**.

#### <a name="step-4-review-deployment"></a>Etapa 4: examinar a implantação

- Na guia **examinar implantação** , examine as informações de implantação e, em seguida, selecione **criar** para concluir a implantação.

## <a name="diagnostic-steps"></a>Etapas de diagnóstico

Se você encontrar um problema, os logs de contêiner serão a melhor maneira de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Use as ferramentas e os comandos desta seção para coletar informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os contêineres necessários estão instalados e funcionando conforme o esperado

1. Execute o seguinte comando em seu dispositivo IoT Edge:

    `sudo docker ps`

1. Verifique se os seguintes contêineres estão em execução:

   | Nome | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Se os contêineres mínimos necessários não estiverem presentes, verifique se o manifesto de implantação do IoT Edge está alinhado com as configurações recomendadas. Para obter mais informações, consulte [implantar IOT Edge Module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os logs de módulo para obter erros

1. Execute o seguinte comando em seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Para logs mais detalhados, adicione a seguinte variável de ambiente à implantação do módulo **azureiotsecurity** : `logLevel=Debug` .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, vá para o guia de instruções para configuração de módulo.
> [!div class="nextstepaction"]
> [Guia de instruções de configuração de módulo](./how-to-agent-configuration.md)
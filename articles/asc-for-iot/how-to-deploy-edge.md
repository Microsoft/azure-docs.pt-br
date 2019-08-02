---
title: Implantar a Central de segurança do Azure para o módulo do IoT Edge | Microsoft Docs
description: Saiba mais sobre como implantar uma central de segurança do Azure para o agente de segurança de IoT no IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 49ed4c6515f8fb63c3331b05e1bb29b13985e6b3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618333"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implantar um módulo de segurança em seu dispositivo IoT Edge

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Central de segurança do Azure (ASC) para IoT** módulo fornece uma solução abrangente de segurança para seu dispositivo IoT Edge.
Módulo de segurança coleta, agrega e analisa os dados brutos de segurança do seu sistema operacional e contêiner do sistema em alertas e recomendações de práticas de segurança.
Para obter mais informações, consulte [módulo de segurança do IoT Edge](security-edge-architecture.md).

Neste guia, você aprenderá como implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar o módulo de segurança

Use as etapas a seguir para implantar um ASC para o módulo de segurança de IoT para o IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

- Em seu IoT Hub, verifique se o dispositivo está [registrado como um dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Requer o ASC para o módulo do IoT Edge [AuditD framework](https://linux.die.net/man/8/auditd) está instalado no dispositivo do IoT Edge.

    - Instale o framework, executando o comando a seguir em seu dispositivo IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Verifique se que auditd está ativo, executando o seguinte comando:
   
      `sudo systemctl status auditd`
      
        A resposta esperada é `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Implantação usando o portal do Azure

1. No portal do Azure, abra **Marketplace**.

1. Selecione **Internet das coisas**, em seguida, procure **Central de segurança do Azure para IoT** e selecioná-lo.

   ![Selecione Central de segurança do Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique em **criar** para configurar a implantação. 

1. Escolha o Azure **assinatura** do IoT Hub, em seguida, selecione sua **IoT Hub**.<br>Selecione **implantar em um dispositivo** para um único dispositivo de destino ou selecione **implantar em escala** vários dispositivos de destino e, em seguida, clique em **criar**. Para obter mais informações sobre a implantação em grande escala, consulte [como implantar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se você selecionou **implantar em escala**, adicione o nome do dispositivo e os detalhes antes de continuar para o **Adicionar módulos** guia nas instruções a seguir.     

Há três etapas para criar uma implantação do IoT Edge para a Central de segurança do Azure para IoT. As seções a seguir explicam cada uma delas. 

#### <a name="step-1-add-modules"></a>Etapa 1: Adicionar módulos

1. Dos **Adicionar módulos** guia **módulos de implantação** área, clique em **AzureSecurityCenterforIoT**. 
   
1. Alterar o **nome** à **azureiotsecurity**.
1. Alterar o **URI de imagem** à **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Verifique se o **opções de criar contêiner** valor é definido como:      
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
1. Verifique **propriedades desejadas do gêmeo do módulo de conjunto** é selecionado e alterar o objeto de configuração para:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Clique em **Salvar**.
1. Role até a parte inferior da guia e selecione **definir configurações de tempo de execução do Edge avançadas**.
   
   >[!Note]
   > Fazer **não** desabilitar a comunicação de AMQP para o Hub do IoT Edge.
   > Central de segurança do Azure para o módulo do IoT requer comunicação AMQP com o Hub do IoT Edge.
   
1. Alterar o **imagem** sob **Hub de borda** para **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Central de segurança do Azure para o módulo do IoT requer uma versão bifurcada do Hub do IoT Edge, com base no SDK versão 1.20.
   > Alterando a imagem do Hub do IoT Edge, você está instruindo o dispositivo do IoT Edge para substituir a versão estável mais recente com a versão bifurcada do Hub do IoT Edge, que não é oficialmente suportado pelo serviço do IoT Edge.

1. Verifique se **criar opções** é definida como: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Clique em **Salvar**.
   
1. Clique em **Avançar**.

#### <a name="step-2-specify-routes"></a>Etapa 2: Especificar rotas 

1. No **especificar rotas** guia, defina as **ASCForIoTToIoTHub** rotear para **"de mensagens/módulos/azureiotsecurity/\* em $upstream"** e clique em  **Próxima**.

   ![Especificar Rotas](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Etapa 3: Examinar implantação

1. No **implantação revise** guia, examine as informações da implantação e selecione **enviar** para concluir a implantação.

## <a name="diagnostic-steps"></a>Etapas de diagnóstico

Se você encontrar um problema, os logs do contêiner são a melhor maneira de saber mais sobre o estado de um dispositivo de módulo de segurança do IoT Edge. Use as ferramentas e os comandos desta seção para coletar informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se que os contêineres necessários estão instalados e funcionando conforme o esperado

1. Execute o seguinte comando no seu dispositivo IoT Edge:
    
     `sudo docker ps`
   
1. Verifique se o contêiner a seguir estão em execução:
   
   | Nome | IMAGEM |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Se o mínimo necessário de contêineres não estiverem presentes, verifique se o manifesto de implantação do IoT Edge é alinhado com as configurações recomendadas. Para obter mais informações, consulte [módulo do IoT Edge implantar](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os logs de módulo para erros
   
1. Execute o seguinte comando no seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Para logs mais detalhados, adicione a seguinte variável de ambiente para **azureiotsecurity** implantação de módulo: `logLevel=Debug`.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre opções de configuração, continue para o guia de instruções para configuração do módulo. 
> [!div class="nextstepaction"]
> [Guia de instruções de configuração do módulo](./how-to-agent-configuration.md)

---
title: Guia de Início Rápido – Usar uma chave simétrica para provisionar um dispositivo no Hub IoT do Azure usando o Node.js
description: Neste início rápido, você usará o SDK de IoT do Azure para Node.js com o Serviço de Provisionamento de Dispositivos (DPS) para provisionar um dispositivo de chave simétrica para um hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429199"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>Início Rápido: Provisionar um dispositivo simétrico usando Node.js

Neste guia de início rápido, você aprenderá a provisionar um computador de desenvolvimento do Windows como um dispositivo em um hub IoT usando o Node.js. Esse dispositivo usará uma chave simétrica e uma inscrição individual para se autenticar em uma instância do DPS (Serviço de Provisionamento de Dispositivos) a fim de ser atribuído a um hub IoT. O código de exemplo do [SDK do Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node.git) será usado para provisionar o dispositivo. 

Embora este artigo demonstre o provisionamento com um registro individual, também é possível usar grupos de registros. Há algumas diferenças ao usar grupos de registro. Por exemplo, você precisa usar uma chave de dispositivo derivada com uma ID de registro exclusiva para o dispositivo. [Provisionar dispositivos com chaves simétricas](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de registro. Para obter mais informações sobre grupos de registro, consulte [Registros de grupo para atestado de chave simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process). 

Não se esqueça de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o Portal do Azure](./quick-setup-auto-provision.md) antes de continuar com este início rápido. Este início rápido requer que você já tenha criado a instância de Serviço de Provisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, é possível executar os procedimentos no Linux. Para um exemplo do Linux, confira [Provisionar para multilocação](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

- Familiaridade com os conceitos de [provisionamento](about-iot-dps.md#provisioning-process).
- Conclusão de [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0 e posterior](https://nodejs.org).
- [Git](https://git-scm.com/download/).


## <a name="create-a-device-enrollment"></a>Criar um registro do dispositivo

1. Entre no [portal do Azure](https://portal.azure.com), selecione o botão **Todos os recursos** no menu esquerdo e abra a instância do DPS (serviço de Provisionamento de Dispositivos).

2. Selecione a guia **Gerenciar registros** e selecione o botão **Adicionar registro individual** na parte superior. 

3. No painel **Adicionar Registro**, insira as informações a seguir e pressione o botão **Salvar**.

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* do atestado de identidade.

   - **Autogerar Chaves**: marque essa caixa.

   - **ID de registro**: insira uma ID de registro para identificar o registro. Use apenas caracteres alfanuméricos minúsculos e traço ('-'). Por exemplo, **symm-key-nodejs-device-01**.

   - **ID do dispositivo do IoT Hub:** insira um identificador de dispositivo. Por exemplo, **nodejs-device-01**.

     ![Adicionar registro individual para atestado de chave simétrica no portal](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. Após salvar o registro, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à entrada de registro. O registro do dispositivo de chave simétrica é exibido como **symm-key-nodejs-device-01** na coluna *ID de Registro* da guia *Registros Individuais*. 

5. Abra o registro e copie o valor da **Chave Primária** gerada. Você usará esse valor de chave e a **ID de Registro** mais tarde quando adicionar variáveis de ambiente para uso com o código de exemplo de provisionamento de dispositivos.



## <a name="prepare-the-nodejs-environment"></a>Preparar o ambiente Node.js 

1. Abra um ambiente de linha de comando Git CMD ou Git Bash. Clone o repositório GitHub [SDK do Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node.git) usando o seguinte comando:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Preparar o código de provisionamento de dispositivos

Nesta seção, você adicionará as quatro variáveis de ambiente a seguir que serão usadas como parâmetros para o código de exemplo de provisionamento de dispositivos para o dispositivo de chave simétrica. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

O código de provisionamento entrará em contato com a instância do DPS com base nessas variáveis para autenticar o dispositivo. O dispositivo será então atribuído a um hub IoT já vinculado à instância do DPS com base na configuração de registro individual. Depois de provisionado, o código de exemplo enviará uma telemetria de teste para o hub IoT.

1. No [portal do Azure](https://portal.azure.com), no menu do Serviço de Provisionamento de Dispositivos, selecione **Visão geral** e copie o _Ponto de Extremidade de Serviço_ e o _Escopo da ID_. Você usará esses valores para as variáveis de ambiente `PROVISIONING_HOST` e `PROVISIONING_IDSCOPE`.

    ![Informações de serviço](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Abra um prompt de comando para executar comandos do node.js e navegue até o seguinte diretório *provisionamento/dispositivo/exemplos*.

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. Na pasta *provisionamento/dispositivo/exemplos*, abra *register_symkey.js* e examine o código. 

    Observe que o código de exemplo define uma carga personalizada...

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Esse código não é necessário com este início rápido. Esse código é um exemplo de configuração de uma carga personalizada se você quisesse usar uma função de alocação personalizada para atribuir seu dispositivo a um Hub IoT. Para saber mais, confira [Tutorial: Usar políticas de alocação personalizadas](tutorial-custom-allocation-policies.md).

    O método `provisioningClient.register()` tenta o registro do seu dispositivo.

    Nenhuma alteração será necessária para o código de exemplo para registrar seu dispositivo.

4. No prompt de comando, adicione as variáveis de ambiente ao host de provisionamento, ao escopo da ID, à ID de registro e à chave simétrica primária copiadas do registro individual na seção anterior.  

    Os comandos a seguir são exemplos que mostram a sintaxe do comando. Lembre-se de usar os valores corretos.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. Compile e execute o código de exemplo usando os comandos a seguir.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. A saída esperada deverá ser semelhante à mostrada a seguir, que indica o hub IoT vinculado ao qual o dispositivo foi atribuído com base nas configurações de registro individuais. Uma cadeia de caracteres "Olá, Mundo" é enviada ao Hub como uma mensagem de teste:

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. No portal do Azure, procure o hub IoT vinculado ao serviço de provisionamento e abra a folha **Dispositivos IoT**. Após o provisionamento bem-sucedido do dispositivo de chave simétrica no hub, a identificação do dispositivo é mostrada com o *STATUS* como **habilitado**. Talvez você precise selecionar o botão **Atualizar** na parte superior se já abriu a folha antes de executar o código de exemplo do dispositivo. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> Se você tiver alterado o *estado de dispositivo gêmeo inicial* do valor padrão na entrada de registro para o seu dispositivo, pode receber o estado desejado duas do hub e agir de acordo. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste início rápido. Caso contrário, use as seguintes etapas para excluir todos os recursos criados por este início rápido.

1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra **Gerenciar Registros** de seu serviço e selecione a guia **Registros Individuais**. Marque a caixa de seleção ao lado da *ID DE REGISTRO* do dispositivo registrado neste início rápido e pressione o botão **Excluir**, na parte superior do painel. 
1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Abra **Dispositivos IoT** do hub, marque a caixa de seleção ao lado da *ID DO DISPOSITIVO* registrado neste início rápido e pressione o botão **Excluir** na parte superior do painel.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você provisionou um dispositivo de chave simétrica baseado no Windows no hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Para saber como provisionar dispositivos de certificado X.509 usando o Node.js, prossiga para o guia de início rápido abaixo para os dispositivos X.509. 

> [!div class="nextstepaction"]
> [Guia de início rápido do Azure – Provisionar dispositivos X.509 usando o DPS e o Node.js](quick-create-simulated-device-x509-node.md)

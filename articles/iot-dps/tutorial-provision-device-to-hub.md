---
title: Tutorial – Provisionar um dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
description: Este tutorial mostra como provisionar seu dispositivo para um único hub IoT usando o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 876fd8260b64fba4d3d34a766b4259323c660b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968070"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Tutorial: Provisionar o dispositivo para um Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, você aprendeu como configurar um dispositivo para se conectar ao seu Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá a usar este serviço para provisionar seu dispositivo para um único Hub IoT usando **_listas de registro_** e provisionamento automático. Este tutorial mostra como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, não deixe de configurar seu dispositivo conforme discutido no tutorial [Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](./tutorial-set-up-device.md).

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process) antes de continuar.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrar o dispositivo

Esta etapa envolve adicionar os artefatos de segurança exclusivos do dispositivo ao Serviço de Provisionamento de Dispositivos. Esses artefatos de segurança se baseiam no [mecanismo de atestado](concepts-service.md#attestation-mechanism) do dispositivo, da seguinte maneira:

- Para dispositivos baseados no TPM, você precisará do seguinte:
    - A *Chave de Endosso* que é exclusiva para cada chip ou simulação do TPM, obtida do fabricante do chip do TPM.  Leia [Compreender a chave de endosso do TPM](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) para obter mais informações.
    - A *ID do registro* é usada para identificar exclusivamente um dispositivo no namespace/escopo. Essa ID pode ou não ser a mesmo ID do dispositivo. A ID é obrigatória para cada dispositivo. Para dispositivos baseados em TPM, a ID do registro pode ser derivada do TPM em si, por exemplo, um hash SHA-256 da chave de endosso do TPM.

      [![Informações de registro do TPM no portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Para dispositivos baseados em X.509, você precisará do seguinte:
    - O [certificado emitido para o chip ou simulação X.509](/windows/win32/seccertenroll/about-x-509-public-key-certificates), na forma de um arquivo *.pem* ou um *.cer*. Para registro individual, é necessário usar o *certificado assinado* por dispositivo para o sistema X.509, enquanto que para grupos de registro, é necessário usar o *certificado raiz*. 

      [![Adicionar um registro individual para atestado de X.509 no portal](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Há duas maneiras de registrar o dispositivo no Serviço de Provisionamento de Dispositivos:

- **Grupos de registro** Isso representa um grupo de dispositivos que compartilham um mecanismo de atestado específico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário. Para obter mais informações sobre o atestado de identidade para grupos de registro, consulte [Segurança](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Adicionar um registro de grupo para atestado de X.509 no portal](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Registros individuais** isso representa uma entrada para um único dispositivo que pode ser registrado no Serviço de Provisionamento de Dispositivos. Registros individuais podem usar tokens de certificados x509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar inscrições individuais para dispositivos que exigem configurações iniciais exclusivas e para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual, como o mecanismo de atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

Agora registre o dispositivo com sua instância do Serviço de Provisionamento de Dispositivos usando os artefatos de segurança necessários com base no mecanismo de atestado do dispositivo: 

1. Entre no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** ou a guia **Grupos de Registro**, de acordo com a configuração do dispositivo. Clique no botão **Adicionar** na parte superior. Selecione **TPM** ou **X.509** como o *mecanismo* de atestado de identidade e insira os artefatos de segurança apropriados, conforme discutido anteriormente. Você pode inserir uma nova **ID de dispositivo do Hub IoT**. Uma vez concluído, clique no botão **Salvar**. 

3. Quando o dispositivo for registrado com êxito, você o verá exibido no portal conforme demonstrado abaixo:

    ![Registro de TPM bem-sucedido no portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Após o registro, o serviço de provisionamento fica aguardando que um desses dispositivos inicialize e se conecte com ele em algum momento. Quando o dispositivo é inicializado pela primeira vez, a biblioteca do SDK do cliente interage com o chip para extrair os artefatos de segurança do dispositivo e verifica o registro com o Serviço de Provisionamento de Dispositivos. 

## <a name="start-the-iot-device"></a>Iniciar o dispositivo IoT

Seu dispositivo IoT pode ser um dispositivo real ou um dispositivo simulado. Após o dispositivo IoT ter sido registrado com uma instância do serviço de provisionamento de dispositivos, o dispositivo agora pode inicializar e chamar o serviço de provisionamento para ser reconhecido usando o mecanismo de atestado. Depois que o serviço de provisionamento reconhecer o dispositivo, ele será atribuído a um Hub IoT. 

Exemplos de dispositivo simulado, usando o atestado de TPM e X.509, estão incluídos para C, Java, C#, Node.js e Python. Por exemplo, um dispositivo simulado usando TPM e o [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) seguiria o processo abordado na seção [Simular a primeira sequência de inicialização para um dispositivo](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device). O mesmo dispositivo usando o atestado de certificado X.509 seria referente a esta seção de [sequência de inicialização](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device).

Consulte o [Guia de instruções para o MXChip IoT DevKit](how-to-connect-mxchip-iot-devkit.md) como um exemplo para um dispositivo real.

Inicie o dispositivo para permitir que o aplicativo cliente do seu dispositivo inicie o registro com o Serviço de Provisionamento de Dispositivos.  

## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registrado

Quando o dispositivo inicializar, as ações a seguir deverão ocorrer:

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
2. Para dispositivos do TPM, o Serviço de Provisionamento de Dispositivos envia de volta um desafio de registro que é respondido pelo dispositivo. 
3. Após um registro bem-sucedido, o Serviço de Provisionamento de Dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo. 
4. O aplicativo cliente do Hub IoT no dispositivo se conecta então ao seu hub. 
5. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no gerenciador de **Dispositivos IoT** do Hub IoT. 

    ![Conexão bem-sucedida ao hub no portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Para obter mais informações, confira o exemplo de cliente do dispositivo de provisionamento, [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). O exemplo demonstra o provisionamento de um dispositivo simulado usando TPM, certificados X.509 e chaves simétricas. Confira os inícios rápidos do [TPM](./quick-create-simulated-device.md), do [X.509](./quick-create-simulated-device-x509.md) e da [chave simétrica](./quick-create-simulated-device-symm-key.md) para obter instruções passo a passo sobre como usar o exemplo.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
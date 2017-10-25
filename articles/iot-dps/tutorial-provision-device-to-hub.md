---
title: "Provisionar um dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs"
description: "Provisionar o dispositivo para um único Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Provisionar o dispositivo para um Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, você aprendeu como configurar um dispositivo para se conectar ao seu Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá a usar este serviço para provisionar seu dispositivo para um único Hub IoT usando **_listas de registro_**. Este tutorial mostra como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de configurar seu dispositivo e o respectivo *Hardware Security Module*, conforme discutido no tutorial [Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Azure Hub IoT](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrar o dispositivo

Esta etapa envolve adicionar os artefatos de segurança exclusivos do dispositivo ao Serviço de Provisionamento de Dispositivos. Esses artefatos de segurança são os seguintes:

- Para dispositivos baseados em TPM:
    - A *chave de endosso* que é exclusiva para cada simulação ou chip do TPM. Leia [Compreender a chave de endosso do TPM](https://technet.microsoft.com/library/cc770443.aspx) para obter mais informações.
    - A *ID do registro* é usada para identificar exclusivamente um dispositivo no namespace/escopo. Isso pode ou não ser o mesmo que a ID do dispositivo. A ID é obrigatória para cada dispositivo. Para dispositivos baseados em TPM, a ID do registro pode ser derivada do TPM em si, por exemplo, um hash SHA-256 da chave de endosso do TPM.

    ![Informações de registro do TPM no portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- Para dispositivos baseados em X.509:
    - O [certificado emitido para o chip ou simulação X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), na forma de um arquivo *.pem* ou um *.cer*. Para o registro individual, você precisa usar o *certificado assinante* para seu sistema X.509, enquanto para grupos de registro, você precisa usar o *certificado raiz*.

    ![Informações de registro de X.509 no portal](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Há duas maneiras de registrar o dispositivo no Serviço de Provisionamento de Dispositivos:

- **Grupos de registro** Isso representa um grupo de dispositivos que compartilham um mecanismo de atestado específico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário.

    ![Grupos de registro de X.509 no portal](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Registros individuais** isso representa uma entrada para um único dispositivo que pode ser registrado no Serviço de Provisionamento de Dispositivos. Registros individuais podem usar tokens de certificados x509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual como o mecanismo de Atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

A seguir estão as etapas para registrar o dispositivo no portal:

1. Observe os artefatos de segurança para o HSM no seu dispositivo. Talvez seja necessário usar as APIs mencionadas na seção intitulada [Extrair os artefatos de segurança](./tutorial-set-up-device.md#extractsecurity) do tutorial anterior em um ambiente de desenvolvimento.

1. Faça logon no Portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o Serviço de Provisionamento de Dispositivos.

1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** ou a guia **Grupos de Registro**, de acordo com a configuração do dispositivo. Clique no botão **Adicionar** na parte superior. Selecione **TPM** ou **X.509** como o *mecanismo* de atestado de identidade e insira os artefatos de segurança apropriados, conforme discutido anteriormente. Você pode inserir uma nova **ID de dispositivo do Hub IoT**. Uma vez concluído, clique no botão **Salvar**. 

1. Quando o dispositivo for registrado com êxito, você o verá exibido no portal conforme demonstrado a seguir:

    ![Registro de TPM bem-sucedido no portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Iniciar o dispositivo

Neste ponto, a configuração a seguir está pronta para registro do dispositivo:

1. Seu dispositivo ou grupo de dispositivos são registrados no Serviço de Provisionamento de Dispositivos e 
2. O dispositivo está pronto com o chip do HSM configurado e acessível por meio do aplicativo usando o SDK do cliente do Serviço de Provisionamento de Dispositivos.

Inicie o dispositivo para permitir que seu aplicativo cliente inicie o registro com o Serviço de Provisionamento de Dispositivos.  


## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registrado

Uma vez que o dispositivo é inicializado, as ações a seguir devem ocorrer. Consulte o aplicativo de exemplo do simulador de TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) para obter mais detalhes. 

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
2. Para dispositivos do TPM, o Serviço de Provisionamento de Dispositivos envia de volta um desafio de registro que é respondido pelo dispositivo. 
3. Após um registro bem-sucedido, o Serviço de Provisionamento de Dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo. 
4. O aplicativo cliente do Hub IoT no dispositivo se conecta então ao seu hub. 
5. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no **Device Explorer** do Hub IoT. 

    ![Conexão bem-sucedida ao hub no portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)

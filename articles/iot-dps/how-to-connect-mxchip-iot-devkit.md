---
title: Como usar o provisionamento automático do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT | Microsoft Docs
description: Como usar o provisionamento automático do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: c809077b0ec14240d231e9b281285080f68596b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439498"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Use o provisionamento automático do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT

Este artigo descreve como usar o [provisionamento automático](concepts-auto-provisioning.md) do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT do Azure. Neste tutorial, você aprenderá como:

* Configure o terminal global do serviço de provisionamento de dispositivos em um dispositivo.
* Use UDS (Unique Device Secret) para gerar um certificado X.509.
* Inscreva um dispositivo individual.
* Verifique se o dispositivo está registrado.

O [ Kit de Desenvolvimento da IoT para MXChip](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando o [Workbench do dispositivo de IoT do Azure](https://aka.ms/iot-workbench) ou pacote de extensão com as ferramentas do [ Azure IoT](https://aka.ms/azure-iot-tools) no Visual Studio Code. O Kit de Desenvolvimento vem com um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções da IoT (Internet das Coisas) que aproveitam os serviços do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste tutorial, faça primeiro as tarefas a seguir:

* Configurar o Wi-Fi do seu kit de desenvolvimento e prepare seu ambiente de desenvolvimento seguindo a seção "Preparar o ambiente de desenvolvimento" etapas em [conectar IoT DevKit AZ3166 ao Hub do IoT do Azure na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Atualize para o firmware mais recente (1.3.0 ou posterior) com o tutorial [Atualização do firmware do Kit de Desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Crie e vincule um Hub IoT a uma instância do serviço de provisionamento de dispositivo seguindo as etapas em [Configurar o serviço de provisionamento de dispositivo do Hub IoT com o portal do Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Abra um projeto de exemplo

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Abrir Exemplos...** . Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página de exemplos do Workbench, encontre **Registro de Dispositivo com DPS** em clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.
    ![Abrir exemplo](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Salve o Unique Device Secret no armazenamento de segurança do dispositivo

O provisionamento automático pode ser configurado em um dispositivo baseado no [mecanismo de atestado](concepts-security.md#attestation-mechanism) do dispositivo. O Kit de Desenvolvimento da IoT para MXChip usa [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) do [ TCG (Trusted Computing Group)](https://trustedcomputinggroup.org). Um **Unique Device Secret** (UDS) salvo no circuito integrado de segurança ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) no DevKit é utilizado para gerar o [certificado X.509](concepts-security.md#x509-certificates) exclusivo do dispositivo. O certificado é usado posteriormente para o processo de inscrição no serviço de provisionamento de dispositivos e durante o registro em tempo de execução.

Um UDS típico é uma cadeia de 64 caracteres como mostrado no exemplo a seguir:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Salvar uma UDS no Kit de desenvolvimento:

1. No VS Code, clique na barra de status para selecionar a porta COM para o Kit de desenvolvimento.
  ![Selecionar uma porta COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. No kit de desenvolvimento, mantenha pressionado o **botão A**, pressione e solte o botão **redefinir** e, em seguida, solte o **botão A**. Seu kit de desenvolvimento entra no modo de configuração.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Definir as configurações do dispositivo... > Definir a Cadeia de Unique Device String (UDS)** .
  ![Configurar UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Anote a cadeia de caracteres do UDS gerado. Você precisará dele para gerar o certificado X.509. Em seguida, pressione `Enter`.
  ![Copiar UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Confirme da notificação que o UDS foi configurado no STSAFE com êxito.
  ![Configurar o sucesso UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Como alternativa, você pode configurar UDS via porta serial usando utilitários como o Putty. Siga [Usar o modo de configuração](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para fazer isso.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Atualizar o ponto de extremidade do dispositivo Global e o escopo da ID

No código de dispositivo, você precisará especificar o [ponto de extremidade de provisionamento de dispositivos](/azure/iot-dps/concepts-service#device-provisioning-endpoint) e escopo da ID para garantir o isolamento de locatários.

1. No portal do Azure, selecione a **visão geral** painel de seu serviço de provisionamento de dispositivos e anote o **ponto de extremidade do dispositivo Global** e **escopo da ID** valores.
  ![Endpoint global e escopo de ID do serviço de provisionamento de dispositivo](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Abra **DeKitDPS.ino**. Localize e substitua `[Global Device Endpoint]` e `[ID Scope]` pelos valores anotados.
  ![Ponto de extremidade de serviço de provisionamento do dispositivo](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Preencha a `registrationId` variável no código. Somente alfanuméricas, minúsculas, e é permitida a combinação de hífen com um máximo de 128 caracteres. Também anotou o valor.
  ![ID de registro](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Clique em `F1`, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Carregar o código de dispositivo**. Inicia a compilação e carregamento do código para o seu kit de desenvolvimento.
  ![Upload de dispositivo](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Gerar certificado X.509

O [mecanismo de atestado](/azure/iot-dps/concepts-device#attestation-mechanism) usado por este exemplo é o certificado X.509. Você precisa usar um utilitário para gerá-lo.

> [!NOTE]
> O gerador de certificado x. 509 só dá suporte ao Windows agora.

1. No VS Code, clique em `F1`, digite e selecione **abrir Terminal novo** para abrir a janela de terminal.

1. Execute `dps_cert_gen.exe` na `tool` pasta.

1. Especifique o local do arquivo binário compilado como `..\.build\DevKitDPS`. Em seguida, cole o **UDS** e **registrationId** que foram anotados. 
  ![Gerar X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Um `.pem` certificado X. 509 gera na mesma pasta.
  ![Arquivo X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de registro de dispositivo

1. No portal do Azure, abra seu serviço de provisionamento de dispositivos, Gerenciar seção de registros e clique em **Adicionar registro individual**.
  ![Adicionar registro individual](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Clique no ícone do arquivo próximo a **Arquivo primário certificado .pem ou .cer** para carregar o `.pem` arquivo gerado.
  ![Carregar .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Verifique se o Kit de Desenvolvimento está registrado com o Hub IoT do Azure

Pressione o botão de **Reinicialização** no seu DevKit. Você deve ver o **DPS conectado!** na tela do kit d desenvolvimento. Quando o dispositivo reinicializar, as ações a seguir ocorrerão:

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
1. O serviço de provisionamento de dispositivos envia de volta um desafio de registro ao qual seu dispositivo responde.
1. No registro bem-sucedido, o serviço de provisionamento de dispositivos envia o URI do Hub da Internet das coisas, o ID do dispositivo e a chave criptografada de volta ao dispositivo.
1. O aplicativo cliente do Hub IoT no dispositivo se conecta ao seu hub.
1. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no Device Explorer do Hub IoT.
  ![Dispositivo registrado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte as [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) do Kit de Desenvolvimento ou entre em contato com conosco pelos canais abaixo para obter suporte:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a registrar um dispositivo com segurança no Serviço de Aprovisionamento de Dispositivos usando o Mecanismo de Composição de Identidade do Dispositivo, para que o dispositivo possa se registrar automaticamente no Azure Hub do IoT. 

Em resumo, você aprendeu a:

> [!div class="checklist"]
> * Configure o terminal global do serviço de provisionamento de dispositivos em um dispositivo.
> * Usar um unique device secret para gerar um certificado X.509.
> * Inscreva um dispositivo individual.
> * Verifique se o dispositivo está registrado.

Saiba como [Criar e provisionar um dispositivo simulado](./quick-create-simulated-device.md).


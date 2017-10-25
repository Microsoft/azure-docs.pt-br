---
title: "Dispositivo SensorTag e Gateway do IoT do Azure – Lição 1: configurar NUC da Intel | Microsoft Docs"
description: "Configure um NUC da Intel para funcionar como um gateway IoT entre um sensor e o Hub IoT do Azure a fim de coletar informações do sensor e enviá-las para o Hub IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gateway iot, intel nuc, computador nuc, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurar um NUC da Intel como um gateway IoT
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>O que você fará

- Configure um NUC da Intel como um gateway IoT.
- Instale o pacote do Azure IoT Edge na NUC da Intel.
- Execute um aplicativo de exemplo "hello_world" no NUC da Intel para verificar a funcionalidade do gateway.

  > Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá:

- Como conectar um NUC da Intel com periféricos.
- Como instalar e atualizar os pacotes necessários no NUC da Intel usando o Smart Package Manager.
- Como executar o aplicativo de exemplo "hello_world" para verificar a funcionalidade do gateway.

## <a name="what-you-need"></a>O que você precisa

- Um Kit DE3815TYKE do NUC da Intel com o Pacote de Software do Gateway IoT da Intel (Wind River Linux *7.0.0.13) pré-instalado. [Clique aqui para adquirir Kit de Gateway Comercial Grove IoT](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Um cabo Ethernet.
- Um teclado.
- Um cabo HDMI ou VGA.
- Um monitor com uma porta HDMI ou VGA.
- Opcional: [Marcação de Sensor Texas Instruments (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Kit de Gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Conectar um NUC da Intel com os periféricos

A imagem abaixo é um exemplo de NUC da Intel conectado a vários periféricos:

1. Conectado a um teclado.
2. Conectado a um monitor por um cabo VGA ou HDMI.
3. Conectado a uma rede com fios por um cabo Ethernet.
4. Conectado a uma fonte de alimentação por um cabo de alimentação.

![NUC da Intel conectado a periféricos](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Conectar-se ao sistema do NUC da Intel do computador host via SSH (Secure Shell)

Você precisará de um teclado e um monitor para obter o endereço IP do dispositivo NUC da Intel. Se você já sabe o endereço IP, pode pular para a etapa 3 nesta seção.

1. Ative o NUC da Intel pressionando o botão de energia e então faça logon.

   O nome de usuário e a senha padrão são ambos `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Obtenha o endereço IP do NUC da Intel executando o comando `ifconfig` nesse dispositivo.

   Aqui está um exemplo do resultado do comando.

   ![Saída ifconfig mostrando o IP do NUC da Intel](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Neste exemplo, o valor após `inet addr:` é o endereço IP necessário para se conectar de um computador host ao NUC da Intel.

3. Use um dos clientes SSH do seu computador host a seguir para se conectar ao NUC da Intel.

    - [PuTTY](http://www.putty.org/) para Windows.
    - O cliente SSH interno no Ubuntu ou macOS.

   É mais eficiente e produtivo operar um NUC da Intel desde um computador host. Você precisará do endereço IP, nome de usuário e senha do NUC para se conectar a ele via um cliente SSH. Aqui está um exemplo que usa um cliente SSH no macOS.
   ![Cliente SSH em execução no macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Instalar o pacote do Azure IoT Edge

O pacote do Azure IoT Edge contém os binários pré-compilados do IoT Edge e suas dependências. Esses binários são o Azure IoT Edge, o SDK de IoT do Azure e as ferramentas correspondentes. O pacote também contém um aplicativo de exemplo "hello_world" usado para validar a funcionalidade do gateway. O IoT Edge é a parte principal do gateway. 

Para instalar o pacote, siga estas etapas.

1. Adicione o repositório de Nuvem IoT executando os seguintes comandos em uma janela de terminal:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Insira “y” quando ele solicitar “Incluir este canal”?
   
   Se você receber um erro `import read failed(-1)`, use os comandos a seguir para resolver o problema:
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   O comando `rpm` importa a chave de rpm. O comando `smart channel` adiciona o canal de rpm ao Smart Package Manager. Antes de executar o comando `smart update`, você verá uma saída semelhante à mostrada abaixo.

   ![saída de comandos de canal inteligente e rpm](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Execute o comando de atualização inteligente:

   ```bash
   smart update
   ```

3. Instale o pacote de Gateway IoT do Azure executando o seguinte comando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` é o nome do pacote. O comando `smart install` é usado para instalar o pacote.

    > Execute o comando a seguir se você vir este erro: 'chave pública não disponível'

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Reinicialize o NUC da Intel se você receber este erro: “Nenhum pacote fornece o util-linux-dev”

   Depois que o pacote é instalado, o NUC da Intel está pronto para funcionar como um gateway.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Executar o aplicativo de exemplo “hello_world” do Azure IoT Edge

O aplicativo de exemplo a seguir cria um gateway com base em um arquivo `hello_world.json` e usa os componentes fundamentais da arquitetura do Azure IoT Edge para registrar uma mensagem “olá, mundo” em um arquivo (log.txt) a cada cinco segundos.

Você pode executar o exemplo Hello World executando os seguintes comandos:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Deixe que o aplicativo Hello World seja executado por alguns minutos e, em seguida, pressione a tecla Enter para interrompê-lo.
![saída do aplicativo](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Você pode ignorar quaisquer erros de 'identificador de argumento inválido(NULL)' que apareçam depois que você pressionar Enter.

Você pode verificar se o gateway foi executado com êxito abrindo o arquivo log.txt que agora está na pasta hello_world: ![exibição de diretório de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Abra o log.txt usando o seguinte comando:

```bash
vim log.txt
```

Você verá então o conteúdo de log.txt, que será uma saída em formato JSON das mensagens de log que foram gravadas a cada cinco segundos pelo módulo de Hello World do gateway.
![exibição de diretório de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Resumo

Parabéns! Você terminou de configurar o NUC da Intel como um gateway. Agora você está pronto para passar para a próxima lição para configurar o computador host, criar um Hub IoT do Azure e registrar o dispositivo lógico do Hub IoT do Azure.

## <a name="next-steps"></a>Próximas etapas
[Usar um gateway IoT para conectar um dispositivo ao Hub IoT do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)


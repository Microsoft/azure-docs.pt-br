---
title: Provisionar automaticamente os dispositivos do Linux com o DPS - Azure IoT Edge | Microsoft Docs
description: Use um TPM simulado em uma VM Linux para testar o Serviço de Provisionamento de Dispositivos do Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 03/01/2019
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a549221a9e1864e1b7565f35139cb4c2a6ca65e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248106"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Criar e provisionar um dispositivo IoT Edge com um TPM virtual em uma máquina virtual Linux

Dispositivos do IoT Edge do Azure podem ser provisionados automaticamente usando o [serviço de provisionamento de dispositivo](../iot-dps/index.yml). Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar. 

Este artigo mostra como testar o provisionamento automático em um dispositivo simulado do IoT Edge com as seguintes etapas: 

* Crie uma máquina virtual Linux (VM) no Hyper-V com Trusted Platform Module (TPM) para segurança de hardware.
* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um registro individual para o dispositivo
* Instale o tempo de execução do IoT Edge e conecte o dispositivo ao Hub IoT

As etapas neste artigo destinam-se para fins de teste.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento do Windows com [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo usa o Windows 10 em execução de uma VM do Ubuntu Server. 
* Um Hub IoT ativo. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crie máquinas virtuais Linux com um TPM virtual

Nesta seção, você pode criar uma nova máquina virtual Linux no Hyper-V. Você configurou essa máquina virtual com um TPM simulado para que você pode usá-lo para testar como automático provisionamento funciona com o IoT Edge. 

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual se conecte a uma rede física.

1. Abra o Gerenciador do Hyper-V em seu computador Windows. 

2. No menu **Ações**, selecione **Gerenciador de Comutador Virtual**. 

3. Escolha um comutador virtual **Externo** e, em seguida, selecione **Criar Comutador Virtual**. 

4. Nomeie seu novo comutador virtual, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de conexão é definido como **rede externa**, em seguida, selecione **Ok**.

5. Um pop-up avisa que a conectividade de rede pode ser interrompida. Escolha **Sim** para continuar. 

Se você vir erros ao criar o novo comutador virtual, verifique se nenhum outro comutador está usando o adaptador Ethernet e se nenhum outro comutador usa o mesmo nome. 

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. Baixe um arquivo de imagem de disco para usar para sua máquina virtual e salve-o localmente. Por exemplo: [servidor Ubuntu](https://www.ubuntu.com/download/server). 

2. No Gerenciador do Hyper-V novamente, selecione **New** > **Máquina Virtual** no **ações** menu.

3. Conclua o **Assistente de nova máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar Geração**: Selecione **Geração 2**. Máquinas virtuais de geração 2 têm virtualização aninhada habilitada, que é necessário para execução do IoT Edge em uma máquina virtual.
   2. **Configurara Rede**: Configure o valor de **Conexão** para o comutador virtual que você criou na seção anterior. 
   3. **Opções de instalação**: Selecione **Instalar um sistema operacional a partir de um arquivo de imagem inicializável** e navegue até o arquivo de imagem do disco que você salvou localmente.

4. Selecione **concluir** no Assistente para criar a máquina virtual.

A criação da nova VM pode levar alguns minutos. 

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Depois de criar sua VM, abra suas configurações para habilitar o virtual trusted platform module (TPM) que permite que você autoprovision o dispositivo. 

1. Selecione a máquina virtual e, em seguida, abra sua **configurações**.

2. Navegar para **Segurança**. 

3. Desmarque a opção **habilitar inicialização segura**.

4. Verificar **Habilitar Trusted Platform Module**. 

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e coletar dados TPM

Na máquina virtual, criar uma ferramenta de SDK de C que você pode usar para recuperar o dispositivo **ID do registro** e **chave de endosso**. 

1. Inicie a máquina virtual e conectá-lo.

2. Siga os prompts dentro da máquina virtual para concluir o processo de instalação e reinicializar o computador. 

3. Entrar em sua VM e, em seguida, siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) instalar e criar o SDK do dispositivo do IoT do Azure para C. 

   >[!TIP]
   >No decorrer deste artigo, você copiar e colar da máquina virtual, que não é fácil por meio do aplicativo de conexão do Gerenciador do Hyper-V. Você talvez queira se conectar à máquina virtual por meio do Gerenciador do Hyper-V uma vez para recuperar seu endereço IP: `ifconfig`. Em seguida, você pode usar o endereço IP para se conectar por meio do SSH: `ssh <username>@<ipaddress>`.

4. Execute os seguintes comandos para criar uma ferramenta do SDK de C que recupera as informações de provisionamento do dispositivo. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

5. Copie os valores para **ID do registro** e **chave de endosso**. Você pode usar esses valores para criar um registro individual para seu dispositivo no DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o tempo de execução do Azure IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Recuperar as informações de provisionamento da sua máquina virtual e usá-la para criar um registro individual no serviço de provisionamento do dispositivo. 

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md). 


1. No [portal do Azure](https://portal.azure.com), e navegue até sua instância do serviço de provisionamento de dispositivo do IoT Hub. 

2. Em **Configurações**, selecione **Gerenciar registros**. 

3. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Em **Mecanismo**, selecione **TPM**. 
   
   2. Forneça a **chave de endosso** e **ID de registro** que você copiou da sua máquina virtual.
   
   3. Selecione **verdadeira** para declarar que essa máquina virtual é um dispositivo IoT Edge. 
   
   4. Escolha o **IoT Hub** vinculado que você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a uma delas acordo com a política de alocação selecionado. 
   
   5. Forneça uma ID para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer uma ID de dispositivo, a ID do registro é usada.
   
   6. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Por exemplo:  

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Clique em **Salvar**. 

Agora que existe um registro para este dispositivo, o tempo de execução do IoT Edge pode provisionar o dispositivo automaticamente durante a instalação. 

## <a name="install-the-iot-edge-runtime"></a>Instalar o tempo de execução do Azure IoT Edge

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o tempo de execução do IoT Edge em sua máquina virtual. 

Saiba seu DPS **Escopo da ID** e do dispositivo **ID de registro** antes de começar o artigo que combine com o seu tipo de dispositivo. Se você instalou o servidor do Ubuntu de exemplo, use as instruções **x64**. Certifique-se de configurar o tempo de execução do IoT Edge para provisionamento automático, não manual. 

* [Instalar o tempo de execução do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md)
* [Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acesso de IoT Edge no TPM

Em ordem para o tempo de execução do IoT Edge provisionar automaticamente o seu dispositivo, ele precisa acessar o TPM. 

Você pode conceder acesso TPM no tempo de execução do IoT Edge, substituindo as configurações de systemd, de modo que o serviço **iotedge** tenha privilégios de raiz. Se você não deseja elevar os privilégios de serviço, você também pode usar as etapas a seguir para fornecer manualmente o acesso TPM. 

1. Localizar o caminho do arquivo para o módulo de hardware do TPM no seu dispositivo e salve-o como uma variável local. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará a borda de IoT acesso de tempo de execução ao tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso para o arquivo de regras. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salve e feche o arquivo. 

6. Dispare o sistema de udev para avaliar a nova regra. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com êxito.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem sucedida se parece com o seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se você não vir que as permissões corretas foram aplicadas, tente reinicializar o computador para atualizar udev. 

8. Abra o tempo de execução do IoT Edge substitui os arquivos. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Adicione o seguinte código para estabelecer uma variável de ambiente do TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Salve e feche o arquivo.

11. Verifique se a substituição teve êxito.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    A saída bem-sucedida exibe as variáveis de serviço padrão **iotedge** e, em seguida, mostra a variável de ambiente que você definiu em **override.conf**. 

12. Carregando as configurações.

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>Reinicie o tempo de execução do IoT Edge

Reinicie o tempo de execução do IoT Edge para que ele pega todas as alterações de configuração feitas no dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o tempo de execução do IoT Edge está sendo executado. 

   ```bash
   sudo systemctl status iotedge
   ```

Se você vir erros de provisionamento, pode ser que as alterações de configuração ainda não entraram em vigor. Tente reiniciar o daemon do IoT Edge novamente. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Ou então, tente reiniciar sua máquina virtual para ver se as alterações entram em vigor em um novo início. 

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o tempo de execução foi iniciado com êxito, você pode entrar em seu Hub IoT e veja que o novo dispositivo foi provisionado automaticamente. Agora seu dispositivo está pronto para executar os módulos do IoT Edge. 

Verifique o status do Daemon do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```

Você pode verificar se o registro individual que você criou no serviço de provisionamento de dispositivo foi usado. Navegue até sua instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes do registro para o registro individual que você criou. Observe que é o status do registro **atribuído** e o dispositivo de ID é listada. 

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando a CLI do Azure](how-to-deploy-monitor-cli.md).

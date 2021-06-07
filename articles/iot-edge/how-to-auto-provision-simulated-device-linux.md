---
title: Provisionar dispositivo com um TPM virtual na VM Linux-Azure IoT Edge
description: Use um TPM simulado em uma VM Linux para testar o Serviço de Provisionamento de Dispositivos do Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9356da9516ea17243be8e0e5519ec4b19e06db8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200867"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Criar e provisionar um dispositivo IoT Edge com um TPM no Linux

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge Linux usando um Trusted Platform Module (TPM). Você pode provisionar automaticamente Azure IoT Edge dispositivos com o [serviço de provisionamento de dispositivos](../iot-dps/index.yml). Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

:::moniker range=">=iotedge-2020-11"
> [!NOTE]
> Atualmente, o provisionamento automático usando a autenticação do TPM não tem suporte na versão IoT Edge 1,2.
:::moniker-end

As tarefas são as seguintes:

1. Crie uma máquina virtual Linux (VM) no Hyper-V com Trusted Platform Module (TPM) para segurança de hardware.
1. Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
1. Crie um registro individual para o dispositivo.
1. Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT.

> [!TIP]
> Este artigo descreve como testar o provisionamento de DPS usando um simulador de TPM, mas grande parte dele se aplica ao hardware físico do TPM, como o [Infineon OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), um dispositivo certificado pelo Azure para IOT.
>
> Se você estiver usando um dispositivo físico, poderá pular para a seção [recuperar informações de provisionamento de um dispositivo físico](#retrieve-provisioning-information-from-a-physical-device) neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento do Windows com [Hyper-V habilitado](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo usa o Windows 10 em execução de uma VM do Ubuntu Server.
* Um Hub IoT ativo.

> [!NOTE]
> O TPM 2,0 é necessário ao usar o atestado de TPM com o DPS e só pode ser usado para criar registros individuais, não de grupo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crie máquinas virtuais Linux com um TPM virtual

Nesta seção, você criará uma nova máquina virtual do Linux no Hyper-V. Você configura essa máquina virtual com um TPM simulado para testar como o provisionamento automático funciona com o IoT Edge.

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual se conecte a uma rede física.

1. Abra o Gerenciador do Hyper-V no computador com Windows.

2. No menu **Ações**, selecione **Gerenciador de Comutador Virtual**.

3. Escolha um comutador virtual **Externo** e, em seguida, selecione **Criar Comutador Virtual**.

4. Nomeie seu novo comutador virtual, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de conexão é definido como **rede externa**, em seguida, selecione **Ok**.

5. Um pop-up avisa que a conectividade de rede pode ser interrompida. Clique em **Sim** para continuar.

Se você vir erros ao criar o novo comutador virtual, verifique se nenhum outro comutador está usando o adaptador Ethernet e se nenhum outro comutador usa o mesmo nome.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. Baixe um arquivo de imagem de disco para usar para sua máquina virtual e salve-o localmente. Por exemplo, [ubuntu server 18, 4](http://releases.ubuntu.com/18.04/). Para obter informações sobre os sistemas operacionais com suporte para dispositivos IoT Edge, consulte [Azure IOT Edge sistemas com suporte](support.md).

2. No Gerenciador do Hyper-V novamente, selecione **ação**  >  **nova**  >  **máquina virtual** no menu **ações** .

3. Conclua o **Assistente de nova máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar geração**: selecione **geração 2**. As máquinas virtuais de geração 2 têm virtualização aninhada habilitada, que é necessária para executar IoT Edge em uma máquina virtual.
   2. **Configurar rede**: defina o valor de **Conexão** para o comutador virtual que você criou na seção anterior.
   3. **Opções de instalação**: Selecione **Instalar um sistema operacional a partir de um arquivo de imagem inicializável** e navegue até o arquivo de imagem de disco que você salvou localmente.

4. Selecione **concluir** no Assistente para criar a máquina virtual.

A criação da nova VM pode levar alguns minutos.

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Depois que a VM for criada, abra suas configurações para habilitar o TPM (Trusted Platform Module) que permite que você provisione automaticamente o dispositivo.

1. No Gerenciador do Hyper-V, clique com o botão direito do mouse na VM e selecione **configurações**.

2. Navegar para **Segurança**.

3. Desmarque a opção **habilitar inicialização segura**.

4. Verificar **Habilitar Trusted Platform Module**.

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e coletar dados TPM

Na máquina virtual, crie uma ferramenta que você possa usar para recuperar a **ID de registro** e a **chave de endosso** do dispositivo.

1. No Gerenciador do Hyper-V, inicie sua VM e conecte-se a ela.

1. Siga os prompts na máquina virtual para concluir o processo de instalação e reinicializar o computador.

1. Entre em sua VM e siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e criar o SDK do dispositivo IOT do Azure para C.

   >[!TIP]
   >No decorrer deste artigo, você copiará e colará a máquina virtual, que não é fácil por meio do aplicativo de conexão do Gerenciador do Hyper-V. Talvez você queira se conectar à máquina virtual por meio do Gerenciador do Hyper-V uma vez para recuperar seu endereço IP. Primeiro execute `sudo apt install net-tools` e, em seguida, `hostname -I` . Em seguida, você pode usar o endereço IP para se conectar por meio de SSH: `ssh <username>@<ipaddress>` .

1. Execute os comandos a seguir para criar a ferramenta SDK que recupera as informações de provisionamento do dispositivo do TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A janela saída exibe a ID de **registro** do dispositivo e a **chave de endosso**. Copie esses valores para uso posterior quando você criar um registro individual para seu dispositivo.

Depois de ter sua ID de registro e a chave de endosso, prossiga para a seção [Configurar o serviço de provisionamento de dispositivos no Hub IOT](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperar informações de provisionamento de um dispositivo físico

Se você estiver usando um dispositivo de IoT Edge físico em vez de uma VM, crie uma ferramenta que você possa usar para recuperar as informações de provisionamento do dispositivo.

1. Siga as etapas em [configurar um ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e criar o SDK do dispositivo IOT do Azure para C.

1. Execute os comandos a seguir para criar a ferramenta SDK que recupera as informações de provisionamento do dispositivo do dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copie os valores para **ID de registro** e **chave de endosso**. Você pode usar esses valores para criar um registro individual para seu dispositivo no DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Recuperar as informações de provisionamento da sua máquina virtual e usá-la para criar um registro individual no serviço de provisionamento do dispositivo.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> No CLI do Azure, você pode criar um [registro](/cli/azure/ext/azure-iot/iot/dps/enrollment) e usar o sinalizador **habilitado para borda** para especificar que um dispositivo é um dispositivo IOT Edge.

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

2. Em **Configurações**, selecione **Gerenciar registros**.

3. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Em **Mecanismo**, selecione **TPM**.

   2. Forneça a **chave de endosso** e a **ID de registro** que você copiou de sua máquina virtual.

      > [!TIP]
      > Se você estiver usando um dispositivo TPM físico, você precisará determinar a **chave de endosso**, que é exclusiva de cada chip TPM e é obtida do fabricante do chip TPM associado a ela. Você pode derivar uma **ID de registro** exclusiva para seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.

   3. Forneça uma ID para seu dispositivo, se desejar. Se você não fornecer uma ID de dispositivo, a ID de registro será usada.

   4. Selecione **true** para declarar que esta máquina virtual é um dispositivo IOT Edge.

   5. Escolha o Hub IoT vinculado ao qual você deseja conectar o dispositivo ou selecione **vincular ao novo hub IOT**. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   6. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Para obter mais informações, consulte [implantar módulos IOT Edge em escala](how-to-deploy-at-scale.md).

   7. Clique em **Salvar**.

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o runtime do IoT Edge em sua máquina virtual.

Siga as etapas em [instalar o Azure IOT Edge Runtime](how-to-install-iot-edge.md)e, em seguida, retorne a este artigo para provisionar o dispositivo.

## <a name="configure-the-device-with-provisioning-information"></a>Configurar o dispositivo com informações de provisionamento

Depois que o tempo de execução for instalado em seu dispositivo, configure o dispositivo com as informações que ele usa para se conectar ao serviço de provisionamento de dispositivos e ao Hub IoT.

1. Conheça o **escopo da ID** de DPS e a **ID de registro** do dispositivo que foram coletadas nas seções anteriores.

1. Abra o arquivo de configuração no dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Localize a seção Configurações de provisionamento do arquivo. Remova a marca de comentário das linhas do provisionamento do TPM e verifique se todas as outras linhas de provisionamento foram comentadas.

   A `provisioning:` linha não deve ter nenhum espaço em branco anterior e itens aninhados devem ser recuados em dois espaços.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

   Opcionalmente, use as `always_reprovision_on_startup` `dynamic_reprovisioning` linhas ou para configurar o comportamento de reprovisionamento do seu dispositivo. Se um dispositivo estiver configurado para reprovisionar na inicialização, ele sempre tentará provisionar com o DPS primeiro e, em seguida, retornará para o backup de provisionamento se isso falhar. Se um dispositivo estiver definido para reprovisionar dinamicamente, IoT Edge será reiniciado e reprovisionado se um evento de reprovisionamento for detectado. Para obter mais informações, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](../iot-dps/concepts-device-reprovision.md).

1. Atualize os valores de `scope_id` e `registration_id` com suas informações de dispositivo e DPS.

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acesso de IoT Edge no TPM

O tempo de execução do IoT Edge precisa acessar o TPM para provisionar automaticamente seu dispositivo.

Você pode conceder acesso de TPM ao tempo de execução de IoT Edge substituindo as configurações do sistema para que o `iotedge` serviço tenha privilégios de raiz. Se você não deseja elevar os privilégios de serviço, você também pode usar as etapas a seguir para fornecer manualmente o acesso TPM.

1. Localizar o caminho do arquivo para o módulo de hardware do TPM no seu dispositivo e salve-o como uma variável local.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará ao runtime do IoT Edge acesso ao tpm0.

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   A saída bem-sucedida aparece da seguinte maneira:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se você não vir que as permissões corretas foram aplicadas, tente reinicializar o computador para atualizar udev.

## <a name="restart-the-iot-edge-runtime"></a>Reinicie o runtime do IoT Edge

Reinicie o runtime do IoT Edge para que ele pega todas as alterações de configuração feitas no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o runtime do IoT Edge está sendo executado.

   ```bash
   sudo systemctl status iotedge
   ```

Se você vir erros de provisionamento, pode ser que as alterações de configuração ainda não entraram em vigor. Tente reiniciar o daemon do IoT Edge novamente.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou então, tente reiniciar sua máquina virtual para ver se as alterações entram em vigor em um novo início.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e veja que o novo dispositivo foi provisionado automaticamente. Agora seu dispositivo está pronto para executar os módulos do IoT Edge.

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

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada.

## <a name="next-steps"></a>Próximas etapas

O processo de registro de DPS permite que você defina a ID do dispositivo e as marcas de configuração do dispositivo ao mesmo tempo que provisiona o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [implantar e monitorar módulos IOT Edge em escala usando o portal do Azure](how-to-deploy-at-scale.md) ou [usando CLI do Azure](how-to-deploy-cli-at-scale.md).

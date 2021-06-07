---
title: Instalar Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge instruções de instalação em dispositivos Windows ou Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: a98eed61904b580988fe34302999f3ec6a24ac9e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640924"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Instalar ou desinstalar o Azure IoT Edge para Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime em dispositivos Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [ID de dispositivo registrada](how-to-register-device.md)

  Se você registrou seu dispositivo com a autenticação de chave simétrica, tenha a cadeia de conexão do dispositivo pronta.

  Se você registrou seu dispositivo com a autenticação de certificado autoassinado X. 509, tenha pelo menos um dos certificados de identidade que você usou para registrar o dispositivo e sua chave privada correspondente disponível em seu dispositivo.

* Um dispositivo Linux

  Ter um dispositivo Linux de x64, ARM32 ou ARM64. A Microsoft fornece pacotes de instalação para sistemas operacionais Ubuntu Server 18, 4 e Raspberry Pi OS.

  Para obter as informações mais recentes sobre quais sistemas operacionais têm suporte no momento para cenários de produção, consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems)

  >[!NOTE]
  >O suporte para dispositivos ARM64 está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Prepare seu dispositivo para acessar os pacotes de instalação da Microsoft.

  Instale a configuração do repositório que corresponde ao sistema operacional do seu dispositivo.

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Ampliação do SO do Raspberry Pi**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Copie a lista gerada para o diretório sources.list.d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Instale a chave pública do Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge pacotes de software estão sujeitos aos termos de licença localizados em cada pacote ( `usr/share/doc/{package-name}` ou no `LICENSE` diretório). Leia os termos da licença antes de usar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="install-a-container-engine"></a>Instalar um mecanismo de contêiner

O Azure IoT Edge depende de um runtime de contêiner compatível com OCI. Para cenários de produção, recomendamos que você use o mecanismo Moby. O mecanismo Moby é o único mecanismo de contêiner com suporte oficial com Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o runtime Moby.

Atualize as listas de pacotes no dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o mecanismo de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Se você receber erros ao instalar o mecanismo de contêiner do Moby, verifique a compatibilidade do kernel do Linux para Moby. Alguns fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm kernels Linux personalizados sem os recursos necessários para a compatibilidade do mecanismo de contêiner. Execute o comando a seguir, que usa o [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fornecido por Moby para verificar a configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Na saída do script, verifique se todos os itens em `Generally Necessary` e `Network Drivers` estão habilitados. Se você não tiver recursos, habilite-os recriando o kernel da origem e selecionando os módulos associados para inclusão no kernel. config apropriado. Da mesma forma, se você estiver usando um gerador de configuração de kernel como `defconfig` ou `menuconfig` , localize e habilite os respectivos recursos e reconstrua o kernel de acordo. Depois de implantar o kernel recém-modificado, execute o script check-config novamente para verificar se todos os recursos necessários foram habilitados com êxito.

## <a name="install-iot-edge"></a>Instalar o Edge IoT

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

O daemon de segurança do IoT Edge fornece e mantém os padrões de segurança no dispositivo IoT Edge. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do runtime do IoT Edge.

As etapas nesta seção representam o processo típico para instalar a versão mais recente em um dispositivo que tenha conexão com a Internet. Se você precisar instalar uma versão específica, como uma versão de pré-lançamento ou precisar instalar enquanto estiver offline, siga as etapas de [instalação offline ou específica](#offline-or-specific-version-installation-optional) , mais adiante neste artigo.

Atualize as listas de pacotes no dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se você quiser instalar a versão mais recente do daemon de segurança, use o seguinte comando que também instala a versão mais recente do pacote **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Ou, se você quiser instalar uma versão específica do daemon de segurança, especifique a versão da saída da lista de apt. Especifique também a mesma versão para o pacote **libiothsm-STD** , que, caso contrário, instalaria sua versão mais recente. Por exemplo, o seguinte comando instala a versão mais recente da versão 1.0.10:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Se a versão que você deseja instalar não estiver listada, siga as etapas de [instalação de versão offline ou específica](#offline-or-specific-version-installation-optional) posteriormente neste artigo. Essa seção mostra como direcionar qualquer versão anterior do daemon de segurança do IoT Edge ou versões Release Candidate.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

O serviço de IoT Edge fornece e mantém os padrões de segurança no dispositivo IoT Edge. O serviço é iniciado em cada inicialização e inicializa o dispositivo iniciando o restante do IoT Edge tempo de execução.

O serviço de identidade de IoT foi introduzido junto com a versão 1,2 do IoT Edge. Esse serviço lida com o provisionamento e gerenciamento de identidades para IoT Edge e para outros componentes de dispositivo que precisam se comunicar com o Hub IoT.

As etapas nesta seção representam o processo típico para instalar a versão mais recente em um dispositivo que tenha conexão com a Internet. Se você precisar instalar uma versão específica, como uma versão de pré-lançamento ou precisar instalar enquanto estiver offline, siga as etapas de [instalação offline ou específica](#offline-or-specific-version-installation-optional) , mais adiante neste artigo.

>[!NOTE]
>As etapas nesta seção mostram como instalar IoT Edge versão 1,2, que está atualmente em visualização pública. Se você estiver procurando as etapas para instalar a versão mais recente do IoT Edge disponível, consulte a versão [1,1 (LTS)](?view=iotedge-2018-06&preserve-view=true) deste artigo.
>
>Se você já tiver um dispositivo IoT Edge executando uma versão mais antiga e quiser atualizar para o 1,2, use as etapas em [atualizar o daemon de segurança do IOT Edge e o tempo de execução](how-to-update-iot-edge.md). A versão 1,2 é suficientemente diferente das versões anteriores do IoT Edge que as etapas específicas são necessárias para a atualização.

Atualize as listas de pacotes no dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a aziot-edge
   ```

Se você quiser instalar a versão mais recente do IoT Edge, use o seguinte comando que também instala a versão mais recente do pacote de serviço de identidade:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionar o dispositivo com sua identidade de nuvem

Agora que o mecanismo de contêiner e o tempo de execução do IoT Edge estão instalados em seu dispositivo, você está pronto para a próxima etapa, que é configurar o dispositivo com sua identidade de nuvem e informações de autenticação.

Escolha a próxima seção com base no tipo de autenticação que você deseja usar:

* [Opção 1: autenticar com chaves simétricas](#option-1-authenticate-with-symmetric-keys)
* [Opção 2: autenticar com certificados X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opção 1: autenticar com chaves simétricas

Neste ponto, o tempo de execução do IoT Edge é instalado em seu dispositivo Linux e você precisa provisionar o dispositivo com suas informações de autenticação e identidade de nuvem.

Esta seção percorre as etapas para provisionar um dispositivo com a autenticação de chave simétrica. Você deve ter registrado seu dispositivo no Hub IoT e recuperado a cadeia de conexão das informações do dispositivo. Caso contrário, siga as etapas em [registrar um dispositivo de IOT Edge no Hub IOT](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

No dispositivo IoT Edge, abra o arquivo de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da **configuração manual de provisionamento usando uma seção cadeia de conexão** , se ela ainda não tiver sido comentada.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Verifique se todas as outras seções de provisionamento foram comentadas. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

Para colar o conteúdo da área de transferência no Nano, `Shift+Right Click` ou clique em `Shift+Insert`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Crie o arquivo de configuração para seu dispositivo com base em um arquivo de modelo fornecido como parte da instalação do IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

No dispositivo IoT Edge, abra o arquivo de configuração.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Localize a seção de **provisionamento** do arquivo e remova o comentário do provisionamento manual com linhas de cadeia de conexão.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Atualize o valor de **connection_string** com a cadeia de conexão do seu dispositivo IOT Edge.

Para colar o conteúdo da área de transferência no Nano, `Shift+Right Click` ou clique em `Shift+Insert`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, aplique as alterações:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Opção 2: autenticar com certificados X. 509

Neste ponto, o tempo de execução do IoT Edge é instalado em seu dispositivo Linux e você precisa provisionar o dispositivo com suas informações de autenticação e identidade de nuvem.

Esta seção percorre as etapas para provisionar um dispositivo com autenticação de certificado X. 509. Você deve ter registrado seu dispositivo no Hub IoT, fornecendo impressões digitais que correspondam ao certificado e à chave privada localizadas em seu dispositivo de IoT Edge. Caso contrário, siga as etapas em [registrar um dispositivo de IOT Edge no Hub IOT](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

No dispositivo IoT Edge, abra o arquivo de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Localize a seção Configurações de provisionamento do arquivo e remova a marca de comentário da **configuração de provisionamento manual usando uma seção certificado de identidade X. 509** . Verifique se todas as outras seções de provisionamento foram comentadas. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Atualize os seguintes campos:

* **iothub_hostname**: nome do host do Hub IOT ao qual o dispositivo se conectará. Por exemplo, `{IoT hub name}.azure-devices.net`.
* **DEVICE_ID**: a ID que você forneceu quando registrou o dispositivo.
* **identity_cert**: URI para um certificado de identidade no dispositivo. Por exemplo, `file:///path/identity_certificate.pem`.
* **identity_pk**: URI para o arquivo de chave privada para o certificado de identidade fornecido. Por exemplo, `file:///path/identity_key.pem`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Crie o arquivo de configuração para seu dispositivo com base em um arquivo de modelo fornecido como parte da instalação do IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

No dispositivo IoT Edge, abra o arquivo de configuração.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Localize a seção de **provisionamento** do arquivo e remova a marca de comentário das linhas para provisionamento manual com o certificado de identidade X. 509. Verifique se todas as outras seções de provisionamento foram comentadas.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Atualize os seguintes campos:

* **iothub_hostname**: nome do host do Hub IOT ao qual o dispositivo se conectará. Por exemplo, `{IoT hub name}.azure-devices.net`.
* **DEVICE_ID**: a ID que você forneceu quando registrou o dispositivo.
* **identity_cert**: URI para um certificado de identidade no dispositivo, por exemplo: `file:///path/identity_certificate.pem` . Ou, emitir dinamicamente o certificado usando a EST ou uma autoridade de certificação local.
* **identity_pk**: URI para o arquivo de chave privada para o certificado de identidade fornecido, por exemplo: `file:///path/identity_key.pem` . Ou forneça um URI PKCS # 11 e forneça as informações de configuração na seção **PKCS # 11** posteriormente no arquivo de configuração.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, aplique as alterações:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Verificar configuração bem-sucedida

Verifique se o runtime foi instalado e configurado com êxito em seu dispositivo IoT Edge.

>[!TIP]
>Você precisa de privilégios elevados para executar comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o runtime do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

Verifique se o serviço do sistema IoT Edge está em execução.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Uma resposta de status bem-sucedida é `Ok` .

::: moniker-end

Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Use a `check` ferramenta para verificar a configuração e o status de conexão do dispositivo.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Sempre use `sudo` para executar a ferramenta de verificação, mesmo depois que suas permissões forem atualizadas. A ferramenta precisa de privilégios elevados para acessar o arquivo de configuração para verificar o status da configuração.

Exiba todos os módulos em execução no seu dispositivo IoT Edge. Quando o serviço é iniciado pela primeira vez, você só deve ver o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e a iniciar quaisquer módulos adicionais que você implante em seu dispositivo.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Instalação offline ou de versão específica (opcional)

As etapas nesta seção são para cenários não cobertos pelas etapas de instalação padrão. Isso pode incluir:

* Instalar IoT Edge enquanto estiver offline
* Instalar uma versão Release Candidate

Use as etapas nesta seção se você quiser instalar uma versão específica do Azure IoT Edge Runtime que não está disponível por meio do `apt-get install` . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas subversãos, portanto, essas etapas são para qualquer pessoa que queira instalar uma versão mais antiga ou uma versão Release Candidate.

Usando comandos de rotação, você pode direcionar os arquivos de componentes diretamente do repositório GitHub IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar.

2. Expanda a seção **ativos** para essa versão.

3. Cada versão deve ter novos arquivos para o daemon de segurança IoT Edge e o hsmlib. Se você for instalar IoT Edge em um dispositivo offline, baixe esses arquivos antes do tempo. Caso contrário, use os comandos a seguir para atualizar esses componentes.

   1. Localize o arquivo **libiothsm-STD** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Localize o arquivo **iotedge** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão do daemon de segurança IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Se o dispositivo estiver executando IoT Edge versão 1,1 ou mais antiga, desinstale os pacotes **iotedge** e **libiothsm-STD** antes de seguir as etapas desta seção. Para obter mais informações, consulte [Atualizar de 1,0 ou 1,1 para 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar.

2. Expanda a seção **ativos** para essa versão.

3. Cada versão deve ter novos arquivos para IoT Edge e o serviço de identidade. Se você for instalar IoT Edge em um dispositivo offline, baixe esses arquivos antes do tempo. Caso contrário, use os comandos a seguir para atualizar esses componentes.

   1. Localize o arquivo **aziot-Identity-Service** que corresponde à arquitetura do seu dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do serviço de identidade:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Localize o arquivo **aziot-Edge** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão do IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Agora que o mecanismo de contêiner e o tempo de execução do IoT Edge estão instalados em seu dispositivo, você está pronto para a próxima etapa, que é [provisionar o dispositivo com sua identidade de nuvem](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo, use os comandos a seguir.

Remova o runtime do IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Use o `--purge` sinalizador se desejar excluir todos os arquivos associados a IOT Edge, incluindo os arquivos de configuração. Deixe esse sinalizador para fora se você quiser reinstalar IoT Edge e usar as mesmas informações de configuração no futuro.

Quando o tempo de execução de IoT Edge é removido, todos os contêineres que ele criou são interrompidos, mas ainda existem em seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem.

```bash
sudo docker ps -a
```

Exclua os contêineres do seu dispositivo, incluindo dois contêineres de runtime.

```bash
sudo docker rm -f <container name>
```

Por fim, remova o runtime do contêiner do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Próximas etapas

Continue a [implantar módulos IOT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos em seu dispositivo.

---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a739736182713b35c3a5e9e25742aa39c5d1122
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633130"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o runtime

Como o serviço de IoT Edge libera novas versões, convém atualizar seus dispositivos de IoT Edge para os recursos e aprimoramentos de segurança mais recentes. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de runtime quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o runtime, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o runtime poderá ser atualizado no dispositivo ou remotamente.

Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`.

# <a name="linux"></a>[Linux](#tab/linux)

Em dispositivos Linux x64, use apt-get ou o Gerenciador de pacotes apropriado para atualizar o daemon de segurança para a versão mais recente.

Obtenha a configuração mais recente do repositório da Microsoft:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ampliação do SO do Raspberry Pi**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Atualizar apt.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se você quiser atualizar para a versão mais recente do daemon de segurança, use o seguinte comando que também atualiza **libiothsm-STD** para a versão mais recente:

   ```bash
   sudo apt-get install iotedge
   ```

Se você quiser atualizar para uma versão específica do daemon de segurança, especifique a versão da saída da lista de apt. Sempre que o **iotedge** for atualizado, ele tentará atualizar automaticamente o pacote **libiothsm-STD** para sua versão mais recente, o que pode causar um conflito de dependência. Se você não for para a versão mais recente, certifique-se de direcionar ambos os pacotes para a mesma versão. Por exemplo, o comando a seguir instala uma versão específica da versão 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Se a versão que você deseja instalar não estiver disponível por meio de apt-get, você poderá usar a rotação para direcionar qualquer versão do repositório de [versões IOT Edge](https://github.com/Azure/azure-iotedge/releases) . Para qualquer versão que você deseja instalar, localize os arquivos **libiothsm-STD** e **iotedge** apropriados para seu dispositivo. Para cada arquivo, clique com o botão direito do mouse no link do arquivo e copie o endereço do link. Use o link address para instalar as versões específicas desses componentes:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

Com o IoT Edge para Linux no Windows, IoT Edge é executado em uma máquina virtual Linux hospedada em um dispositivo Windows. Essa máquina virtual é pré-instalada com o IoT Edge e é gerenciada com Microsoft Update para manter os componentes atualizados. No momento, não há atualizações disponíveis.

::: moniker-end

Com o IoT Edge para Windows, IoT Edge é executado diretamente no dispositivo Windows. Para obter instruções de atualização usando os scripts do PowerShell, consulte [instalar e gerenciar o Azure IOT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md).

---

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de runtime

A maneira como você atualiza o agente de IoT Edge e os contêineres de IoT Edge Hub depende se você usa marcas sem interrupção (como 1,0) ou marcas específicas (como 1.0.7) em sua implantação.

Verifique a versão do agente do IoT Edge e de módulos de hub do IoT Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`.

  ![Encontre a versão do contêiner nos logs](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do IoT Edge e as imagens de hub do IoT Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de runtime:

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o runtime do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de runtime serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção.

* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.7 não será alterado após sua versão inicial. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de tag sem interrupção

Se você usar marcas sem interrupção em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), será necessário forçar o runtime do contêiner em seu dispositivo para obter a versão mais recente da imagem.

Exclua a versão local da imagem do seu dispositivo IoT Edge. Em computadores Windows, desinstalar o daemon de segurança também remove as imagens de runtime, portanto, você não precisará realizar esta etapa novamente.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Talvez você precise usar a marca `-f` de forçar para remover as imagens.

O serviço do IoT Edge obterá as versões mais recentes das imagens do runtime e as iniciará automaticamente em seu dispositivo outra vez.

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem de tag específica

Se você usar marcas específicas em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), tudo o que você precisa fazer é atualizar a marca em seu manifesto de implantação e aplicar as alterações ao seu dispositivo.

1. No Hub IoT no portal do Azure, selecione o dispositivo IoT Edge e selecione **definir módulos**.

1. Na seção **módulos IOT Edge** , selecione **configurações de tempo de execução**.

   ![Definir configurações de tempo de execução](./media/how-to-update-iot-edge/configure-runtime.png)

1. Em **configurações de tempo de execução**, atualize o valor da **imagem** para o **Hub do Edge** com a versão desejada. Não selecione **salvar** ainda.

   ![Atualizar versão da imagem do Hub do Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Recolha as configurações do **Hub do Edge** ou role para baixo e atualize o valor da **imagem** para o **agente do Edge** com a mesma versão desejada.

   ![Atualizar versão do agente do Hub do Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Clique em **Salvar**.

1. Selecione **revisão + criar**, examine a implantação e selecione **criar**.

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão Release Candidate

Azure IoT Edge lança regularmente novas versões do serviço de IoT Edge. Antes de cada versão estável, há uma ou mais versões RC (Release Candidate). As versões RC incluem todos os recursos planejados para a versão, mas ainda passam por testes e validação. Se você quiser testar um novo recurso antecipadamente, poderá instalar uma versão RC e fornecer comentários por meio do GitHub.

As versões Release Candidate seguem a mesma convenção de numeração de versões, mas têm **-RC** mais um número incremental acrescentado ao final. Você pode ver os candidatos à versão na mesma lista de [versões de Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) como as versões estáveis. Por exemplo, localize **1.0.9-RC5** e **1.0.9-RC6**, dois dos candidatos à versão que vieram antes de **1.0.9**. Você também pode ver que as versões RC são marcadas com rótulos de **pré-lançamento** .

O agente de IoT Edge e os módulos de Hub têm versões RC marcadas com a mesma convenção. Por exemplo, **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

Como as visualizações, as versões Release Candidate não são incluídas como a versão mais recente que os instaladores normais visam. Em vez disso, você precisa direcionar manualmente os ativos para a versão RC que deseja testar. Para a maior parte, instalar ou atualizar para uma versão RC é o mesmo que visar qualquer outra versão específica do IoT Edge.

Use as seções deste artigo para saber como atualizar um dispositivo IoT Edge para uma versão específica do daemon de segurança ou módulos de tempo de execução.

Se você estiver instalando IoT Edge, em vez de atualizar uma instalação existente, use as etapas em [instalação offline ou de versão específica](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Próximas etapas

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Mantenha-se atualizado com as atualizações e os comunicados recentes no [blog Internet das coisas](https://azure.microsoft.com/blog/topics/internet-of-things/)
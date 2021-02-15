---
title: Após a instalação e o provisionamento, verifique o êxito e a solução de problemas
description: incluir arquivo
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979376"
---
## <a name="verify-successful-setup"></a>Verificar a configuração bem-sucedida

Verifique o status do serviço do IoT Edge. Ele deve estar listado como em execução.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Examine os logs de serviço.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Se acabar de instalar o IoT Edge Runtime, você poderá ver uma lista de erros do tempo entre a execução de **Deploy-IoTEdge** e **Initialize-IoTEdge**. Esses erros são esperados, pois o serviço está tentando iniciar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Execute a [ferramenta de solução de problemas](../articles/iot-edge/troubleshoot.md#run-the-check-command) para verificar os erros de configuração e de rede mais comuns.

```powershell
iotedge check
```

Até que você implante seu primeiro módulo para IoT Edge em seu dispositivo, o módulo do sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automática retornará um erro para a verificação de `Edge Hub can bind to ports on host` conectividade. Esse erro pode ser ignorado, a menos que ocorra após a implantação de um módulo no dispositivo.

Por fim, liste módulos em execução:

```powershell
iotedge list
```

Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

Em dispositivos com restrição de recursos, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas](../articles/iot-edge/troubleshoot.md).

Se o dispositivo não puder se conectar ao Hub IoT e sua rede tiver um servidor proxy, siga as etapas em [configurar seu IOT Edge dispositivo para se comunicar por meio de um servidor proxy](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

Em dispositivos Linux, você precisa de privilégios elevados para executar `iotedge` comandos. Após instalar o runtime, saia da sua máquina virtual e entre novamente para atualizar suas permissões automaticamente. Até lá, use `sudo` para executar comandos com privilégios elevados.

# <a name="windows"></a>[Windows](#tab/windows)

Em dispositivos Windows que executam contêineres do Windows, o mecanismo de contêiner Moby foi instalado como parte do IoT Edge. O mecanismo Moby foi projetado para ser executado em paralelo com o Docker desktop. Você pode usar `docker` comandos se desejar interagir diretamente com os contêineres em seu dispositivo. No entanto, você deve direcionar especificamente o mecanismo Moby, caso o Docker desktop também esteja instalado no dispositivo.

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens Moby, modifique o mesmo comando com um ponteiro para o mecanismo Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O URI do mecanismo é listado na saída do script de instalação ou você pode encontrá-lo na seção Configurações de tempo de execução do contêiner para o arquivo config. YAML.

![URI de moby_runtime em config. YAML](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---

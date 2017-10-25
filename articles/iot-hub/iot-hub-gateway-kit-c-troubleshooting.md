---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Solução de problemas | Microsoft Docs"
description: "Página de solução de problemas de gateway NUC Intel"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: problemas de iot, problemas internet das coisas
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting"></a>Solucionar problemas

## <a name="hardware-issues"></a>Problemas de hardware

### <a name="ti-sensortag-cannot-be-connected"></a>SensorTag de TI não pode ser conectado

Para solucionar problemas de conectividade do SensorTag, use o [aplicativo SensorTag](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Problemas com o NUC da Intel

Para solucionar problemas de inicialização, consulte [solução de problemas de inicialização no NUC da Intel](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Para solucionar problemas do sistema operacional, consulte [solução de problemas de sistema operacional no NUC da Intel](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Para solucionar outros problemas, consulte [Códigos de Piscada e de Bipe para o NUC da Intel](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>Problemas do pacote de Node.js

### <a name="no-response-during-gulp-tasks"></a>Sem resposta durante as tarefas de gulp

Se você encontrar problemas ao executar tarefas de gulp, adicione a opção `--verbose` para depuração. Tente finalizar as tarefas de gulp atuais usando `Ctrl + C` e, em seguida, execute o seguinte comando na janela do console para ver mensagens de depuração. Você pode ver mensagens de erro detalhadas em sua saída do console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de descoberta de dispositivo

Para obter ajuda na solução de problemas comuns com o comando `discover-sensortag`, consulte a [página wiki](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>problemas de npm

Tente atualizar o pacote npm executando o comando a seguir:

```bash
npm install -g npm
```

Se o problema persistir, deixe seus comentários no final deste artigo ou crie um problema de GitHub em nosso [repositório de exemplo](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Depuração Remota
> As instruções abaixo se destinam à depuração dos scripts node.js usados neste tutorial.
### <a name="run-the-sample-application-in-debug-mode"></a>Execute o aplicativo de exemplo no modo de depuração

Execute o aplicativo de exemplo no modo de depuração executando o seguinte comando:

```bash
gulp run --debug
```

Quando o mecanismo de depuração estiver pronto, você deverá ver `Debugger listening on port 5858` na saída do console.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configurar o Visual Studio Code para se conectar ao dispositivo remoto

1. Abra o painel **Depurar** painel no lado esquerdo.
2. Clique no botão verde **Iniciar Depuração** (F5). O Visual Studio Code abre um arquivo `launch.json`.
3. Atualize o arquivo `launch.json` com o seguinte conteúdo. Substitua `[device hostname or IP address]` pelo nome do host ou endereço IP do dispositivo real.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Configuração de Depuração Remota](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Anexar ao aplicativo remoto

Clique no botão verde **Iniciar Depuração** (F5) para iniciar a depuração.

Leia [JavaScript no Código do VS](https://code.visualstudio.com/docs/languages/javascript#_debugging) para saber mais sobre o depurador.

![Depurar o Exemplo de BLE](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Problemas da CLI do Azure

A interface de linha de comando do Azure (CLI do Azure) é uma compilação de visualização. Para procurar soluções, você pode consultar o [Guia de Instalação de Visualização](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Se você encontrar erros com a ferramenta, emita um [problema](https://github.com/Azure/azure-cli/issues) na seção **problemas** do repositório do GitHub.

Para obter ajuda para solucionar problemas comuns, consulte o [Leiame](https://github.com/Azure/azure-cli/blob/master/README.rst).

Se você receber a mensagem “Não foi possível localizar uma versão que atenda ao requisito”, execute o seguinte comando para atualizar o pip para a versão mais recente.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemas de instalação do Python

### <a name="legacy-installation-issues-macos"></a>Problemas de instalação herdada (macOS)

Ao instalar o pip, um erro de permissão será lançado quando houver pacotes herdados instalados com permissões **su**. Essa situação ocorre porque a instalação anterior do Python usando brew (macOS) não está completamente desinstalada. Alguns pacotes do pip de uma instalação anterior foram criados pela raiz, o que causa o erro de permissão. A solução é remover os pacotes instalados pela raiz. Use as etapas a seguir para concluir esta tarefa:

1. Acesse `/usr/local/lib/python2.7/site-packages`
2. Listar pacotes criados por raiz: `ls -l | grep root`
3. Desinstalar pacotes da etapa 2: `sudo rm -rf {package name}`
4. Reinstale o Python.

## <a name="azure-iot-hub-issues"></a>Problemas do Hub IoT do Azure

Se você tiver provisionado com êxito o Hub IoT do Azure com a CLI do Azure e precisar de uma ferramenta para gerenciar os dispositivos conectados ao seu Hub IoT, tente as seguintes ferramentas.

### <a name="device-explorer"></a>Gerenciador de Dispositivos

O [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) é executado no computador local do Windows e se conecta ao seu hub IoT no Azure. Ele se comunica com os seguintes [pontos de extremidade de Hub IoT](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Gerenciamento de identidade do dispositivo para provisionar e gerenciar dispositivos registrados com o Hub IoT.
- Receber do dispositivo para nuvem para que você possa monitorar as mensagens enviadas de seu dispositivo ao Hub IoT.
- Enviar da nuvem para o dispositivo para que você possa enviar mensagens para seus dispositivos do Hub IoT.

Configure a cadeia de conexão do Hub IoT dentro desta ferramenta para usar todos os seus recursos.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) é uma ferramenta de CLI de várias plataformas de exemplo para gerenciar clientes de dispositivo. Use a ferramenta para gerenciar os dispositivos no registro de identidade, monitorar mensagens de dispositivo para a nuvem e enviar comandos de nuvem para o dispositivo.

Para instalar a versão mais recente (pré-lançamento) da ferramenta iothub-explorer, execute o comando a seguir:

```bash
npm install -g iothub-explorer@latest
```

Para obter ajuda adicional sobre todos os comandos do iothub-explorer e seus parâmetros, execute o comando a seguir:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>O Portal do Azure

Uma experiência completa de CLI ajuda você a criar e gerenciar todos os recursos do Azure. Você também poderá usar o [portal do Azure](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) para ajudar a provisionar, gerenciar e depurar seus recursos do Azure.

## <a name="azure-storage-issues"></a>Problemas de Armazenamento do Azure

[O Gerenciador de Armazenamento do Microsoft Azure (Preview)](http://storageexplorer.com/) é um aplicativo autônomo da Microsoft que pode ser usado para trabalhar com dados do Armazenamento do Azure no Windows, macOS e Linux. Com essa ferramenta você pode se conectar à sua tabela e ver os dados contidos nela. Você pode usar essa ferramenta para solucionar seus problemas de Armazenamento do Azure.

---
title: OpenCensus de Insights de aplicativo do Azure distribuída rastreamento encaminhador local (versão prévia) | Docs do Microsoft
description: Encaminhe períodos e rastreamento distribuído OpenCensus em linguagens como Python e Go para o Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699286"
---
# <a name="local-forwarder-preview"></a>Encaminhador de local (versão prévia)

O encaminhador local é um agente que coleta telemetria do Application Insights ou do [OpenCensus](https://opencensus.io/) de vários SDKs e a encaminha ao Application Insights. Ele pode ser executado em Windows e Linux. Também é possível executá-lo em macOS, mas ele não tem suporte oficial no momento.

## <a name="running-local-forwarder"></a>Como executar o encaminhador local

O encaminhador local é um [projeto de software livre no GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Há várias formas de executar o encaminhador local em várias plataformas.

### <a name="windows"></a> Windows

#### <a name="windows-service"></a>Windows Service

A maneira mais fácil de executar o encaminhador local no Windows é instalando-o como um Windows Service. A versão vem com um executável do Windows Service (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) que pode ser facilmente registrado no sistema operacional.

> [!NOTE]
> O serviço do encaminhador local exige, no mínimo, a versão 4.7 do .NET Framework. Se você não tiver o .NET Framework 4.7, o serviço será instalado, mas não será iniciado. Para acessar a versão mais recente do .NET Framework, **[visite a página de download do .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Faça o download do arquivo LF.WindowsServiceHost.zip na [página de versão do encaminhador local](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) no GitHub.

    ![Captura de tela da página de download de versões do encaminhador local](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Neste exemplo, para facilitar a demonstração, vamos apenas extrair o arquivo .zip no caminho `C:\LF-WindowsServiceHost`.

    Para registrar o serviço e configurá-lo para iniciar na inicialização do sistema, execute o seguinte na linha de comando como Administrador:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Você deverá receber uma resposta:
    
    `[SC] CreateService SUCCESS`
    
    Para examinar o novo serviço na GUI do Services, digite ``services.msc``
        
     ![Captura de tela do serviço do encaminhador local](./media/opencensus-local-forwarder/002-services.png)

3. **Clique com botão direito do mouse** no novo encaminhador local e escolha **Iniciar**. Agora o serviço começará a ser executado.

4. Por padrão, o serviço é criado sem ações de recuperação. Você pode **clicar com o botão direito do mouse** e escolher **Propriedades** > **Recuperação** para configurar respostas automáticas em caso de falha de serviço.

    Como alternativa, se preferir definir opções de recuperação automática programaticamente em caso de falhas, você poderá usar:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. No mesmo local do arquivo ``Microsoft.LocalForwarder.WindowsServiceHost.exe``, que neste exemplo é ``C:\LF-WindowsServiceHost``, há um arquivo chamado ``LocalForwarder.config``. Esse arquivo é baseado em xml e permite que você ajuste a configuração de seu encaminhador local e especifique a chave de instrumentação do recurso do Application Insights para onde os dados de rastreamento distribuído devem ser encaminhados. 

    Depois de editar o arquivo ``LocalForwarder.config`` para adicionar sua chave de instrumentação, reinicie o **serviço do Encaminhador Local** para permitir que as alterações entrem em vigor.
    
6. Para confirmar se as configurações desejadas estão em vigor e se o encaminhador local está escutando os dados de rastreamento como esperado, verifique o arquivo ``LocalForwarder.log``. Você deve ver resultados semelhantes à imagem abaixo na parte inferior do arquivo:

    ![Captura de tela do arquivo LocalForwarder.log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplicativo de console

Para determinados casos de uso, pode ser útil executar o encaminhador local como um aplicativo de console. A versão vem com as seguintes versões executáveis do host do console:
* um binário .NET Core dependente de estrutura */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. A execução desse binário requer um tempo de execução do .NET Core instalado. Confira esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de download para obter detalhes.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* um conjunto .NET Core autossuficiente de binários para plataformas x86 e x64. Isso não requer tempo de execução do .NET Core para ser executado. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Assim como acontece com o Windows, a versão vem com as seguintes versões executáveis do host do console:
* um binário .NET Core dependente de estrutura */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. A execução desse binário requer um tempo de execução do .NET Core instalado. Confira esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de download para obter detalhes.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* um conjunto .NET Core autossuficiente de binários para linux-64. Isso não requer tempo de execução do .NET Core para ser executado. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Muitos usuários do Linux desejarão executar o encaminhador local como um daemon. Os sistemas Linux contam com uma variedade de soluções de gerenciamento de serviço, como Upstart, sysv ou systemd. Qualquer que seja sua versão, você pode usá-la para executar o encaminhador local de uma maneira que seja mais apropriada para seu cenário.

Por exemplo, vamos criar um serviço do daemon usando systemd. Vamos usar a versão dependente de estrutura. No entanto, o mesmo pode ser feito em versões autossuficientes.

* Crie o arquivo de serviço a seguir, chamado *localforwarder.service*, e coloque-o em */lib/systemd/system*.
Este exemplo supõe que seu nome de usuário seja SAMPLE_USER e que você tenha copiado os binários do encaminhador local dependentes de estrutura (de */ConsoleHost/publish*) para */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Execute o comando a seguir para instruir o systemd a iniciar o encaminhador local em cada inicialização

```
systemctl enable localforwarder
```

* Execute o comando a seguir para instruir o systemd a iniciar o encaminhador local imediatamente

```
systemctl start localforwarder
```

* Monitore o serviço inspecionando os arquivos **.log* no diretório /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
O encaminhador local pode funcionar com o macOS, mas ele não tem suporte oficial no momento.

### <a name="self-hosting"></a>Hospedagem interna
O encaminhador local também é distribuído como um pacote do NuGet do .NET Standard, permitindo que você o hospede em seu aplicativo .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Como configurar o encaminhador local

* Ao executar um dos próprios hosts locais do encaminhador (Host de Console ou Host do Windows Service), você verá **LocalForwarder.config** ao lado do binário.
* Ao realizar a hospedagem interna do encaminhador local do NuGet, a configuração deverá ser fornecida no mesmo formato no código (consulte a seção sobre hospedagem interna). Para obter a sintaxe de configuração, verifique [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) no repositório do GitHub. 

> [!NOTE]
> A configuração pode mudar de versão para versão, portanto, preste atenção na versão que você está usando.

## <a name="monitoring-local-forwarder"></a>Como monitor o encaminhador local

Os rastreamentos são gravados no sistema de arquivo ao lado do executável que executa o encaminhador local (procure os arquivos **.log*). É possível colocar um arquivo chamado *NLog.config* ao lado do executável para fornecer sua própria configuração em vez da configuração padrão. Confira a [documentação](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) para obter a descrição do formato.

Se nenhum arquivo de configuração for fornecido (o que é o padrão), o encaminhador local usará a configuração padrão, que pode ser encontrada [aqui](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Próximas etapas

* [Open Census](https://opencensus.io/)

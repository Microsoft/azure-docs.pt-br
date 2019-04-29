---
title: Configurar um ambiente de desenvolvimento do Windows para microsserviços do Azure | Microsoft Docs
description: Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/02/2019
ms.author: aljo
ms.openlocfilehash: 45d5d1b4e050d01b003b6321e1af44c0da762e19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946517"
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparar seu ambiente de desenvolvimento no Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Para compilar e executar os [aplicativos do Azure Service Fabric][1] em seu computador de desenvolvimento do Windows, instale o tempo de execução do Service Fabric, o SDK e as ferramentas. Você também precisa [habilitar a execução dos scripts do Windows PowerShell](#enable-powershell-script-execution) incluídos no SDK.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte
Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Suporte do Windows 7:
> - O Windows 7 inclui, por padrão, apenas o Windows PowerShell 2.0. Cmdlets de PowerShell do Service Fabric exigem o PowerShell 3.0 ou superior. É possível [baixar o Windows PowerShell 5.0][powershell5-download] do Centro de Download da Microsoft.
> - O Proxy Reverso do Service Fabric não está disponível no Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Instalar o SDK e as ferramentas
O WebPI (Web Platform Installer) é a maneira recomendada de instalar o SDK e as ferramentas. Caso receba os erros de tempo de execução usando o WebPI, você também poderá encontrar links diretos para os instaladores nas notas sobre a versão específica do Service Fabric. As notas sobre a versão podem ser encontradas em vários anúncios de lançamentos no [blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Não há suporte para atualizações de cluster de desenvolvimento do Service Fabric local.

### <a name="to-use-visual-studio-2017"></a>Para usar o Virtual Studio 2017
As ferramentas do Service Fabric fazem parte da carga de trabalho Desenvolvimento do Azure no Visual Studio 2017. Habilite essa carga de trabalho como parte da instalação do Visual Studio.
Além disso, você precisa instalar o SDK e o tempo de execução do Microsoft Azure Service Fabric usando o Web Platform Installer.

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Para usar o Visual Studio 2015 (requer a atualização do Visual Studio 2015 2 ou posterior)
Para Visual Studio 2015, as ferramentas do Service Fabric são instaladas junto com o SDK e o tempo de execução, usando o Web Platform Installer:

* [Instalar as ferramentas e o SDK do Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Somente instalação do SDK
Se só precisar do SDK, você poderá instalar este pacote:
* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

As versões atuais são:
* SDK do Service Fabric e ferramentas 3.3.654
* Tempo de execução do Service Fabric 6.4.654
* Ferramentas do Service Fabric para Visual Studio 2015 2.4.11116.1
* O Visual Studio 2017 15.9 inclui as Ferramentas do Service Fabric para Visual Studio 2.4.11024.1 

Para obter uma lista das versões com suporte, consulte [versões do Service Fabric](service-fabric-versions.md)

> [!NOTE]
> Os clusters de computador único (OneBox) não têm suporte para atualizações de Aplicativo ou Cluster. Exclua o cluster OneBox e recrie-o se precisar executar uma atualização de Cluster ou se tiver problemas ao executar uma atualização de Aplicativo. 

## <a name="enable-powershell-script-execution"></a>Habilitar a execução de script do PowerShell
A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Instalar o Docker (opcional)
[O Service Fabric é um orquestrador de contêineres](service-fabric-containers-overview.md) para implantação de microsserviços em um cluster de computadores. Para executar aplicativos de contêiner do Windows no cluster de desenvolvimento local, você deve primeiro instalar o Docker for Windows. Obtenha o [Docker CE for Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique no ícone de bandeja e selecione **Alternar para contêineres do Windows**. Essa etapa é necessária para executar imagens do Docker com base no Windows.

## <a name="next-steps"></a>Próximas etapas
Agora que você terminou de configurar seu ambiente de desenvolvimento, comece a compilar e executar aplicativos.

* [Saiba como criar, implantar e gerenciar aplicativos](service-fabric-tutorial-create-dotnet-app.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Confira os exemplos de código do Service Fabric no GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster usando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI link"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

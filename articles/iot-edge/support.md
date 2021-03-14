---
title: Sistemas operacionais compatíveis, mecanismos de contêiner – Azure IoT Edge
description: Saiba quais sistemas operacionais podem executar o runtime e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: edca5a278414288ceb3c3d13f5d49e723e0ee5c2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615917"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

Este artigo fornece detalhes sobre quais sistemas e componentes têm suporte pelo IoT Edge, seja oficialmente ou em versão prévia.

## <a name="get-support"></a>Obter suporte

Se você tiver problemas ao usar o serviço de Azure IoT Edge, há várias maneiras de buscar o suporte. Experimente um dos seguintes canais para obter suporte:

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. Bugs relacionados a Azure IoT Edge para Linux no Windows podem ser relatados na [página problemas do iotedge-eFlow](https://github.com/azure/iotedge-eflow/issues). As correções fazem seu caminho rapidamente dos projetos para atualizações de produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contêiner

Os módulos do Azure IoT Edge são implementados como contêineres, de modo que a IoT Edge precise de um mecanismo de contêiner para inicializá-la. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Esse contêiner é baseado no projeto de software livre Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Também são baseados no projeto de software livre Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte ao esforço para sistemas que usam esses mecanismos de contêiner, no entanto, a Microsoft não podem enviar correções para problemas inerentes a esses mecanismos. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>

![O mecanismo Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais

O Azure IoT Edge executa na maioria dos sistemas operacionais que podem executar contêineres, no entanto, nem todos estes sistemas têm suporte de forma igual. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.

* Há suporte para sistemas da camada 1. Para sistemas da camada 1, a Microsoft:
  * tem esse sistema operacional em testes automatizados
  * fornece pacotes de instalação aos sistemas
* Os sistemas da Camada 2 são compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para sistemas de camada 2:
  * A Microsoft fez testes informais nas plataformas ou conhece um parceiro executando o Azure IoT Edge com êxito na plataforma
  * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas

A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. Ao usar Windows, haverá suporte apenas para contêineres isolados de processo e não para contêineres isolados do Hyper-V.  

IoT Edge para Linux no Windows usa IoT Edge em uma máquina virtual Linux em execução em um host do Windows. Dessa forma, você pode executar módulos do Linux em um dispositivo Windows.

### <a name="tier-1"></a>Camada 1

Os sistemas listados nas tabelas a seguir têm suporte da Microsoft, seja geralmente disponível ou em visualização pública, e são testados com cada nova versão.

O Azure IoT Edge dá suporte a módulos criados como contêineres Linux ou Windows. Os contêineres do Linux podem ser implantados em dispositivos Linux ou implantados em dispositivos Windows usando o IoT Edge para Linux no Windows. Os contêineres do Windows só podem ser implantados em dispositivos Windows.

#### <a name="linux-containers"></a>Contêineres do Linux

Módulos criados como contêineres do Linux podem ser implantados em dispositivos Linux ou Windows. Para dispositivos Linux, o tempo de execução do IoT Edge é instalado diretamente no dispositivo host. Para dispositivos Windows, uma máquina virtual Linux criada com o IoT Edge Runtime é executada no dispositivo host.

O [IOT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md) está atualmente em visualização pública, mas é a maneira recomendada para executar IOT Edge em dispositivos Windows.

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Stretch do sistema operacional Raspberry Pi |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Versão prévia pública |
| Windows 10 Pro | Versão prévia pública |  |  |
| Windows 10 Enterprise | Versão prévia pública |  |  |
| Windows 10 IoT Enterprise | Versão prévia pública |  |  |
| Windows Server 2019 | Versão prévia pública |  |  |

Todos os sistemas operacionais Windows devem ser da versão 1809 (Build 17763) ou posterior.

>[!NOTE]
>O suporte ao Ubuntu Server 16, 4 foi encerrado com o lançamento da versão 1,1 do IoT Edge.

#### <a name="windows-containers"></a>Contêineres do Windows

>[!IMPORTANT]
>IoT Edge 1,1 LTS é o último canal de lançamento que dará suporte a contêineres do Windows. A partir da versão 1,2, os contêineres do Windows não terão suporte. Considere usar ou mudar para [IOT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md) executar IOT Edge em dispositivos Windows.

Módulos criados como contêineres do Windows podem ser implantados somente em dispositivos Windows.

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Todos os sistemas operacionais Windows devem ser da versão 1809 (Build 17763). A compilação específica do Windows é necessária para IoT Edge no Windows porque a versão dos contêineres do Windows deve corresponder exatamente à versão do dispositivo Windows do host. Atualmente, os contêineres do Windows usam o Build 17763.

>[!NOTE]
>O suporte do Windows 10 IoT Core terminou com o lançamento do IoT Edge versão 1,1.

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela a seguir são considerados compatíveis com Azure IoT Edge, mas não são ativamente testados ou mantidos pela Microsoft.

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20, 4 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> as etapas de instalação do Ubuntu Server 18, 4 em [instalar ou desinstalar o Azure IOT Edge para Linux](how-to-install-iot-edge.md) devem funcionar sem nenhuma alteração no Ubuntu 20, 4.

## <a name="releases"></a>Lançamentos

IoT Edge ativos de lançamento e notas de versão estão disponíveis na página [versões do Azure-iotedge](https://github.com/Azure/azure-iotedge/releases). Esta seção reflete as informações dessas notas de versão para ajudá-lo a visualizar os componentes de cada versão com mais facilidade.

Os componentes do IoT Edge podem ser instalados ou atualizados individualmente, e são compatíveis com versões anteriores de componentes. A tabela a seguir lista os componentes incluídos em cada versão:

| Versão | Security daemon | Hub do Edge<br>Agente do Edge | Libiothsm | Moby |
|--|--|--|--|--|
| **1.1.0 LTS**<sup>1</sup> | 1.1.0 | 1.1.0 | 1.1.0 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1,1 é o primeiro canal de versão do LTS (suporte a longo prazo). Essa versão não introduziu nenhum recurso novo, mas receberá correções de bugs e patches de segurança. IoT Edge 1,1 LTS usa o .NET Core 3,1 e terá suporte até 3 de dezembro de 2022 para corresponder ao [ciclo de vida de versão do .NET Core e do .NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>Com o lançamento de um canal de suporte a longo prazo, recomendamos que todos os clientes atuais que executam o 1.0. x atualizem seus dispositivos para o 1.1. x para receber suporte contínuo.

IoT Edge usa o SDK Microsoft. Azure. Devices. Client. Para obter mais informações, consulte o [repositório GitHub do SDK C# de IoT do Azure](https://github.com/Azure/azure-iot-sdk-csharp) ou o [conteúdo de referência do SDK do Azure para .NET](/dotnet/api/overview/azure/iot/client). A lista a seguir mostra a versão do SDK do cliente para a qual cada versão é testada:

| Versão do IoT Edge | Versão do SDK do Microsoft. Azure. Devices. Client |
|------------------|--------------------------------------------|
| 1.1.0 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Máquinas Virtuais

O Azure IoT Edge pode ser executado em máquinas virtuais. O uso de uma máquina virtual como dispositivo IoT Edge é comum quando os clientes desejam aumentar a infraestrutura existente com inteligência de borda. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Esse requisito é o mesmo de quando o Azure IoT Edge é executado diretamente em um dispositivo. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema

O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. A escolha do hardware certo para o seu cenário depende das cargas de trabalho que você deseja executar. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. As perguntas que você deve considerar incluem:

* Quantos módulos estão em sua carga de trabalho?
* Quantas camadas os contêineres dos seus módulos compartilham?
* Em qual linguagem os módulos são gravados?
* Qual a quantidade de dados que seus módulos processarão?
* Seus módulos precisam de qualquer hardware especializado para acelerar suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?

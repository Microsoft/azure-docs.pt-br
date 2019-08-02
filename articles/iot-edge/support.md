---
title: Sistemas operacionais compatíveis, mecanismos de contêiner – Azure IoT Edge | Microsoft Docs
description: Saiba quais sistemas operacionais podem executar o tempo de execução e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 62b8ed553e3b4cec3750dae4f0426b6f0dd38855
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414394"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

Este artigo fornece detalhes sobre quais sistemas e componentes têm suporte pelo IoT Edge, seja oficialmente ou em versão prévia. 

Se você tiver problemas ao usar o serviço de Azure IoT Edge, há várias maneiras de buscar o suporte. Experimente um dos seguintes canais para obter suporte:

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contêiner

Azure IoT Edge módulos são implementados como contêineres, portanto IoT Edge precisa de um mecanismo de contêiner para iniciá-los. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Esse mecanismo de contêiner é baseado no projeto de código-fonte aberto do Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Eles também são baseados no projeto de código-fonte aberto do Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor esforço para sistemas que usam esses mecanismos de contêiner; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>

![Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais
Azure IoT Edge é executado na maioria dos sistemas operacionais que podem executar contêineres; no entanto, não há suporte igualmente para todos esses sistemas. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.
* Há suporte para sistemas da camada 1. Para sistemas da camada 1, a Microsoft:
    * tem esse sistema operacional em testes automatizados
    * fornece pacotes de instalação aos sistemas
* Os sistemas de camada 2 são compatíveis com Azure IoT Edge e podem ser usados relativamente facilmente. Para sistemas de camada 2:
    * A Microsoft fez testes ad hoc nas plataformas ou sabe que um parceiro executou Azure IoT Edge com êxito na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. Ao usar o Windows, somente os contêineres isolados de processo têm suporte, não contêineres isolados do Hyper-V.  

<br>
<center>

![O sistema operacional host corresponde ao SO convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Camada 1

Os sistemas listados na tabela a seguir têm suporte da Microsoft, seja geralmente disponível ou em visualização pública, e são testados com cada nova versão. 

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian-stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) | Versão prévia pública |
| Ubuntu Server 16.04 | ![Ubuntu Server 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Enterprise, Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, Build 17763 | Versão prévia pública |  |  |


Os sistemas operacionais Windows listados acima são os requisitos para dispositivos que executam contêineres do Windows no Windows. Essa configuração é a única configuração com suporte para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres do Linux no Windows; no entanto, essa configuração é apenas para desenvolvimento e teste. O uso de contêineres do Linux no Windows não é uma configuração com suporte para produção. Qualquer versão do Windows 10 build 14393 ou mais recente e do Windows Server 2016 ou mais recente pode ser usada para esse cenário de desenvolvimento.

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela a seguir são considerados compatíveis com Azure IoT Edge, mas não são ativamente testados ou mantidos. 

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| Wind River 8 | ![Vento Rio 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 sistemas, incluindo Raspian Buster, usam uma versão do OpenSSL que o IOT Edge não dá suporte. Use o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Máquinas virtuais
O Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como um dispositivo IoT Edge é comum quando os clientes desejam aumentar a infraestrutura existente com o Edge Intelligence. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Esse requisito é o mesmo que quando Azure IoT Edge é executado diretamente em um dispositivo. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. Escolher o hardware certo para seu cenário depende das cargas de trabalho que você deseja executar. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. As perguntas que você deve considerar incluem: 

* Quantos módulos estão em sua carga de trabalho?
* Quantas camadas os contêineres dos seus módulos compartilham?
* Em qual idioma os módulos são gravados? 
* Qual a quantidade de dados que seus módulos serão processando?
* Seus módulos precisam de qualquer hardware especializado para acelerar suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?

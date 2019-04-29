---
title: Sistemas operacionais compatíveis, mecanismos de contêiner – Azure IoT Edge | Microsoft Docs
description: Saiba quais sistemas operacionais podem executar o tempo de execução e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5bc133e81f9917aafb406a6bfb27922cdba48ef5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612211"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

Há várias maneiras de buscar suporte para o produto Azure IoT Edge.

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contêiner
O Azure IoT Edge precisa de um mecanismo de contêiner para iniciar os módulos, pois eles são implementados como contêineres. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Ele é baseado no projeto de software livre Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Também são baseados no projeto de software livre Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte ao esforço para sistemas que usam esses mecanismos de contêiner, no entanto, a Microsoft não tem capacidade de enviar correções para problemas inerentes a esses mecanismos. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>

![Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais
O Azure IoT Edge é executado na maioria dos sistemas operacionais que podem ser executados contêineres; Porém, todos esses sistemas não são igualmente suportados. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.
* Sistemas de camada 1 podem ser considerados como oficialmente com suporte. Para sistemas de camada 1, Microsoft:
    * tem esse sistema operacional em testes automatizados
    * fornece pacotes de instalação aos sistemas
* Os sistemas da Camada 2 podem ser considerados compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para sistemas de camada 2:
    * Microsoft realizou testes ad hoc nas plataformas ou sabe de um parceiro executado com êxito o Azure IoT Edge na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. Ao usar o Windows, o processo somente há suporte para contêineres isolados, não o Hyper-V isolado contêineres.  

<br>
<center>

![Sistema operacional do host corresponda ao sistema operacional convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Camada 1
Disponível para o público geral

| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Não  | Sim|
| Ubuntu Server 16.04 | Sim | Não  |
| Ubuntu Server 18.04 | Sim | Não  |

Versão prévia pública

| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Do Windows 10 IoT Core, build 17763 | Sim | Não  |
| Windows 10 IoT Enterprise, build 17763 | Sim | Não  |
| Windows Server 2019 | Sim | Não  |

Sistemas operacionais Windows listados acima são os requisitos para dispositivos que executam os contêineres do Windows no Windows. Essa configuração é a única configuração com suporte para a produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres do Linux no Windows; No entanto, essa configuração é somente para desenvolvimento e teste. O uso de contêineres do Linux no Windows não é uma configuração com suporte para produção. Qualquer versão do Windows 10 build 14393 ou mais recente e do Windows Server 2016 ou mais recente pode ser usada para esse cenário de desenvolvimento.

### <a name="tier-2"></a>Camada 2

| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Sim | Sim |
| Debian 8 | Sim | Sim |
| Debian 9 | Sim | Sim |
| RHEL 7.5 | Sim | Sim |
| Ubuntu 18.04 | Sim | Sim |
| Ubuntu 16.04 | Sim | Sim |
| Wind River 8 | Sim | Não  |
| Yocto | Sim | Não  |


## <a name="virtual-machines"></a>Máquinas Virtuais
O Azure IoT Edge pode ser executado em máquinas virtuais. Usando uma máquina virtual como uma borda de IoT dispositivo é comum quando os clientes desejam ampliar a infraestrutura existente com inteligência de borda. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Esse requisito é igual ao Azure IoT Edge é executado diretamente em um dispositivo. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>

![O Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. Escolher o hardware certo para seu cenário depende de cargas de trabalho que você deseja executar. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. Perguntas, que você deve considerar incluem: 

* Quantos módulos estão em sua carga de trabalho?
* Quantas camadas de contêineres dos seus módulos compartilhamento?
* Em quais idiomas os módulos são gravados? 
* A quantidade de dados seus módulos processará?
* Os módulos precisa de qualquer hardware especializado para acelerar suas cargas de trabalho?
* Quais são as características de desempenho desejado da sua solução?
* O que é seu orçamento de hardware?

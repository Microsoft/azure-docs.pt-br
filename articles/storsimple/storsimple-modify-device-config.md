---
title: "Modificar a configuração do dispositivo StorSimple | Microsoft Docs"
description: "Descreve como usar o serviço StorSimple Manager para reconfigurar um dispositivo StorSimple que já foi implantado."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: v-sharos
ms.openlocfilehash: 5bfbeb038dbedae2bf77016abbc19458c3dc22c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Usar o serviço StorSimple Manager para modificar a configuração do dispositivo StorSimple
## <a name="overview"></a>Visão geral
A página **Configurar** do Portal clássico do Azure contém todos os parâmetros de dispositivo que você pode reconfigurar em um dispositivo StorSimple gerenciado por um serviço StorSimple Manager. Este tutorial explica como você pode usar a página **Configurar** para realizar as seguintes tarefas em nível do dispositivo:

* Modificar as configurações de dispositivo 
* Modificar as configurações de tempo 
* Modificar as configurações de DNS 
* Modificar as interfaces de rede
* Alternar ou reatribuir IPs

## <a name="modify-device-settings"></a>Modificar as configurações de dispositivo
As configurações de dispositivo incluem o nome amigável do dispositivo e a descrição do dispositivo.

> [!NOTE] 
> Não é possível modificar o nome do dispositivo no portal clássico do Azure. Não há suporte para renomear o dispositivo.

Um dispositivo StorSimple que está conectado ao serviço StorSimple Manager recebe um nome padrão. O nome padrão normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo padrão que é de 15 caracteres, como 8600-SHX0991003G44HT, indica o seguinte:

* **8600** – Indica o modelo do dispositivo.
* **SHX** – Indica o site de produção.
* **0991003** - Indica um produto específico.
* **G44HT**- Os últimos cinco dígitos são aumentados para criar números de série exclusivos. Isso pode não ser um conjunto sequencial.

É possível especificar uma descrição de dispositivo. Uma descrição de dispositivo geralmente ajuda a identificar o proprietário e o local físico do dispositivo. O campo de descrição deve conter menos de 256 caracteres.

## <a name="modify-time-settings"></a>Modificar as configurações de tempo
O dispositivo deve sincronizar a hora para autenticar com seu provedor de serviços de armazenamento em nuvem. Selecione seu fuso horário na lista suspensa e especifique até dois servidores NTP (Protocolo de Tempo de Rede). O servidor NTP primário é necessário e é especificado quando você usa o Windows PowerShell para StorSimple para configurar seu dispositivo. É possível especificar o **time.windows.com** padrão do Windows Server como o servidor NTP. É possível exibir a configuração do servidor NTP primário por meio do Portal Clássico do Azure, mas é preciso usar a interface do Windows PowerShell para alterá-la.

A configuração do servidor NTP secundário é opcional. É possível usar o Portal Clássico para configurar um servidor NTP secundário. 

Ao configurar o servidor NTP, verifique se sua rede permite que o tráfego NTP passe do datacenter para a Internet. Ao especificar um servidor NTP público, é preciso garantir que os firewalls de rede e outros dispositivos de segurança estejam configurados para permitir que o tráfego NTP viaje a partir da rede externa e para ela. Se o tráfego NTP bidirecional não for permitido, será preciso usar um servidor NTP interno (um controlador de domínio do Windows oferece essa função). Se o dispositivo não puder sincronizar a hora, talvez não seja possível se comunicar com o provedor de armazenamento em nuvem.

Para ver uma lista de servidores NTP públicos, vá até o [NTP Servers Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implantado em um fuso horário diferente?
Se o dispositivo for implantado em um fuso horário diferente, o fuso horário do dispositivo será alterado. Considerando que todas as políticas de backup usam o fuso horário do dispositivo, as políticas de backup se ajustarão automaticamente de acordo com o novo fuso horário. Nenhuma intervenção do usuário é necessária.

## <a name="modify-dns-settings"></a>Modificar as configurações de DNS
Um servidor DNS é usado quando o dispositivo tenta se comunicar com seu provedor de serviços de armazenamento em nuvem. Para obter alta disponibilidade, é preciso configurar os servidores DNS primário e secundário durante a implantação inicial do dispositivo. Para reconfigurar o servidor DNS primário, será preciso usar a interface do Windows PowerShell em seu dispositivo StorSimple.

Para modificar o servidor DNS secundário, é possível usar o Portal clássico do Azure.

## <a name="modify-network-interfaces"></a>Modificar as interfaces de rede
O dispositivo tem seis interfaces de rede, quatro delas são de 1 GbE e duas são de 10 GbE. Essas interfaces são rotuladas como DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 e DATA 5 são de 1 GbE, enquanto DATA 2 e DATA 3 são interfaces de rede de 10 GbE.

Configure **Configurações de Interface de Rede** para cada uma das interfaces a serem usadas. Para garantir alta disponibilidade, recomendamos que você tenha pelo menos duas interfaces iSCSI e duas interfaces habilitadas para a nuvem em seu dispositivo. É recomendável, mas não obrigatório, que as interfaces não utilizadas sejam desabilitadas.

Quando você configurar qualquer uma das interfaces de rede, deverá configurar um IP virtual (VIP).

DATA 0 é habilitada para nuvem por padrão. Ao configurar DATA 0, também é necessário configurar dois endereços IP fixos, um para cada controlador. Esses endereços IP fixos podem ser usados para acessar diretamente os controladores de dispositivo e são úteis quando você instala atualizações no dispositivo ou quando acessa os controladores para fins de solução de problemas.

No StorSimple 8000 Series Update 1, a métrica de roteamento de DATA 0 é definida como a menor; portanto, se o dispositivo estiver executando o StorSimple 8000 Series Update 1, todo o tráfego de nuvem será roteado através de DATA 0. Anote isso se houver mais de uma interface de rede habilitada para nuvem em seu dispositivo StorSimple.

> [!NOTE]
> Os endereços IP fixos do controlador são usados para atender às atualizações do dispositivo. Portanto, os IPs fixos devem ser roteáveis e conseguir se conectar à Internet.
> 
> 

Para cada interface de rede, os parâmetros a seguir são exibidos:

* **Velocidade** – Não é um parâmetro configurável pelo usuário. DATA 0, DATA 1, DATA 4 e DATA 5 são sempre de 1 GbE, enquanto DATA 2 e DATA 3 são interfaces de 10 GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre negociados automaticamente. Não há suporte para quadros Jumbo.
  > 
  > 
* **Estado da interface** – Uma interface pode ser habilitada ou desabilitada. Se for habilitada, o dispositivo tentará usar a interface. É recomendável que apenas as interfaces que estejam conectadas à rede e sendo usadas sejam habilitadas. Desabilite qualquer interface que não estiver em uso.
* **Tipo de interface** – Este parâmetro permite isolar o tráfego iSCSI do tráfego do armazenamento em nuvem. Esse parâmetro pode ser um dos seguintes:
  
  * **Habilitado para nuvem** – quando habilitado, o dispositivo usará essa interface para se comunicar com a nuvem.
  * **Habilitado para iSCSI** – quando habilitado, o dispositivo usará essa interface para se comunicar com o host iSCSI.
    
    É recomendável isolar o tráfego iSCSI do tráfego do armazenamento em nuvem. Também observe que, se o host estiver na mesma sub-rede que o dispositivo, não será preciso atribuir um gateway; no entanto, se o host estiver em uma sub-rede diferente de seu dispositivo, será preciso atribuir um gateway.
* **Endereço IP** – Pode ser IPv4, IPv6 ou ambos. As famílias de endereço IPv4 e IPv6 são compatíveis com as interfaces de rede do dispositivo. Ao usar o IPv4, especifique um endereço IP de 32 bits (*xxx.xxx.xxx.xxx*) em notação de ponto decimal. Ao usar o IPv6, basta fornecer um prefixo de quatro dígitos, e um endereço de 128 bits será gerado automaticamente para a interface de rede do dispositivo com base nesse prefixo.
* **Sub-rede** – Refere-se à máscara de sub-rede e é configurada por meio da interface do Windows PowerShell.
* **Gateway** – Este é o gateway padrão que deve ser usado por esta interface quando ele tenta se comunicar com nós que não estão no mesmo espaço do endereço IP (sub-rede). O gateway padrão deve estar no mesmo espaço do endereço (sub-rede) que a interface do endereço IP, conforme determinado pela máscara de sub-rede.
* **Endereço IP fixo** – Este campo está disponível apenas enquanto você configura a interface DATA 0. Para operações como atualizações ou solução de problemas do dispositivo, talvez seja necessário conectar diretamente ao controlador do dispositivo. O endereço IP fixo pode ser usado para acessar o controlador ativo e passivo no dispositivo.

É possível reconfigurar o Controlador 0 e o Controlador 1 por meio do Portal Clássico do Azure.

> [!NOTE]
> * Para garantir a operação correta, verifique a velocidade da interface e o duplex no comutador ao qual cada interface de dispositivo está conectada. As interfaces de comutador devem negociar com Gigabit Ethernet (1000 Mbps) ou serem configuradas para ela e devem ser full duplex. Interfaces que operam em velocidades menores ou em half-duplex causarão problemas de desempenho.
> * Para minimizar interrupções e o tempo de inatividade, recomendamos que você habilite o portfast em cada uma das portas do comutador ao qual a interface de rede iSCSI do dispositivo estará conectada. Isso garantirá que a conectividade de rede possa ser estabelecida rapidamente no caso de um failover.
> 
> 

## <a name="swap-or-reassign-ips"></a>Alternar ou reatribuir IPs
Atualmente, se alguma interface de rede do controlador receber um VIP que está em uso (pelo mesmo dispositivo ou por outro dispositivo de rede), o controlador fará failover. Portanto, será necessário seguir o procedimento adequado se você estiver alternando VIPs para a interface de rede do dispositivo, pois criará uma situação de IP duplicado.

Realize as etapas a seguir para alternar ou reatribuir os VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs
1. Limpe o endereço IP para ambas as interfaces.
2. Depois que os endereços IP estiverem limpos, atribua novos endereços IP às respectivas interfaces.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [configurar o MPIO para seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).


---
title: Modificar a configuração do dispositivo StorSimple série 8000 | Microsoft Docs
description: Descreve como usar o serviço Gerenciador de Dispositivos do StorSimple para reconfigurar um dispositivo StorSimple que já foi implantado.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632019"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Usar o serviço Gerenciador de Dispositivos do StorSimple para modificar a configuração do dispositivo StorSimple

## <a name="overview"></a>Visão geral

A seção **Configurações de dispositivo** do Portal do Azure na folha **Configurações** contém todos os parâmetros de dispositivo que podem ser reconfigurados em um dispositivo StorSimple gerenciado por um serviço Gerenciador de Dispositivos do StorSimple. Este tutorial explica como é possível usar a folha **Configurações** para realizar as seguintes tarefas em nível do dispositivo:

* Modificar o nome amigável do dispositivo
* Modificar as configurações de hora do dispositivo
* Atribuir um DNS secundário
* Modificar as interfaces de rede
* Alternar ou reatribuir IPs

## <a name="modify-device-friendly-name"></a>Modificar o nome amigável do dispositivo

É possível usar o Portal do Azure para alterar o nome do dispositivo e atribuir a ele um nome amigável exclusivo de sua escolha. Use a folha **Configurações gerais** em seu dispositivo para modificar o nome amigável do dispositivo. O nome amigável pode conter qualquer caractere e ter no máximo 64 caracteres.

> [!NOTE] 
> Só será possível modificar o nome do dispositivo no Portal do Azure antes que a instalação do dispositivo for concluída. Quando a instalação mínima do dispositivo for concluída, não será possível alterar o nome do dispositivo.

![Nome do dispositivo nas Configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Um dispositivo StorSimple conectado ao serviço do Gerenciador de Dispositivos do StorSimple foi atribuído a um nome padrão. O nome padrão normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo padrão que é de 15 caracteres, como 8600-SHX0991003G44HT, indica o seguinte:

* **8600** – Indica o modelo do dispositivo.
* **SHX** – Indica o site de produção.
* **0991003** - Indica um produto específico.
* **G44HT**- Os últimos cinco dígitos são aumentados para criar números de série exclusivos. Isso pode não ser um conjunto sequencial.

## <a name="modify-device-description"></a>Modificar a descrição do dispositivo

Use a folha **Configurações gerais** em seu dispositivo para modificar a descrição do dispositivo.

![Descrição do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Uma descrição de dispositivo geralmente ajuda a identificar o proprietário e o local físico do dispositivo. O campo de descrição deve conter menos de 256 caracteres.

## <a name="modify-time-settings"></a>Modificar as configurações de tempo

O dispositivo deve sincronizar a hora para autenticar com seu provedor de serviços de armazenamento em nuvem. Use a folha **Configurações gerais** em seu dispositivo para modificar as configurações de hora do dispositivo.

![Descrição do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecione seu fuso horário na lista suspensa. É possível especificar até dois servidores NTP (protocolo NTP):

 - **Servidor NTP primário** – A configuração é necessária e é especificada quando você usa o Windows PowerShell para StorSimple para configurar seu dispositivo. É possível especificar o **time.windows.com** padrão do Windows Server como o servidor NTP. É possível exibir a configuração do servidor NTP primário por meio do Portal do Azure, mas é necessário usar a interface do Windows PowerShell para alterá-la. Use o cmdlet `Set-HcsNTPClientServerAddress` para modificar o servidor NTP primário do seu dispositivo. Para obter mais informações, procure o cmdlet [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) em syntax.

- **Servidor NTP secundário** – A configuração é opcional. É possível usar o portal para configurar um servidor NTP secundário.

Ao configurar o servidor NTP, verifique se sua rede permite que o tráfego NTP passe do datacenter para a Internet. Ao especificar um servidor NTP público, é preciso garantir que os firewalls de rede e outros dispositivos de segurança estejam configurados para permitir que o tráfego NTP viaje a partir da rede externa e para ela. Se o tráfego NTP bidirecional não for permitido, será preciso usar um servidor NTP interno (um controlador de domínio do Windows oferece essa função). Se o dispositivo não puder sincronizar a hora, talvez não seja possível se comunicar com o provedor de armazenamento em nuvem.

Para ver uma lista de servidores NTP públicos, vá até o [NTP Servers Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implantado em um fuso horário diferente?

Se o dispositivo for implantado em um fuso horário diferente, o fuso horário do dispositivo será alterado. Considerando que todas as políticas de backup usam o fuso horário do dispositivo, as políticas de backup se ajustarão automaticamente de acordo com o novo fuso horário. Nenhuma intervenção do usuário é necessária.

## <a name="modify-dns-settings"></a>Modificar as configurações de DNS

Um servidor DNS é usado quando o dispositivo tenta se comunicar com seu provedor de serviços de armazenamento em nuvem. Use a folha **Configurações de rede** em seu dispositivo para exibir e modificar as configurações do DNS configurado. 

![Configurações de DNS nas Configurações de rede](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para obter alta disponibilidade, é preciso configurar os servidores DNS primário e secundário durante a implantação inicial do dispositivo.

**Servidor DNS primário** – Use o Windows PowerShell para StorSimple para especificar primeiro o servidor DNS primário durante a instalação inicial. É possível reconfigurar o servidor DNS primário somente por meio da interface do Windows PowerShell. Use o cmdlet `Set-HcsDNSClientServerAddress` para modificar o servidor DNS primário do seu dispositivo. Para obter mais informações, procure o cmdlet [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) em syntax.

**Servidor DNS secundário** – Para modificar o servidor DNS secundário, use o cmdlet `Set-HcsDNSClientServerAddress` na interface do Windows PowerShell do dispositivo ou a folha **Configurações de rede** do seu dispositivo StorSimple no Portal do Azure.

Para modificar o servidor DNS secundário no Portal do Azure, siga as etapas abaixo.

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na lista de dispositivos, selecione e clique em seu dispositivo.

2. Na folha **Configurações**, acesse **Configurações do dispositivo > Rede**. Isso abrirá a folha **Configurações gerais**. Clique no bloco **Configurações de DNS**. Modifique o endereço IP do servidor DNS secundário.

    ![Modifique o endereço IP do servidor DNS secundário](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na barra de comandos, clique em **Salvar** e, quando for solicitado a confirmar, clique em **OK**.

    ![Salvar e confirmar as alterações](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar as interfaces de rede

O dispositivo tem seis interfaces de rede, quatro delas são de 1 GbE e duas são de 10 GbE. Essas interfaces são rotuladas como DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 e DATA 5 são de 1 GbE, enquanto DATA 2 e DATA 3 são interfaces de rede de 10 GbE.

Use a folha **Configurações de rede** para configurar cada uma das interfaces a serem usadas.

![Configurar adaptadores de rede por meio de Configurações de rede](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantir alta disponibilidade, recomendamos que você tenha pelo menos duas interfaces iSCSI e duas interfaces habilitadas para a nuvem em seu dispositivo. É recomendável, mas não obrigatório, que as interfaces não utilizadas sejam desabilitadas.

Para cada interface de rede, os parâmetros a seguir são exibidos:

* **Velocidade** – Não é um parâmetro configurável pelo usuário. DATA 0, DATA 1, DATA 4 e DATA 5 são sempre de 1 GbE, enquanto DATA 2 e DATA 3 são interfaces de 10 GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre negociados automaticamente. Não há suporte para quadros Jumbo.
  
* **Estado da interface** – Uma interface pode ser habilitada ou desabilitada. Se for habilitada, o dispositivo tentará usar a interface. É recomendável que apenas as interfaces que estejam conectadas à rede e sendo usadas sejam habilitadas. Desabilite qualquer interface que não estiver em uso.
* **Tipo de interface** – Este parâmetro permite isolar o tráfego iSCSI do tráfego do armazenamento em nuvem. Esse parâmetro pode ser um dos seguintes:
  
  * **Habilitado para nuvem** – quando habilitado, o dispositivo usará essa interface para se comunicar com a nuvem.
  * **Habilitado para iSCSI** – quando habilitado, o dispositivo usará essa interface para se comunicar com o host iSCSI.
    
    É recomendável isolar o tráfego iSCSI do tráfego do armazenamento em nuvem. Também observe que, se o host estiver na mesma sub-rede que o dispositivo, não será preciso atribuir um gateway; no entanto, se o host estiver em uma sub-rede diferente de seu dispositivo, será preciso atribuir um gateway.
* **Endereço IP** – Quando você configurar qualquer um dos adaptadores de rede, será necessário configurar um VIP (IP virtual). Ele poderá ser IPv4 ou IPv6 ou ambos. As famílias de endereço IPv4 e IPv6 são compatíveis com as interfaces de rede do dispositivo. Ao usar o IPv4, especifique um endereço IP de 32 bits (*xxx.xxx.xxx.xxx*) em notação de ponto decimal. Ao usar o IPv6, basta fornecer um prefixo de quatro dígitos, e um endereço de 128 bits será gerado automaticamente para a interface de rede do dispositivo com base nesse prefixo.
* **Sub-rede** – Refere-se à máscara de sub-rede e é configurada por meio da interface do Windows PowerShell.
* **Gateway** – Este é o gateway padrão que deve ser usado por esta interface quando ele tenta se comunicar com nós que não estão no mesmo espaço do endereço IP (sub-rede). O gateway padrão deve estar no mesmo espaço do endereço (sub-rede) que a interface do endereço IP, conforme determinado pela máscara de sub-rede.
* **Endereço IP fixo** – Este campo está disponível apenas enquanto você configura a interface DATA 0. Para operações como atualizações ou solução de problemas do dispositivo, talvez seja necessário conectar diretamente ao controlador do dispositivo. O endereço IP fixo pode ser usado para acessar o controlador ativo e passivo no dispositivo.

> [!NOTE]
> * Para garantir a operação correta, verifique a velocidade da interface e o duplex no comutador ao qual cada interface de dispositivo está conectada. As interfaces de comutador devem negociar com Gigabit Ethernet (1000 Mbps) ou serem configuradas para ela e devem ser full duplex. Interfaces que operam em velocidades menores ou em half-duplex causarão problemas de desempenho.
> * Para minimizar interrupções e o tempo de inatividade, recomendamos que você habilite o portfast em cada uma das portas do comutador ao qual a interface de rede iSCSI do dispositivo estará conectada. Isso garantirá que a conectividade de rede possa ser estabelecida rapidamente no caso de um failover.

### <a name="configure-data-0"></a>Configurar DATA 0

DATA 0 é habilitada para nuvem por padrão. Ao configurar DATA 0, também é necessário configurar dois endereços IP fixos, um para cada controlador. Esses endereços IP fixos podem ser usados para acessar diretamente os controladores de dispositivo e são úteis quando você instala atualizações no dispositivo para a coleta de lixo funcionar corretamente ou quando acessa os controladores para fins de solução de problemas.

É possível reconfigurar os controladores fixos de IP por meio da folha de configurações DATA 0.

![Configurar adaptador de rede – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Os endereços IP fixos do controlador são usados para atender às atualizações do dispositivo e para que algoritmos de recuperação de espaço (coleta de lixo) funcionem corretamente. Portanto, os IPs fixos devem ser roteáveis e conseguir se conectar à Internet.

### <a name="configure-data-1---data-5"></a>Configurar DATA 1 – DATA 5

Para os adaptadores de rede DATA 1 – DATA 5, é possível configurar todas as configurações de rede conforme mostrado no instantâneo a seguir:

![Configurar os adaptadores de rede DATA 1 – DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Alternar ou reatribuir IPs

Atualmente, se alguma interface de rede do controlador receber um VIP que está em uso (pelo mesmo dispositivo ou por outro dispositivo de rede), o controlador fará failover. Se você trocar ou reatribuir VIPs para um adaptador de rede do dispositivo, será necessário seguir um procedimento adequado como se você pudesse criar uma situação de IP duplicada.

Realize as etapas a seguir para alternar ou reatribuir os VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs

1. Limpe o endereço IP para ambas as interfaces.
2. Depois que os endereços IP estiverem limpos, atribua novos endereços IP às respectivas interfaces.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [configurar o MPIO para seu dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


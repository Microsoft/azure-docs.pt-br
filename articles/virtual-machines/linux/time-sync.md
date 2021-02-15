---
title: Sincronização de tempo para VMs Linux no Azure
description: Sincronização de tempo para máquinas virtuais Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 399022c1ef740865e4b2f7b82e2175e748a2a925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306949"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronização de tempo para VMs Linux no Azure

A sincronização de Data/Hora é importante para segurança e correlação de eventos. Às vezes, é usada para implementação de transações distribuídas. A precisão da hora entre vários sistemas de computador é obtida por meio da sincronização. A sincronização pode ser afetada por vários motivos, incluindo reinicializações e tráfego entre a fonte de tempo e o computador buscando a hora. 

O Azure é apoiado pela infraestrutura que executa o Windows Server 2016. O Windows Server 2016 aprimorou os algoritmos usados para corrigir a hora e condicionar o relógio local a sincronizar com o UTC.  O recurso Windows Server 2016 Accurate Time melhorou muito a forma como o serviço VMICTimeSync controla as VMs com o host para o tempo exato. Os aprimoramentos incluem um tempo inicial mais preciso no início da VM ou na restauração da VM e interrupção da correção de latência. 

> [!NOTE]
> Para obter uma visão geral rápida do Serviço de Horário do Windows, assista a este [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [Hora precisa para Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Visão geral

A precisão de um relógio de computador é medida na proximidade do relógio do computador com o padrão de hora UTC (Tempo Universal Coordenado). O UTC é definido por uma amostra multinacional de relógios atômicos precisos que só pode ser desligada em um segundo em 300 anos. Mas ler o UTC diretamente requer um hardware especializado. Em vez disso, os servidores de horário são sincronizados com o UTC e acessados de outros computadores para fornecer escalabilidade e robustez. Todo computador tem o serviço de sincronização de tempo em execução que sabe a que horas os servidores devem ser usados e verifica periodicamente se o relógio do computador precisa ser corrigido e ajusta o tempo, se necessário. 

Os hosts do Azure são sincronizados para servidores de horário internos da Microsoft que usam dispositivos da Microsoft da Stratum 1, com antenas de GPS. As máquinas virtuais no Azure podem depender do host para passar a hora exata (*hora do host*) para a VM ou a VM pode obter a hora diretamente de um servidor de horário ou uma combinação de ambos. 

No hardware autônomo, o sistema operacional Linux só lê o relógio de hardware do host na inicialização. Depois disso, o relógio é mantido usando o timer de interrupção no kernel do Linux. Nesta configuração, o relógio se deslocará com o tempo. Em distribuições mais recentes do Linux no Azure, as VMs podem usar o provedor VMICTimeSync, incluído nos serviços de integração do Linux (LIS), para consultar atualizações de relógio do host com mais frequência.

As interações da máquina virtual com o host também podem afetar o relógio. Durante a manutenção de [memória preservando a manutenção](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), as VMs são pausadas por até 30 segundos. Por exemplo, antes de iniciar a manutenção, o relógio da VM exibe as 10:00:00 e dura 28 segundos. Depois que a VM for retomada, o relógio na VM ainda mostrará 10:00:00 AM, o que seria 28 segundos de folga. Para corrigir isso, o serviço VMICTimeSync monitora o que está acontecendo no host e solicita que as alterações ocorram nas VMs para compensar.

Sem a sincronização de data/hora, o relógio na VM acumularia erros. Quando há apenas uma VM, o efeito pode não ser significativo, a menos que a carga de trabalho exija uma cronometragem altamente precisa. Mas na maioria dos casos, temos várias VMs interconectadas que usam o tempo para rastrear transações, e a hora precisa ser consistente durante toda a implantação. Quando a hora entre as VMs for diferente, você poderá ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificado requerem que a hora esteja consistente em todos os sistemas.
- É muito difícil descobrir o que aconteceu em um sistema se os logs (ou outros dados) não corresponderem com a hora. O mesmo evento poderia parecer que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a cobrança poderá ser calculada incorretamente.



## <a name="configuration-options"></a>Opções de configuração

Geralmente, há três maneiras de configurar a sincronização de horário para suas VMs Linux hospedadas no Azure:

- A configuração padrão das imagens do Azure Marketplace usa tempo NTP e tempo de host VMICTimeSync. 
- Somente host usando o VMICTimeSync.
- Use outro servidor de horário externo com ou sem o uso do tempo de host VMICTimeSync.


### <a name="use-the-default"></a>Usar o padrão

Por padrão, a maioria das imagens do Azure Marketplace para Linux é configurada para sincronização de duas origens: 

- NTP como primário, que recebe tempo de um servidor NTP. Por exemplo, as imagens do Ubuntu 16.04 LTS Marketplace usam **ntp.ubuntu.com**.
- O serviço VMICTimeSync como secundário, usado para comunicar o horário do host para as VMs e fazer correções após a VM ser pausada para manutenção. Os hosts do Azure usam dispositivos Stratum 1 da Microsoft para manter a hora exata.

Em distribuições mais recentes do Linux, o serviço VMICTimeSync fornece uma fonte de relógio de hardware de protocolo PTP, mas as distribuições anteriores podem não fornecer essa fonte de relógio e voltarão ao NTP para obter tempo do host.

Para confirmar que o NTP está sincronizando corretamente, execute o comando `ntpq -p`.

### <a name="host-only"></a>Somente do host 

Como os servidores NTP como time.windows.com e ntp.ubuntu.com são públicos, sincronizar o tempo com eles requer o envio de tráfego pela Internet. Os atrasos de pacotes variados podem afetar negativamente a qualidade da sincronização de tempo. A remoção do NTP alternando para a sincronização somente de host pode às vezes melhorar o tempo de sincronização dos resultados.

Alternar para a sincronização de data/hora somente do host será viável se você tiver problemas de sincronização de data/hora usando a configuração padrão. Experimente a sincronização somente do host para ver se isso melhoraria a sincronização de tempo na sua VM. 

### <a name="external-time-server"></a>Servidor de horário externo

Se você tiver requisitos específicos de sincronização de data/hora, também haverá uma opção de usar servidores de horário externos. Servidores de horário externos podem fornecer hora específica, o que pode ser útil para cenários de teste, garantindo uniformidade de hora com máquinas hospedadas em datacenters que não sejam da Microsoft ou lidando com segundos bissextos de uma maneira especial.

Você pode combinar um servidor de horário externo com o serviço VMICTimeSync para fornecer resultados semelhantes à configuração padrão. A combinação de um servidor de horário externo com o VMICTimeSync é a melhor opção para lidar com problemas que podem ser causados quando as VMs são pausadas para manutenção. 

## <a name="tools-and-resources"></a>Ferramentas e recursos

Há alguns comandos básicos para verificar sua configuração de sincronização de tempo. A documentação para distribuição do Linux terá mais detalhes sobre a melhor maneira de configurar a sincronização de horário para essa distribuição.

### <a name="integration-services"></a>Serviços de integração

Verifique se o serviço de integração (hv_utils) está carregado.

```bash
lsmod | grep hv_utils
```
Você deve ver algo semelhante a isto:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Veja se o daemon de serviços de integração do Hyper-V está sendo executado.

```bash
ps -ef | grep hv
```

Você deve ver algo semelhante a isto:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Verificar origem do relógio PTP

Com versões mais recentes do Linux, uma fonte de relógio do Precision Time Protocol (PTP) está disponível como parte do provedor VMICTimeSync. Em versões mais antigas do Red Hat Enterprise Linux ou CentOS 7.x, o [ Linux Integration Services ](https://github.com/LIS/lis-next) pode ser baixado e usado para instalar o driver atualizado. Quando a origem do relógio do PTP estiver disponível, o dispositivo Linux será do formato/dev/PTP*x*. 

Veja quais fontes de relógio PTP estão disponíveis.

```bash
ls /sys/class/ptp
```

Neste exemplo, o valor retornado é *ptp0*, portanto, usamos isso para verificar o nome do relógio. Para verificar o dispositivo, verifique o nome do relógio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Isso deve retornar `hyperv`.

### <a name="chrony"></a>chrony

No Ubuntu 19,10 e versões posteriores, Red Hat Enterprise Linux e CentOS 8. x, o [chrony](https://chrony.tuxfamily.org/) é configurado para usar um relógio de origem do PTP. Em vez de chrony, as versões mais antigas do Linux usam o daemon de protocolo NTP (ntpd), que não dá suporte a fontes PTP. Para habilitar o PTP nessas versões, chrony deve ser instalado e configurado manualmente (em chrony. conf) usando o seguinte código:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Para obter mais informações sobre o Ubuntu e o NTP, consulte [sincronização de horário](https://ubuntu.com/server/docs/network-ntp).

Para obter mais informações sobre o Red Hat e o NTP, consulte [Configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Para obter mais informações sobre chrony, consulte [usando o chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Se as fontes chrony e VMICTimeSync forem habilitadas simultaneamente, você poderá marcar uma como **preferir**, que define a outra fonte como um backup. Como os serviços NTP não atualizam o relógio para grandes distorções, exceto após um longo período, o VMICTimeSync recuperará o relógio de eventos de VM pausados muito mais rapidamente do que as ferramentas baseadas em NTP.

Por padrão, o chronyd acelera ou reduz o relógio do sistema para corrigir qualquer descompasso de tempo. Se o descompasso se tornar muito grande, o chrony falhará ao corrigir o descompasso. Para superar isso, o `makestep` parâmetro em **/etc/chrony.conf** pode ser alterado para forçar uma sincronização de horário se a descompasso exceder o limite especificado.

 ```bash
makestep 1.0 -1
```

Aqui, o chrony forçará uma atualização de tempo se a descompasso for maior que 1 segundo. Para aplicar as alterações, reinicie o serviço chronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

Em versões SUSE e Ubuntu anteriores a 19,10, a sincronização de horário é configurada usando o [sistema](https://www.freedesktop.org/wiki/Software/systemd/). Para obter mais informações sobre o Ubuntu, consulte [sincronização de horário](https://help.ubuntu.com/lts/serverguide/NTP.html). Para obter mais informações sobre o SUSE, consulte a seção 4.5.8 em [notas de versão do SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Hora precisa para Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).



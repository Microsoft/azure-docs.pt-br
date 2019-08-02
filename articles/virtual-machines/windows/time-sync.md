---
title: Sincronização de Data/Hora para VMs do Windows no Azure| Microsoft Docs
description: Sincronização de Data/Hora para máquinas virtuais do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: d413fe73735f526444aea76d68f44163065578a0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710242"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronização de Data/Hora para VMs do Windows no Azure

A sincronização de Data/Hora é importante para segurança e correlação de eventos. Às vezes, é usada para implementação de transações distribuídas. A precisão da hora entre vários sistemas de computador é obtida por meio da sincronização. A sincronização pode ser afetada por vários motivos, incluindo reinicializações e tráfego entre a fonte de tempo e o computador buscando a hora. 

O Azure agora é apoiado pela infraestrutura que executa o Windows Server 2016. O Windows Server 2016 aprimorou os algoritmos usados para corrigir a hora e condicionar o relógio local a sincronizar com o UTC.  O Windows Server 2016 também aprimorou o serviço VMICTimeSync, que determina como as VMs são sincronizadas com o host para uma hora precisa. Os aprimoramentos incluem um tempo inicial mais preciso no início da VM ou na restauração da VM e interrupção da correção de latência para exemplos fornecidos para o Horário do Windows (W32Time). 


>[!NOTE]
>Para uma rápida visão geral do Serviço de Tempo do Windows, dê uma olhada neste [vídeo de visão geral de alto nível](https://aka.ms/WS2016TimeVideo).
>
> Para obter mais informações, consulte [Hora precisa para Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Visão geral

A precisão de um relógio de computador é medida na proximidade do relógio do computador com o padrão de hora UTC (Tempo Universal Coordenado). O UTC é definido por uma amostra multinacional de relógios atômicos precisos que só pode ser desligada em um segundo em 300 anos. Mas ler o UTC diretamente requer um hardware especializado. Em vez disso, os servidores de horário são sincronizados com o UTC e acessados de outros computadores para fornecer escalabilidade e robustez. Todo computador tem o serviço de sincronização de tempo em execução que sabe a que horas os servidores devem ser usados e verifica periodicamente se o relógio do computador precisa ser corrigido e ajusta o tempo, se necessário. 

Os hosts do Azure são sincronizados para servidores de horário internos da Microsoft que usam dispositivos da Microsoft da Stratum 1, com antenas de GPS. As máquinas virtuais no Azure podem depender do host para passar a hora exata (*hora do host*) para a VM ou a VM pode obter a hora diretamente de um servidor de horário ou uma combinação de ambos. 

As interações da máquina virtual com o host também podem afetar o relógio. Durante a manutenção de [memória preservando a manutenção](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), as VMs são pausadas por até 30 segundos. Por exemplo, antes de iniciar a manutenção, o relógio da VM exibe as 10:00:00 e dura 28 segundos. Depois que a VM for retomada, o relógio na VM ainda mostrará 10:00:00 AM, o que seria 28 segundos de folga. Para corrigir isso, o serviço VMICTimeSync monitora o que está acontecendo no host e solicita que as alterações ocorram nas VMs para compensar.

O serviço VMICTimeSync opera no modo de amostra ou de sincronização e só influenciará o relógio para frente. No modo de amostra, que requer a execução do W32time, o serviço VMICTimeSync pesquisa o host a cada 5 segundos e fornece amostras de tempo para o W32time. Aproximadamente a cada 30 segundos, o serviço W32time pega a última amostra de tempo e a utiliza para influenciar o relógio do hóspede. O modo de sincronização é ativado se um convidado for retomado ou se o relógio de um visitante se deslocar mais de 5 segundos após o relógio do host. Nos casos em que o serviço W32time está sendo executado corretamente, o último caso nunca deve acontecer.

Sem a sincronização de data/hora, o relógio na VM acumularia erros. Quando há apenas uma VM, o efeito pode não ser significativo, a menos que a carga de trabalho exija uma cronometragem altamente precisa. Mas na maioria dos casos, temos várias VMs interconectadas que usam o tempo para rastrear transações, e a hora precisa ser consistente durante toda a implantação. Quando a hora entre as VMs for diferente, você poderá ver os seguintes efeitos:

- A autenticação falhará. Protocolos de segurança como Kerberos ou tecnologia dependente de certificado requerem que a hora esteja consistente em todos os sistemas. 
- É muito difícil descobrir o que aconteceu em um sistema se os logs (ou outros dados) não corresponderem com a hora. O mesmo evento poderia parecer que ocorreu em momentos diferentes, dificultando a correlação.
- Se o relógio estiver desligado, a cobrança poderá ser calculada incorretamente.

Os melhores resultados para implantações do Windows são obtidos usando o Windows Server 2016 como o sistema operacional convidado, o que garante que você possa usar os últimos aprimoramentos na sincronização de data/hora.

## <a name="configuration-options"></a>Opções de configuração

Há três opções para configurar a sincronização de data/hora nas VMs do Windows hospedadas no Azure:

- Hora do host e time.windows.com. Essa é a configuração padrão usada nas imagens do Azure Marketplace.
- Somente do host.
- Use outro servidor de horário externo com ou sem o uso de hora do host.


### <a name="use-the-default"></a>Usar o padrão

Por padrão, as imagens de VM do Sistema Operacional do Windows são configuradas para que o w32time sincronize a partir de duas origens: 

- O provedor NtpClient, que obtém informações de time.windows.com.
- O serviço VMICTimeSync, usado para comunicar a hora do host para as VMs e fazer correções após a VM ser pausada para manutenção. Os hosts do Azure usam dispositivos Stratum 1 da Microsoft para manter a hora exata.

O w32time preferiria o provedor de horário na seguinte ordem de prioridade: nível de camada, atraso de raiz, dispersão de raiz, diferença de horário. Na maioria dos casos, o w32time preferiria time.windows.com ao host porque time.windows.com reporta uma camada inferior. 

Para máquinas ingressadas no domínio, o domínio em si estabelece a hierarquia de sincronização de data/hora, mas a raiz da floresta ainda exigiria tempo de algum lugar e as seguintes considerações ainda seriam verdadeiras.


### <a name="host-only"></a>Somente do host 

Como o time.windows.com é um servidor NTP público, sincronizar o horário com ele requer o envio de tráfego pela Internet, o atraso variável no pacote pode afetar negativamente a qualidade da sincronização de data/hora. A remoção de time.windows.com, alternando para a sincronização somente do host pode, às vezes, melhorar os resultados de sincronização de data/hora.

Alternar para a sincronização de data/hora somente do host será viável se você tiver problemas de sincronização de data/hora usando a configuração padrão. Experimente a sincronização somente do host para ver se melhoraria a sincronização de data/hora na VM. 

### <a name="external-time-server"></a>Servidor de horário externo

Se você tiver requisitos específicos de sincronização de data/hora, também haverá uma opção de usar servidores de horário externos. Servidores de horário externos podem fornecer hora específica, o que pode ser útil para cenários de teste, garantindo uniformidade de hora com máquinas hospedadas em datacenters que não sejam da Microsoft ou lidando com segundos bissextos de uma maneira especial.

É possível combinar servidores externos com o serviço VMICTimeSync e o VMICTimeProvider para fornecer resultados semelhantes à configuração padrão. 

## <a name="check-your-configuration"></a>Verificar a configuração


Verifique se o provedor de horário NtpClient está configurado para usar servidores NTP explícitos (NTP) ou sincronização de data/hora de domínio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se a VM estiver usando NTP, você verá a seguinte saída:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Para ver o servidor de horário que o provedor de horário NtpClient está usando, em um prompt de comando com privilégios elevados digite:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se a VM estiver usando o padrão, a saída será semelhante a:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Para ver qual provedor de horário está sendo usado atualmente.

```
w32tm /query /source
```


Aqui está a saída que você pode ver e o que significaria:
    
- **time.windows.com** - na configuração padrão, w32time obteria a hora do time.windows.com. A qualidade da sincronização de data/hora depende da conectividade com a Internet e é afetada por atrasos de pacotes. Essa é a saída usual da configuração padrão.
- **Provedor de sincronização de data/hora de IC da VM**  - a VM está sincronizando a hora do host. Isso geralmente é o resultado se você optar por sincronizar somente o host ou se NtpServer não estiver disponível no momento. 
- *Seu servidor de domínio* - a máquina atual está em um domínio e o domínio define a hierarquia de sincronização de data/hora.
- *Algum outro servidor* - O w32time foi explicitamente configurado para obter a hora desse outro servidor. A qualidade da sincronização de data/hora depende dessa qualidade do servidor de horário.
- **Relógio CMOS local** - relógio não está sincronizado. É possível obter essa saída se o w32time não tiver tido tempo suficiente para iniciar após um reinício ou quando todas as fontes de tempo configuradas não estiverem disponíveis.


## <a name="opt-in-for-host-only-time-sync"></a>Optar pela sincronização de data/hora somente do host

O Azure está constantemente trabalhando para melhorar a sincronização de data/hora nos hosts e pode garantir que toda a infraestrutura de sincronização de data/hora seja colocada em datacenters de propriedade da Microsoft. Se você tiver problemas de sincronização de data/hora com a configuração padrão que prefere usar o time.windows.com como a fonte de hora principal, você pode usar os seguintes comandos para ativar a sincronização de data/hora somente do host.

Marque o provedor VMIC como habilitado. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Marque o provedor NTPClient como desabilitado.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Reinicie o serviço w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>VMs do Windows Server 2012 e R2 

O Windows Server 2012 e o Windows Server 2012 R2 têm configurações padrão diferentes para sincronização de data/hora. O w32time, por padrão, é configurado de uma maneira que prefere pouca sobrecarga do serviço para hora precisa. 

Se você quiser mover as implantações do Windows Server 2012 e 2012 R2 para usar os padrões mais recentes que preferem hora precisa, aplique as configurações a seguir.

Atualize a pesquisa do w32time e atualize os intervalos para que correspondam às configurações do Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Para o w32time poder usar os novos intervalos de sondagem, os NtpServers serão marcados como usando-os. Se os servidores forem anotados com máscara 0x1 bitflag, isso substituirá esse mecanismo e o w32time usaria SpecialPollInterval. Certifique-se de que os servidores NTP especificados estejam usando o sinalizador 0x8 ou nenhum sinalizador:

Verifique quais sinalizadores estão sendo usados para os servidores NTP utilizados.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Próximas etapas

Abaixo, são apresentados links para mais detalhes sobre a sincronização de data/hora:

- [Configurações e ferramentas do Serviço de Tempo do Windows](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Aprimoramentos do Windows Server 2016 ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Hora precisa do Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Limite de suporte para configurar o Serviço de Tempo do Windows para ambiente de alta precisão](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)



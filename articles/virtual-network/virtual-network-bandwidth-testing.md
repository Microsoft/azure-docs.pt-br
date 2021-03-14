---
title: Testar a taxa de transferência de rede da VM do Azure
titlesuffix: Azure Virtual Network
description: Use NTTTCP para direcionar a rede para testar e minimizar o uso de outros recursos que poderiam afetar o desempenho.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: abcd2f6e23ea0c7c26b2ee26b2f6ca4ad502b769
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177174"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Teste de Largura de Banda/Taxa de Transferência (NTTTCP)

Ao testar o desempenho de taxa de transferência de rede no Azure, é melhor usar uma ferramenta que se destina à rede para teste e que minimiza o uso de outros recursos que poderiam afetar o desempenho. É recomendável usar o NTTTCP.

Copie a ferramenta para duas VMs do Azure do mesmo tamanho. Uma VM funciona como REMETENTE e a outra como RECEPTOR.

#### <a name="deploying-vms-for-testing"></a>Implantando VMs para teste
Para os fins deste teste, as duas VMs devem estar no mesmo [grupo de posicionamento de proximidade](../virtual-machines/co-location.md) ou no mesmo conjunto de disponibilidade para que possamos usar seus IPs internos e excluir os balanceadores de carga do teste. É possível testar com o VIP, mas essa variante de teste está fora do escopo deste documento.

Anote o endereço IP do RECEPTOR. Vamos chamar esse IP de “a.b.c.r”

Anote o número de núcleos da VM. Vamos chamar isso de “\#num\_cores”

Execute o teste NTTTCP durante 300 segundos (ou 5 minutos) na VM remetente e na VM receptora.

Dica: ao configurar esse teste pela primeira vez, você poderá tentar um período de teste mais curto para obter comentários mais rápidos. Depois que a ferramenta estiver funcionando como esperado, estenda o período de teste para 300 segundos para obter os resultados mais precisos.

> [!NOTE]
> O remetente **e** o receptor devem especificar **o mesmo** parâmetro de duração de teste (-t).

Para testar um único fluxo TCP por 10 segundos:

Parâmetros do receptor: ntttcp -r -t 10 -P 1

Parâmetros do remetente: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> A amostra anterior só deve ser usada para confirmar a configuração. Exemplos válidos de teste são abordados adiante neste documento.

## <a name="testing-vms-running-windows"></a>Testando VMs que executam o WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Instale o NTTTCP nas VMs.

Faça o download da versão mais recente: https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

Ou exiba a página do GitHub de nível superior: <https://github.com/microsoft/ntttcp>\

Considere colocar o NTTTCP em uma pasta separada, como c:\\tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir o NTTTCP pelo firewall do Windows
No RECEPTOR, crie uma regra de Permissão no Firewall do Windows para permitir a chegada do tráfego NTTTCP. É mais fácil permitir todo o programa NTTTCP por nome, em vez de permitir portas TCP de entrada específicas.

Permita o NTTTCP pelo Firewall do Windows desta maneira:

netsh advfirewall firewall add rule Program = \<PATH\> \\ntttcp.exe Name = "ntttcp" protocolo = qualquer dir = in Action = permitir Enable = Yes perfil = any

Por exemplo, se você tiver copiado ntttcp.exe para a pasta “c:\\tools”, este será o comando: 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Executando testes NTTTCP

Inicie o NTTTCP no RECEPTOR (**execute no CMD**, não no PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Se a VM tiver quatro núcleos e um endereço IP 10.0.0.4, ela será parecida com esta:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Inicie o NTTTCP no REMETENTE (**execute no CMD**, não no PowerShell):

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

Aguarde os resultados.


## <a name="testing-vms-running-linux"></a>Testando VMs que executam o LINUX:

Use nttcp-for-linux. Ele está disponível no <https://github.com/Microsoft/ntttcp-for-linux>

Nas VMs Linux (REMETENTE e RECEPTOR), execute estes comandos para preparar o ntttcp-for-linux nas VMs:

CentOS-instalar GCC e git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – instalar build – essential e git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
SUSE-install git-Core, gcc e make:
``` bash
  zypper in -y git-core gcc make
```
Crie e instale nas duas:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Como no exemplo do Windows, vamos supor que o IP do RECEPTOR do Linux é 10.0.0.4

Inicie NTTTCP-for-Linux no RECEPTOR:

``` bash
ntttcp -r -t 300
```

E no REMETENTE, execute:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
O tamanho do teste usa como padrão 60 segundos se nenhum parâmetro de tempo é fornecido

## <a name="testing-between-vms-running-windows-and-linux"></a>Testar entre VMs executando o Windows e LINUX:

Nesses cenários, devemos habilitar o modo sem sincronização para que o teste possa ser executado. Isso é feito utilizando o **sinalizador -N** para Linux e **sinalizadores -ns** para Windows.

#### <a name="from-linux-to-windows"></a>Do Linux para Windows:

Receptor \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Remetente \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Do Windows para Linux:

Receptor \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Remetente \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testando as Instâncias de Serviço de Nuvem:
Você precisa adicionar a seção a seguir em seu ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Próximas etapas
* Dependendo dos resultados, pode haver espaço para [Otimizar a taxa de transferência de rede nos computadores](virtual-network-optimize-network-bandwidth.md) para seu cenário.
* Leia sobre como [a largura de banda é alocada para máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)

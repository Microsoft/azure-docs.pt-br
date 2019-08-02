---
title: Conexões de rede e alimentação do Microsoft Azure FXT Edge Filer
description: Como cabear as portas de rede e anexar a alimentação ao hardware do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ae179e8ce2a2ba772a7fb14825660e0fff9e7410
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542930"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: Estabelecer conexões de rede e fornecer alimentação para o nó do Azure FXT Edge Filer

Este tutorial ensina como cabear as conexões de rede para um nó de hardware do Azure FXT Edge Filer.

Neste tutorial, você irá aprender: 

> [!div class="checklist"]
> * Como escolher o tipo de cabo de rede para o seu ambiente
> * Como conectar um nó do Azure FXT Edge Filer à rede do datacenter
> * Como rotear os cabos por meio do CMA (braço de gerenciamento de cabos)
> * Como conectar a alimentação ao dispositivo em rack e ligá-lo

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, o Azure FXT Edge Filer deve ser instalado em um rack de equipamento padrão. O CMA deve ser instalado no nó do arquivista. 

## <a name="identify-ports"></a>Identificar as portas

Identifique as várias portas na parte traseira do Azure FXT Edge Filer. 
 
![Parte traseira de um dispositivo cabeado](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Cabear o dispositivo

* Conecte as portas RJ-45 à fonte de rede do data center, conforme descrito em [Portas de rede](#network-ports).  
* Conecte a [porta do iDRAC](#idrac-port) com segurança a uma rede separada com um servidor DHCP seguro. 
* Use as portas USB e a porta VGA para conectar um teclado e um monitor ao nó para a instalação inicial. É necessário inicializar o nó e [definir uma senha inicial](fxt-node-password.md) para ativar as outras portas do nó. Leia [Definir senhas iniciais](fxt-node-password.md) para obter detalhes. 

Este artigo também descreve como [conectar a alimentação AC](#connect-power-cables) do nó. 

Este artigo também explica como se conectar à [porta serial](#serial-port-only-when-necessary) do nó, se necessário, para solução de problemas especializada. 

### <a name="network-ports"></a>Portas de rede 

Cada nó do Azure FXT Edge Filer inclui as seguintes portas de rede: 

* Seis portas de dados de alta velocidade de 25 GbE/10 GbE de taxa dupla: 

  * Quatro portas fornecidas por dois adaptadores de rede de plug-in de porta dupla
  * Duas portas fornecidas pelo adaptador de rede de mezanino da placa-mãe 

* Duas portas de 1 GbE fornecidas pelo adaptador de rede de mezanino da placa-mãe 

As portas de dados de 25 GbE/10 GbE de alta velocidade têm compartimentos padrão compatíveis com SFP28. Para usar cabos ópticos, é necessário instalar módulos de transceptor óptico SFP28 (não fornecidos).

As portas de 1 GbE têm conectores RJ-45 padrão.

Para obter uma lista completa de cabos, comutadores e transceptores compatíveis, confira a [Matriz de interoperabilidade da Cavium FastlinQ série 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Os tipos de conexões a serem usados para o sistema dependem do ambiente de data center.

* Caso esteja se conectando a uma rede de 25 GbE, cabeie cada uma das portas de dados de alta velocidade com um dos seguintes tipos de cabo:

  * Cabo óptico e transceptor óptico SFP28 com capacidade de 25 GbE ou 25 GbE/10 GbE de taxa dupla
  * Cabo axial geminado de anexação direta compatível com 25 GbE do tipo SFP28

* Caso esteja se conectando a uma rede de 10 GbE, cabeie cada uma das portas de dados de alta velocidade com um dos seguintes: 

  * Cabo óptico e transceptor óptico SFP28 com capacidade de 10 GbE ou 25 GbE/10 GbE de taxa dupla.
  * Cabo axial geminado de anexação direta compatível com 25 GbE do tipo SFP28
  * Cabo axial geminado de anexação direta compatível com 10 GbE do tipo SFP28

* As portas de rede de 1 GbE são usadas para o tráfego de gerenciamento de cluster. Marque a opção **Usar a rede de gerenciamento de 1 GB** ao criar o cluster, caso deseje criar uma rede fisicamente separada para a configuração de cluster (descrita em [Configurar a rede de gerenciamento](fxt-cluster-create.md#configure-the-management-network)). Cabeie as portas com um cabo Cat5 padrão ou melhor, conforme descrito na lista de cabos compatíveis.

  Você pode manter as portas de 1 GbE não cabeadas se pretende usar as portas de alta velocidade para todo o tráfego. Por padrão, as portas de rede de 1 GbE não são usadas se uma porta de dados de velocidade mais alta está disponível.  

### <a name="idrac-port"></a>Porta do iDRAC  

A porta rotulada do iDRAC é uma conexão de 1Gb que permite a comunicação com um controlador de acesso remoto usado para gerenciamento de hardware e monitoramento. O software FXT usa a IPMI (Interface de Gerenciamento de Plataforma Inteligente) com esse controlador para solução de problemas e recuperação. Você pode usar a [interface do iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) interna para monitorar o hardware por meio dessa porta. O acesso ao iDRAC e à IPMI está habilitado por padrão. 

> [!Note]
> A porta do iDRAC pode ignorar o sistema operacional e interagir diretamente com o hardware no nó. 

Use estas estratégias de segurança ao se conectar à porta do iDRAC e configurá-la:

* Apenas conecte as portas do iDRAC a uma rede que esteja fisicamente separada da rede de dados usada para acessar o cluster.
* Defina uma senha segura de administrador do iDRAC em cada nó. É necessário definir essa senha para ativar o hardware – siga as instruções em [Definir senhas de hardware](fxt-node-password.md).
* A configuração padrão da porta do iDRAC usa o DHCP e o IPv4 para atribuição de endereço IP. Verifique se o ambiente do DHCP está bem protegido e se as conexões são restritas entre os clientes DHCP e o servidor DHCP. (O painel de controle do cluster inclui configurações para alterar o método de configuração do endereço dos nós depois que você cria o cluster.)
* Mantenha a porta do iDRAC definida como "modo dedicado" (o padrão), o que restringe o tráfego de rede do iDRAC/da IPMI à porta RJ-45 dedicada.

A porta do iDRAC não exige uma conexão de rede de alta velocidade.
  
### <a name="serial-port-only-when-necessary"></a>Porta serial (somente quando necessário)

Em algumas situações, o Serviço e Suporte da Microsoft pode orientá-lo a conectar um terminal à porta serial de um nó para diagnosticar um problema.  

Para anexar o console:

1. Localize a porta serial (COM1) na parte traseira do nó do FXT Edge Filer.
1. Use um cabo de modem nulo para conectar a porta serial a um terminal configurado para ANSI-115200-8N1.
1. Entre no console e execute outras etapas, conforme orientado pela equipe de suporte.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Rotear os cabos no CMA (braço de gerenciamento de cabos)

Cada nó do Azure FXT Edge Filer vem com um braço de gerenciamento de cabos opcional. O CMA simplifica o roteamento de cabos e fornece acesso mais fácil à parte traseira do chassi sem a necessidade de desconectar os cabos. 

Siga estas instruções para rotear os cabos pelo CMA: 

1. Usando as presilhas plásticas fornecidas, amarre os cabos conforme eles entram e saem das cestas, de modo que não interfiram com os sistemas adjacentes (1).
1. Com o CMA na posição de serviço, roteie o grupo de cabos pelas cestas interna e externa (2).
1. Use as tiras com fixador de contato pré-instaladas em ambas as extremidades das cestas para prender os cabos (3).
1. Gire o CMA para colocá-lo novamente no lugar na bandeja (4).
1. Instale o cabo de indicador de status na parte traseira do sistema e prenda o cabo roteando-o pelo CMA. Anexe a outra extremidade do cabo ao canto da cesta externa do CMA (5). 

   > [!CAUTION]
   > Para evitar o dano potencial de cabos salientes, prenda qualquer folga no cabo de indicador de status após o roteamento desse cabo pelo CMA. 

![Ilustração do CMA com os cabos instalados](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Se você não instalou o CMA, use as duas tiras com fixador de contato fornecidas no kit de trilho para rotear os cabos na parte traseira do sistema.
> 
>  1. Localize os suportes externos do CMA nas laterais interiores de ambas as bordas do rack.
>  2. Agrupe os cabos com cuidado, afastando-os dos conectores do sistema nas laterais esquerda e direita.
>  3. Passe as tiras com fixador de contato pelos slots com ferramentas nos suportes externos do CMA em cada lateral do sistema para fixar os conjuntos de cabos.
> 
>     ![Cabos roteados sem um CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Sobre os requisitos de endereço IP

Para os nós de hardware em um cache de armazenamento híbrido do Azure FXT Edge Filer, os endereços IP são gerenciados pelo software do cluster.

Cada nó exige, pelo menos, um endereço IP, mas os endereços do nó são atribuídos quando os nós são adicionados ou removidos do cluster. 

O número total de endereços IP necessários depende do número de nós que compõem o cache. 

Configure o intervalo de endereços IP usando o software Painel de Controle depois que os nós forem instalados. Para saber mais, leia [Coletar informações para o cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Conectar os cabos de alimentação

Cada nó do Azure FXT Edge Filer usa duas PSUs (unidades de alimentação). 

> [!TIP] 
> Para aproveitar as duas PSUs redundantes, anexe cada cabo de alimentação AC a uma PDU (unidade de distribuição de alimentação) em um circuito de branch independente.  
> 
> Você pode usar um no-break para alimentar as PDUs para proteção extra. 

1. Conecte os cabos de alimentação incluídos às PSUs no chassi. Verifique se os cabos e as PSUs estão encaixados corretamente. 
1. Anexe os cabos de alimentação às unidades de distribuição de alimentação no rack de equipamento. Se possível, use duas fontes de alimentação separadas para os dois cabos. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Ligar um nó do Azure FXT Edge Filer

Para ligar o nó, pressione o botão de energia na parte frontal do sistema. O botão está localizado no painel de controle do lado direito. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Desligar um nó do Azure FXT Edge Filer

O botão de energia pode ser usado para desligar o sistema durante o teste e antes de adicioná-lo a um cluster. No entanto, depois que um nó do Azure FXT Edge Filer estiver em uso como parte de um cluster, você deverá usar o software do painel de controle do cluster para desligar o hardware. Leia [Como desligar o hardware do Azure FXT Edge Filer com segurança](fxt-power-off.md) para obter detalhes. 

## <a name="next-steps"></a>Próximas etapas

Depois de concluir o cabeamento do hardware, ligue cada um dos nós e inicialize-os definindo suas senhas raiz. 
> [!div class="nextstepaction"]
> [Definir senhas iniciais](fxt-node-password.md)

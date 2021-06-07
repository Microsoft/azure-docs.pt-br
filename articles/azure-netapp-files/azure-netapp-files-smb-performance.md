---
title: Perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files | Microsoft Docs
description: Responde a perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929449"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files

Este artigo responde às perguntas frequentes sobre as práticas recomendadas de desempenho do SMB para Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>O SMB multicanal está habilitado em compartilhamentos SMB? 

Sim, o SMB multicanal está habilitado por padrão, uma alteração foi colocada no início de janeiro de 2020. Todos os compartilhamentos SMB já têm o recurso habilitado e todos os volumes recém-criados também terão o recurso habilitado no momento da criação. 

Qualquer conexão SMB estabelecida antes da habilitação do recurso precisará ser redefinida para tirar proveito da funcionalidade de Fibre Channel do SMB. Para redefinir, você pode desconectar e reconectar o compartilhamento SMB.

## <a name="is-rss-supported"></a>Há suporte para RSS?

Sim, Azure NetApp Files dá suporte ao RSS (recebimento em escala).

Com o SMB multicanal habilitado, um cliente SMB3 estabelece várias conexões TCP com o servidor SMB Azure NetApp Files por meio de uma NIC (placa de interface de rede) com capacidade de RSS único. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quais versões do Windows oferecem suporte a SMB Multichannel?

O Windows tem suporte para o SMB Multichannel desde o Windows 2012 para permitir o melhor desempenho.  Consulte [implantar o SMB multicanal](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [os fundamentos do SMB Multichannel](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) para obter detalhes. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Minha máquina virtual do Azure dá suporte a RSS?

Para ver se suas NICs de máquina virtual do Azure dão suporte a RSS, execute o comando da `Get-SmbClientNetworkInterface` seguinte maneira e verifique o campo `RSS Capable` : 

![Captura de tela que mostra a saída RSS para a máquina virtual do Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files dá suporte ao SMB Direct?

Não, o Azure NetApp Files não dá suporte ao SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual é o benefício do SMB multicanal? 

O recurso de vários canais SMB permite que um cliente SMB3 estabeleça um pool de conexões em uma única NIC (placa de interface de rede) ou em várias NICs e use-as para enviar solicitações para uma única sessão SMB. Por outro lado, por design, o SMB1 e o SMB2 exigem que o cliente estabeleça uma conexão e envie todo o tráfego SMB para uma determinada sessão por essa conexão. Essa conexão única limita o desempenho geral do protocolo que pode ser obtido de um único cliente.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Devo configurar várias NICs em meu cliente para SMB?

Não. O cliente SMB fará a correspondência da contagem de NIC retornada pelo servidor SMB.  Cada volume de armazenamento pode ser acessado somente de um ponto de extremidade de armazenamento.  Isso significa que apenas uma NIC será usada para qualquer relação SMB específica.  

Como a saída `Get-SmbClientNetworkInterace` mostrada abaixo, a máquina virtual tem 2 interfaces de rede--15 e 12.  Conforme mostrado no comando a seguir `Get-SmbMultichannelConnection` , embora haja duas NICs compatíveis com RSS, somente a interface 12 é usada em conjunto com o compartilhamento SMB; a interface 15 não está em uso.

![Captura que mostra a saída para NICS compatíveis com RSS.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>O agrupamento NIC é compatível com o Azure?

O agrupamento NIC não tem suporte no Azure. Embora haja suporte para várias interfaces de rede em máquinas virtuais do Azure, elas representam uma construção lógica em vez de um constructo físico. Dessa forma, eles não fornecem tolerância a falhas.  Além disso, a largura de banda disponível para uma máquina virtual do Azure é calculada para a própria máquina e não para qualquer interface de rede individual.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Qual é o desempenho como para o Fibre Channel do SMB?

Os seguintes testes e grafos demonstram o poder do SMB multicanal em cargas de trabalho de instância única.

### <a name="random-io"></a>E/s aleatória  

Com o SMB multicanal desabilitado no cliente, testes de leitura e gravação puros de 4 KiB foram executados usando FIO e um conjunto de trabalho de 40 GiB.  O compartilhamento SMB foi desanexado entre cada teste, com incrementos da contagem de conexões de cliente SMB por configurações de interface de rede RSS de,,, `1` `4` `8` `16` , `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Os testes mostram que a configuração padrão do `4` é suficiente para cargas de trabalho com uso intensivo de e/s; incrementar para `8` e `16` teve um efeito insignificante. 

O comando `netstat -na | findstr 445` provou que conexões adicionais foram estabelecidas com incrementos de `1` para `4` para `8` e para `16` .  Quatro núcleos de CPU foram totalmente utilizados para SMB durante cada teste, conforme confirmado pela estatística Perfmon `Per Processor Network Activity Cycles` (não incluído neste artigo).

![Gráfico que mostra a comparação aleatória de e/s do SMB Multichannel.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

A máquina virtual do Azure não afeta os limites de e/s de armazenamento SMB (nem NFS).  Conforme mostrado no gráfico a seguir, o tipo de instância D32ds tem uma taxa limitada de 308.000 para IOPS de armazenamento em cache e 51.200 para IOPS de armazenamento não armazenado em cache.  No entanto, o gráfico acima mostra significativamente mais e/s sobre SMB.

![Gráfico que mostra o teste de comparação de e/s aleatório.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>E/s sequencial 

Testes semelhantes aos testes de e/s aleatórios descritos anteriormente foram executados com e/s sequencial de 64-KiB. Embora o aumento na contagem de conexões de cliente por interface de rede RSS além de 4 ' não tenha nenhum efeito perceptível na e/s aleatória, o mesmo não se aplica à e/s sequencial. Como mostra o gráfico a seguir, cada aumento é associado a um aumento correspondente na taxa de transferência de leitura. A taxa de transferência de gravação permaneceu simples devido a restrições de largura de banda de rede colocadas pelo Azure para cada tipo/tamanho de instância. 

![Gráfico que mostra a comparação de testes de taxa de transferência.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

O Azure coloca limites de taxa de rede em cada tipo/tamanho de máquina virtual. O limite de taxa é imposto somente no tráfego de saída. O número de NICs presentes em uma máquina virtual não tem nenhuma influência sobre a quantidade total de largura de banda disponível para o computador.  Por exemplo, o tipo de instância D32ds tem um limite de rede imposto de 16.000 Mbps (2.000 MiB/s).  Como mostra o grafo sequencial acima, o limite afeta o tráfego de saída (gravações), mas não as leituras multicanal.

![Gráfico que mostra O teste de comparação de e/s sequencial.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Qual desempenho é esperado com uma única instância com um conjunto de um DataSet de 1 TB?

Para fornecer uma visão mais detalhada das cargas de trabalho com combinações de leitura/gravação, os dois gráficos a seguir mostram o desempenho de um volume de nuvem de nível de serviço único de 50 TB com um conjunto de informações de 1 TB e com o SMB multicanal de 4. Um IODepth ideal de 16 foi usado e parâmetros de e/s flexíveis (FIO) foram usados para garantir o uso completo da largura de banda da rede ( `numjobs=16` ).

O gráfico a seguir mostra os resultados para a e/s aleatória de 4K, com uma única instância de VM e uma combinação de leitura/gravação em intervalos de 10%:

![Gráfico que mostra o teste de e/s aleatória do Windows 2019 Standard _D32ds_v4 4K.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

O gráfico a seguir mostra os resultados para e/s sequencial:

![Gráfico que mostra a taxa de transferência sequencial do Windows 2019 Standard _D32ds_v4 64K.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Qual desempenho é esperado ao escalar horizontalmente usando 5 VMs com um conjunto de um DataSet de 1 TB?

Esses testes com 5 VMs usam o mesmo ambiente de teste que a VM única, sendo que cada processo grava em seu próprio arquivo.

O gráfico a seguir mostra os resultados para e/s aleatória:

![Gráfico que mostra o Windows 2019 Standard _D32ds_v4 4K 5-Instance randio i Test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

O gráfico a seguir mostra os resultados para e/s sequencial:

![Gráfico que mostra a taxa de transferência seqüencial da instância do Windows 2019 Standard _D32ds_v4 64K 5.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Como monitorar os adaptadores de Ethernet do Hyper-V e garantir que você maximize a capacidade da rede?  

Uma estratégia usada no teste com FIO é definir `numjobs=16` . Fazer isso bifurca cada trabalho em 16 instâncias específicas para maximizar o adaptador de rede Microsoft Hyper-V.

Você pode verificar a atividade em cada um dos adaptadores no monitor de desempenho do Windows selecionando **Monitor de desempenho > adicionar contadores > interface de rede > Microsoft Hyper-V adaptador de rede**.

![Captura de tela que mostra a interface do contador adicionar monitor de desempenho.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Depois de ter o tráfego de dados em execução em seus volumes, você pode monitorar seus adaptadores no monitor de desempenho do Windows. Se você não usar todos esses 16 adaptadores virtuais, talvez não esteja maximizando sua capacidade de largura de banda de rede.

![Captura de tela que mostra a saída do monitor de desempenho.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>A rede acelerada é recomendada?

Para obter o máximo de desempenho, é recomendável configurar a [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) sempre que possível. Tenha em mente as seguintes considerações:  

* O portal do Azure habilita a rede acelerada por padrão para máquinas virtuais que dão suporte a esse recurso.  No entanto, outros métodos de implantação, como Ansible e ferramentas de configuração semelhantes, podem não.  A falha ao habilitar a rede acelerada pode hobble o desempenho de um computador.  
* Se a rede acelerada não estiver habilitada na interface de rede de uma máquina virtual devido à falta de suporte para um tipo ou tamanho de instância, ela permanecerá desabilitada com tipos de instância maiores. Você precisará de intervenção manual nesses casos.

## <a name="are-jumbo-frames-supported"></a>Há suporte para quadros Jumbo?

Não há suporte para quadros jumbo com máquinas virtuais do Azure.

## <a name="is-smb-signing-supported"></a>Há suporte para assinatura SMB? 

O protocolo SMB fornece a base para o compartilhamento de arquivos e impressão e outras operações de rede, como a administração remota do Windows. Para impedir ataques man-in-the-middle que modificam pacotes SMB em trânsito, o protocolo SMB oferece suporte para assinatura digital de pacotes SMB. 

A assinatura SMB tem suporte para todas as versões de protocolo SMB com suporte pelo Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual é o impacto no desempenho da assinatura SMB?  

A assinatura SMB tem um efeito deletério sobre o desempenho do SMB. Entre outras causas potenciais da degradação do desempenho, a assinatura digital de cada pacote consome uma CPU adicional do lado do cliente, conforme mostrado na saída do Perfmon abaixo. Nesse caso, o núcleo 0 é exibido responsável por SMB, incluindo a assinatura SMB.  Uma comparação com os números de taxa de transferência de leitura sequencial não multicanal na seção anterior mostra que a assinatura SMB reduz a taxa de transferência geral de 875MiB/s para aproximadamente 250MiB/s. 

![Gráfico que mostra o impacto no desempenho de assinatura SMB.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Próximas etapas  

- [Perguntas frequentes sobre Azure NetApp Files](azure-netapp-files-faqs.md)
- Consulte o [Azure NetApp Files: compartilhamentos de arquivos corporativos gerenciados para cargas de trabalho SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) sobre como usar compartilhamentos de arquivos smb com Azure NetApp files.
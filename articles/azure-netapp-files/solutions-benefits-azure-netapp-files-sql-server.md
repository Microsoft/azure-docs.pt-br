---
title: Benefícios do uso de Azure NetApp Files para SQL Server implantação | Microsoft Docs
description: Mostra os benefícios de desempenho de análise de custo detalhado sobre como usar Azure NetApp Files para SQL Server implantação.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863897"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Benefícios do uso de Azure NetApp Files para SQL Server implantação

Azure NetApp Files reduz SQL Server TCO (custo total de propriedade) em comparação com as soluções de armazenamento em bloco.  Com o armazenamento em bloco, as máquinas virtuais têm limites impostos sobre e/s e largura de banda para operações de disco, limites de largura de banda de rede sozinhos são aplicados a Azure NetApp Files apenas.  Em outras palavras, nenhum limite de e/s de nível de VM é aplicado a Azure NetApp Files. Sem esses limites de e/s, SQL Server em execução em máquinas virtuais menores conectadas a Azure NetApp Files podem ser executadas, bem como SQL Server em execução em máquinas virtuais muito maiores. Dimensionar instâncias para baixo, pois isso reduz o custo de computação para 25% da marca de preço anterior.  *Você pode reduzir os custos de computação com Azure NetApp Files.*  

No entanto, os custos de computação são pequenos em comparação com os custos de licença SQL Server.  Microsoft SQL Server [Licenciamento](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) está vinculado à contagem de núcleos físicos. Dessa forma, diminuir o tamanho da instância apresenta um economia de custo ainda maior para o licenciamento de software. *Você pode reduzir os custos de licença de software com o Azure NetApp Files.*

O custo do próprio armazenamento é variável, dependendo do tamanho real do banco de dados. Independentemente do armazenamento selecionado, a capacidade tem custo, seja um disco gerenciado ou um compartilhamento de arquivos.  À medida que os tamanhos de banco de dados aumentam e o armazenamento aumenta em custos, o armazenamento contribui para o TCO aumenta, afetando o custo geral.  Assim, a asserção é ajustada da seguinte maneira: *você pode reduzir SQL Server os custos de implantação com Azure NetApp files.* 

Este artigo mostra uma análise de custo detalhada e benefícios de desempenho sobre o uso de Azure NetApp Files para SQL Server implantação. Não apenas as instâncias menores têm CPU suficiente para fazer com que o banco de dados só funcione com o bloqueio em instâncias maiores, *em muitos casos, as instâncias menores são ainda mais perfeitas do que suas contrapartes maiores, baseadas em disco, devido à Azure NetApp files.* 

## <a name="detailed-cost-analysis"></a>Análise de custo detalhada 

Os dois conjuntos de gráficos nesta seção mostram o exemplo de TCO.  O número e o tipo de discos gerenciados, o nível de serviço Azure NetApp Files e a capacidade de cada cenário foram selecionados para atingir o melhor preço-capacidade-desempenho.  Cada elemento gráfico é composto de máquinas agrupadas (D16 com Azure NetApp Files, em comparação com o D64 com disco gerenciado por exemplo), e os preços são divididos para cada tipo de computador.  

O primeiro conjunto de gráficos mostra o custo geral da solução usando um tamanho de banco de dados de 1 TiB, comparando o D16s_v3 ao D64, a D8 para o d32 e o D4 para o D16. O IOPs projetado para cada configuração é indicado por uma linha verde ou amarela e corresponde ao eixo Y do lado direito.

[![Gráfico que mostra o custo geral da solução usando um tamanho de banco de dados de 1 Tib. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


O segundo conjunto de gráficos mostra o custo geral usando um banco de dados 50-TiB. As comparações são, de outra forma, as mesmas – D16 comparadas com Azure NetApp Files versus D64 com um exemplo de bloco. 

[![Gráfico que mostra o custo geral usando um tamanho de banco de dados 50-Tib. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Desempenho e muito mais  

Para fornecer a declaração de redução de custos significativa requer muito desempenho – as maiores instâncias do inventário do Azure geral dão suporte ao 80.000 IOPS de disco por exemplo. Um único volume de Azure NetApp Files pode atingir o IOPS de banco de dados 80.000, e instâncias como D16 são capazes de consumir o mesmo. O D16, normalmente capaz de 25.600 IOPS de disco, é de 25% do tamanho do D64.  O D64s_v3 é capaz de 80.000 IOPS de disco e, como tal, apresenta um excelente ponto de comparação de nível superior.

O D16s_v3 pode direcionar um volume de Azure NetApp Files para o IOPS de banco de dados 80.000. Como comprovado pela ferramenta de benchmarking do SSB (SQL Storage benchmark), a instância D16 atingiu uma carga de trabalho 125% maior do que a possível para o disco da instância D64.  Consulte a seção [ferramenta de teste do SSB](#ssb-testing-tool) para obter detalhes sobre a ferramenta.

Usando um tamanho de conjunto de trabalho de 1 TiB e uma carga de 80% de leitura, 20% de atualização SQL Server, os recursos de desempenho da maioria das instâncias na classe de instância D foram medidos; a maioria, nem todas, como as instâncias D2 e D64 em si, foram excluídas do teste. A primeira era deixada como não dá suporte à rede acelerada e a última porque é o ponto de comparação. Consulte o grafo a seguir para entender os limites de D4s_v3, D8s_v3, D16s_v3 e D32s_v3, respectivamente.  Os testes de armazenamento em disco gerenciado não são mostrados no grafo. Os valores de comparação são desenhados diretamente da [tabela de limites da máquina virtual do Azure](../virtual-machines/dv3-dsv3-series.md) para o tipo de instância da classe D.

Com Azure NetApp Files, cada uma das instâncias na classe D pode atender ou exceder os recursos de desempenho de disco de instâncias duas vezes maiores.  *Você pode reduzir significativamente os custos de licença de software com o Azure NetApp Files.*  

* O D4 a 75% de utilização da CPU correspondeu aos recursos de disco do D16.  
    * O D16 tem taxa limitada em IOPS de disco de 25.600.  
* A D8 a 75% de utilização da CPU correspondeu aos recursos de disco do D32.  
    * O d32 tem taxa limitada em IOPS de disco de 51.200.  
* A utilização de CPU D16 a 55% correspondeu aos recursos de disco do D64.  
    * O D64 tem taxa limitada em IOPS de disco de 80.000.  
* O d32 com 15% de utilização da CPU também correspondeu aos recursos de disco do D64.  
    * O D64 conforme declarado acima é limitado em IOPS de disco de 80.000.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Teste de limites de CPU S3B – desempenho versus capacidade de processamento

O diagrama a seguir resume o teste de limites de CPU S3B:

![Diagrama que mostra a porcentagem média de CPU para SQL Server de instância única em Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

A escalabilidade é apenas parte da história. A outra parte é a latência.  É uma coisa para que máquinas virtuais menores tenham a capacidade de impulsionar taxas de e/s muito maiores, é outra coisa fazer isso com latências de um único dígito, conforme mostrado abaixo.  

* O D4 gerou 26.000 IOPS em relação a Azure NetApp Files a latência de 2,3 ms.  
* A D8 gerou 51.000 IOPS em relação a Azure NetApp Files a latência de 2,0 ms.  
* O D16 gerou 88.000 IOPS em relação ao Azure NetApp Files em latência de 2,8 ms.
* O d32 gerou 80.000 IOPS em relação ao Azure NetApp Files em latência de 2,4 ms.  

### <a name="s3b-per-instance-type-latency-results"></a>Resultados de latência de tipo de S3B por instância

O diagrama a seguir mostra a latência para SQL Server de instância única Azure NetApp Files:

![Diagrama que mostra a latência para SQL Server de instância única Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Ferramenta de teste do SSB 
 
A ferramenta de benchmark do [TPC-E](http://www.tpc.org/tpce/) , por design, enfatiza a *computação* em vez do *armazenamento*. Os resultados de teste mostrados nesta seção são baseados em uma ferramenta de teste de estresse chamada SQL Storage benchmark (SSB).  O benchmark de armazenamento SQL Server pode direcionar a execução de SQL em grande escala contra um banco de dados SQL Server para simular uma carga de trabalho OLTP, semelhante à [ferramenta de benchmark do SLOB2 Oracle](https://kevinclosson.net/slob/). 

A ferramenta SSB gera uma carga de trabalho baseada em SELECT e UPDATE, emitindo as instruções citadas diretamente para o banco de dados SQL Server em execução na máquina virtual do Azure.  Para este projeto, as cargas de trabalho do SSB aumentaram de 1 a 100 SQL Server usuários, com 10 ou 12 pontos intermediários em 15 minutos por contagem de usuário.  Todas as métricas de desempenho dessas execuções eram do ponto de vista do Perfmon, para SSB de repetição executadas três vezes por cenário. 

Os próprios testes foram configurados como 80% SELECT e 20% da instrução UPDATE, portanto, 90% de leitura aleatória.  O próprio banco de dados, que SSB criou, foi de 1000 GB de tamanho. Ele é composto por 15 tabelas de usuário e 9 milhões linhas por tabela de usuário e 8192 bytes por linha. 

O parâmetro de comparação SSB é uma ferramenta de código-fonte aberto.  Ele está disponível gratuitamente na [página do GitHub de comparação de armazenamento do SQL](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>Em resumo  

Com o Azure NetApp Files, você pode aumentar o desempenho do SQL Server enquanto reduz significativamente o custo total de propriedade. 

## <a name="next-steps"></a>Próximas etapas

* [Criar um volume SMB para o Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Arquiteturas de solução usando Azure NetApp Files – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 


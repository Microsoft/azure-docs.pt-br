---
title: Arquitetura de armazenamento do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Arquitetura de rede de como implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b303a18d481ae1a682d81d87e7c14060ffdfaf14
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869193"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)

O layout de armazenamento para o SAP HANA no Azure (Instâncias Grandes) é configurado pelo SAP HANA no modelo de implantação clássico de acordo com as diretrizes recomendadas da SAP. As diretrizes estão documentadas no white paper [Requisitos de armazenamento do SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

O SAP HANA em Instâncias Grandes da classe do Tipo I é fornecido com quatro vezes o volume de memória como volume de armazenamento. Para a classe do Tipo II de unidades do HANA em Instâncias Grandes, o armazenamento não é quatro vezes maior. As unidades são fornecidas com um volume destinado ao armazenamento de backups do log de transações do HANA. Para obter mais informações, consulte [ Instalar e configurar o SAP HANA (Instâncias Grandes) do Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela a seguir em termos de alocação de armazenamento. A tabela lista a capacidade aproximada dos diferentes volumes fornecidos com as diferentes unidades do SAP HANA Instâncias Grandes.

| SKU de Instância Grande do HANA | hana/data | Hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3\.328 GB | 768 GB |1\.280 GB | 768 GB |
| S96 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4\.608 GB | 1\.024 GB | 1\.536 GB | 1\.024 GB |
| S192m | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S192xm |  11.520 GB |  1\.536 GB |  1\.792 GB |  1\.536 GB |
| S384 | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S384m | 12.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xm | 16.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xxm |  20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576m | 20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576xm | 31.744 GB | 4\.096 GB | 2\.048 GB | 4\.096 GB |
| S768m | 28.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S768xm | 40.960 GB | 6\.144 GB | 4\.096 GB | 6\.144 GB |
| S960m | 36.000 GB | 4\.100 GB | 2\.050 GB | 4\.100 GB |


Os volumes implantados reais podem variar com base na implantação e na ferramenta utilizada para mostrar os tamanhos do volume.

Se você subdividir uma SKU do SAP HANA em Instâncias Grandes, alguns exemplos de possíveis divisões poderão parecer:

| Partição de memória em GB | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| 1\.024 | 1\.792 GB | 640 GB | 1\.024 GB | 640 GB |
| 1\.536 | 3\.328 GB | 768 GB | 1\.280 GB | 768 GB |


Esses tamanhos são números de volume aproximado que podem variar um pouco com base na implantação e nas ferramentas utilizadas para examinar os volumes. Há também outros tamanhos de partição, como 2,5 TB. Esses tamanhos de armazenamento são calculados com uma fórmula semelhante àquela usada para as partições anteriores. O termo "partições" não significa que o sistema operacional, a memória ou os recursos de CPU sejam de alguma forma particionados. Ele indica as partições de armazenamento para as diferentes instâncias do HANA que talvez você quira implantar em uma única unidade do SAP HANA em Instâncias Grandes. 

Talvez seja necessário mais armazenamento. É possível adicionar armazenamento, comprando armazenamento adicional em unidades de 1 TB. Esse armazenamento adicional pode ser adicionado como volume adicional. Também pode ser usado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes conforme originalmente implantados e principalmente documentados pelas tabelas anteriores. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento descritos anteriormente são anexados às unidades do SAP HANA em Instâncias Grandes como volumes NFS4.

É possível usar instantâneos de armazenamento para fins de backup, restauração e recuperação de desastre. Para obter mais informações, consulte [Alta disponibilidade e recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte os [cenários suportados pela HLI](hana-supported-scenario.md) para obter detalhes do layout de armazenamento para seu cenário.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar várias instâncias do SAP HANA em uma unidade do SAP HANA em Instâncias Grandes

É possível hospedar mais de uma instância ativa do SAP HANA das unidades do SAP HANA em Instâncias Grandes. Para fornecer os recursos de instantâneos de armazenamento e recuperação de desastre, essa configuração requer um conjunto de volumes por instância. Atualmente, as unidades do SAP HANA em Instâncias Grandes podem ser subdivididas da seguinte maneira:

- **S72, S72m, S96, S144, S192**: Em incrementos de 256 GB, com 256 GB a menor unidade de inicialização. Diferentes incrementos, como 256 GB e 512 GB, podem ser combinados ao máximo da memória da unidade.
- **S144m e S192m**: Em incrementos de 256 GB, com 512 GB a menor unidade. Diferentes incrementos, como 512 GB e 768 GB, podem ser combinados ao máximo da memória da unidade.
- **Classe do tipo II**: Em incrementos de 512 GB, com a menor unidade inicial de 2 TB. Diferentes incrementos, como 512 GB, 1 TB e 1,5 TB, podem ser combinados ao máximo da memória da unidade.

Alguns exemplos de execução de várias instâncias do SAP HANA podem ser semelhantes aos seguintes.

| SKU | Tamanho da memória | Tamanho do armazenamento | Tamanhos com vários bancos de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | Instância do HANA 1x768-GB<br /> ou instância de 1x512 GB + instância de 1x256 GB<br /> ou instâncias de 3x256 GB | 
| S72m | 1,5 TB | 6 TB | 3x instâncias do HANA de 512GB<br />ou a instância de 1 x 512 GB + a instância de 1 x 1 TB<br />ou instâncias de 6 x 256 GB<br />ou instância 1x1.5 TB | 
| S192m | 4 TB | 16 TB | instâncias de 8 x 512 GB<br />ou instâncias de 4 x 1 TB<br />ou instâncias de 4 x 512 GB + 2 x 1 TB<br />ou instâncias de 4 x 768 GB + 2 x 512 GB<br />ou instância de 1 x 4 TB |
| S384xm | 8 TB | 22 TB | instâncias de 4 x 2 TB<br />ou instâncias de 2 x 4 TB<br />ou instâncias de 2 x 3 TB + 2 x 1 TB<br />ou instâncias de 2x2.5 TB + 3 x 1 TB<br />ou instância de 1 x 8 TB |


Também há outras variações. 

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
O armazenamento usado para o SAP HANA em instâncias grandes usa a criptografia transparente para os dados conforme eles são armazenados nos discos desde o fim do ano 2018. Em implantações anteriores, você pode optar por obter os volumes criptografados. Se você decidir sobre essa opção, poderá solicitar que os volumes sejam criptografados online. A passagem de volumes não criptografados para volumes criptografados é transparente e não requer tempo de inatividade. 

Com a classe Tipo I de SKUs, o volume no qual o LUN de inicialização está armazenado é criptografado. Na revisão 3 carimbos de instância grande do HANA, usando a classe Type II de SKUs de instância grande do HANA, você precisa criptografar o LUN de inicialização com métodos do sistema operacional. Na revisão 4 os carimbos de instância grande do HANA, usando unidades do tipo II, o volume do LUN de inicialização é armazenado e também é criptografado em repouso por padrão. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Configurações necessárias para instâncias maiores do HANA em instâncias grandes do HANA
O armazenamento usado no HANA em instâncias grandes tem uma limitação de tamanho de arquivo. A [limitação de tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por arquivo. Ao contrário das limitações de tamanho de arquivo nos sistemas de arquivos EXT3, o HANA não reconhece implicitamente a limitação de armazenamento imposta pelo armazenamento do SAP HANA em instâncias grandes. Como resultado, o HANA não criará automaticamente um novo arquivo de dados quando o limite de tamanho de arquivo de 16TB for atingido. Como o HANA tenta aumentar o arquivo para além de 16 TB, o HANA relatará erros e o servidor de índice falhará no final.

> [!IMPORTANT]
> Para evitar que o HANA tente aumentar os arquivos de dados além do limite de tamanho de arquivo de 16 TB do armazenamento de instância grande do HANA, você precisa definir os seguintes parâmetros no arquivo de configuração global. ini do HANA
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Consulte também SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Lembre-se do SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Próximas etapas**
- Veja [Cenários com suporte para HANA em Instâncias Grandes](hana-supported-scenario.md)
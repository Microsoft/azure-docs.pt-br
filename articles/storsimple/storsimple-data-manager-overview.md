---
title: Visão geral do Gerenciador de Dados do Microsoft Azure StorSimple | Microsoft Docs
description: Fornece uma visão geral do serviço Gerenciador de Dados do StorSimple
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876112"
---
# <a name="storsimple-data-manager-solution-overview"></a>Visão geral da solução do Gerenciador de Dados do StorSimple

## <a name="overview"></a>Visão geral

O Microsoft Azure StorSimple usa o armazenamento em nuvem como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e na nuvem. Os dados são armazenados na nuvem em um formato compactado e com eliminação de duplicação para máxima eficiência e reduzir os custos. Como os dados são armazenados no formato do StorSimple, eles não são prontamente consumíveis por outros aplicativos de nuvem que você queira usar.

O Gerenciador de Dados do StorSimple permite acessar e usar os dados no formato do StorSimple na nuvem. Ele faz isso transformando o formato do StorSimple em arquivos e blobs nativos, que você pode usar com outros serviços, como os Serviços de Mídia do Azure, Azure HDInsights e Azure Machine Learning.

Este artigo fornece uma visão geral da solução do Gerenciador de Dados do StorSimple. Também explica como você pode usar esse serviço para escrever aplicativos que usam dados do StorSimple e outros serviços do Azure na nuvem.

## <a name="how-it-works"></a>Como ele funciona?

O serviço do Gerenciador de Dados do StorSimple identifica dados do StorSimple na nuvem a partir de um dispositivo local de série StorSimple 8000. Os dados do StorSimple na nuvem são duplicados e compactados no formato do StorSimple. O serviço do Gerenciador de Dados fornece APIs para extrair os dados no formato do StorSimple e transformá-las em outros formatos, como blobs do Azure e arquivos do Azure. Esses dados transformados são consumidos prontamente pelo HDInsight do Azure e Serviços de Mídia do Azure. A transformação de dados, portanto, permite que esses serviços operem os dados transformados do StorSimple do dispositivo local de séries do StorSimple 8000. Esse fluxo é ilustrado no diagrama a seguir.

![Diagrama de alto nível](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Casos de uso do Gerenciador de Dados

Você pode usar o Gerenciador de Dados com as Funções do Azure, a Automação do Azure e o Azure Data Factory para ter fluxos de trabalho em execução em seus dados ao chegarem no StorSimple. Você talvez queira processar seu conteúdo de mídia que você armazenou no StorSimple com o Azure Media Services, ou executar um algoritmo do Machine Learning nesses dados ou colocar um cluster de Hadoop para analisar os dados armazenados no StorSimple. Com a grande variedade de serviços disponíveis no Azure combinados com os dados no StorSimple, você pode desbloquear a capacidade dos seus dados.


## <a name="region-availability"></a>Disponibilidade de região

O Gerenciador de Dados do StorSimple está disponível nas 7 regiões a seguir:

 - Sudeste Asiático
 - East US
 - Oeste dos EUA
 - Oeste dos EUA 2
 - Centro-Oeste dos EUA
 - Norte da Europa
 - Europa Ocidental

No entanto, o Gerenciador de Dados do StorSimple pode ser usado para transformar dados nas regiões a seguir. 

![Regiões disponíveis para dados](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Este conjunto é maior, porque a implantação de recursos em qualquer uma das regiões acima é capaz de colocar o processo de transformação nas regiões abaixo. Portanto, enquanto os dados residirem em qualquer uma das 19 regiões, será possível transformar os dados usando esse serviço.


## <a name="choosing-a-region"></a>Escolhendo uma região

Recomendamos que:
 - Sua conta de armazenamento de origem (aquela associada ao seu dispositivo do StorSimple) e a conta de armazenamento de destino (onde você deseja os dados em formato nativo) na mesma região do Azure.
 - Você leva sua definição de trabalho e o Gerenciador de Dados na região que contém a conta de armazenamento do StorSimple. Se isso não for possível, coloque o Gerenciador de Dados na região do Azure mais próxima e, em seguida, crie a definição do trabalho na mesma região da sua conta de armazenamento do StorSimple. 

    Se sua conta de armazenamento do StorSimple não estiver nas 26 regiões que dão suporte à criação de definição de trabalho, recomendamos que você não execute Gerenciador de Dados do StorSimple, pois você verá latências longas e possíveis encargos de saída.
    
A Microsoft se esforça para garantir que os serviços do Azure estejam sempre disponíveis em todas as regiões. No entanto, as interrupções de serviço não planejadas podem ocorrer por períodos curtos em uma determinada região. Nesses casos, você pode abrir uma definição de Gerenciador de Dados e de trabalho em uma região que não é afetada pela interrupção e executar o trabalho de transformação. Você pode encontrar alguma latência adicional nesse cenário, mas essa pode ser a sua estratégia de recuperação no caso raro de uma interrupção regional.

## <a name="security-considerations"></a>Considerações sobre segurança

O Gerenciador de Dados do StorSimple precisa da chave de criptografia de dados de serviço para transformar a partir do formato do StorSimple para um formato nativo. A chave da criptografia de dados do serviço é gerada quando o primeiro dispositivo registra com o serviço do StorSimple. Para obter mais informações sobre essa chave, acesse [Segurança do StorSimple](storsimple-8000-security.md).

A chave de criptografia de dados de serviço fornecida como uma entrada é armazenada em um cofre de chaves criado quando você cria um Gerenciador de Dados. O cofre reside na mesma região do Azure que o Gerenciador de Dados do StorSimple. Essa chave é excluída quando você exclui o serviço do Gerenciador de Dados.

Essa chave é usada pelos recursos de computação para executar a transformação. Esses recursos de computação estão localizados na mesma região do Azure que a sua definição de trabalho. Essa região pode ou não ser a mesma que a região onde você pode colocar seu Gerenciador de Dados.

Se sua região do Gerenciador de Dados for diferente da sua região de definição de trabalho, é importante entender quais dados/metadados residem em cada uma dessas regiões. O diagrama a seguir ilustra o efeito de ter diferentes regiões para o Gerenciador de Dados e definição de trabalho.

![Definição de serviço e trabalho em regiões diferentes](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Gerenciando informações pessoais

O Gerenciador de Dados do StorSimple não coleta nem exibe informações pessoais. Para mais informações, revise a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, o serviço tem as seguintes limitações:
- Atualmente, o Gerenciador de Dados do StorSimple não funciona com volumes que são criptografados com o BitLocker. Se tentar executar o serviço com uma unidade criptografada, você verá falhas de trabalho.
- Alguns metadados de arquivos (incluindo ACLs) não serão mantidos nos dados transformados.
- Esse serviço funciona apenas com volumes NTFS.
- Tamanhos de caminho do arquivo precisarão ser menores que 256 caracteres, caso contrário o trabalho falhará.

## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).

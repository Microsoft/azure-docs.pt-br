---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0eb47c8ec470ef05f3c6ae37bdc75e5bb1043eb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418636"
---
Se você encontrar problemas ao executar quaisquer operações em galerias de imagens compartilhadas, definições de imagem e versões de imagem, execute o comando com falha novamente no modo de depuração. O modo de depuração é ativado passando a opção **-debug** com a CLI e a opção **-Debug** com o PowerShell. Depois de localizar o erro, siga este documento para solucionar os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Não é possível criar uma galeria de imagens compartilhadas

Possíveis causas:

*O nome da galeria é inválido.*

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode ter traços. Altere o nome da galeria e tente novamente. 

*O nome da galeria não é exclusivo na sua assinatura.*

Escolha outro nome de galeria e tente novamente.


## <a name="unable-to-create-an-image-definition"></a>Não é possível criar uma definição de imagem 

Possíveis causas:

*o nome da definição de imagem é inválido.*

Os caracteres permitidos para a definição da imagem são letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. Altere o nome da definição de imagem e tente novamente.

*As propriedades obrigatórias para criar uma definição de imagem não são preenchidas.*

As propriedades como nome, editor, oferta, sku e tipo de sistema operacional são obrigatórias. Verifique se todas as propriedades estão sendo passadas.

Verifique se o **OSType**, seja Linux ou Windows, da definição da imagem é igual ao da imagem gerenciada de origem que você está usando para criar a versão da imagem. 


## <a name="unable-to-create-an-image-version"></a>Não é possível criar uma versão da imagem 

Possíveis causas:

*O nome de versão da imagem é inválido.*

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion.MinorVersion.Patch*. Altere o nome da versão de imagem e tente novamente.

*A imagem gerenciada de origem da qual a versão da imagem está sendo criada não foi encontrada.* 

Verifique se a imagem de origem existe e está na mesma região que a versão da imagem.

*A imagem gerenciada não terminou de ser provisionada.*

Verifique se o estado de provisionamento da imagem gerenciada de origem é **Êxito**.

*Ainda não há suporte para a região de origem.*

Use a tabela a seguir para ver se a região de origem pretendida tem suporte:
<br>

| Criar galeria no ou "região de origem"   | Replicar a versão ou "região de destino" |
|----------------------------------------|-------------------------------------------|
| Centro-Oeste dos EUA                        | Todas as regiões de Nuvem Pública do Azure            |
| Centro-Sul dos Estados Unidos                       |                                           |
| Leste dos EUA 2                              |                                           |
| Sudeste Asiático                         |                                           |
| Europa Ocidental                            |                                           |

<br>

A lista de regiões de destino deve incluir a região de origem da versão da imagem. Inclua a região de origem na lista de regiões de destino para as quais você deseja que o Azure replique sua versão da imagem.

*A replicação para todas as regiões de destino não foi concluída.*

Use o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas até a conclusão do trabalho. Se falhar, execute o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino para as quais a versão da imagem esteja sendo replicada, considere fazer a replicação em fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Não é possível criar uma VM ou conjunto de dimensionamento 

Possíveis causas:

*O usuário que está tentando criar uma VM ou um conjunto de dimensionamento de máquinas virtuais ou não tem acesso de leitura para a versão da imagem.*

Entre em contato com o proprietário da assinatura e peça para ele conceder acesso de leitura para a versão da imagem ou os recursos pai (como a galeria de imagens compartilhadas ou a definição de imagem) por meio de RBAC ([Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)). 

*A versão da imagem não foi encontrada.*

Verifique se a região em que você está tentando criar uma escala de VM ou máquina virtual está incluída na lista de regiões de destino da versão da imagem. Se a região já estiver na lista de regiões de destino, verifique se o trabalho de replicação foi concluído. Você pode usar o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. 

*A criação do conjunto de dimensionamento de máquinas virtuais ou VM leva muito tempo.*

Verifique se que o **OSType** da versão da imagem da qual você está tentando criar a VM ou conjunto de dimensionamento de máquinas virtuais tem o mesmo **OSType** da imagem gerenciada de origem que você usou para criar a versão da imagem. 

## <a name="unable-to-share-resources"></a>Não é possível compartilhar recursos

O compartilhamento de recursos de versão de imagem, definição de imagem e galeria de imagens compartilhadas entre assinaturas é habilitado por meio do RBAC ([Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)). 

## <a name="replication-is-slow"></a>A replicação é lenta

Use o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas até a conclusão do trabalho. Se falhar, dispare o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino para as quais a versão da imagem esteja sendo replicada, considere fazer a replicação em fases.

## <a name="azure-limits-and-quotas"></a>Limites e cotas do Azure 

[Limites e cotas do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) se aplicam a todos os recursos de versão de imagem, definição da imagem e galeria de imagens compartilhadas. Fique dentro dos limites para suas assinaturas. 




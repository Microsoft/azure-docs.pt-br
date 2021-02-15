---
title: Práticas recomendadas de marca de imagem
description: Práticas recomendadas para marcação e controle de versão de imagens de contêiner do Docker ao enviar imagens e extrair imagens de um registro de contêiner do Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75445745"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Recomendações para marcação e controle de versão de imagens de contêiner

Ao enviar por push as imagens de contêiner para um registro de contêiner e, em seguida, implantá-las, você precisa de uma estratégia para marcação e controle de imagens. Este artigo aborda duas abordagens e onde cada uma se ajusta durante o ciclo de vida do contêiner:

* **Marcas estáveis** -marcas que você reutiliza, por exemplo, para indicar uma versão principal ou secundária, como *mycontainerimage: 1.0*.
* **Marcas exclusivas** – uma marca diferente para cada imagem que você envia por push para um registro, como *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Marcas estáveis

**Recomendação**: use marcas estáveis para manter as **imagens base** para suas compilações de contêiner. Evite implantações com marcas estáveis, pois essas marcas continuam a receber atualizações e podem introduzir inconsistências em ambientes de produção.

*Marcas estáveis* significam que um desenvolvedor, ou um sistema de compilação, pode continuar a extrair uma marca específica, o que continua a obter atualizações. Estável não significa que o conteúdo está congelado. Em vez disso, o estável implica que a imagem deve ser estável para a intenção dessa versão. Para permanecer "estável", pode ser atendido para aplicar patches de segurança ou atualizações de estrutura.

### <a name="example"></a>Exemplo

Uma equipe de estrutura vem com A versão 1,0. Eles sabem que enviarão atualizações, incluindo atualizações secundárias. Para dar suporte a marcas estáveis para uma determinada versão principal e secundária, elas têm dois conjuntos de marcas estáveis.

* `:1` – uma marca estável para a versão principal. `1` representa a versão "mais nova" ou "1. * mais recente".
* `:1.0`-uma marca estável para a versão 1,0, permitindo que um desenvolvedor se vincule às atualizações de 1,0 e não seja rolado para a 1,1 quando for liberado.

A equipe também usa a `:latest` marca, que aponta para a marca estável mais recente, não importa qual é a versão principal atual.

Quando as atualizações da imagem base estão disponíveis ou qualquer tipo de lançamento de serviço da estrutura, as imagens com as marcas estáveis são atualizadas para o resumo mais recente que representa a versão estável mais atual dessa versão.

Nesse caso, as marcas principal e secundária estão sendo atendidas continuamente. A partir de um cenário de imagem base, isso permite que o proprietário da imagem forneça imagens atendidas.

### <a name="delete-untagged-manifests"></a>Excluir manifestos não marcados

Se uma imagem com uma marca estável for atualizada, a imagem marcada anteriormente será desmarcada, resultando em uma imagem órfã. O manifesto da imagem anterior e os dados da camada exclusiva permanecem no registro. Para manter o tamanho do registro, você pode excluir periodicamente os manifestos não marcados resultantes de atualizações de imagem estáveis. Por exemplo, [limpar automaticamente](container-registry-auto-purge.md) manifestos não marcados com mais de uma duração especificada ou definir uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="unique-tags"></a>Marcas exclusivas

**Recomendação**: use marcas exclusivas para **implantações**, especialmente em um ambiente que possa ser dimensionado em vários nós. Você provavelmente desejará implantações deliberadas de uma versão consistente dos componentes. Se o contêiner for reiniciado ou um orquestrador expandir mais instâncias, seus hosts não receberão acidentalmente uma versão mais nova, inconsistente com os outros nós.

A marcação exclusiva significa simplesmente que cada imagem enviada por push a um registro tem uma marca exclusiva. As marcas não são reutilizadas. Há vários padrões que você pode seguir para gerar marcas exclusivas, incluindo:

* **Carimbo de data/hora** – essa abordagem é bastante comum, pois você pode dizer claramente quando a imagem foi criada. Mas como correlacioná-lo de volta ao seu sistema de compilação? Você precisa encontrar a compilação que foi concluída ao mesmo tempo? Em qual fuso horário você está? Todos os sistemas de compilação são calibrados para o UTC?
* **Confirmação do git**  – essa abordagem funciona até que você comece a dar suporte a atualizações da imagem base. Se ocorrer uma atualização de imagem base, o sistema de compilação iniciará a mesma confirmação de git que a compilação anterior. No entanto, a imagem base tem um novo conteúdo. Em geral, uma *confirmação de git fornece uma marca*semiestável.
* **Resumo do manifesto** -cada imagem de contêiner enviada por push para um registro de contêiner é associada a um manifesto, identificado por um hash SHA-256 exclusivo ou resumo. Embora seja exclusivo, o resumo é longo, difícil de ler e não correlacionado ao seu ambiente de compilação.
* **ID da compilação** -essa opção pode ser melhor, pois provavelmente é incremental e permite que você se correlacione de volta à compilação específica para localizar todos os artefatos e logs. No entanto, como um resumo de manifesto, pode ser difícil para uma leitura humana.

  Se sua organização tiver vários sistemas de compilação, a prefixação da marca com o nome do sistema de compilação será uma variação nessa opção: `<build-system>-<build-id>` . Por exemplo, você poderia diferenciar as compilações do sistema de compilação Jenkins da equipe de API e do sistema de Build de Azure Pipelines da equipe da Web.

### <a name="lock-deployed-image-tags"></a>Bloquear marcas de imagem implantadas

Como prática recomendada, recomendamos que você [bloqueie](container-registry-image-lock.md) qualquer marca de imagem implantada, definindo seu `write-enabled` atributo como `false` . Essa prática impede que você remova inadvertidamente uma imagem do registro e possivelmente interrompa suas implantações. Você pode incluir a etapa de bloqueio em seu pipeline de liberação.

O bloqueio de uma imagem implantada ainda permite remover outras imagens não implantadas do registro usando recursos do registro de contêiner do Azure para manter o registro. Por exemplo, [limpe automaticamente](container-registry-auto-purge.md) manifestos não marcados ou imagens desbloqueadas com mais de uma duração especificada ou defina uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma discussão mais detalhada sobre os conceitos deste artigo, consulte a postagem do blog [marcação do Docker: práticas recomendadas para marcação e controle de versão de imagens do Docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Para ajudar a maximizar o desempenho e o uso econômico de seu registro de contêiner do Azure, consulte [práticas recomendadas para o registro de contêiner do Azure](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


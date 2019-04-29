---
title: implante imagens personalizadas, vários contêineres ou imagens internas - Serviço de Aplicativo do Azure | Microsoft Docs
description: Como decidir entre a implantação de contêiner do Docker personalizada, vários contêineres e uma estrutura do aplicativo interna para o Serviço de Aplicativo no Linux
keywords: serviço de aplicativo do azure, aplicativo web, linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c8a700bcd2780ef7b0c7ad1fbb513d4b4febffcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849975"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Imagem personalizada, vários contêineres ou imagem da plataforma interna?

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) oferece três caminhos diferentes para publicar aplicativos na Web:

- **Implantação de imagem personalizada**: transforme seu aplicativo em Docker em uma imagem do Docker que contém todos os arquivos e as dependências em um pacote pronto para execução.
- **Implantação de vários contêineres**: transforme seu aplicativo em Docker entre vários contêineres usando um arquivo de configuração Kubernetes ou Docker Compose.
- **Implantação de aplicativo com uma imagem de plataforma interna**: nossas imagens de plataforma interna contêm tempos de execução de aplicativo Web comuns e dependências, como Node e PHP. Use qualquer um dos [métodos de implantação do Serviço de Aplicativo do Azure](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implantar seu aplicativo no armazenamento de seu aplicativo Web e, em seguida, use uma imagem de plataforma interna para executá-lo.

## <a name="which-method-is-right-for-your-app"></a>Qual método é adequado para seu aplicativo? 

Os principais fatores a considerar são:

- **Disponibilidade do Docker em seu fluxo de trabalho de desenvolvimento**: o desenvolvimento de imagem personalizada requer conhecimento básico do fluxo de trabalho de desenvolvimento do Docker. A implantação de uma imagem personalizada em um aplicativo Web exige a publicação de sua imagem personalizada em um host do repositório como o Hub do Docker. Se você estiver familiarizado com o Docker e puder adicionar tarefas de Docker ao fluxo de trabalho do build ou se você já estiver publicando seu aplicativo como uma imagem do Docker, uma imagem personalizada será certamente a melhor opção.
- **Arquitetura de várias camadas**: implantar vários contêineres como, por exemplo, uma camada de aplicativo Web e uma camada de API, para separar os recursos usando vários contêineres. 
- **Desempenho do aplicativo**: aumentar o desempenho do seu aplicativo de vários contêineres usando uma camada de cache, como o Redis. Selecione vários contêineres para fazer isso.
- **Requisitos de tempo de execução exclusivos**: as imagens de plataforma interna são projetadas para atender às necessidades da maioria dos aplicativos Web, mas são limitadas na sua capacidade de personalização. Seu aplicativo pode ter dependências exclusivas ou outros requisitos de tempo de execução que excedem o que as imagens internas são capazes.
- **Requisitos de build**: com a [implantação contínua](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), você pode ter seu aplicativo em execução no Azure diretamente do código-fonte. Nenhum processo de publicação ou de build externo é necessário. No entanto, há um limite para a capacidade de personalização e a disponibilidade das ferramentas de build dentro do mecanismo de implantação do [Kudu](https://github.com/projectkudu/kudu/wiki). Seu aplicativo pode superar os recursos do Kudu à medida que cresce em suas dependências ou requisitos para a lógica de build personalizada.
- **Requisitos de leitura/gravação em disco**: todos os aplicativos Web têm alocado um volume de armazenamento para conteúdo da Web. Este volume, com o apoio do Armazenamento do Azure, está montado no sistema de arquivos do aplicativo `/home`. Ao contrário dos arquivos no sistema de arquivos do contêiner, os arquivos no volume de conteúdo estão acessíveis em todas as instâncias de escala de um aplicativo e as modificações persistirão entre as reinicializações de aplicativo. No entanto, a latência de disco do volume de conteúdo é maior e mais variável do que a latência do sistema de arquivos local do contêiner e o acesso pode ser afetado por atualizações de plataforma, tempo de inatividade não planejado e problemas de conectividade de rede. Aplicativos que exigem grande acesso somente leitura a arquivos de conteúdo podem se beneficiar da implantação de imagem personalizada, o que coloca arquivos no sistema de arquivos de imagem em vez de no volume de conteúdo.
- **Uso de recursos de build**: quando um aplicativo é implantado da origem, os scripts de implantação executados com o Kudu usam os mesmos recursos de armazenamento e computação do Plano do Serviço de Aplicativo que o aplicativo em execução. Implantações de aplicativo grande podem consumir mais recursos ou tempo que o desejado. Em particular, muitos fluxos de trabalho de implantação geram muita atividade de disco no volume de conteúdo do aplicativo, que não é otimizado para essa atividade. Uma imagem personalizada fornece todos os arquivos e as dependências do aplicativo ao Azure em um único pacote sem a necessidade de transferências de arquivos adicionais ou ações de implantação.
- **Necessidade de iteração rápida**: transformar um aplicativo em Docker requer etapas de build adicionais. Para que as alterações entrem em vigor, você deve enviar por push a nova imagem a um repositório com cada atualização. Essas atualizações são acionadas para o ambiente do Azure. Se um dos contêineres internos atende às necessidades do seu aplicativo, a implantação da origem pode oferecer um fluxo de trabalho de desenvolvimento mais rápido.

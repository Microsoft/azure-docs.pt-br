---
title: "Implantação do Aplicativo Web do Azure no Linux – imagem personalizada ou imagem da plataforma interna?  | Microsoft Docs"
description: "Como decidir entre a implantação de contêiner do Docker personalizada e uma estrutura de aplicativos internos"
keywords: "serviço de aplicativo do azure, aplicativo web, linux, oss"
services: app-service
documentationCenter: 
authors: nickwalk
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: nickwalk
ms.openlocfilehash: 322533a9dce54222943f8b2a3d653780a674c8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-image-or-built-in-platform-image"></a>Imagem personalizada ou imagem da plataforma interna?

O [Aplicativo Web no Linux](app-service-linux-intro.md) oferece dois caminhos diferentes para publicar aplicativos na Web:

- **Implantação de imagem personalizada**: transforme seu aplicativo em Docker em uma imagem do Docker que contém todos os arquivos e as dependências em um pacote pronto para execução.
- **Implantação de aplicativo com uma imagem de plataforma interna**: nossas imagens de plataforma interna contêm tempos de execução de aplicativo Web comuns e dependências, como o Node e PHP. Use qualquer um dos [métodos de implantação do Serviço de Aplicativo do Azure](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implantar seu aplicativo no armazenamento de seu aplicativo Web e, em seguida, use uma imagem de plataforma interna para executá-lo.

Qual método é adequado para seu aplicativo? Os principais fatores a considerar são:

- **Disponibilidade do Docker em seu fluxo de trabalho de desenvolvimento**: o desenvolvimento de imagem personalizada requer conhecimento básico do fluxo de trabalho de desenvolvimento do Docker. A implantação de uma imagem personalizada em um aplicativo Web exige a publicação de sua imagem personalizada em um host do repositório como o Hub do Docker. Se você estiver familiarizado com o Docker e puder adicionar tarefas de Docker ao fluxo de trabalho do build ou se você já estiver publicando seu aplicativo como uma imagem do Docker, uma imagem personalizada será certamente a melhor opção.
- **Requisitos de tempo de execução exclusivos**: as imagens de plataforma interna são projetadas para atender às necessidades da maioria dos aplicativos Web, mas são limitadas na sua capacidade de personalização. Seu aplicativo pode ter dependências exclusivas ou outros requisitos de tempo de execução que excedem o que as imagens internas são capazes.
- **Requisitos de build**: com a [implantação contínua](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), você pode ter seu aplicativo em execução no Azure diretamente do código-fonte. Nenhum processo de publicação ou de build externo é necessário. No entanto, há um limite para a capacidade de personalização e a disponibilidade das ferramentas de build dentro do mecanismo de implantação do [Kudu](https://github.com/projectkudu/kudu/wiki). Seu aplicativo pode superar os recursos do Kudu à medida que cresce em suas dependências ou requisitos para a lógica de build personalizada.
- **Requisitos de leitura/gravação em disco**: todos os aplicativos Web têm alocado um volume de armazenamento para conteúdo da web. Este volume, com o apoio do Armazenamento do Azure, está montado no sistema de arquivos do aplicativo `/home`. Ao contrário dos arquivos no sistema de arquivos do contêiner, os arquivos no volume de conteúdo estão acessíveis em todas as instâncias de escala de um aplicativo e as modificações persistirão entre as reinicializações de aplicativo. No entanto, a latência de disco do volume de conteúdo é maior e mais variável do que a latência do sistema de arquivos local do contêiner e o acesso pode ser afetado por atualizações de plataforma, tempo de inatividade não planejado e problemas de conectividade de rede. Aplicativos que exigem grande acesso somente leitura a arquivos de conteúdo podem se beneficiar da implantação de imagem personalizada, o que coloca arquivos no sistema de arquivos de imagem em vez de no volume de conteúdo.
- **Uso de recursos de build**: quando um aplicativo é implantado da origem, os scripts de implantação executados com o Kudu usam os mesmos recursos de armazenamento e computação do Plano do Serviço de Aplicativo como o aplicativo em execução. Implantações de aplicativo grande podem consumir mais recursos ou tempo que o desejado. Em particular, muitos fluxos de trabalho de implantação geram muita atividade de disco no volume de conteúdo do aplicativo, que não é otimizado para essa atividade. Uma imagem personalizada fornece todos os arquivos e as dependências do aplicativo ao Azure em um único pacote sem a necessidade de transferências de arquivos adicionais ou ações de implantação.
- **Necessidade de iteração rápida**: transformar um aplicativo em Docker requer etapas de build adicionais. Para que as alterações entrem em vigor, você deve enviar por push a nova imagem a um repositório com cada atualização. Essas atualizações são acionadas para o ambiente do Azure. Se um dos contêineres internos atende às necessidades do seu aplicativo, a implantação da origem pode oferecer um fluxo de trabalho de desenvolvimento mais rápido.
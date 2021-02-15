---
title: Visão geral das Tarefas do ACR
description: Uma introdução às tarefas do ACR, um conjunto de recursos no registro de contêiner do Azure que fornece criação de imagem de contêiner segura e automatizada, gerenciamento e aplicação de patches na nuvem.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: b6df415bd55979ef00f6921321dbc254ef7a7e59
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562847"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar compilações de imagem de contêiner e manutenção com tarefas ACR

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. No entanto, o que resta é a necessidade de abordar como essa virtualização de aplicativos é gerenciada e corrigida no ciclo de vida do contêiner.

## <a name="what-is-acr-tasks"></a>O que são as Tarefas do ACR?

As **Tarefas do ACR** são um pacote de recursos do Registro de Contêiner do Azure. Ele fornece criação de imagem de contêiner baseada em nuvem para [plataformas](#image-platforms) , incluindo Linux, Windows e ARM, e pode automatizar a [aplicação de patches do sistema operacional e da estrutura](#automate-os-and-framework-patching) para seus contêineres do Docker. As tarefas de ACR não apenas estende o ciclo de desenvolvimento de "loop interno" para a nuvem com compilações de imagem de contêiner sob demanda, mas também permite compilações automatizadas disparadas por atualizações de código-fonte, atualizações para a imagem base de um contêiner ou temporizadores. Por exemplo, com gatilhos de atualização de imagem base, você pode automatizar o fluxo de trabalho do sistema operacional e aplicação de patch de estrutura de aplicativo, mantendo ambientes seguros enquanto obedece aos princípios de contêineres imutáveis.

## <a name="task-scenarios"></a>Cenários de tarefas

As tarefas ACR dão suporte a vários cenários para criar e manter imagens de contêiner e outros artefatos. Consulte as seções a seguir neste artigo para obter detalhes.

* **[Tarefa rápida](#quick-task)** – crie e envie por push uma única imagem de contêiner para um registro de contêiner sob demanda, no Azure, sem a necessidade de uma instalação de mecanismo do Docker local. Pense em `docker build`, `docker push` na nuvem.
* **Tarefas disparadas automaticamente** – habilite um ou mais *gatilhos* para criar uma imagem:
  * **[Disparar na atualização do código-fonte](#trigger-task-on-source-code-update)** 
  * **[Disparar na atualização da imagem base](#automate-os-and-framework-patching)** 
  * **[Disparar em uma agenda](#schedule-a-task)** 
* **[Tarefa de várias etapas](#multi-step-tasks)** – estenda a funcionalidade de compilação e envio de imagem única de tarefas ACR com fluxos de trabalho de várias etapas com base em vários contêineres. 

Cada tarefa ACR tem um [contexto de código-fonte](#context-locations) associado-o local de um conjunto de arquivos de origem usados para criar uma imagem de contêiner ou outro artefato. Os contextos de exemplo incluem um repositório git ou um sistema de arquivos local.

As tarefas também podem aproveitar as [variáveis de execução](container-registry-tasks-reference-yaml.md#run-variables), para que você possa reutilizar definições de tarefas e padronizar marcas para imagens e artefatos.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, compilar e testar o aplicativo antes de confirmar no controle do código-fonte, é realmente o início do gerenciamento de ciclo de vida do contêiner.

Antes que você confirme sua primeira linha de código, o recurso [tarefas rápidas](container-registry-tutorial-quick-task.md) das Tarefas do ACR pode fornecer uma experiência de desenvolvimento integrada ao descarregar os builds de imagem de contêiner no Azure. Com as tarefas rápidas, você pode verificar suas definições de build automatizadas e detectar possíveis problemas antes de confirmar o código.

Usando o formato `docker build` familiar, o comando [az acr build][az-acr-build] na CLI do Azure usa um [contexto](#context-locations) (o conjunto de arquivos a ser compilado), envia-o às Tarefas do ACR e, por padrão, envia a imagem de build por push ao seu registro após a conclusão.

Para obter uma introdução, consulte o guia de início rápido para [criar e executar uma imagem de contêiner](container-registry-quickstart-task-cli.md) no registro de contêiner do Azure.  

O recurso Tarefas de ACR foi projetado como uma primitiva do ciclo de vida do contêiner. Por exemplo, é possível integrar as Tarefas do ACR à sua solução de CI/CD. Executando [az login][az-login] com uma [entidade de serviço][az-login-service-principal], sua solução CI/CD pode enviar os comandos [az acr build][az-acr-build] para iniciar compilações da imagem.

Saiba como usar as tarefas rápidas no primeiro tutorial das Tarefas do ACR, [Compilar imagens de contêiner na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se você quiser criar e enviar por push uma imagem diretamente do código-fonte, sem um Dockerfile, o registro de contêiner do Azure fornecerá o comando [AZ ACR Pack Build][az-acr-pack-build] (versão prévia). Essa ferramenta cria e envia uma imagem do código-fonte do aplicativo usando a [nuvem nativa Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Disparar tarefa na atualização do código-fonte

Dispare uma compilação de imagem de contêiner ou uma tarefa de várias etapas quando o código é confirmado ou uma solicitação de pull é feita ou atualizada para um repositório git público ou privado no GitHub ou no Azure DevOps. Por exemplo, configure uma tarefa de compilação com o comando de CLI do Azure [AZ ACR Task Create][az-acr-task-create] especificando um repositório git e, opcionalmente, uma ramificação e Dockerfile. Quando a sua equipe atualiza o código no repositório, um webhook criado por tarefas de ACR dispara uma compilação da imagem de contêiner definida no repositório. 

As tarefas ACR dão suporte aos seguintes gatilhos quando você define um repositório Git como o contexto da tarefa:

| Gatilho | Habilitado por padrão |
| ------- | ------------------ |
| Commit | Sim |
| Solicitação de pull | Não |

Para configurar um gatilho de atualização de código-fonte, você precisa fornecer à tarefa um PAT (token de acesso pessoal) para definir o webhook no GitHub público ou privado ou no repositório DevOps do Azure.

> [!NOTE]
> Atualmente, as Tarefas do ACR não dão suporte a gatilhos de solicitação de pull ou commit em Repos do GitHub Enterprise.

Saiba como disparar builds na confirmação do código-fonte no segundo tutorial das Tarefas do ACR, [Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder das tarefas do ACR para aprimorar verdadeiramente seu fluxo de trabalho de Build de contêiner vem da sua capacidade de detectar uma atualização para uma *imagem de base*. Um recurso da maioria das imagens de contêiner, uma imagem de base é uma imagem pai na qual uma ou mais imagens de aplicativo se baseiam. As imagens básicas normalmente contêm o sistema operacional e, às vezes, estruturas de aplicativo. 

Você pode configurar uma tarefa ACR para rastrear uma dependência em uma imagem base ao criar uma imagem de aplicativo. Quando a imagem base atualizada é enviada para o registro ou uma imagem base é atualizada em um repositório público, como no Hub do Docker, as tarefas do ACR podem criar automaticamente qualquer imagem de aplicativo baseada nela.
Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

Saiba mais sobre [gatilhos de atualização de imagem base](container-registry-tasks-base-images.md) para tarefas ACR. E saiba como disparar uma compilação de imagem quando uma imagem base é enviada por push para um registro de contêiner no tutorial [automatizar a imagem de contêiner cria quando uma imagem base é atualizada em um registro de contêiner do Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Agendar uma tarefa

Opcionalmente, agende uma tarefa Configurando um ou mais *gatilhos de temporizador* ao criar ou atualizar a tarefa. Agendar uma tarefa é útil para executar cargas de trabalho de contêiner em um agendamento definido ou executar operações de manutenção ou testes em imagens enviadas por push regularmente para o registro. Para obter detalhes, consulte [executar uma tarefa ACR em um agendamento definido](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas de várias etapas

As tarefas de várias etapas fornecem uma definição de tarefas com base em etapa e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas de tarefa definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md) especificam operações individuais de criação e envio por push para imagens de contêiner ou outros artefatos. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapa que automatiza o seguinte:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Executar o contêiner de teste de aplicativo Web, que executa testes em relação ao contêiner de aplicativo em execução
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo morto do gráfico do Helm

As tarefas de várias etapas permitem que você divida o build, a execução e o teste de uma imagem em etapas mais combináveis, com suporte para dependência entre etapas. Com as tarefas de várias etapas nas Tarefas do ACR, há um controle mais granular sobre os fluxos de trabalho de build de imagem, de teste e de aplicação de patch no sistema operacional e na estrutura.

Saiba mais sobre as tarefas de várias etapas em [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Executar tarefas de várias etapas de build, teste e patch nas Tarefas do ACR).

## <a name="context-locations"></a>Locais de contexto

A tabela a seguir mostra exemplos de locais de contexto com suporte para tarefas ACR:

| Local do contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch principal do GitHub | Arquivos dentro da ramificação principal (ou outro padrão) de um repositório GitHub público ou privado.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Branch específico de um repositório GitHub público ou privado.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório GitHub público ou privado. O exemplo mostra a combinação de uma especificação de ramificação e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Confirmação do GitHub | Confirmação específica em um repositório GitHub público ou privado. O exemplo mostra a combinação de uma especificação de hash de confirmação (SHA) e de subpasta. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Subpasta DevOps do Azure | Arquivos dentro de uma subpasta em um repositório público ou privado do Azure. O exemplo mostra a combinação de especificação de ramificação e subpasta. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |
| Artefato no registro de contêiner | Arquivos de [artefato de OCI](container-registry-oci-artifacts.md) em um repositório de registro de contêiner. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Ao usar um repositório git privado como um contexto para uma tarefa, você precisa fornecer um PAT (token de acesso pessoal).

## <a name="image-platforms"></a>Plataformas de imagem

Por padrão, as tarefas ACR criam imagens para o SO Linux e a arquitetura AMD64. Especifique a `--platform` marca para criar imagens do Windows ou imagens do Linux para outras arquiteturas. Especifique o sistema operacional e, opcionalmente, uma arquitetura com suporte no formato de sistema operacional/arquitetura (por exemplo, `--platform Linux/arm` ). Para arquiteturas ARM, especifique opcionalmente uma variante no formato do sistema operacional/arquitetura/variante (por exemplo, `--platform Linux/arm64/v8` ):

| Sistema operacional | Arquitetura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Exibir saída da tarefa

Cada execução de tarefa gera a saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com êxito. Quando você dispara uma tarefa manualmente, a saída de log para a execução da tarefa é transmitida para o console e também armazenada para recuperação posterior. Quando uma tarefa é disparada automaticamente, por exemplo, por uma confirmação de código-fonte ou uma atualização de imagem base, os logs de tarefa são armazenados somente. Exiba os logs de execução no portal do Azure ou use o comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Veja mais sobre como [Exibir e gerenciar logs de tarefas](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Próximas etapas

Quando estiver pronto para automatizar as compilações e a manutenção da imagem de contêiner na nuvem, confira a [série de tutoriais de tarefas do ACR](container-registry-tutorial-quick-task.md).

Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

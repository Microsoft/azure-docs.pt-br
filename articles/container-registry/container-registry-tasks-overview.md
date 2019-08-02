---
title: Automatizar a criação e aplicação de patch de imagens de contêiner com tarefas de registro de contêiner do Azure (tarefas ACR)
description: Uma introdução às tarefas do ACR, um conjunto de recursos no registro de contêiner do Azure que fornece criação de imagem de contêiner segura e automatizada, gerenciamento e aplicação de patches na nuvem.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 65debc8c65752150651d00d84eeff469cefbc268
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311880"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar compilações de imagem de contêiner e manutenção com tarefas ACR

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. No entanto, o que resta é a necessidade de abordar como essa virtualização de aplicativos é gerenciada e corrigida no ciclo de vida do contêiner.

## <a name="what-is-acr-tasks"></a>O que são as Tarefas do ACR?

As **Tarefas do ACR** são um pacote de recursos do Registro de Contêiner do Azure. Ele fornece criação de imagens de contêiner baseada em nuvem para Linux, Windows e ARM e pode automatizar [o patch de sistema operacional e de estrutura](#automate-os-and-framework-patching) para seus contêineres do Docker. Além das Tarefas do ACR ampliarem seu ciclo de desenvolvimento de "loop interno" para a nuvem com builds de imagem de contêiner sob demanda, elas também permitem que builds automatizados na confirmação de código-fonte ou quando a imagem base do contêiner é atualizada. Com gatilhos de atualização de imagem de base, você pode automatizar seu sistema operacional e o fluxo de trabalho de aplicação de patch da estrutura de aplicativos, mantendo os ambientes protegidos enquanto se adere às entidades de segurança de contêineres imutáveis.

Compile e teste imagens de contêiner com as Tarefas do ACR de quatro maneiras:

* [Tarefa rápida](#quick-task): Compile e envie por push as imagens de contêiner sob demanda, no Azure, sem a necessidade de uma instalação local do Docker Engine. Pense em `docker build`, `docker push` na nuvem. Compile usando o código-fonte local ou um repositório Git.
* [Build na confirmação de código-fonte](#automatic-build-on-source-code-commit): Dispare automaticamente um build de imagem de contêiner quando o código é confirmado para um repositório Git.
* [Compilar na atualização de imagem base](#automate-os-and-framework-patching): Dispare um build de imagem de contêiner quando a imagem base da imagem que foi atualizada.
* [Tarefas de várias etapas](#multi-step-tasks): Defina tarefas de várias etapas que criam imagens, executam contêineres como comandos e enviam imagens por push para um Registro. Esse recurso de tarefas ACR dá suporte à execução de tarefas sob demanda e às operações de compilação, teste e envio de imagens paralelas.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, compilar e testar o aplicativo antes de confirmar no controle do código-fonte, é realmente o início do gerenciamento de ciclo de vida do contêiner.

Antes que você confirme sua primeira linha de código, o recurso [tarefas rápidas](container-registry-tutorial-quick-task.md) das Tarefas do ACR pode fornecer uma experiência de desenvolvimento integrada ao descarregar os builds de imagem de contêiner no Azure. Com as tarefas rápidas, você pode verificar suas definições de build automatizadas e detectar possíveis problemas antes de confirmar o código.

Usando o formato `docker build` conhecido, o comando [AZ ACR Build][az-acr-build] no CLI do Azure usa um *contexto* (o conjunto de arquivos a serem compilados), envia as tarefas de ACR e, por padrão, empurra a imagem interna para o registro após a conclusão.

Para obter uma introdução, consulte o guia de início rápido para [criar e executar uma imagem de contêiner](container-registry-quickstart-task-cli.md) no registro de contêiner do Azure.  

A tabela a seguir mostra alguns exemplos de locais de contexto com suporte para as Tarefas do ACR:

| Local do contexto | DESCRIÇÃO | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro da ramificação principal (ou outro padrão) de um repositório GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Filial específica de um repositório GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório do GitHub. O exemplo mostra a combinação de uma especificação de ramificação e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |

O recurso Tarefas de ACR foi projetado como uma primitiva do ciclo de vida do contêiner. Por exemplo, é possível integrar as Tarefas do ACR à sua solução de CI/CD. Ao executar o [AZ login][az-login] with a [service principal][az-login-service-principal], sua solução de CI/CD poderá emitir comandos [AZ ACR Build][AZ-ACR-Build] para disparar compilações de imagem.

Saiba como usar as tarefas rápidas no primeiro tutorial das Tarefas do ACR, [Compilar imagens de contêiner na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Build automático na confirmação de código-fonte

Use as Tarefas do ACR para disparar automaticamente um build de imagem de contêiner quando o código é confirmado para um repositório Git. Tarefas de compilação, configuráveis com o comando CLI do Azure [AZ ACR Task][az-acr-task], permitem que você especifique um repositório git e, opcionalmente, uma ramificação e Dockerfile. Quando sua equipe confirmar o código para o repositório, um webhook criado nas Tarefas do ACR disparará um build de imagem de contêiner definido no repositório.

> [!IMPORTANT]
> Se você já tiver criado tarefas durante a versão prévia com o comando `az acr build-task`, essas tarefas precisarão ser recriadas usando o comando [az acr task][az-acr-task].

Saiba como disparar builds na confirmação do código-fonte no segundo tutorial das Tarefas do ACR, [Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder das Tarefas do ACR de realmente aprimorar seu fluxo de trabalho de build de contêiner vem da capacidade de detectar uma atualização para uma imagem de base. Quando a imagem base atualizada é enviada para o registro ou uma imagem base é atualizada em um repositório público, como no Hub do Docker, as tarefas do ACR podem criar automaticamente qualquer imagem de aplicativo baseada nela.

As imagens de contêiner podem ser amplamente categorizadas em imagens de *base* e imagens de *aplicativo*. Suas imagens de base normalmente incluem o sistema operacional e estruturas de aplicativo com base nos quais seu aplicativo é criado, em conjunto com outras personalizações. Essas imagens de base são normalmente baseadas em imagens públicas de upstream, por exemplo: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]ou [node. js][base-node]. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Quando um sistema operacional ou uma imagem de estrutura de aplicativo é atualizado pelo mantenedor upstream, por exemplo, com um patch de segurança crítico de sistema operacional, você deverá também atualizar suas imagens de base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Como as Tarefas do ACR descobrem dinamicamente as dependências da imagem base quando criam uma imagem de contêiner, elas podem detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma [tarefa de build](container-registry-tutorial-base-image-update.md#create-a-task) pré-configurada, as Tarefas do ACR **recriam automaticamente cada imagem do aplicativo** para você. Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

Saiba mais sobre a aplicação de patch no sistema operacional e na estrutura no terceiro tutorial das Tarefas do ACR, [Automatizar builds de imagem na atualização da imagem de base com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Atualmente, as atualizações de imagem de base acionam compilações somente quando as imagens base e de aplicativo residem no mesmo registro de contêiner do Azure ou a base reside em um hub público do Docker ou no repositório do registro de contêiner da Microsoft.

## <a name="multi-step-tasks"></a>Tarefas de várias etapas

As tarefas de várias etapas fornecem uma definição de tarefa baseada em etapas e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas da tarefa definem o build de imagem de contêiner individual e operações de push. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapa que automatiza o seguinte:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Executar o contêiner de teste de aplicativo Web que executa testes em relação à execução do contêiner de aplicativo
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo morto do gráfico do Helm

As tarefas de várias etapas permitem que você divida o build, a execução e o teste de uma imagem em etapas mais combináveis, com suporte para dependência entre etapas. Com as tarefas de várias etapas nas Tarefas do ACR, há um controle mais granular sobre os fluxos de trabalho de build de imagem, de teste e de aplicação de patch no sistema operacional e na estrutura.

Saiba mais sobre as tarefas de várias etapas em [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Executar tarefas de várias etapas de build, teste e patch nas Tarefas do ACR).

## <a name="view-task-logs"></a>Exibir logs de tarefa

Cada execução de tarefa gera a saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com êxito. Se você usar o comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)ou [AZ ACR Task execute](/cli/azure/acr/task#az-acr-task-run) para disparar a tarefa, a saída do log para a execução da tarefa será transmitida ao console e também será armazenada para recuperação posterior. Exiba os logs de uma tarefa executada no portal do Azure ou use o comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

A partir de julho de 2019, os dados e logs para tarefas executadas em um registro serão mantidos por padrão por 30 dias e, em seguida, limpos automaticamente. Se você quiser arquivar os dados para uma execução de tarefa, habilite o arquivamento usando o comando [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . O exemplo a seguir habilita o arquivamento para a tarefa executar *CF11* no registro myregistry.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Próximas etapas

Quando estiver pronto para automatizar a aplicação de patch do sistema operacional e da estrutura criando suas imagens de contêiner na nuvem, confira a [série de tutoriais de tarefas ACR](container-registry-tutorial-quick-task.md)de três partes.

Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

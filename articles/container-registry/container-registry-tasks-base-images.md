---
title: Atualizações da imagem de base-tarefas
description: Saiba mais sobre imagens base para imagens de contêiner de aplicativo e sobre como uma atualização de imagem de base pode disparar uma tarefa de registro de contêiner do Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: df33096830cd7b34a288c38c105aff3610315337
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2020
ms.locfileid: "97707479"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Sobre as atualizações de imagem base para tarefas ACR

Este artigo fornece informações básicas sobre as atualizações da imagem base de um aplicativo e como essas atualizações podem disparar uma tarefa de registro de contêiner do Azure.

## <a name="what-are-base-images"></a>O que são as imagens base?

Dockerfiles definir a maioria das imagens de contêiner especifica uma imagem pai da qual a imagem é baseada, geralmente conhecida como sua *imagem base*. Imagens de base normalmente contêm o sistema operacional, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], no qual o resto do camadas do contêiner são aplicadas. Elas também podem incluir estruturas de aplicativo, como [Node.js][base-node] ou [.NET Core][base-dotnet]. Essas imagens básicas normalmente são baseadas em imagens de upstream públicas. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Uma imagem de base geralmente é atualizada pelo mantenedor de imagem para incluir novos recursos ou aprimoramentos para o sistema operacional ou da estrutura na imagem. Patches de segurança são outra causa comum para uma atualização da imagem base. Quando ocorrerem essas atualizações upstream, você também deverá atualizar suas imagens base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Em alguns casos, como uma equipe de desenvolvimento privada, uma imagem de base pode especificar mais do que o sistema operacional ou a estrutura. Por exemplo, uma imagem base pode ser uma imagem de componente de serviço compartilhado que precisa ser controlada. Os membros de uma equipe podem precisar controlar essa imagem base para teste ou atualizar regularmente a imagem ao desenvolver imagens de aplicativos.

## <a name="maintain-copies-of-base-images"></a>Manter cópias de imagens base

Para qualquer conteúdo em seus registros que dependa do conteúdo base mantido em um registro público, como o Hub do Docker, recomendamos que você copie o conteúdo para um registro de contêiner do Azure ou outro registro privado. Em seguida, certifique-se de criar suas imagens de aplicativo referenciando as imagens base privadas. O registro de contêiner do Azure fornece uma capacidade de [importação de imagem](container-registry-import-images.md) para copiar facilmente o conteúdo de registros públicos ou outros registros de contêiner do Azure. A próxima seção descreve o uso de tarefas ACR para rastrear atualizações de imagem de base ao criar atualizações de aplicativo. Você pode rastrear as atualizações da imagem base em seus próprios registros de contêiner do Azure e, opcionalmente, em registros públicos upstream.

## <a name="track-base-image-updates"></a>Rastrear atualizações de imagem de base

As Tarefas do ACR incluem a capacidade de compilar imagens automaticamente para você quando a imagem base do contêiner for atualizada. Você pode usar essa capacidade para manter e atualizar cópias de imagens de base públicas em seus registros de contêiner do Azure e, em seguida, recriar imagens de aplicativo que dependem de imagens base.

As tarefas ACR detectam dinamicamente as dependências da imagem base ao criar uma imagem de contêiner. Como resultado, ele pode detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma tarefa de compilação pré-configurada, as tarefas de ACR podem recriar automaticamente cada imagem de aplicativo que faz referência à imagem base. Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

## <a name="base-image-locations"></a>Locais de imagem base

Para builds de imagem com base em um Dockerfile, uma tarefa do ACR detecta dependências em imagens base nas seguintes localizações:

* O mesmo Registro de Contêiner do Azure em que a tarefa é executada
* Outro registro de contêiner do Azure privado no mesmo ou em uma região diferente 
* Um repositório público no Docker Hub 
* Um repositório público no Registro de Contêiner da Microsoft

Se a imagem base especificada na `FROM` instrução residir em um desses locais, a tarefa ACR adicionará um gancho para garantir que a imagem seja recriada sempre que sua base for atualizada.

## <a name="base-image-notifications"></a>Notificações de imagem base

O tempo entre o momento em que uma imagem base é atualizada e quando a tarefa dependente é disparada depende do local da imagem base:

* **Imagens base de um repositório público no Docker Hub ou MCR** -para imagens base em repositórios públicos, uma tarefa ACR verifica se há atualizações de imagem em um intervalo aleatório entre 10 e 60 minutos. As tarefas dependentes são executadas de acordo.
* **Imagens base de um registro de contêiner do Azure** -para imagens base em registros de contêiner do Azure, uma tarefa ACR dispara imediatamente uma execução quando sua imagem base é atualizada. A imagem base pode estar no mesmo ACR em que a tarefa é executada ou em um ACR diferente em qualquer região.

## <a name="additional-considerations"></a>Considerações adicionais

* **Imagens base para imagens de aplicativo** – atualmente, uma tarefa ACR rastreia apenas as atualizações de imagem base para imagens de aplicativo (*tempo de execução*). Elas não rastreiam atualizações de imagem base para imagens intermediárias (*buildtime*) usadas em Dockerfiles de vários estágios.  

* **Habilitado por padrão** – quando você cria uma tarefa ACR com o comando [AZ ACR Task Create][az-acr-task-create] , por padrão, a tarefa é *habilitada* para o gatilho por uma atualização de imagem de base. Isto é, a propriedade `base-image-trigger-enabled` é definida como True. Se desejar desabilitar esse comportamento da tarefa, atualize a propriedade para False. Por exemplo, execute o seguinte comando [az acr task update][az-acr-task-update]:

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Gatilho para rastrear dependências** – para habilitar uma tarefa ACR para determinar e controlar as dependências de uma imagem de contêiner – que incluem sua imagem base--você deve primeiro disparar a tarefa para criar a imagem **pelo menos uma vez**. Por exemplo, disparar a tarefa manualmente usando o comando [az acr task run][az-acr-task-run].

* **Marca estável para a imagem base** – para disparar uma tarefa na atualização da imagem base, a imagem base deve ter uma marca *estável* , como `node:9-alpine` . Essa marcação é típica de uma imagem base que é atualizada com o sistema operacional e patches de estrutura para uma versão estável mais recente. Se a imagem base é atualizada com uma nova marca de versão, ela não dispara uma tarefa. Para obter mais informações sobre a marcação de imagens, confira as [diretrizes de práticas recomendadas](container-registry-image-tag-version.md). 

* **Outros gatilhos de tarefa** – em uma tarefa disparada por atualizações de imagem base, você também pode habilitar gatilhos com base na [confirmação do código-fonte](container-registry-tutorial-build-task.md) ou em [uma agenda](container-registry-tasks-scheduled.md). Uma atualização de imagem de base também pode disparar uma [tarefa de várias etapas](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes tutoriais para cenários para automatizar as compilações de imagem de aplicativo depois que uma imagem base é atualizada:

* [Automatizar compilações de imagem de contêiner quando uma imagem base é atualizada no mesmo registro](container-registry-tutorial-base-image-update.md)

* [Automatizar compilações de imagem de contêiner quando uma imagem base é atualizada em um registro diferente](container-registry-tutorial-base-image-update.md)


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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

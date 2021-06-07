---
title: Solucionar problemas de aplicativos de funções do Python no Azure Functions
description: Saiba como solucionar problemas de funções do Python.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846030"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Solucionar erros de Python no Azure Functions

Veja a seguir uma lista de guias de solução de problemas comuns em funções do Python:

* [ModuleNotFoundError e ImportError](#troubleshoot-modulenotfounderror)
* [Não é possível importar ' cygrpc '](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>Solucionar problemas de ModuleNotFoundError

Esta seção ajuda a solucionar problemas de erros relacionados ao módulo em seu aplicativo de funções do Python. Esses erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Esse problema de erro ocorre quando um aplicativo de função do Python falha ao carregar um módulo do Python. A causa raiz desse erro é um dos seguintes problemas:

- [Não foi possível encontrar o pacote](#the-package-cant-be-found)
- [O pacote não foi resolvido com o wheel apropriado do Linux](#the-package-isnt-resolved-with-proper-linux-wheel)
- [O pacote é incompatível com a versão do interpretador do Python](#the-package-is-incompatible-with-the-python-interpreter-version)
- [O pacote está em conflito com outros pacotes](#the-package-conflicts-with-other-packages)
- [O pacote só dá suporte a plataformas Windows ou macOS](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Exibir arquivos de projeto

Para identificar a causa real do problema, você precisa obter os arquivos de projeto do Python executados no seu aplicativo de funções. Se você não tiver os arquivos de projeto no computador local, poderá obtê-los de uma das seguintes maneiras:

- Se o aplicativo de funções tiver a configuração de aplicativo `WEBSITE_RUN_FROM_PACKAGE` e seu valor for uma URL, copie e cole a URL no seu navegador para baixar o arquivo.
- Se o aplicativo de funções tiver `WEBSITE_RUN_FROM_PACKAGE` e estiver definido como `1`, navegue até `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` e baixe o arquivo da URL de `href` mais recente.
- Se o aplicativo de funções não tiver a configuração de aplicativo mencionada acima, navegue até `https://<app-name>.scm.azurewebsites.net/api/settings` e localize a URL em `SCM_RUN_FROM_PACKAGE`. Copie e cole a URL no navegador para baixar o arquivo.
- Se nenhuma delas funcionar, navegue para `https://<app-name>.scm.azurewebsites.net/DebugConsole` e revele o conteúdo em `/home/site/wwwroot`.

O restante deste artigo ajuda a solucionar possíveis causas desse erro com a inspeção do conteúdo do aplicativo de funções, a identificação da causa raiz e a resolução do problema específico.

### <a name="diagnose-modulenotfounderror"></a>Diagnosticar ModuleNotFoundError

Esta seção detalha possíveis causas raiz de erros relacionados a módulos. Depois de encontrar a causa raiz provável, você pode ir para a mitigação relacionada.

#### <a name="the-package-cant-be-found"></a>Não foi possível encontrar o pacote

Navegue para `.python_packages/lib/python3.6/site-packages/<package-name>` ou `.python_packages/lib/site-packages/<package-name>`. Se o caminho do arquivo não existir, a falta desse caminho provavelmente será a causa raiz.

O uso de ferramentas de terceiros ou desatualizadas durante a implantação pode causar esse problema.

Consulte [Habilitar build remoto](#enable-remote-build) ou [Dependências de build nativas](#build-native-dependencies) para migração.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>O pacote não foi resolvido com o wheel apropriado do Linux

Acesse `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` ou `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`. Use seu editor de texto favorito para abrir o arquivo do **wheel** e marque a seção **Tag:** . Se o valor da tag não contiver **linux**, esse poderá ser o problema.

As funções do Python são executadas somente no Linux no Azure: O runtime v2.x do Functions é executado no Debian Stretch e o runtime v3.x no Debian Buster. Espera-se que o artefato contenha os binários corretos do Linux. O uso do sinalizador `--build local` em ferramentas do Core Tools, de terceiros ou desatualizadas pode fazer com que binários mais antigos sejam usados.

Consulte [Habilitar build remoto](#enable-remote-build) ou [Dependências de build nativas](#build-native-dependencies) para migração.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>O pacote é incompatível com a versão do interpretador do Python

Acesse `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` ou `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`. Usando um editor de texto, abra o arquivo METADATA e marque a seção **Classificadores:** . Se a seção não contiver `Python :: 3`, `Python :: 3.6`, `Python :: 3.7` ou `Python :: 3.8`, isso significa que a versão do pacote é muito antiga, ou provavelmente o pacote já está fora de manutenção.

Você pode verificar a versão do Python do seu aplicativo de funções no [portal do Azure](https://portal.azure.com). Navegue até seu aplicativo de funções, escolha **Gerenciador de Recursos** e selecione **Ir**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Abra o Gerenciador de Recursos para o aplicativo de funções no portal":::

Depois que o Gerenciador for carregado, pesquise **LinuxFxVersion**, que mostra a versão do Python.

Consulte [Atualizar o pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [Substituir o pacote por equivalentes](#replace-the-package-with-equivalents) para migração.

#### <a name="the-package-conflicts-with-other-packages"></a>O pacote está em conflito com outros pacotes

Se você verificou que o pacote foi resolvido corretamente com os wheels adequados do Linux, pode haver um conflito com outros pacotes. Em alguns pacotes, as documentações de PyPi podem esclarecer os módulos incompatíveis. Por exemplo, no [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/), há uma instrução da seguinte maneira:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Você pode encontrar a documentação para a versão do seu pacote em `https://pypi.org/project/<package-name>/<package-version>`.

Consulte [Atualizar o pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [Substituir o pacote por equivalentes](#replace-the-package-with-equivalents) para migração.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>O pacote só dá suporte a plataformas Windows ou macOS

Abra `requirements.txt` com um editor de texto e verifique o pacote em `https://pypi.org/project/<package-name>`. Alguns pacotes são executados somente em plataformas Windows ou macOS. Por exemplo, pywin32 só é executado no Windows.

O erro `Module Not Found` pode não ocorrer quando você está usando o Windows ou o macOS para desenvolvimento local. No entanto, o pacote não é importado no Azure Functions, que usa o Linux no runtime. Esse problema provavelmente é causado pelo uso de `pip freeze` para exportar o ambiente virtual para requirements.txt, a partir do seu computador Windows ou macOS durante a inicialização do projeto.

Consulte [Substituir o pacote por equivalentes](#replace-the-package-with-equivalents) ou [Criar requirements. txt](#handcraft-requirementstxt) para mitigação.

### <a name="mitigate-modulenotfounderror"></a>Mitigar ModuleNotFoundError

Veja a seguir as possíveis mitigações para problemas relacionados a módulo. Use os [diagnósticos acima](#diagnose-modulenotfounderror) para determinar quais dessas mitigações tentar.

#### <a name="enable-remote-build"></a>Habilitar a compilação remota

Verifique se a compilação remota está habilitada. Como fazer isso depende do seu método de implantação.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Verifique se a versão mais recente da [extensão do Azure Functions Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) está instalada. Verifique se `.vscode/settings.json` existe e contém a configuração `"azureFunctions.scmDoBuildDuringDeployment": true`. Caso contrário, crie esse arquivo com a configuração `azureFunctions.scmDoBuildDuringDeployment` habilitada e reimplante o projeto.

## <a name="azure-functions-core-tools"></a>[Ferramentas básicas do Azure Functions](#tab/coretools)

Verifique se a versão mais recente do [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Acesse a pasta do projeto de função local e use `func azure functionapp publish <app-name>` para implantação.

## <a name="manual-publishing"></a>[Publicação manual](#tab/manual)

Se estiver publicando o pacote manualmente no ponto de extremidade `https://<app-name>.scm.azurewebsites.net/api/zipdeploy`, verifique se **SCM_DO_BUILD_DURING_DEPLOYMENT** e **ENABLE_ORYX_BUILD** estão definidos como **true**. Para saber mais, confira [como trabalhar com as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Compilar dependências nativas

Veja se a versão mais recente do **docker** e do [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) estão instaladas. Acesse a pasta do projeto de função local e use `func azure functionapp publish <app-name> --build-native-deps` para implantação.

#### <a name="update-your-package-to-the-latest-version"></a>Atualizar seu pacote para a versão mais recente

Procure a versão mais recente do pacote em `https://pypi.org/project/<package-name>` e marque a seção **Classificadores:** . O pacote deve ser `OS Independent` ou compatível com `POSIX` ou `POSIX :: Linux` no **sistema operacional**. Além disso, a linguagem de programação deve conter `Python :: 3`, `Python :: 3.6`, `Python :: 3.7`ou `Python :: 3.8`.

Se estiverem corretos, você poderá atualizar o pacote para a versão mais recente alterando a linha `<package-name>~=<latest-version>` em requirements. txt.

#### <a name="handcraft-requirementstxt"></a>Criar requirements.txt

Alguns desenvolvedores usam `pip freeze > requirements.txt` para gerar a lista de pacotes do Python para seus ambientes de desenvolvimento. Embora essa conveniência funcione na maioria dos casos, pode haver problemas em cenários de implantação entre plataformas, por exemplo, ao desenvolver localmente funções no Windows ou no macOS, mas publicar em um aplicativo de funções executado no Linux. Nesse cenário, `pip freeze` pode introduzir dependências específicas do sistema operacional inesperadas ou dependências para seu ambiente de desenvolvimento local. Essas dependências podem interromper o aplicativo de funções do Python quando ele é executado no Linux.

A prática recomendada é verificar a instrução de importação de cada arquivo. py no código-fonte do projeto e apenas fazer check-in desses módulos no arquivo requirements. txt. Isso garante que a resolução de pacotes possa ser tratada corretamente em sistemas operacionais diferentes.

#### <a name="replace-the-package-with-equivalents"></a>Substituir os pacotes por equivalentes

Primeiro, devemos dar uma olhada na versão mais recente do pacote em `https://pypi.org/project/<package-name>`. Normalmente, esse pacote tem sua própria página do GitHub. Acesse a seção **Problemas** no GitHub e pesquise se o problema foi corrigido. Se sim, atualize o pacote para a versão mais recente.

Às vezes, o pacote pode ter sido integrado na [Biblioteca padrão do Python](https://docs.python.org/3/library/) (como pathlib). Nesse caso, como fornecemos uma determinada distribuição do Python no Azure Functions (Python 3.6, Python 3.7 e Python 3.8), o pacote no requirements. txt deve ser removido.

No entanto, se você estiver enfrentando um problema que não foi corrigido e estiver perto de um prazo final , aconselhamos fazer uma pesquisa e encontrar um pacote semelhante para seu projeto. Normalmente, a Comunidade do Python fornecerá uma ampla variedade de bibliotecas semelhantes que podem ser usadas.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>Solucionar problemas não é possível importar ' cygrpc '

Esta seção ajuda a solucionar os erros relacionados ao ' cygrpc ' em seu aplicativo de funções do Python. Esses erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

Esse problema de erro ocorre quando um aplicativo de função Python não é iniciado com um intérprete Python adequado. A causa raiz desse erro é um dos seguintes problemas:

- [O intérprete do Python não corresponde à arquitetura do sistema operacional](#the-python-interpreter-mismatches-os-architecture)
- [O interpretador do Python não é suportado pelo Azure Functions Worker do Python](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>Diagnosticar erro de referência ' cygrpc '

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>O intérprete do Python não corresponde à arquitetura do sistema operacional

Isso é provavelmente causado por um interpretador do Python de 32 bits instalado em seu sistema operacional de 64 bits.

Se estiver executando em um sistema operacional x64, verifique se o intérprete Python 3,6, 3,7 ou 3,8 também está na versão de 64 bits.

Você pode verificar o seu bit de interpretador do Python pelos seguintes comandos:

No Windows no PowerShell: `py -c 'import platform; print(platform.architecture()[0])'`

No Shell do tipo UNIX: `python3 -c 'import platform; print(platform.architecture()[0])'`

Se houver uma incompatibilidade entre a arquitetura do sistema operacional e o meio de bit do intérprete do Python, baixe um intérprete do Python adequado do [Python Software Foundation](https://python.org/downloads/release).

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>O interpretador do Python não é suportado pelo Azure Functions Worker do Python

O Azure Functions Worker do Python dá suporte apenas ao Python 3,6, 3,7 e 3,8.
Verifique se o intérprete do Python corresponde à nossa versão esperada `py --version` no Windows ou `python3 --version` em sistemas semelhantes ao Unix. Verifique se o resultado de retorno é Python 3.6. x, Python 3.7. x ou Python 3.8. x.

Se a versão do intérprete do Python não atender à nossa expectativa, baixe o intérprete Python 3,6, 3,7 ou 3,8 do [Python Software Foundation](https://python.org/downloads/release).

## <a name="next-steps"></a>Próximas etapas

Se não for possível resolver o problema, relate-o à equipe do Functions:

> [!div class="nextstepaction"]
> [Relatar um problema não resolvido](https://github.com/Azure/azure-functions-python-worker/issues)

---
title: Como configurar o Azure Machine Learning Workbench para funcionar com um IDE?  | Microsoft Docs
description: Uma guia para configurar o Azure Machine Learning Workbench para funcionar com o seu IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 4e18a413a0559b1ddebecf1b29722d21ef35c337
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Como configurar o Azure Machine Learning Workbench para funcionar com um IDE 

O Azure Machine Learning Workbench pode ser configurado para funcionar com IDEs (Ambiente de Desenvolvimento Integrado) populares do Python. Ele possibilita uma experiência de desenvolvimento de ciência de dados tranquila percorrendo preparação de dados, criação de código, acompanhamento de execução e operacionalização. No momento, os IDEs com suporte são:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Configurar o workbench
1. Clique no menu **Arquivo** na parte superior esquerda do aplicativo. 
2. Selecione a opção **Configurar IDE do Projeto** no submenu 
3. Digite `VS Code` ou `PyCharm` no campo **Nome** (o nome é arbitrário)
4. Insira o local do executável do IDE (completo com o nome e a extensão do executável) no **Caminho de Execução**

### <a name="default-install-path-for-visual-studio-code"></a>Caminho de instalação padrão para o Visual Studio Code  

* Windows 32 bits – `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64 bits – `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS – selecione o caminho .app, por exemplo `/Applications/Visual Studio Code.app` e o aplicativo anexa o restante do caminho para você. O caminho completo para o executável é `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code` por padrão. Se você tiver executado o comando `Shell Command: Install 'code' command in PATH` no código VS, também poderá consultar o script de Código VS em `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Caminho de instalação padrão para PyCharm 

* Windows 32 bits – `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64 bits – `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS – selecione o caminho .app, por exemplo “/Applications/PyCharm CE.app” e o aplicativo anexa o restante do caminho para você. O caminho completo para o executável é `/Applications/PyCharm CE.app/Contents/MacOS/pycharm` por padrão. Você também pode encontrar PyCharm na pasta bin, `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Abra o projeto no IDE 
Quando a configuração estiver concluída, você pode abrir um Projeto do Azure Machine Learning abrindo o menu **Arquivo** no Azure Machine Learning Workbench e clicando em **Abrir Projeto (<IDE_Name>)**. Essa ação abre o projeto ativo atual no IDE configurado. _Observação: se você não estiver em um projeto, a opção **Abrir Projeto (<IDE_Name>)** será desabilitada._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Configurando o terminal integrado no Visual Studio Code

### <a name="windows"></a>Windows 
Substituímos o shell padrão para ser cmd, em vez do PowerShell. Ao clicar em **Abrir Projeto (<Nome_do_IDE>)**, você verá um prompt: 

_Deseja permitir o shell: `C:\windows\System32\cmd.exe` (definido como uma configuração de espaço de trabalho) para ser inicializado no terminal?_

Responda `yes` para permitir que a configuração do shell funcione perfeitamente com a interface de linha de comando do Azure ML Workbench.

### <a name="mac"></a>Mac
Para executar um comando `az` usando o terminal integrado do Visual Studio Code no Mac, você precisará definir manualmente `PATH` para que tenha o mesmo valor que `PATH` no arquivo `.vscode/settings.json` do projeto sob a chave `terminal.integrated.env.osx`. Você pode fazer isso executando o seguinte comando no terminal: `PATH=<PATH in .vscode/settings>`

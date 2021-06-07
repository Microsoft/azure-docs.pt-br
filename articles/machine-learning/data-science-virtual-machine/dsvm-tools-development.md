---
title: Ferramentas de desenvolvimento
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as ferramentas e os ambientes de desenvolvimento integrados disponíveis no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cecc195b8b97ffd9b25cf12898726352ddd698a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519432"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento no Máquina Virtual de Ciência de Dados do Azure

O Máquina Virtual de Ciência de Dados (DSVM) agrupa várias ferramentas populares em um ambiente de desenvolvimento integrado (IDE) altamente produtivo. Aqui estão algumas ferramentas oferecidas na DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows: Visual Studio 2017, Windows 2019: Visual Studio 2019      |
| Usos típicos      | Desenvolvimento de software    |
| Como ele é configurado e instalado no DSVM?      | Carga de Trabalho de Ciência de Dados (ferramentas Python e R), carga de trabalho do Azure (Hadoop, Data Lake), Node.js, ferramentas do SQL Server, [Azure Machine Learning para o Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho da área de trabalho ( `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe` ). Graficamente, abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar** . Pesquise programas (tecla do logotipo do Windows + S), seguido de **Visual Studio**. Nesse local, você pode criar projetos em linguagens como C#, Python, R e Node.js.   |
| Ferramentas relacionadas no DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Você poderá receber uma mensagem informando que o período de avaliação expirou. Insira suas credenciais de conta da Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE de uso geral      |
| Versões do DSVM com suporte      | Windows, Linux     |
| Usos típicos      | Editor de código e a integração de Git   |
| Como usá-lo e executá-lo      | Atalho da área de trabalho ( `C:\Program Files (x86)\Microsoft VS Code\Code.exe` ) no Windows, atalho da área de trabalho ou terminal ( `code` ) no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho ( `C:\Program Files\RStudio\bin\rstudio.exe` ) no Windows, atalho da área de trabalho ( `/usr/bin/rstudio` ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE de cliente para a linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões do DSVM com suporte      | Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usá-lo e executá-lo      | Habilite o serviço com _systemctl habilite o RStudio-Server_ e inicie o serviço com _systemctl Start RStudio-Server_. Em seguida, entre no RStudio Server em http: \/ /Your-VM-IP: 8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio desktop      |

## <a name="juno"></a>Juno 

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Julia   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em Julia     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho ( `C:\JuliaPro-0.5.1.1\Juno.bat` ) no Windows, atalho da área de trabalho ( `/opt/JuliaPro-VERSION/Juno` ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Python    |
| Versões do DSVM com suporte      | Windows 2019, Linux      |
| Usos típicos      |  Desenvolvimento em Python     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho ( `C:\Program Files\tk` ) no Windows. Atalho da área de trabalho ( `/usr/bin/pycharm` ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

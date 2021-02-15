---
title: Linguagens compatíveis
titleSuffix: Azure Data Science Virtual Machine
description: Os idiomas do programa com suporte e as ferramentas relacionadas pré-instaladas no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516423"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Linguagens com suporte na Máquina Virtual de Ciência de Dados 

O Máquina Virtual de Ciência de Dados (DSVM) vem com várias linguagens predefinidas e ferramentas de desenvolvimento para criar seus aplicativos de ia (inteligência artificial). Aqui estão algumas das notáveis.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Categoria | Valor |
| ------------- | ------------- |
| Versões de idioma com suporte | Python 2,7 e 3,7 |
| Edições DSVM com suporte      | Windows Server 2016     |
| Como é configurado/instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * O `root` ambiente localizado em `/anaconda/` é Python 3,7. <br/> * O `python2` ambiente localizado em `/anaconda/envs/python2` é Python 2,7.       |
| Links para exemplos      | Os notebooks Jupyter de exemplo para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> As compilações do Windows Server 2016 que foram criadas antes de 2018 de março contêm Python 3,5 e Python 2,7. O Python 2,7 é o ambiente **raiz** Conda e **py37** é o ambiente Python 3,7.

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Executar em um prompt de comando:

  Abra um prompt de comando e use um dos seguintes métodos, dependendo da versão do Python que você deseja executar:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Use em um IDE:

  Use o Ferramentas Python para Visual Studio (PTVS), instalado no Visual Studio Community Edition. Por padrão, o único ambiente configurado automaticamente no PTVS é Python 3,6. 

    > [!NOTE]
    > Para apontar o PTVS no Python 2,7, você deve criar um ambiente personalizado no PTVS. Para definir esse caminho de ambiente no Visual Studio Community Edition, vá para **ferramentas**  ->  **python**  ->  **ambientes Python** e selecione **+ personalizado**. Em seguida, defina o local como **c:\anaconda\envs\python2** e selecione **detecção automática**.

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como _Python [Conda root]_ para Python 3,7 e _Python [Conda env: Python2]_ para Python 2,7.

* Instale os pacotes do Python:

  Os ambientes padrão do Python no DSVM são ambientes globais que podem ser lidos por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou python2 usando o `activate` comando como administrador. Em seguida, você pode usar um Gerenciador de pacotes como `conda` ou `pip` para instalar ou atualizar pacotes.

## <a name="python-linux-edition"></a>Python (edição Linux)

| Categoria | Valor |
| ------------- | ------------- |
| Versões de idioma com suporte | Python 2,7 e 3,5 |
| Edições DSVM com suporte      | Linux   |
| Como é configurado/instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * `root` o ambiente localizado em `/anaconda/` é Python 2,7. <br/> * `py35` o ambiente localizado em `/anaconda/envs/py35` é Python 3,5.       |
| Links para exemplos      | Os notebooks Jupyter de exemplo para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Executar em um terminal:

  Abra o terminal e faça um dos seguintes, dependendo da versão do Python que você deseja executar:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Use em um IDE:

  Use o PyCharm, instalado no Visual Studio Community Edition. 

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **Python [Conda root]** para Python 2,7 e **Python [Conda env: py35]** para o ambiente Python 3,5. 

* Instale os pacotes do Python:

  Os ambientes Python padrão no DSVM são ambientes globais legíveis por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou py35 usando o `source activate` comando como administrador ou como um usuário com permissões sudo. Em seguida, você pode usar um Gerenciador de pacotes como `conda` ou `pip` para instalar ou atualizar pacotes.


## <a name="r"></a>R

| Categoria | Valor |
| ------------- | ------------- |
| Versões de idioma com suporte | Microsoft R Open 3. x (100% compatível com CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (uma plataforma de R pronta para empresas escalonável)|
| Edições DSVM com suporte      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links para exemplos      | Os notebooks Jupyter de exemplo para R estão incluídos.     |
| Ferramentas relacionadas no DSVM      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Executar em um prompt de comando:

  Abra um prompt de comando e digite `R`.

* Use em um IDE:

  Use RTVS (RTools para Visual Studio) instalado no Visual Studio Community Edition ou RStudio. Elas estão disponíveis no menu iniciar ou como um ícone de área de trabalho. 

* Usar em Jupyter

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **R** para usar o kernel do Jupyter R (IRKernel).

* Instalar pacotes do R:

  O R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode executar o Gerenciador de pacotes do R `install.packages()` para instalar ou atualizar pacotes.

**Linux**:

* Executar no terminal:

  Abra um terminal e execute `R` .  

* Use em um IDE:

  Use o RStudio, instalado no DSVM do Linux.  

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **R** para usar o kernel do Jupyter R (IRKernel). 

* Instalar pacotes do R:

  O R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode executar o Gerenciador de pacotes do R `install.packages()` para instalar ou atualizar pacotes.


## <a name="julia"></a>Julia

| Categoria | Valor |
| ------------- | ------------- |
| Versões de idioma com suporte | 0,6 |
| Edições DSVM com suporte      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: instalado em `C:\JuliaPro-VERSION`<br /> Linux: instalado em `/opt/JuliaPro-VERSION`    |
| Links para exemplos      | Os notebooks Jupyter de exemplo para Julia estão incluídos.     |
| Ferramentas relacionadas no DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Executar em um prompt de comando

  Abra um prompt de comando e execute `julia` .
* Use em um IDE:

  Use `Juno` com o IDE Julia instalado no DSVM e disponível como um atalho da área de trabalho.

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como a **versão Julia**.

* Instalar pacotes Julia:

  O local Julia padrão é um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar comandos do Gerenciador de pacotes Julia como `Pkg.add()` instalar ou atualizar pacotes.


**Linux**:
* Executar em um terminal:

  Abra um terminal e execute `julia` .
* Use em um IDE:

  Use `Juno` o, com o IDE Julia instalado no DSVM e disponível como um atalho de menu do **aplicativo** .

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como a **versão Julia**.

* Instalar pacotes Julia:

  O local Julia padrão é um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar comandos do Gerenciador de pacotes Julia como `Pkg.add()` instalar ou atualizar pacotes.

## <a name="other-languages"></a>Outros idiomas

**C#**: disponível no Windows e acessível por meio do Visual Studio Community Edition ou no `Developer Command Prompt for Visual Studio` , onde você pode executar o `csc` comando.

**Java**: OpenJDK está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para usar o Java, digite `javac` o `java` comando ou em um prompt de comando no Windows ou no shell bash no Linux.

**Node.js**: Node.js está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para acessar Node.js, digite o `node` `npm` comando ou em um prompt de comando no Windows ou no shell bash no Linux. No Windows, a extensão do Visual Studio para as ferramentas de Node.js é instalada para fornecer um IDE gráfico para desenvolver seu aplicativo Node.js.

**F #**: disponível no Windows e acessível por meio do Visual Studio Community Edition ou em um `Developer Command Prompt for Visual Studio` , onde você pode executar o `fsc` comando.

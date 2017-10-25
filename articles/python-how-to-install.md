---
title: Instalar o Python e o SDK - Azure
description: Saiba como instalar o Python e o SDK para usar com o Azure.
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
ms.openlocfilehash: e69fff29be5b12c3c0004b4101eba69c7da87d3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="installing-python-and-the-sdk"></a>Instalando o Python e o SDK
O Python é fácil de ser instalado no Windows e é fornecido pré-instalado no Mac, no Linux e no [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Novidades no SDK do Python Azure
O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

* **Bibliotecas de gerenciamento**. Essas bibliotecas de classes fornecem uma interface para gerenciar recursos do Azure, como as contas de armazenamento e as máquinas virtuais.
* **Bibliotecas de tempo de execução**. Essas bibliotecas de classes fornecem uma interface para acessar recursos do Azure, como o armazenamento e o barramento de serviço.

## <a name="which-python-and-which-version-to-use"></a>Qual Python e qual versão usar
Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

* CPython - o interpretador do Python padrão e mais geralmente usado
* PyPy – implementação alternativa rápida e compatível para CPython
* IronPython -interpretador do Python que é executado no .Net/CLR
* Jython – interpretador do Python que é executado na Máquina Virtual Java

**CPython** v2.7 ou v3.3+ e o PyPy 5.4.0 são testados e dão suporte ao SDK do Azure para Python.

## <a name="where-to-get-python"></a>Onde obter o Python?
Existem várias maneiras de obter o CPython:

* Diretamente de [www.python.org][www.python.org]
* De um distribuidor respeitável, como [www.continuum.io][www.continuum.io], [www.enthought.com][www.enthought.com] ou [www.activestate.com][www.activestate.com]
* Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalação do SDK no Windows, Linux e MacOS (apenas bibliotecas de cliente)
Se já tiver o Python instalado, você poderá usar pip para instalar um pacote de todas as bibliotecas de cliente em seu ambiente Python 2.7 ou Python 3.3+ existente. Isso baixa os pacotes do [Índice de Pacotes do Python][Python Package Index] (PyPI).

Você pode precisar de direitos de administrador:

* Para Linux e MacOS, use o comando `sudo`: `sudo pip install azure-mgmt-compute`.
* Para Windows: abra o PowerShell/prompt de comando como administrador

Você pode instalar cada biblioteca individualmente para cada serviço do Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pacotes de preview podem ser instalados usando o sinalizador `--pre` :

```console
   $ pip install --pre azure-mgmt-compute # installs only the latest Compute Management library
```

Você também pode instalar um conjunto de bibliotecas do Azure em uma única linha usando o pacote meta `azure` . Como nem todos os pacotes neste pacote meta estão publicados como estáveis, o pacote meta `azure` ainda está em preview.
No entanto, os pacotes essenciais podem ser considerados "estáveis" no momento do ponto de vista da integridade e da qualidade do código.

* Ela é oficialmente rotulada como tal em sincronia com outras linguagens assim que possível.
  Não planejamos outras alterações importantes até então.

Como se trata de uma versão de preview, você precisa usar o sinalizador `--pre` :

```console
   $ pip install --pre azure
```

ou diretamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obter mais paquetes
O [Índice de Pacotes do Python][Python Package Index] (PyPI) tem uma seleção completa de bibliotecas do Python.  Caso tenha optado por instalar uma Distribuição, você já terá a maioria das partes interessantes para vários cenários, do desenvolvimento para Web até a Computação Técnica.

## <a name="python-tools-for-visual-studio"></a>Python Tools para Visual Studio
[Python Tools para Visual Studio][Ferramentas do Python Visual Studio] (PTVS) é um plug-in gratuito/OSS da Microsoft que transforma o VS em um IDE completo para Python:

![como-instalar-o-webpi-do-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

O uso do PTVS é opcional, mas recomendável, pois ele dá suporte a Solução/Projeto Web e Python, depuração, criação de perfil, janela interativa, edição de Modelos e Intellisense.

O PTVS também torna fácil a implantação ao Microsoft Azure, com suporte para implementação em [Serviços de Nuvem](cloud-services/cloud-services-python-ptvs.md) e [Sites](app-service/app-service-web-overview.md).

O PTVS funciona com sua instalação existente do Visual Studio 2013, 2015 ou 2017.  Para documentação, downloads e discussões, consulte [Ferramentas do Python Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Cenários do Python Azure para Linux e MacOS
Para Linux ou MacOS, os principais cenários do Azure têm suporte:

1. Usando os Serviços do Azure por meio das bibliotecas de cliente para Python
2. Executando o seu aplicativo em uma VM com Linux
3. Desenvolver e publicar sites do Azure usando Git

O primeiro cenário permite que você crie aplicativos Web avançados que aproveitam recursos do Azure PaaS como [armazenamento de blobs](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [armazenamento de filas](storage/queues/storage-python-how-to-use-queue-storage.md), [armazenamento de tabelas](cosmos-db/table-storage-how-to-use-python.md), etc., por meio de wrappers do Pythonic para as APIs REST do Azure. Eles funcionam de forma idêntica no Windows, no Mac e no Linux.  Você também pode usar essas bibliotecas cliente de sua máquina de desenvolvimento local ou em uma VM do Linux em execução no Azure.

Para o cenário da VM, basta iniciar uma VM com Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar os itens desejados.  Por exemplo, você pode executar [IPython][IPython] REPL/notebook no seu computador com Windows/Mac/Linux e apontar o navegador para uma VM com Linux ou Windows de proc múltiplo que esteja executando o IPython Engine no Azure.

Para saber mais sobre como configurar uma VM do Linux, veja o tutorial [Criar uma Máquina Virtual Executando o Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Usando a implantação do Git, você pode desenvolver um aplicativo da Web Python e publicá-lo em um site do Azure de qualquer sistema operacional.  Quando você envia seu repositório para o Azure, ele cria automaticamente um ambiente virtual e o pip instalará os pacotes necessários.

Para obter mais informações sobre como usar qualquer estrutura em conformidade com WSGI, consulte [Configurando o Python com os Sites do Azure](app-service/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>Software adicional e recursos:
* [SDK do Azure para Python no ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [SDK do Azure para Python no GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Exemplos oficiais do Azure para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuição do Python de Análise de Continuidade][Continuum Analytics Python Distribution]
* [Distribuição do Python de Enthought][Enthought Python Distribution]
* [Distribuição do Python de ActiveState][ActiveState Python Distribution]
* [SciPy – um conjunto de bibliotecas científicas para Python][SciPy - A suite of Scientific Python libraries]
* [NumPy – uma biblioteca de numéricos para o Python][NumPy - A numerics library for Python]
* [Projeto Django – uma estrutura da Web/CMS sólida][Django Project - A mature web framework/CMS]
* [IPython – um REPL/Notebook avançado para o Python][IPython - an advanced REPL/Notebook for Python]
* [Ferramentas Python para Visual Studio no GitHub][Python Tools for Visual Studio on GitHub]
* [Centro de desenvolvedores do Python](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[Ferramentas do Python Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[blob storage]:storage/blobs/storage-python-how-to-use-blob-storage.md

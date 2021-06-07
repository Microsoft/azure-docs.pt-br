---
title: Solucionar problemas de imagens de ambiente
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas com Builds de imagem de ambiente e instalações de pacote.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519122"
---
# <a name="troubleshoot-environment-image-builds"></a>Solucionar problemas de compilação de imagem de ambiente

Saiba como solucionar problemas com Builds de imagem de ambiente do Docker e instalações de pacote.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install).
* O [CLI do Azure](/cli/azure/install-azure-cli).
* A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação do Docker em funcionamento no sistema local.

## <a name="docker-image-build-failures"></a>Falhas de Build de imagem do Docker
 
Para a maioria das falhas de compilação de imagem, você encontrará a causa raiz no log de compilação da imagem.
Localize o log de Build de imagem no portal de Azure Machine Learning (20 \_log.txt de compilação de imagem \_ \_ ) ou na tarefa de registro de contêiner do Azure executar logs.
 
Geralmente, é mais fácil reproduzir erros localmente. Verifique o tipo de erro e tente um dos seguintes `setuptools` :

- Instalar uma dependência do Conda localmente: `conda install suspicious-dependency==X.Y.Z` .
- Instalar uma dependência Pip localmente: `pip install suspicious-dependency==X.Y.Z` .
- Tente materializar todo o ambiente: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Verifique se a plataforma e o intérprete em seu cluster de computação local correspondem aos do cluster de computação remota. 

### <a name="timeout"></a>Tempo limite 
 
Os seguintes problemas de rede podem causar erros de tempo limite:

- Baixa largura de banda da Internet
- Problemas do servidor
- Grandes dependências que não podem ser baixadas com as configurações de tempo limite de Conda ou Pip fornecidas
 
Mensagens semelhantes aos exemplos a seguir indicarão o problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Se você receber uma mensagem de erro, tente uma das seguintes soluções possíveis:
 
- Experimente uma fonte diferente, como espelhos, armazenamento de BLOBs do Azure ou outros feeds do Python, para a dependência.
- Atualize Conda ou Pip. Se você estiver usando um arquivo do Docker personalizado, atualize as configurações de tempo limite.
- Algumas versões do PIP têm problemas conhecidos. Considere adicionar uma versão específica do Pip às dependências do ambiente.

### <a name="package-not-found"></a>Pacote não encontrado

Os seguintes erros são mais comuns para falhas de compilação de imagem:

- Não foi possível encontrar o pacote Conda:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Não foi possível encontrar o pacote PIP ou a versão especificada:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Dependência de Pip aninhada inadequada:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Verifique se o pacote existe nas fontes especificadas. Use a [pesquisa de Pip](https://pip.pypa.io/en/stable/reference/pip_search/) para verificar as dependências de Pip:

- `pip search azureml-core`

Para dependências Conda, use a [pesquisa Conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Para obter mais opções, tente:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notas do instalador

Verifique se a distribuição necessária existe para a plataforma especificada e a versão do intérprete do Python.

Para dependências Pip, vá para `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` para ver se a versão necessária está disponível. Vá para https://pypi.org/project/azureml-core/1.11.0/#files para ver um exemplo.

Para dependências Conda, verifique o pacote no repositório do canal.
Para canais mantidos pela Anaconda, Inc., verifique a [página de pacotes do Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Atualização do pacote Pip

Durante uma instalação ou uma atualização de um pacote Pip, o resolvedor pode precisar atualizar um pacote já instalado para atender aos novos requisitos.
A desinstalação pode falhar por vários motivos relacionados à versão de PIP ou à maneira como a dependência foi instalada.
O cenário mais comum é que uma dependência instalada pelo Conda não pôde ser desinstalada por Pip.
Para este cenário, considere desinstalar a dependência usando `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemas do instalador

Certas versões do instalador têm problemas nos resolvedores de pacotes que podem levar a uma falha de compilação.

Se você estiver usando uma imagem de base personalizada ou Dockerfile, é recomendável usar o Conda versão 4.5.4 ou posterior.

Um pacote Pip é necessário para instalar dependências de Pip. Se uma versão não for especificada no ambiente, a versão mais recente será usada.
É recomendável usar uma versão conhecida do Pip para evitar problemas transitórios ou alterações significativas que a versão mais recente da ferramenta pode causar.

Considere fixar a versão do Pip em seu ambiente se você vir a seguinte mensagem:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Erro de subprocesso de Pip:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

A instalação do Pip pode ser paralisada em um loop infinito se houver conflitos não resolvidos nas dependências. Se você estiver trabalhando localmente, faça downgrade da versão Pip para < 20,3. Em um ambiente Conda criado a partir de um arquivo YAML, você verá esse problema somente se o Conda-forjar for o canal de prioridade mais alta. Para atenuar o problema, especifique explicitamente Pip < 20,3 (! = 20,3 ou = 20.2.4 pin para outra versão) como uma dependência Conda no arquivo de especificação Conda.

## <a name="service-side-failures"></a>Falhas do lado do serviço

Consulte os cenários a seguir para solucionar possíveis falhas no lado do serviço.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Não é possível efetuar pull de uma imagem de um registro de contêiner ou o endereço não pôde ser resolvido para um registro de contêiner

Possíveis problemas:
- O nome do caminho para o registro de contêiner pode não estar resolvido corretamente. Verifique se os nomes de imagem usam barras duplas e se a direção das barras no Linux e nos hosts do Windows está correta.
- Se um registro de contêiner por trás de uma rede virtual estiver usando um ponto de extremidade privado em [uma região sem suporte](../private-link/private-link-overview.md#availability), configure o registro de contêiner usando o ponto de extremidade de serviço (acesso público) do portal e tente novamente.
- Depois de colocar o registro de contêiner por trás de uma rede virtual, execute o [modelo de Azure Resource Manager](./how-to-network-security-overview.md) para que o espaço de trabalho possa se comunicar com a instância do registro de contêiner.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Você recebe um erro 401 de um registro de contêiner de espaço de trabalho

Ressincronize as chaves de armazenamento usando [WS.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>O ambiente continua lançando um "aguardando que outras operações de Conda sejam concluídas..." ao

Quando um Build de imagem está em andamento, o Conda é bloqueado pelo cliente do SDK. Se o processo tiver falhado ou cancelado incorretamente pelo usuário, o Conda permanecerá no estado bloqueado. Para resolver esse problema, exclua manualmente o arquivo de bloqueio. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>A imagem personalizada do Docker não está no registro

Verifique se a [marca correta](./how-to-use-environments.md#create-an-environment) é usada e isso `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` desabilita o Conda e usa os pacotes instalados do usuário.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Você Obtém um dos seguintes problemas comuns de rede virtual

- Verifique se a conta de armazenamento, o cluster de computação e o registro de contêiner estão todos na mesma sub-rede da rede virtual.
- Quando o registro de contêiner está atrás de uma rede virtual, ele não pode ser usado diretamente para criar imagens. Você precisará usar o cluster de computação para criar imagens.
- O armazenamento pode precisar ser colocado atrás de uma rede virtual se você:
    - Use o inferência ou a roda privada.
    - Consulte erros de serviço 403 (não autorizado).
    - Não é possível obter detalhes da imagem do registro de contêiner do Azure.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>A criação da imagem falha quando você está tentando acessar o armazenamento protegido da rede

- As tarefas do registro de contêiner do Azure não funcionam por trás de uma rede virtual. Se o usuário tiver seu registro de contêiner por trás de uma rede virtual, ele precisará usar o cluster de computação para criar uma imagem.
- O armazenamento deve estar atrás de uma rede virtual para extrair dependências dela.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Não é possível executar experimentos quando o armazenamento tem segurança de rede habilitada

Se você estiver usando imagens padrão do Docker e habilitando dependências gerenciadas pelo usuário, use as [marcas de serviço](./how-to-network-security-overview.md) MicrosoftContainerRegistry e AzureFrontDoor. FirstParty para permitir o registro de contêiner do Azure daList e suas dependências.

 Para obter mais informações, consulte [habilitando redes virtuais](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Você precisa criar um ICM

Quando você estiver criando/atribuindo um ICM ao metastore, inclua o tíquete de suporte do CSS para que possamos entender melhor o problema.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo de aprendizado de máquina para categorizar flores](how-to-train-scikit-learn.md)
- [Treinar um modelo de aprendizado de máquina usando uma imagem personalizada do Docker](how-to-train-with-custom-image.md)

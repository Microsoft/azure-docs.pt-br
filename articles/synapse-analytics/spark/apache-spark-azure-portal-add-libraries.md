---
title: Gerenciamento de pacotes
description: Saiba como adicionar e gerenciar bibliotecas usadas por Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c6d720c3feec29eb32b1cfa9c31ea45839c98ec7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176409"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics
As bibliotecas fornecem código reutilizável que você talvez queira incluir em seus programas ou projetos. 

Talvez seja necessário atualizar seu ambiente de pool de Apache Spark sem servidor por vários motivos. Por exemplo, você pode achar que:
- uma de suas dependências principais lançou uma nova versão.
- Você precisa de um pacote extra para treinar seu modelo de aprendizado de máquina ou preparar seus dados.
- Você encontrou um pacote melhor e não precisa mais do pacote mais antigo.
- sua equipe criou um pacote personalizado que você precisa disponível em seu pool de Apache Spark.

Para disponibilizar código de terceiros ou compilado localmente para seus aplicativos, você pode instalar uma biblioteca em um dos pools de Apache Spark sem servidor ou na sessão do bloco de anotações.
  
## <a name="default-installation"></a>Instalação padrão
Apache Spark no Azure Synapse Analytics tem uma instalação completa do Anacondas, além de bibliotecas adicionais. A lista de bibliotecas completa pode ser encontrada em [suporte à versão Apache Spark](apache-spark-version-support.md). 

Quando uma instância do Spark é iniciada, essas bibliotecas serão incluídas automaticamente. Pacotes adicionais podem ser adicionados no nível de sessão ou nível de pool do Spark.

## <a name="workspace-packages"></a>Pacotes de espaço de trabalho
Ao desenvolver aplicativos ou modelos personalizados, sua equipe pode desenvolver vários artefatos de código, como arquivos de roda ou jar, para empacotar seu código. 

No Synapse, os pacotes de espaço de trabalho podem ser arquivos de roda ou jar personalizados ou privados. Você pode carregar esses pacotes em seu espaço de trabalho e, posteriormente, atribuí-los a um pool do Spark específico. Depois de atribuídas, esses pacotes de espaço de trabalho são instalados automaticamente em todas as sessões do pool do Spark.

Para saber mais sobre como gerenciar bibliotecas de espaços de trabalho, visite os seguintes guias de instruções:

- [Pacotes de espaço de trabalho do Python (versão prévia): ](./apache-spark-manage-python-packages.md#install-wheel-files) Carregue os arquivos de roda do Python como um pacote de espaço de trabalho e adicione-os posteriormente a pools de Apache Spark sem servidor específicos.
- [Pacotes de espaço de trabalho escalares/Java (visualização): ](./apache-spark-manage-scala-packages.md#workspace-packages) Carregue arquivos escalares e Java jar como um pacote de espaço de trabalho e, posteriormente, adicione esses pacotes a pools Apache Spark sem servidor específicos.

## <a name="pool-packages"></a>Pacotes de pool
Em alguns casos, talvez você queira padronizar o conjunto de pacotes que são usados em um determinado pool de Apache Spark. Essa padronização pode ser útil se os mesmos pacotes são normalmente instalados por várias pessoas em sua equipe. 

Usando os recursos de gerenciamento de pool do Azure Synapse Analytics, você pode configurar o conjunto padrão de bibliotecas que deseja instalar em um determinado pool de Apache Spark sem servidor. Essas bibliotecas são instaladas na parte superior do [tempo de execução base](./apache-spark-version-support.md). 

Atualmente, o gerenciamento de pool só tem suporte para Python. Para Python, os pools do Spark Synapse usam o Conda para instalar e gerenciar as dependências do pacote do Python. Ao especificar as bibliotecas de nível de pool, agora você pode fornecer um requirements.txt ou um ambiente. yml. Esse arquivo de configuração de ambiente é usado toda vez que uma instância do Spark é criada a partir desse pool do Spark. 

Para saber mais sobre esses recursos, visite a documentação sobre [Gerenciamento de pools do Python](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Se o pacote que você está instalando for grande ou demorar muito para ser instalado, isso afetará o tempo de inicialização da instância do Spark.
> - Não há suporte para a alteração da versão PySpark, Python, escala/Java, .NET ou Spark.
> - Não há suporte para a instalação de pacotes do PyPI em espaços de trabalho habilitados para DEP.

## <a name="session-scoped-packages"></a>Pacotes no escopo da sessão
Geralmente, ao fazer análises de dados interativas ou aprendizado de máquina, você pode achar que deseja experimentar pacotes mais recentes ou pode precisar de pacotes que ainda não estão disponíveis em seu pool de Apache Spark. Em vez de atualizar a configuração do pool, os usuários agora podem usar pacotes no escopo da sessão para adicionar, gerenciar e atualizar dependências de sessão.

Os pacotes no escopo da sessão permitem que os usuários definam as dependências do pacote no início da sessão. Quando você instala um pacote com escopo de sessão, somente a sessão atual tem acesso aos pacotes especificados. Como resultado, esses pacotes no escopo da sessão não afetarão outras sessões ou trabalhos usando o mesmo pool de Apache Spark. Além disso, essas bibliotecas são instaladas sobre o tempo de execução base e os pacotes de nível de pool. 

Esses pacotes são adicionados automaticamente ao seu ambiente Python. Os pacotes não devem ser mencionados no arquivo de *requirements.txt* .

Observe que, atualmente, esse método suports apenas `*.whl` arquivos. Não adicione nenhum `*.tar.gz` arquivo ao contêiner.

Para saber mais sobre como gerenciar pacotes no escopo da sessão, visite os seguintes guias de instruções:

- [Pacotes de sessão do Python (versão prévia):](./apache-spark-manage-python-packages.md) No início de uma sessão, forneça um ambiente Conda *. yml* para instalar pacotes python adicionais de repositórios populares. 
- [Pacotes de sessão escalares/Java: ](./apache-spark-manage-scala-packages.md) No início da sessão, forneça uma lista de arquivos jar a serem instalados usando o `%%configure` .

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)

---
title: Gerenciar bibliotecas para Apache Spark
description: Saiba como adicionar e gerenciar bibliotecas usadas por Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 0458fb8b140166b7bdf0fc0df41dbb207fdce3c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518514"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que você talvez queira incluir em seus programas ou projetos. Para disponibilizar código de terceiros ou localmente compilado para seus aplicativos, você pode instalar uma biblioteca em um dos pools de Apache Spark sem servidor. Depois que uma biblioteca é instalada para um pool do Spark, ela fica disponível para todas as sessões que usam o mesmo pool. 

## <a name="before-you-begin"></a>Antes de começar
- Para instalar e atualizar bibliotecas, você deve ter as permissões de proprietário de dados de armazenamento de **blob de armazenamento** ou de **Data blob** na conta de armazenamento Gen2 primária que está vinculada ao espaço de trabalho do Azure Synapse Analytics.
  
## <a name="default-installation"></a>Instalação padrão
Apache Spark no Azure Synapse Analytics tem uma instalação completa do Anacondas, além de bibliotecas adicionais. A lista de bibliotecas completa pode ser encontrada em [suporte à versão Apache Spark](apache-spark-version-support.md). 

Quando uma instância do Spark é iniciada, essas bibliotecas serão incluídas automaticamente. Pacotes adicionais de Python e personalizados podem ser adicionados no nível do pool do Spark.


## <a name="manage-python-packages"></a>Gerenciar pacotes do Python
Depois de identificar as bibliotecas que você gostaria de usar para o aplicativo Spark, você pode instalá-las em um pool do Spark. 

 Um arquivo de *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente virtual. Os pacotes listados neste arquivo para instalação ou atualização são baixados de PyPI no momento da inicialização do pool. Esse arquivo de requisitos é usado toda vez que uma instância do Spark é criada a partir desse pool do Spark.

> [!IMPORTANT]
> - Se o pacote que você está instalando for grande ou demorar muito para ser instalado, isso afetará o tempo de inicialização da instância do Spark.
> - Os pacotes que exigem suporte ao compilador no momento da instalação, como o GCC, não têm suporte.
> - Os pacotes não podem ser desatualizados, somente adicionados ou atualizados.
> - Não há suporte para a alteração da versão PySpark, Python, escala/Java, .NET ou Spark.
> - Não há suporte para a instalação de pacotes do PyPI em espaços de trabalho habilitados para DEP.


### <a name="requirements-format"></a>Formato dos requisitos

O trecho a seguir mostra o formato do arquivo de requisitos. O nome do pacote PyPi é listado junto com uma versão exata. Esse arquivo segue o formato descrito na documentação de referência do [Pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Este exemplo fixa uma versão específica. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Instalar pacotes do Python
Ao desenvolver seu aplicativo Spark, talvez você ache que precisa atualizar as novas bibliotecas existentes ou instalá-las. As bibliotecas podem ser atualizadas durante ou após a criação do pool.

> [!IMPORTANT]
> Para instalar bibliotecas, você deve ter as permissões de proprietário de dados de blob de armazenamento ou de dados de blob de armazenamento na conta de armazenamento Gen2 primária vinculada ao espaço de trabalho Synapse.

#### <a name="install-packages-during-pool-creation"></a>Instalar pacotes durante a criação do pool
Para instalar bibliotecas em um pool do Spark durante a criação do pool:
   
1. Navegue até o espaço de trabalho do Azure Synapse Analytics no portal do Azure.
   
2. Selecione **criar Apache Spark pool** e, em seguida, selecione a guia **configurações adicionais** . 
   
3. Carregue o arquivo de configuração do ambiente usando o seletor de arquivo na seção **pacotes** da página. 
   
    ![Adicionar bibliotecas Python durante a criação do pool](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Adicionar bibliotecas do Python")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalar pacotes do espaço de trabalho Synapse
Para atualizar ou adicionar mais bibliotecas a um pool do Spark do portal do Azure Synapse Analytics:

1.  Navegue até o espaço de trabalho do Azure Synapse Analytics no portal do Azure.
   
2.  Inicie o espaço de trabalho do Azure Synapse Analytics no portal do Azure.

3.  Selecione **gerenciar** no painel de navegação principal e, em seguida, selecione **pools de Apache Spark**.
   
4. Selecione um único pool do Spark e carregue o arquivo de configuração do ambiente usando o seletor de arquivo na seção  **pacotes** da página.

    ![Adicionar bibliotecas Python no Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Instalar pacotes do portal do Azure
Para instalar uma biblioteca em um pool do Spark diretamente do portal do Azure:
   
 1. Navegue até o espaço de trabalho do Azure Synapse Analytics no portal do Azure.
   
 2. Na seção **recursos do Synapse** , selecione a guia **pools de Apache Spark** e selecione um pool do Spark na lista.
   
 3. Selecione **pacotes** na seção **configurações** do pool do Spark. 

 4. Carregue o arquivo de configuração do ambiente usando o seletor de arquivo.

    ![Captura de tela que realça o botão carregar arquivo de configuração do ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adicionar bibliotecas do Python")

### <a name="verify-installed-libraries"></a>Verificar bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas estão instaladas, execute o código a seguir

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Atualizar pacotes do Python
Os pacotes podem ser adicionados ou modificados a qualquer momento entre as sessões. Um novo arquivo de configuração de pacote substituirá os pacotes e as versões existentes.  

Para atualizar ou desinstalar uma biblioteca:
1. Navegue até seu espaço de trabalho do Azure Synapse Analytics. 

2. Usando o portal do Azure ou o espaço de trabalho Synapse do Azure, selecione o **pool de Apache Spark** que você deseja atualizar.

3. Navegue até a seção **pacotes** e carregue um novo arquivo de configuração de ambiente
   
4. Depois de salvar as alterações, será necessário encerrar as sessões ativas e permitir que o pool seja reiniciado. Opcionalmente, você pode forçar as sessões ativas a terminar marcando a caixa de seleção para **forçar novas configurações**.

    ![Adicionar bibliotecas do Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Adicionar bibliotecas do Python")
   

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas configurações**, você encerrará todas as sessões atuais para o pool do Spark selecionado. Depois que as sessões forem encerradas, você precisará aguardar até que o pool seja reiniciado. 
>
> Se essa configuração estiver desmarcada, você precisará aguardar a sessão atual do Spark terminar ou interrompê-la manualmente. Depois que a sessão for encerrada, você precisará permitir que o pool seja reiniciado. 


## <a name="manage-a-python-wheel"></a>Gerenciar uma roda em Python

### <a name="install-a-custom-wheel-file"></a>Instalar um arquivo de roda personalizado
Os pacotes de roda personalizados podem ser instalados no pool de Apache Spark carregando todos os arquivos de roda na conta Azure Data Lake Storage (Gen2) que está vinculada ao espaço de trabalho Synapse. 

Os arquivos devem ser carregados no seguinte caminho no contêiner padrão da conta de armazenamento: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Talvez seja necessário adicionar a ```python``` pasta dentro da ```libraries``` pasta, caso ela ainda não exista.

>[!IMPORTANT]
>Personalizado-os pacotes podem ser adicionados ou modificados entre sessões. No entanto, será necessário aguardar a reinicialização do pool e da sessão para ver o pacote atualizado.

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)

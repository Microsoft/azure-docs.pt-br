---
title: Gerenciar bibliotecas do Python para Apache Spark
description: Saiba como adicionar e gerenciar bibliotecas Python usadas pelo Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098767"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerenciar bibliotecas do Python para Apache Spark no Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que você talvez queira incluir em seus programas ou projetos. 

Talvez seja necessário atualizar seu ambiente de pool de Apache Spark sem servidor por vários motivos. Por exemplo, você pode achar que:
- uma das suas principais dependências acabou de lançar uma nova versão.
- Você precisa de um pacote extra para treinar seu modelo de aprendizado de máquina ou preparar seus dados.
- Você encontrou um pacote melhor e não precisa mais do pacote mais antigo.

Para disponibilizar código de terceiros ou compilado localmente para seus aplicativos, você pode instalar uma biblioteca em um dos pools de Apache Spark sem servidor ou na sessão do bloco de anotações. Neste artigo, abordaremos como você pode gerenciar bibliotecas do Python em seu pool de Apache Spark sem servidor.

## <a name="default-installation"></a>Instalação padrão
Apache Spark no Azure Synapse Analytics tem um conjunto completo de bibliotecas para engenharia de dados comum, preparação de dados, aprendizado de máquina e tarefas de visualização de dados. A lista de bibliotecas completa pode ser encontrada em [suporte à versão Apache Spark](apache-spark-version-support.md). 

Quando uma instância do Spark é iniciada, essas bibliotecas serão incluídas automaticamente. Pacotes adicionais de Python e personalizados podem ser adicionados no nível do pool e da sessão do Spark.

## <a name="pool-libraries"></a>Bibliotecas de pool
Depois de identificar as bibliotecas do Python que você gostaria de usar para o aplicativo Spark, você pode instalá-las em um pool do Spark. As bibliotecas de nível de pool estão disponíveis para todos os blocos de anotações e trabalhos em execução no pool.

Há duas maneiras principais de instalar uma biblioteca em um cluster:
-  Instale uma biblioteca de espaço de trabalho que foi carregada como um pacote de espaço de trabalho.
-  Forneça uma especificação de ambiente *requirements.txt* ou *Conda ambiente. yml* para instalar pacotes de repositórios como PyPI, Conda-forjar e muito mais.

> [!IMPORTANT]
> - Se o pacote que você está instalando for grande ou demorar muito para ser instalado, isso afetará o tempo de inicialização da instância do Spark.
> - Não há suporte para a alteração da versão PySpark, Python, escala/Java, .NET ou Spark.
> - A instalação de pacotes de repositórios externos como PyPI, Conda ou canais de Conda padrão não tem suporte em espaços de trabalho habilitados para DEP.

### <a name="install-python-packages"></a>Instalar pacotes do Python
Os pacotes do Python podem ser instalados de repositórios como PyPI e Conda-Forge fornecendo um arquivo de especificação de ambiente. 

#### <a name="environment-specification-formats"></a>Formatos de especificação de ambiente

##### <a name="pip-requirementstxt"></a>requirements.txt PIP
Um arquivo de *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente. Quando um pool é atualizado, os pacotes listados nesse arquivo são baixados de PyPI. As dependências completas são então armazenadas em cache e salvas para reutilização posterior do pool. 

O trecho a seguir mostra o formato do arquivo de requisitos. O nome do pacote PyPI é listado junto com uma versão exata. Esse arquivo segue o formato descrito na documentação de referência do [Pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . 

Este exemplo fixa uma versão específica. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Formato YML (visualização)
Além disso, você também pode fornecer um arquivo *Environment. yml* para atualizar o ambiente do pool. Os pacotes listados neste arquivo são baixados dos canais de Conda padrão, Conda-Forjation e PyPI. Você pode especificar outros canais ou remover os canais padrão usando as opções de configuração.

Este exemplo especifica os canais e as dependências de Conda/PyPI. 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
Para obter detalhes sobre como criar um ambiente desse arquivo environment. yml, consulte [criando um ambiente de um arquivo environment. yml](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Atualizar pacotes do Python
Depois de identificar o arquivo de especificação de ambiente ou o conjunto de bibliotecas que você deseja instalar no pool do Spark, você pode atualizar as bibliotecas do pool do Spark navegando até o Azure Synapse Studio ou portal do Azure. Aqui, você pode fornecer a especificação do ambiente e selecionar as bibliotecas de espaço de trabalho a serem instaladas. 

Depois que as alterações forem salvas, um trabalho do Spark executará a instalação e armazenará em cache o ambiente resultante para reutilização posterior. Quando o trabalho for concluído, novos trabalhos do Spark ou sessões do bloco de anotações usarão as bibliotecas de pools atualizadas. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gerenciar pacotes do Azure Synapse Studio ou portal do Azure
As bibliotecas do pool do Spark podem ser gerenciadas no Azure Synapse Studio ou portal do Azure. 

Para atualizar ou adicionar bibliotecas a um pool do Spark:
1. Navegue até o espaço de trabalho do Azure Synapse Analytics no portal do Azure.

    Se você estiver atualizando do **portal do Azure**:

    - Na seção **recursos do Synapse** , selecione a guia **pools de Apache Spark** e selecione um pool do Spark na lista.
     
    - Selecione os **pacotes** na seção **configurações** do pool do Spark.
  
    ![Captura de tela que realça o botão carregar arquivo de configuração do ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adicionar bibliotecas do Python")
   
    Se você estiver atualizando do **Synapse Studio**:
    - Selecione **gerenciar** no painel de navegação principal e, em seguida, selecione **pools de Apache Spark**.

    - Selecione a seção **pacotes** para um pool do Spark específico.
    ![Captura de tela que realça a opção de configuração do ambiente de carregamento do estúdio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Adicionar bibliotecas Python do estúdio")
   
2. Carregue o arquivo de configuração do ambiente usando o seletor de arquivo na seção  **pacotes** da página.
3. Você também pode selecionar **pacotes de espaço de trabalho** adicionais para adicionar ao pool. 
4. Depois de salvar as alterações, um trabalho do sistema será disparado para instalar e armazenar em cache as bibliotecas especificadas. Esse processo ajuda a reduzir o tempo de inicialização geral da sessão. 
5. Depois que o trabalho for concluído com êxito, todas as novas sessões irão pegar as bibliotecas de pools atualizadas.

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas configurações**, você encerrará todas as sessões atuais para o pool do Spark selecionado. Depois que as sessões forem encerradas, você precisará aguardar até que o pool seja reiniciado. 
>
> Se essa configuração estiver desmarcada, você precisará aguardar a sessão atual do Spark terminar ou interrompê-la manualmente. Depois que a sessão for encerrada, você precisará permitir que o pool seja reiniciado.


##### <a name="track-installation-progress-preview"></a>Acompanhar o progresso da instalação (versão prévia)
Um trabalho do Spark reservado pelo sistema é iniciado cada vez que um pool é atualizado com um novo conjunto de bibliotecas. Esse trabalho do Spark ajuda a monitorar o status da instalação da biblioteca. Se a instalação falhar devido a conflitos de biblioteca ou outros problemas, o pool do Spark será revertido para seu estado anterior ou padrão. 

Além disso, os usuários também podem inspecionar os logs de instalação para identificar conflitos de dependência ou ver quais bibliotecas foram instaladas durante a atualização do pool.

Para exibir esses logs:
1. Navegue até a lista de aplicativos Spark na guia **monitorar** . 
2. Selecione o trabalho do aplicativo Spark do sistema que corresponde à sua atualização de pool. Esses trabalhos do sistema são executados sob o título *SystemReservedJob-LibraryManagement* .
   ![Captura de tela que realça o trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Exibir trabalho da biblioteca do sistema")
3. Alterne para exibir os logs de **Driver** e **stdout** . 
4. Nos resultados, você verá os logs relacionados à instalação de suas dependências.
    ![Captura de tela que realça os resultados do trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Exibir o progresso do trabalho da biblioteca do sistema")

## <a name="install-wheel-files"></a>Instalar arquivos de roda
Os arquivos de roda do Python são uma maneira comum de empacotar as bibliotecas do Python. No Azure Synapse Analytics, os usuários podem carregar seus arquivos de roda para um local conhecido, a conta de Azure Data Lake Storage ou carregar usando a interface de pacotes de espaço de trabalho Synapse do Azure.

### <a name="workspace-packages-preview"></a>Pacotes de espaço de trabalho (versão prévia)
Os pacotes de espaço de trabalho podem ser arquivos de roda personalizados ou privados. Você pode carregar esses pacotes em seu espaço de trabalho e, posteriormente, atribuí-los a um pool do Spark específico.

Para adicionar pacotes de espaço de trabalho:
1. Navegue até a guia **gerenciar**  >  **pacotes de espaço de trabalho** .
2. Carregue seus arquivos de roda usando o seletor de arquivo.
3. Depois que os arquivos tiverem sido carregados no espaço de trabalho Synapse do Azure, você poderá adicionar esses pacotes a um determinado pool de Apache Spark.

![Captura de tela que realça os pacotes de espaço de trabalho.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Exibir pacotes de espaço de trabalho")

### <a name="storage-account"></a>Conta de armazenamento
Os pacotes de roda personalizados podem ser instalados no pool de Apache Spark carregando todos os arquivos de roda na conta Azure Data Lake Storage (Gen2) que está vinculada ao espaço de trabalho Synapse. 

Os arquivos devem ser carregados no seguinte caminho no contêiner padrão da conta de armazenamento: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Talvez seja necessário adicionar a ```python``` pasta dentro da ```libraries``` pasta, caso ela ainda não exista.

> [!IMPORTANT]
> Para instalar bibliotecas personalizadas usando o método de armazenamento do Azure datalake, você deve ter as permissões de proprietário de dados de armazenamento de **blob de armazenamento** ou de **proprietários** da conta de armazenamento do Gen2 primário que está vinculada ao espaço de trabalho do Azure Synapse Analytics.

>[!WARNING]
> Ao fornecer arquivos de roda personalizados, os usuários não podem fornecer arquivos de roda na conta de armazenamento e na interface de biblioteca de espaço de trabalho. Se ambos forem fornecidos, somente os arquivos de roda especificados na lista pacotes de espaço de trabalho serão instalados. 

## <a name="session-scoped-packages-preview"></a>Pacotes no escopo da sessão (versão prévia)
Além dos pacotes de nível de pool, você também pode especificar bibliotecas no escopo da sessão no início de uma sessão do bloco de anotações.  Bibliotecas com escopo de sessão permitem especificar e usar ambientes de Python personalizados em uma sessão de notebook. 

Ao usar bibliotecas com escopo de sessão, é importante ter os seguintes pontos em mente:
   - Quando você instala bibliotecas com escopo de sessão, somente o notebook atual tem acesso às bibliotecas especificadas. 
   - Essas bibliotecas não afetarão outras sessões ou trabalhos usando o mesmo pool do Spark. 
   - Essas bibliotecas são instaladas na parte superior das bibliotecas de tempo de execução base e de pool. 
   - As bibliotecas de notebook terão a precedência mais alta.

Para especificar pacotes no escopo da sessão:
1.  Navegue até o pool do Spark selecionado e verifique se você habilitou as bibliotecas no nível da sessão.  Você pode habilitar essa configuração navegando até a guia **gerenciar**  >  pacotes de **pool Apache Spark**  >   . ![habilite pacotes de sessão.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Habilitar pacotes de sessão")
2.  Depois que a configuração tiver sido aplicada, você poderá abrir um bloco de anotações e selecionar **Configurar** >  **pacotes** de sessão.
  ![Especifique os pacotes de sessão.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Atualizar configuração de sessão")
3.  Aqui, você pode carregar um arquivo Conda *Environment. yml* para instalar ou atualizar pacotes em uma sessão. Depois de iniciar a sessão, as bibliotecas especificadas serão instaladas. Depois que a sessão terminar, essas bibliotecas não estarão mais disponíveis, pois são específicas da sua sessão.

## <a name="verify-installed-libraries"></a>Verificar bibliotecas instaladas
Para verificar se as versões corretas das bibliotecas corretas estão instaladas do PyPI, execute o seguinte código:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Em alguns casos, para exibir as versões do pacote do Conda, talvez seja necessário inspecionar a versão do pacote individualmente.

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)
- Solucionar erros de instalação da biblioteca: [solucionar erros de biblioteca](apache-spark-troubleshoot-library-errors.md)
- Criar um canal Conda privado usando sua conta de Azure Data Lake Storage: [canais privados Conda](./spark/../apache-spark-custom-conda-channel.md)

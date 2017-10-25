---
title: "Provisionar uma Máquina Virtual de Ciência de Dados do Linux CentOS no Azure | Microsoft Docs"
description: "Configure e crie uma Máquina Virtual de Ciência de Dados Linux no Azure para realizar a análise e o machine learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: e36c28ef1c05dcdcebc7372316c7f144c92fd02f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Provisionar uma Máquina Virtual de Ciência de Dados do Linux CentOS no Azure

A máquina virtual de Ciência de Dados do Linux é uma máquina virtual do Azure baseada em CentOS que acompanha um conjunto de ferramentas pré-instaladas. Essas ferramentas normalmente são usadas para fazer análises de dados e machine learning. Os componentes de software principais incluídos são:

* Sistema operacional: Distribuição CentOS Linux.
* Microsoft R Server Developer Edition
* Distribuição do Anaconda Python (versões 2.7 e 3.5), incluindo bibliotecas de análise de dados populares
* JuliaPro - uma distribuição curada da linguagem Julia, com bibliotecas populares de análise de dados e científicas
* Instância autônoma de Spark e Hadoop de nó único (HDFS, Yarn)
* JupyterHub - um servidor de notebook Jupyter multiusuário com suporte para kernels R, Python, PySpark, Julia
* Gerenciador de Armazenamento do Azure
* CLI (interface de linha de comando) do Azure para gerenciar recursos do Azure
* Banco de dados PostgresSQL
* Ferramentas de Machine Learning
  * [Kit de Ferramentas Cognitivas](https://github.com/Microsoft/CNTK): um kit de ferramentas de software de aprendizado profundo da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um sistema de machine learning rápido com suporte a técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
  * [Rattle](http://rattle.togaware.com/) (a "R Analytical Tool To Learn Easily" – Ferramenta Analítica do R para Aprender com Facilidade): uma ferramenta que facilita a introdução à análise de dados e ao machine learning em R, com uma exploração de dados baseada em GUI e modelagem com geração de código R automática.
* SDK do Azure em Java, Python, node.js, Ruby, PHP
* Bibliotecas em R e Python para uso no Azure Machine Learning e outros serviços do Azure
* Ferramentas de desenvolvimento e editores (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi)


Fazer a ciência de dados envolve a iteração em uma sequência de tarefas:

1. Localizar, carregar e pré-processar dados
2. Compilar e testar modelos
3. Implantar os modelos para consumo em aplicativos inteligentes

Cientistas de dados usam várias ferramentas para concluir essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e depois baixar, compilar e instalar essas versões.

A Máquina Virtual de Ciência de Dados Linux pode aliviar essa carga substancialmente. Use-a para iniciar rapidamente seu projeto de análise. Ela permite que você trabalhe nas tarefas em várias linguagens, incluindo R, Python, SQL, Java e C++. O Eclipse fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços em Linux para a plataforma de nuvem da Microsoft. Além disso, você tem acesso a outras linguagens como Ruby, Perl, PHP e node.js, também pré-instaladas.

Não há encargos de software para esta imagem da VM de ciência de dados. Você paga apenas pelas taxas de uso de hardware do Azure, que são avaliadas com base no tamanho da máquina virtual que você provisiona com a imagem de VM. Encontre mais detalhes sobre as taxas de computação na [página de listagem de VMs do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da Máquina Virtual de Ciência de Dados
Uma imagem do [Ubuntu](dsvm-ubuntu-intro.md) também está disponível, com muitas das mesmas ferramentas que a imagem do CentOS além de estruturas de aprendizado aprofundado. Uma imagem do [Windows](provision-vm.md) também está disponível.

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma Máquina Virtual de Ciência de Dados Linux, você deve ter o seguinte:

* **Uma assinatura do Azure**: para obter uma, confira [Obter avaliação gratuita do Azure](https://azure.microsoft.com/free/).
* **Uma conta de armazenamento do Azure**: para criar uma, confira [Criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Como alternativa, se você não quiser usar uma conta existente, a conta de armazenamento poderá ser criada como parte do processo de criação da VM.

## <a name="create-your-linux-data-science-virtual-machine"></a>Criar sua Máquina Virtual de Ciência de Dados Linux
Veja as etapas para criar uma instância da Máquina Virtual de Ciência de Dados Linux:

1. Navegue até a listagem de máquinas virtuais no [Portal do Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2. Clique em **Criar** (na parte inferior) para abrir o assistente. ![configure-data-science-vm](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3. As seções a seguir fornecem as entradas para cada uma das etapas no assistente (enumeradas à direita da figura acima) que são usadas para criar a Máquina Virtual de Ciência de Dados da Microsoft. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:
   
   a. **Noções básicas**:
   
   * **Nome**: o nome do servidor de ciência de dados que você está criando.
   * **Nome de Usuário**: primeira ID de entrada da conta.
   * **Senha**: primeira senha da conta (você pode usar a chave pública SSH, em vez de senha).
   * **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
   * **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente.
   * **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede.
   
   b. **Tamanho**:
   
   * Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Selecione **Exibir Tudo** para ver mais opções de tamanhos de VM.
   
   c. **Configurações**:
   
   * **Tipo de disco**: escolha **Premium** se preferir uma SSD (unidade de estado sólido). Caso contrário, escolha **Standard**.
   * **Conta de Armazenamento:** é possível criar uma nova conta de armazenamento do Azure em sua assinatura ou usar uma já existente no mesmo Local que foi escolhido na etapa **Noções básicas** do assistente.
   * **Outros parâmetros**: na maioria dos casos, você simplesmente usa os valores padrão. Passe o ponteiro do mouse sobre o link informativo para obter ajuda sobre um campo específico, caso você queira considerar o uso de valores não padrão.
   
   d. **Resumo**:
   
   * Verifique se todas as informações inseridas estão corretas.
   
   e. **Comprar**:
   
   * Para iniciar o provisionamento, clique em **Comprar**. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho** .

O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal do Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Como acessar uma Máquina Virtual de Ciência de Dados Linux
Após a criação da VM, você poderá entrar nela usando SSH. Use as credenciais da conta criada na seção **Noções básicas** da etapa 3 para a interface shell de texto. No Windows, você pode baixar uma ferramenta de cliente SSH como o [Putty](http://www.putty.org). Se você preferir uma área de trabalho gráfica (Sistema do Windows X), poderá usar o encaminhamento X11 no Putty ou instalar o cliente X2Go.

> [!NOTE]
> O cliente X2Go apresentou desempenho significativamente melhor do que o encaminhamento X11 nos testes. Recomendamos o uso do cliente X2Go para uma interface gráfica de área de trabalho.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Instalando e configurando o cliente X2Go
A VM Linux já está provisionada com um servidor X2Go e pronta para aceitar conexões de cliente. Para se conectar à área de trabalho gráfica da VM Linux, faça o seguinte em seu cliente:

1. Baixe e instale o cliente X2Go para sua plataforma de cliente [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Execute o cliente X2Go e selecione **Nova Sessão**. Ele abrirá uma janela de configuração com várias guias. Insira os seguintes parâmetros de configuração:
   * **Guia Sessão**:
     * **Host**: o nome do host ou endereço IP da sua VM de Ciência de Dados Linux.
     * **Logon**: nome de usuário na VM Linux.
     * **Porta SSH**: deixe-a em 22, o valor padrão.
     * **Tipo de Sessão**: altere o valor para XFCE. No momento, a VM Linux dá suporte apenas à área de trabalho XFCE.
   * **Guia Mídia**: se você não precisar usar suporte de som e impressão de cliente, poderá desativar esses recursos.
   * **Pastas compartilhadas**: caso você queira que os diretórios de seus computadores cliente sejam montados na VM Linux, adicione os diretórios de computador cliente que você deseja compartilhar com a VM nesta guia.

Após o logon na VM usando o cliente SSH ou a área de trabalho gráfica XFCE por meio do cliente X2Go, você estará pronto para começar a usar as ferramentas instaladas e configuradas na VM. No XFCE, você pode ver atalhos do menu de aplicativos e ícones da área de trabalho para muitas das ferramentas.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Ferramentas Instaladas na Máquina Virtual de Ciência de Dados Linux
### <a name="microsoft-r-server"></a>Servidor R da Microsoft
R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se você quiser usar o R para sua análise, a VM terá o MRS (Microsoft R Server) com o MRO (Microsoft R Open) e a MKL (Math Kernel Library). A MKL otimiza as operações matemáticas frequentes em algoritmos analíticos. O MRO é 100% compatível com CRAN-R e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no MRO. O MRS fornece dimensionamento e operacionalização de modelos R em serviços Web. Edite seus programas R em um dos editores padrão como RStudio, vi, Emacs ou gedit. Se você estiver usando o editor Emacs, observe que o ESS (Emacs Speaks Statistics) do pacote Emacs, que simplifica o trabalho com arquivos em R no editor Emacs, foi pré-instalado.

Para iniciar o console R, basta digitar **R** no shell. Isso leva você para um ambiente interativo. Para desenvolver seu programa R, você normalmente usa um editor como gedit, vi ou Emacs e, em seguida, executa os scripts no R. Com o RStudio, você tem um ambiente IDE gráfico completo para desenvolver o seu programa R.

Também há um script de R para você instalar os [20 melhores pacotes do R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) , caso queira. Esse script pode ser executado quando você estiver na interface interativa de R, na qual você pode entrar (conforme mencionado) digitando **R** no shell.  

### <a name="python"></a>Python
Para o desenvolvimento com Python, as distribuições 2.7 e 3.5 do Anaconda Python foram instaladas. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar os editores de texto padrão. Além disso, você pode usar o Spyder, um IDE do Python que é fornecido com distribuições do Anaconda Python. O Spyder precisa de uma área de trabalho gráfica ou de encaminhamento X11. Um atalho para o Spyder é fornecido na área de trabalho gráfica.

Como temos ambos o Python 2.7 e o 3.5, você precisa ativar especificamente a versão desejada do Python (ambiente conda) com a qual você deseja trabalhar na sessão atual. O processo de ativação define a variável PATH para a versão desejada do Python.

Para ativar o ambiente de conda do Python 2.7, execute o seguinte comando do shell:

    source /anaconda/bin/activate root

O Python 2.7 está instalado em */anaconda/bin*.

Para ativar o ambiente conda do Python 3.5, execute o seguinte do shell:

    source /anaconda/bin/activate py35


O Python 3.5 está instalado em */anaconda/envs/py35/bin*.

Para invocar a sessão interativa do Python, basta digitar **Python** no shell. Se estiver em uma interface gráfica ou tiver a configuração do encaminhamento X11, você poderá digitar o comando **pycharm** para iniciar o IDE do PyCharm Python.

Para instalar bibliotecas Python adicionais, execute o comando ```conda``` ou ````pip```` sob sudo e forneça o caminho completo do Gerenciador de pacotes do Python (conda ou pip) para instalar no ambiente correto de Python. Por exemplo:

    sudo /anaconda/bin/pip install <package> #for Python 2.7 environment
    sudo /anaconda/envs/py35/bin/pip install <package> # for Python 3.5 environment


### <a name="jupyter-notebook"></a>Notebook Jupyter
A distribuição do Anaconda também acompanha um notebook Jupyter, um ambiente de compartilhamento de código e de análise. O notebook Jupyter é acessado com o JupyterHub. Entre usando seu nome de usuário e senha locais do Linux.

O servidor do notebook Jupyter foi previamente configurado com os kernels do Python 2, do Python 3 e do R. Há um ícone de área de trabalho chamado "Bloco de anotações do Jupyter" para iniciar o navegador a fim de acessar o servidor do notebook. Se você estiver usando a VM por meio de cliente SSH ou X2Go, também poderá visitar [https://localhost:8000/](https://localhost:8000/) para acessar o servidor do notebook Jupyter.

> [!NOTE]
> Continue se você obtiver quaisquer avisos de certificado.
> 
> 

Você pode acessar o servidor de bloco de anotações do Jupyter por meio de qualquer host. Basta digitar *https://\<endereço IP ou nome DNS da VM\>:8000/*

> [!NOTE]
> A porta 8000 é aberta no firewall por padrão quando a VM é provisionada.
> 
> 

Empacotamos exemplos de notebooks, um em Python em outro em R. Você pode ver o link para os exemplos na página inicial do bloco de anotações após a autenticação no notebook Jupyter usando a senha e o nome de usuário Linux locais. Você pode criar um novo notebook selecionando **Novo** e o kernel de linguagem apropriado. Caso não visualize o botão **Novo**, clique no ícone do **Jupyter** na parte esquerda superior para ir para a home page do servidor de notebook.

### <a name="apache-spark-standalone"></a>Apache Spark autônomo 
Há uma instância autônoma do Apache Spark pré-instalada no DSVM Linux para ajudar você a desenvolver aplicativos Spark localmente, antes de testar e implantar em clusters maiores. Execute programas PySpark através do kernel de Jupyter. Ao abrir o Jupyter e clicar no botão **Novo**, você deverá ver uma lista de kernels disponíveis. O “Spark – Python” é o kernel PySpark que permite a criação de aplicativos Spark usando a linguagem Python. Também é possível usar um IDE Python como PyCharm ou Spyder para compilar seu programa em Spark. Como essa é uma instância autônoma, a pilha de Spark será executada dentro do programa de chamada cliente. Isso facilita e agiliza a solução de problemas em comparação com o desenvolvimento em um cluster Spark. 

Um exemplo de notebook PySpark é fornecido no Jupyter, e você pode encontrá-lo no diretório "SparkML" no diretório inicial do Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se você estiver programando em R para Spark, use o Microsoft R Server, SparkR ou sparklyr. 

Antes de executar no contexto do Spark no Microsoft R Server, execute uma etapa de configuração única para habilitar uma instância local de HDFS Hadoop e Yarn de nó único. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitá-los, execute os seguintes comandos como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Você pode interromper os serviços Hadoop relacionados quando não precisar mais deles executando ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` Um exemplo que demonstra como desenvolver e testar o MRS no contexto de Spark remoto (que é a instância de Spark autônoma no DSVM) é fornecido e disponibilizado no diretório `/dsvm/samples/MRS`. 

### <a name="ides-and-editors"></a>IDEs e editores
Você tem a opção de vários editores de código. Isso inclui vi/VIM, Emacs, gEdit, PyCharm, RStudio, Eclipse e IntelliJ. gEdit, Eclipse, IntelliJ, RStudio e PyCharm são editores gráficos, e você precisa estar conectado a uma área de trabalho gráfica para usá-los. Há atalhos do menu do aplicativo e da área de trabalho para iniciar esses editores.

**VIM** e **Emacs** são editores baseados em texto. No Emacs, instalamos um pacote complementar chamado ESS (Emacs Speaks Statistics) que facilita o trabalho com R no editor Emacs. Mais informações podem ser encontradas em: [ESS](http://ess.r-project.org/).

**Eclipse** é um IDE de software livre, extensível e que dá suporte a vários idiomas. A edição para desenvolvedores de Java é a instância instalada na VM. Há plug-ins disponíveis para várias linguagens populares, que podem ser instalados para estender o ambiente. Também temos um plug-in instalado no Eclipse chamado **Kit de Ferramentas para Eclipse do Azure**. Ele permite criar, desenvolver, testar e implantar aplicativos do Azure usando o ambiente de desenvolvimento do Eclipse, que oferece suporte a linguagens como Java. Também há um **SDK do Azure para Java** que permite o acesso a diferentes serviços do Azure de dentro de um ambiente Java. É possível encontrar mais informações sobre o kit de ferramentas do Azure para Eclipse em [Kit de ferramentas do Azure para Eclipse](../../azure-toolkit-for-eclipse.md).

**LaTex** é instalado por meio do pacote texlive, juntamente com um pacote complementar do Emacs chamado [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) , que simplifica a criação de seus documentos do LaTex no Emacs.  

### <a name="databases"></a>Bancos de dados
#### <a name="postgres"></a>Postgres
O banco de dados de software livre **Postgres** está disponível na VM com os serviços em execução e initdb já concluído. Você ainda precisa criar bancos de dados e usuários. Para saber mais, confira a [Documentação de Postgres](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Cliente gráfico do SQL
**SQuirrel SQL**, um cliente gráfico do SQL, foi fornecido para conectar-se a bancos de dados diferentes (como o Microsoft SQL Server, Postgres, MySQL) e executar consultas SQL. Você pode executá-lo de uma sessão de área de trabalho gráfica (usando o cliente X2Go, por exemplo). Para invocar o SQuirrel SQL, você pode iniciá-lo clicando no ícone na área de trabalho ou então executar o comando a seguir no shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Antes do primeiro uso, configure os drivers e aliases de banco de dados. Os JDBC drivers estão localizados em:

*/usr/share/java/jdbcdrivers*

Para saber mais, confira [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comando para acessar o Microsoft SQL Server
O pacote de driver ODBC do SQL Server também vem com duas ferramentas de linha de comando:

**bcp**: o utilitário em massa bcp copia dados entre uma instância do Microsoft SQL Server e um arquivo de dados em um formato especificado pelo usuário. O utilitário bcp pode ser usado para importar grandes números de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela ou então entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

Para saber mais, confira [Conectando-se com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: você pode inserir instruções de Transact-SQL com o utilitário sqlcmd, bem como procedimentos do sistema e arquivos de script no prompt de comando. Esse utilitário usa o ODBC para executar lotes do Transact-SQL.

Para saber mais, confira [Conectando-se com o sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Há algumas diferenças nesse utilitário entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.
> 
> 

#### <a name="database-access-libraries"></a>Bibliotecas de acesso do banco de dados
Há bibliotecas disponíveis em R e Python para acessar bancos de dados.

* No R, o pacote **RODBC** ou pacote **dplyr** permite que você consulte ou execute instruções SQL no servidor de banco de dados.
* No Python, a biblioteca **pyodbc** fornece acesso ao banco de dados com o ODBC como a camada subjacente.  

Para acessar o **Postgres**:

* Do R: use o pacote **RPostgreSQL**.
* Do Python: use a biblioteca **psycopg2** .

### <a name="azure-tools"></a>Ferramentas do Azure
As ferramentas do Azure a seguir são instaladas na VM:

* **Interface de linha de comando do Azure**: a CLI do Azure permite a você criar e gerenciar recursos do Azure por meio de comandos do shell. Para invocar as ferramentas do Azure, digite apenas **azure help**. Para saber mais, confira a [página de documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Gerenciador de Armazenamento do Microsoft Azure**: o Gerenciador de Armazenamento do Microsoft Azure é uma ferramenta gráfica usada para navegar pelos objetos armazenados na sua Conta de Armazenamento do Azure e carregar e baixar os dados nos blobs do Azure. Você pode acessar o Gerenciador de Armazenamento do ícone de atalho da área de trabalho. Você pode invocá-lo de um prompt do shell digitando **StorageExplorer**. Você precisa estar conectado em um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas do Azure**: veja a seguir algumas das bibliotecas pré-instaladas.
  
  * **Python**: as bibliotecas relacionadas ao Azure no Python que estão instaladas são **azure**, **azureml**, **pydocumentdb** e **pyodbc**. Com as três primeiras bibliotecas, você pode acessar os serviços de armazenamento do Azure, o Azure Machine Learning e o Azure Cosmos DB (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC Driver for SQL Server), habilita, do Python, o acesso ao SQL Server, ao Banco de Dados SQL do Azure e ao SQL Data Warehouse do Azure pelo uso de uma interface do ODBC. Insira **pip list** para ver todas as bibliotecas listadas. Certifique-se de executar este comando nos ambientes do Python 2.7 e 3.5.
  * **R**: as bibliotecas relacionadas ao Azure em R que estão instaladas são **AzureML** e **RODBC**.
  * **Java**: a lista de bibliotecas Java do Azure pode ser encontrada no diretório **/dsvm/sdk/AzureSDKJava** na VM. As bibliotecas principais são as APIs de armazenamento e gerenciamento do Azure, o Azure Cosmos DB e os drivers JDBC para SQL Server.  

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox previamente instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço de nuvem totalmente gerenciado que habilita você a compilar, implantar e compartilhar soluções de análise preditiva. Você compila seus modelos e experimentos do Azure Machine Learning Studio. Ele pode ser acessado de um navegador da Web na máquina virtual de ciência de dados visitando [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de fazer logon no Azure Machine Learning Studio, você tem acesso a uma tela de experimentação em que você pode compilar um fluxo lógico para os algoritmos de aprendizado de máquina. Você também tem acesso a um Notebook do Jupyter hospedado no Azure Machine Learning e pode trabalhar perfeitamente com o Machine Learning Studio. Coloque em operação os modelos de aprendizado de máquina compilados encapsulando-os em uma interface de serviço Web. Isso habilita clientes escritos em qualquer linguagem a invocar as previsões dos modelos de aprendizado de máquina. Para saber mais, confira a [Documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Você pode também criar seus modelos em R ou Python na VM e, em seguida, implantá-los em produção no Azure Machine Learning. Instalamos bibliotecas em R (**AzureML**) e Python (**azureml**) para habilitar essa funcionalidade.

Para saber mais sobre como implantar modelos em R e Python no Azure Machine Learning, confira a seção [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](vm-do-ten-things.md) (particularmente, a seção “Compilar os modelos usando R ou Python e operacionalizá-los usando o Azure Machine Learning”).

> [!NOTE]
> Essas instruções foram escritas para a versão do Windows da VM de Ciência de Dados. Mas as informações fornecidas sobre a implantação de modelos para o Azure Machine Learning são aplicáveis à VM Linux.
> 
> 

### <a name="machine-learning-tools"></a>Ferramentas do Machine Learning
A VM vem com algumas ferramentas e algoritmos de aprendizado de máquina que foram pré-compiladas e pré-instaladas localmente. Estão incluídos:

* **Kit de Ferramentas Cognitivas da Microsoft**: um kit de ferramentas de aprendizado profundo.
* **Vowpal Wabbit**: um algoritmo de aprendizado rápido online.
* **xgboost**: uma ferramenta que fornece algoritmos de árvore aumentados e otimizados.
* **Python**: o Anaconda Python é fornecido com os algoritmos de amachine learning com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas usando o comando `pip install` .
* **R**: uma vasta biblioteca de funções de machine learning está disponível para R. Algumas das bibliotecas pré-instaladas são lm, glm, randomForest e rpart. Outras bibliotecas podem ser instaladas, executando:
  
        install.packages(<lib name>)

Veja algumas informações adicionais sobre as três primeiras ferramentas de aprendizado de máquina na lista.

#### <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft
Este é um kit de ferramentas de aprendizado aprofundado de software livre. Ele é uma ferramenta de linha de comando (cntk), e já está em PATH.

Para executar um exemplo básico, execute os seguintes comandos no shell:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Para saber mais, confira a seção sobre CNTK do [GitHub](https://github.com/Microsoft/CNTK) e o [wiki de CNTK](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit é um sistema de aprendizado de máquina rápido que usa técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.

Para executar a ferramenta em um exemplo básico, faça o seguinte:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Há outras demonstrações maiores nesse diretório. Para saber mais sobre VW, confira [esta seção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e o [wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>XGBoost
Essa é uma biblioteca que é projetada e otimizada para algoritmos aumentados (de árvore). O objetivo dessa biblioteca é estender os limites de computação de máquinas para os extremos necessários de modo a fornecer aumento de árvore de grande escala escalonável, portátil e preciso.

Ele é fornecido como uma linha de comando, bem como uma biblioteca do R.

Para usar esta biblioteca em R, você pode iniciar a sessão interativa do R (basta digitar **R** no shell) e carregar a biblioteca.

Aqui está um exemplo simples, que você pode executar no prompt do R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Para executar a linha de comando do xgboost, aqui estão os comandos a executar no shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Um arquivo .model é gravado no diretório especificado. Mais informações sobre este exemplo de demonstração podem ser encontradas [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para saber mais sobre o xgboost, confira a [página de documentação do xgboost](https://xgboost.readthedocs.org/en/latest/) e seu [repositório GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (the **R** **A**nalytical **T**ool **T**o **L**earn **E**asily – Ferramenta Analítica do R para Aprender com Facilidade) usa exploração e modelagem de dados com base em GUI. Ele apresenta resumos estatísticos e visuais dos dados, transforma os dados que podem ser modelados prontamente, compila modelos de dados supervisionados e sem supervisão, apresenta o desempenho dos modelos graficamente e calcula as pontuações de novos conjuntos de dados. Ele também gera código R, replicando as operações na interface do usuário que pode ser executado diretamente em R ou usado como ponto de partida para análise posterior.

Para executar o Rattle, você precisa estar em uma sessão de logon da área de trabalho gráfica. No terminal, digite ```R``` para entrar no ambiente R. No prompt do R, digite os seguintes comandos:

    library(rattle)
    rattle()

Agora, uma interface gráfica é aberta com um conjunto de guias. Aqui estão as etapas de início rápido no Rattle necessárias para usar um conjunto de dados meteorológicos de exemplo e compilar um modelo. Em algumas das etapas abaixo, você é solicitado a instalar e carregar automaticamente alguns pacotes do R que ainda não estão no sistema.

> [!NOTE]
> Se não tiver acesso para instalar o pacote no diretório do sistema (o padrão), você poderá ver uma solicitação na janela do console do R para instalar pacotes na sua biblioteca pessoal. Caso veja essas solicitações, responda *s* .
> 
> 

1. Clique em **Executar**.
2. Uma caixa de diálogo é exibida perguntando se você deseja usar o conjunto de dados meteorológicos de exemplo. Clique em **Sim** para carregar o exemplo.
3. Clique na guia **Modelo** .
4. Clique em **Executar** para compilar uma árvore de decisão.
5. Clique em **Desenhar** para exibir a árvore de decisão.
6. Clique no botão de opção **Floresta** e clique em **Executar** para compilar uma floresta aleatória.
7. Clique na guia **Avaliar** .
8. Clique no botão de opção **Risco** e em **Executar** para exibir duas plotagens de desempenho de Risco (Cumulativo).
9. Clique a guia **Log** para mostrar o código R gerado das operações anteriores.
   (Devido a um bug na versão atual do Rattle, você precisa inserir um caractere *#* na frente de *Exportar este log...* no texto do log.)
10. Clique no botão **Exportar** para salvar o script de R chamado *weather_script.R* na pasta base.

Você pode sair do Rattle e do R. Agora você pode modificar o script do R gerado ou usá-la como ele é para executá-lo em qualquer momento, para repetir tudo o que foi feito na interface do usuário do Rattle. Essa é uma maneira fácil, especialmente para iniciantes em R, de fazer análise e aprendizado de máquina rapidamente em uma interface gráfica e, ao mesmo tempo, gerar código em R automaticamente para modificar e/ou aprender.

## <a name="next-steps"></a>Próximas etapas
Veja como você pode continuar seu aprendizado e exploração:

* O passo a passo [Ciência de dados na Máquina Virtual da Ciência de Dados do Linux](linux-dsvm-walkthrough.md) mostra como executar várias tarefas comuns de ciência de dados com a VM de Ciência de Dados Linux provisionada aqui. 
* Explore as várias ferramentas de ciência de dados na VM de ciência de dados ao experimentar as ferramentas descritas neste artigo. Você também pode executar *dsvm-more-info* no shell contido na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visite a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) para obter exemplos de análise de dados e de aprendizado de máquina que usam o Cortana Analytics Suite.


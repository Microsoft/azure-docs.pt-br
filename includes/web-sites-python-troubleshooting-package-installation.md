Alguns pacotes podem não ser instalados usando pip durante a execução do Azure.  É possível simplesmente que o pacote não esteja disponível no índice de pacotes de Python.  Talvez um compilador seja necessário (um compilador não está disponível no computador que executa o aplicativo Web no Serviço de Aplicativo do Azure).

Nesta seção, examinaremos modos de lidar com esse problema.

### <a name="request-wheels"></a>Solicitar discos
Se a instalação do pacote requer um compilador, você deve tentar entrar em contato com o proprietário do pacote para solicitar que discos sejam disponibilizados para o pacote.

Com o lançamento recente do [compilador do Microsoft Visual C++ para Python 2.7][Microsoft Visual C++ Compiler for Python 2.7], agora é mais fácil criar pacotes que têm código nativo para o Python 2.7.

### <a name="build-wheels-requires-windows"></a>Compilar discos(requires Windows)
Observação: ao usar essa opção, compile o pacote usando um ambiente Python que corresponda à plataforma/arquitetura/versão usada no aplicativo Web no Serviço de Aplicativo do Azure (Windows/32 bits/2.7 ou 3.4).

Se o pacote não for instalado porque exige um compilador, você pode instalar o compilador em sua máquina local e criar um disco para o pacote, que em seguida, você incluirá no repositório.

Usuários do Mac/Linux: Se você não tiver acesso a uma máquina do Windows, consulte [Criar uma máquina Virtual executando Windows][Create a Virtual Machine Running Windows] para saber como criar uma máquina virtual no Azure.  Você pode usá-lo para criar os discos, adicioná-los ao repositório e descartar a VM se desejar. 

Para o Python 2.7, você pode instalar o [Compilador do Microsoft Visual C++ para Python 2.7][Microsoft Visual C++ Compiler for Python 2.7].

Para o Python 3.4, você pode instalar o [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

Para criar os discos, você precisará do pacote de discos:

    env\scripts\pip install wheel

Você usará `pip wheel` para compilar uma dependência:

    env\scripts\pip wheel azure==0.8.4

Isso cria um arquivo .whl na pasta \wheelhouse.  Adicione a pasta \wheelhouse e os arquivos de disco a seu repositório.

Edite seu requirements.txt para acrescentar a opção `--find-links` na parte superior. Isso instrui o pip a buscar uma correspondência exata na pasta local antes de consultar o índice de pacotes do python.

    --find-links wheelhouse
    azure==0.8.4

Se você quiser incluir todas as suas dependências na pasta \wheelhouse e não usar o índice de pacotes do python, você pode forçar o pip a ignorar o índice de pacotes adicionando `--no-index` na parte superior do seu requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizar a instalação
Você pode personalizar o script de implantação para instalar um pacote no ambiente virtual usando um instalador alternativo, como easy\_install.  Consulte deploy.cmd para obter um exemplo que é comentado.  Certifique-se de que esses pacotes não são listados em requirements.txt, para impedir que o pip os instale.

Adicione isto ao script de implantação:

    env\scripts\easy_install somepackage

Você também poderá usar easy\_install para instalar por meio de um instalador exe (alguns são compatíveis com zip, portanto easy\_install dá suporte a eles).  Adicione o instalador ao repositório e chame easy\_install, passando o caminho para o executável.

Adicione isto ao script de implantação:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Incluir o ambiente virtual no repositório (requer o Windows)
Observação: ao usar essa opção, use um ambiente virtual que corresponda à plataforma/arquitetura/versão usada no aplicativo Web no Serviço de Aplicativo do Azure (Windows/32 bits/2.7 ou 3.4).

Se você incluir o ambiente virtual no repositório poderá, pela criação de um arquivo vazio, impedir que o script de implantação faça o gerenciamento do ambiente virtual no Azure:

    .skipPythonDeployment

É recomendável que você exclua o ambiente virtual existente no aplicativo Web, para impedir que sobrem arquivos de quando o ambiente virtual for gerenciado automaticamente.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949

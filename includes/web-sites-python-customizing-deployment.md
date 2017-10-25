O Azure determinará que o aplicativo usa Python **se estas duas condições forem verdadeiras**:

* arquivo requirements.txt na pasta raiz
* qualquer arquivo .py na pasta raiz OU um runtime.txt que especifica o python

Quando é o caso desta última opção, ele usará um script de implantação específico do Python, que executa a sincronização padrão de arquivos e também outras operações de Python, como:

* Gerenciamento automático do ambiente virtual
* Instalação de pacotes listados em requirements.txt usando o pip
* Criação do web.config apropriado com base na versão do Python selecionada.
* Coletar arquivos estáticos para aplicativos Django

Você pode controlar determinados aspectos das etapas de implantação padrão sem precisar personalizar o script.

Se você quiser ignorar todas as etapas de implantação específica de Python, você pode criar esse arquivo vazio:

    \.skipPythonDeployment

Para obter mais controle sobre a implantação, você pode substituir o script de implantação padrão ao criar os seguintes arquivos:

    \.deployment
    \deploy.cmd

Você pode usar a [interface de linha de comando do Azure][Azure command-line interface] para criar os arquivos.  Use este comando da pasta do projeto:

    azure site deploymentscript --python

Quando esses arquivos não existem, o Azure cria um script de implantação temporária e o executa.  Ele é idêntico àquele que você cria com o comando acima.

[Azure command-line interface]: http://azure.microsoft.com/downloads/

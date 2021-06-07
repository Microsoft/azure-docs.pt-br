---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102619994"
---
## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para a configuração.  Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Git](https://git-scm.com/).
* [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) e [pip](https://docs.python.org/3/installing/index.html). Do Python 3.4 em diante, o pip é incluído por padrão com os instaladores de binários do Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* O [módulo de solicitações](https://pypi.org/project/requests/2.7.0/).
* Um IDE como o [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Configurar as credenciais de autenticação

Crie um arquivo chamado **.env** e cole os nomes e os valores a seguir nele. Forneça os valores especificados ao criar o recurso de Leitura Avançada.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Não faça commit desse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser disponibilizados publicamente.

Proteja o ponto de extremidade da API **getimmersivereadertoken** com alguma forma de autenticação, por exemplo, [OAuth](https://oauth.net/2/). A autenticação impede que usuários não autorizados obtenham tokens para usar em seu serviço de Leitura Avançada e cobrança. Esse trabalho não está no escopo deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Criar um aplicativo Web do Python em Windows

Crie um aplicativo Web do Python usando `flask` no Windows.

Instale o [Git](https://git-scm.com/).

Depois que o Git tiver sido instalado, abra um prompt de comando e clone o repositório Git do SDK de Leitura Avançada em uma pasta no computador.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

Marque a caixa de seleção **Adicionar Python ao PATH**.

![Caixa de diálogo de instalação do Python Windows etapa 1](../../media/pythoninstallone.jpg)

Adicione **Recursos Opcionais** marcando as caixas de seleção e selecione **Avançar**.

![Caixa de diálogo de instalação do Python Windows etapa 2](../../media/pythoninstalltwo.jpg)

Selecione **Instalação personalizada** e defina o caminho de instalação como sua pasta raiz, por exemplo, `C:\Python37-32\`. Em seguida, escolha **Instalar**.

![Caixa de diálogo de instalação do Python Windows etapa 3](../../media/pythoninstallthree.jpg)

Após a conclusão da instalação do Python, abra um prompt de comando e use `cd` para acessar a pasta Scripts do Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Flask.

```cmd
pip install flask
```

Instale o Jinja2. É um mecanismo de modelo com recursos completos para Python.

```cmd
pip install jinja2
```

Instale o virtualenv. Essa ferramenta cria ambientes do Python isolados.

```cmd
pip install virtualenv
```

Instalar virtualenvwrapper-win. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP Licenciada do Apache2 escrita em Python.

```cmd
pip install requests
```

Instale o módulo python-dotenv. Este módulo lê o par chave-valor do arquivo .env e os adiciona à variável de ambiente.

```cmd
pip install python-dotenv
```

Crie um ambiente virtual.

```cmd
mkvirtualenv advanced-python
```

Use `cd` para acessar a pasta raiz do projeto de exemplo.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Conecte o projeto de exemplo com o ambiente. Essa ação mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```cmd
setprojectdir .
```

Ative o ambiente virtual.

```cmd
activate
```

O projeto agora deve estar ativo, e você verá algo como `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` no prompt de comando.

Desative o ambiente.

```cmd
deactivate
```

O prefixo `(advanced-python)` deve ter sido removido, pois o ambiente está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo digitando `flask run` na pasta raiz do projeto de exemplo.

```cmd
flask run
```

Abra o navegador e acesse http://localhost:5000.

## <a name="create-a-python-web-app-on-osx"></a>Criar um aplicativo Web do Python em OSX

Crie um aplicativo Web do Python usando `flask` no OSX.

Instale o [Git](https://git-scm.com/).

Depois que o Git tiver sido instalado, abra o terminal e clone o repositório Git do SDK de Leitura Avançada em uma pasta no computador.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

A pasta raiz do Python, por exemplo, `Python37-32`, agora deve estar na pasta Aplicativos.

Após a conclusão da instalação do Python, abra o Terminal e use `cd` para acessar a pasta Scripts do Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Execute o código a seguir para instalar o pip para o usuário conectado no momento para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira a senha quando solicitado.
- Adicione o caminho da instalação do pip à variável PATH.
- Vá para a parte inferior do arquivo e insira o caminho que você quer adicionar como o último item da lista, por exemplo, `PATH=$PATH:/usr/local/bin`.
- Selecione **CTRL + X** para sair.
- Insira **Y** para salvar o buffer modificado.
- É isso! Para testá-lo, em uma nova janela do Terminal, digite `echo $PATH`.

Instale o Flask.

```bash
pip install flask --user
```

Instale o Jinja2. É um mecanismo de modelo com recursos completos para Python.

```bash
pip install Jinja2 --user
```

Instale o virtualenv. Essa ferramenta cria ambientes do Python isolados.

```bash
pip install virtualenv --user
```

Instale o virtualenvwrapper. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP Licenciada do Apache2 escrita em Python.

```bash
pip install requests --user
```

Instale o módulo python-dotenv. Este módulo lê o par chave-valor do arquivo .env e os adiciona à variável de ambiente.

```bash
pip install python-dotenv --user
```

Escolha uma pasta na qual você gostaria de manter seus ambientes virtuais e execute este comando:

```bash
mkdir ~/.virtualenvs
```

Use `cd` para acessar a pasta do aplicativo de exemplo Python do SDK de Leitura Avançada.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Crie um ambiente virtual.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Conecte o projeto de exemplo com o ambiente. Essa ação mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```bash
setprojectdir .
```

Ative o ambiente virtual.

```bash
activate
```

O projeto agora deve estar ativo, e você verá algo como `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` no prompt de comando.

Desative o ambiente.

```bash
deactivate
```

O prefixo `(advanced-python)` deve ter sido removido, pois o ambiente está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo digitando `flask run` na pasta raiz do projeto de exemplo.

```bash
flask run
```

Abra o navegador e acesse http://localhost:5000.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [referência de SDK da Leitura Avançada](../../reference.md).
* Veja exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).

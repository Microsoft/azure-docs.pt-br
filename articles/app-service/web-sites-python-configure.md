---
title: "Configurando o Python com Aplicativos Web do Serviço de Aplicativo do Azure"
description: "Este tutorial descreve opções para criar e configurar um servidor Web básico compatível com aplicativos de Python Gateway Interface (WSGI) nos Aplicativos Web do Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: 08b07053dd1507bcf0564f3071642f66f06b3559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurando o Python com Aplicativos Web do Serviço de Aplicativo do Azure
Este tutorial descreve as opções para criação e configuração de uma Web Server Gateway Interface (WSGI) básica compatível com aplicativos Python nos [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

Ele descreve os recursos adicionais de implantação do Git, como o ambiente virtual e a instalação do pacote usando requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django ou Flask?
O Azure Marketplace contém modelos para as estruturas Bottle, Django e Flask. Se estiver desenvolvendo seu primeiro aplicativo Web no Serviço de Aplicativo do Azure, crie um rapidamente no portal do Azure:

* [Criando aplicativos Web com Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Criando aplicativos Web com Django](https://portal.azure.com/#create/PTVS.Django)
* [Criando aplicativos Web com Flask](https://portal.azure.com/#create/PTVS.Flask)

## <a name="web-app-creation-on-azure-portal"></a>Criação de aplicativos Web no Portal do Azure
Este tutorial assume uma assinatura existente do Azure e acesso ao Portal do Azure.

Se não tiver um aplicativo Web já existente, você poderá criar um no [Portal do Azure](https://portal.azure.com).  Clique no botão NOVO no canto superior esquerdo e clique em **Web + Celular** > **aplicativo Web**.

## <a name="git-publishing"></a>Publicação Git
Configure a publicação de Git para seu aplicativo Web recém-criado seguindo as instruções em [Implantação de GIT local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md). Este tutorial usa Git para criar, gerenciar e publicar nosso aplicativo Web Python para o Serviço de Aplicativo do Azure.

Depois que a publicação Git estiver configurada, um repositório Git será criado e associado ao seu aplicativo Web. A URL do repositório será exibida e poderá ser usada para enviar por push dados do ambiente de desenvolvimento local para a nuvem. Para publicar aplicativos via Git, certifique-se de que um cliente Git também esteja instalado e use as instruções fornecidas para enviar o conteúdo do seu aplicativo Web para o Serviço de Aplicativo do Azure.

## <a name="application-overview"></a>Visão geral do aplicativo
Nas próximas seções, os arquivos a seguir são criados. Eles devem ser colocados na raiz do repositório Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Manipulador WSGI
WSGI é um padrão de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definindo uma interface entre o servidor web e o Python. Ele fornece uma interface padronizada para escrever vários aplicativos da web e estruturas usando o Python. As estruturas web populares Python de hoje usam a WSGI. Os Aplicativos Web do Serviço de Aplicativo do Azure oferecem suporte para essas estruturas; além disso, os usuários avançados podem até mesmo criar seus próprios, desde que o manipulador personalizado siga as diretrizes de especificação WSGI.

Aqui está um exemplo de um `app.py` que define um manipulador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Você pode executar esse aplicativo localmente com `python app.py` e, em seguida, navegar até `http://localhost:5555` no seu navegador da Web.

## <a name="virtual-environment"></a>Ambiente virtual
Embora o aplicativo de exemplo acima não exija pacotes externos, é provável que seu aplicativo exija alguns.

Para ajudar a gerenciar as dependências do pacote externo, a implantação do Git do Azure dá suporte à criação de ambientes virtuais.

Quando o Azure detecta um arquivo requirements.txt na raiz do repositório, ele cria automaticamente um ambiente virtual chamado `env`. Isso ocorre apenas na primeira implantação ou durante qualquer implantação depois que o tempo de execução do Python selecionado é alterado.

Você provavelmente desejará criar um ambiente virtual localmente para desenvolvimento, mas não o inclua em seu repositório Git.

## <a name="package-management"></a>Gerenciamento de pacote
Os pacotes listados no arquivo requirements.txt serão instalados automaticamente no ambiente virtual usando pip. Isso ocorre em todas as implantações, mas pip ignorará a instalação se um pacote já estiver instalado.

Exemplo `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versão do Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemplo `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Você precisará criar um arquivo web.config para especificar como o servidor deve manipular solicitações.

Observe que, se você tiver um arquivo web.x.y.config no repositório, em que x.y corresponde ao tempo de execução do Python selecionado, o Azure copiará automaticamente o arquivo apropriado como web.config.

Os exemplos de web.config a seguir contam com um script de proxy do ambiente virtual, que é descrito na próxima seção.  Eles funcionam com o manipulador WSGI usado no exemplo `app.py` acima.

Exemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Arquivos estáticos são manipulados pelo servidor Web diretamente, sem passar pelo código do Python, para melhorar o desempenho.

Nos exemplos acima, o local dos arquivos estáticos no disco deve corresponder ao local na URL. Isso significa que uma solicitação para `http://pythonapp.azurewebsites.net/static/site.css` fornecerá o arquivo no disco em `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` é onde você especifica o manipulador WSGI. Nos exemplos acima, é `app.wsgi_app` porque o manipulador é uma função chamada `wsgi_app` em `app.py` na pasta raiz.

`PYTHONPATH` pode ser personalizado, mas se você instalar todas as suas dependências no ambiente virtual especificando-as em requirements.txt, não deverá precisar alterá-lo.

## <a name="virtual-environment-proxy"></a>Proxy de ambiente virtual
O script a seguir é usado para recuperar o manipulador WSGI, ativar o ambiente virtual e registrar os erros em log. Foi projetado para ser genérico e ser usado sem modificações.

Conteúdo de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personalizar a implantação do Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Solução de problemas - Instalação de pacotes
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Solução de problemas - Ambiente virtual
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira o [Centro de Desenvolvedores do Python](/develop/python/).

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


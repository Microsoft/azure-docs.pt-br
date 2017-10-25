---
title: Aplicativo web de Django em uma VM do Azure do Windows Server | Microsoft Docs
description: "Este tutorial ensina como hospedar um site da web baseado em Django no Azure usando uma VM do Windows Server 2012 R2 Datacenter com o modelo clássico de implantação."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 283a296fb39863c2801be1093cc4f56904786abd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Aplicativo web Django Olá, Mundo em uma VM do Windows Server

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e o modelo de implantação clássico](../../../resource-manager-deployment-model.md). Este artigo descreve o modelo de implantação clássico. Recomendamos que a maioria das novas implantações use o modelo do Resource Manager.

Este tutorial mostra como hospedar um site baseado em Django no Windows Server em Máquinas Virtuais do Azure. No tutorial, supomos que não há nenhuma experiência anterior com o Azure. Quando concluir o tutorial, você pode ter um aplicativo baseado em Django funcionando na nuvem.

Saiba como:

* Configure uma máquina virtual Azure para hospedar o Django. Embora este tutorial explique como fazer isso para **Windows Server**, você pode fazer o mesmo para uma VM do Linux hospedada no Azure.
* Criar um novo aplicativo Django no Windows.

O tutorial mostra como criar um aplicativo Web Olá, Mundo básico. O aplicativo é hospedado em uma máquina virtual do Azure.

A captura de tela a seguir mostra o aplicativo concluído:

![Uma janela do navegador exibe a página Olá, Mundo no Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual do Azure para hospedar o Django

1. Para criar uma máquina virtual do Azure com a distribuição Windows Server 2012 R2, consulte [Criar uma máquina virtual que executa Windows no portal do Azure](tutorial.md).
2. Configure o Azure para direcionar o tráfego da porta 80 da web para a porta 80 na máquina virtual:
   
   1. No portal do Azure, vá para o painel e selecione sua máquina virtual recém-criada.
   2. Clique em **Pontos de Extremidade** e depois em **Adicionar**.

     ![Adicionar um ponto de extremidade](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Na página **Adicionar ponto de extremidade**, para **Nome**, digite **HTTP**. Defina as portas TCP pública e privada para **80**.

     ![Insira o nome e defina as portas pública e privada](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Clique em **OK**.
     
3. No painel, selecione a sua VM. Para usar o Protocolo da Área de Trabalho Remota (RDP) para fazer logon remotamente na máquina virtual recém-criada do Azure, clique em **Conectar**.  

> [!IMPORTANT] 
> As instruções a seguir pressupõem que você conectou-se à máquina virtual corretamente. Elas também supõem que você esteja emitindo comandos na máquina virtual e não no computador local.

## <a id="setup"> </a>Instalar Python, Django e WFastCGI
> [!NOTE]
> Para baixar usando o Internet Explorer, talvez você precise configurar as definições de **Configuração de Segurança Aprimorada** do Internet Explorer. Para fazer isso, clique em **Iniciar** > **Ferramentas Administrativas** > **Gerenciador do Servidor** > **Servidor Local**. Clique em **Configuração de Segurança Aprimorada do IE**e, em seguida, selecione **Desativada**.

1. Instalar as versões mais recentes do Python 2.7 ou Python 3.4 a partir do [python.org][python.org].
2. Instale os pacotes wfastcgi e django usando pip.
   
    No Python 2.7, use o comando a seguir:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    No Python 3.4, use o comando a seguir:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Instale o IIS com o FastCGI
* Instale os Serviços de Informações da Internet (IIS) com suporte do FastCGI. Isso pode levar alguns minutos.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Criar um novo aplicativo Django
1. Em C:\inetpub\wwwroot, digite o seguinte comando para criar um novo projeto de Django:
   
   No Python 2.7, use o comando a seguir:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   No Python 3.4, use o comando a seguir:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![O resultado do comando New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. O comando `django-admin` gera uma estrutura básica para sites da web baseados em Django:
   
   * `helloworld\manage.py` ajuda a iniciar a hospedagem e parar a hospedagem do seu site da web baseado em Django.
   * `helloworld\helloworld\settings.py` tem configurações do Django para seu aplicativo.
   * `helloworld\helloworld\urls.py` tem o código de mapeamento entre cada URL e sua exibição.
3. No diretório C:\inetpub\wwwroot\helloworld\helloworld, crie um novo arquivo chamado views.py. Esse arquivo contém a exibição que renderiza a página “Olá, Mundo”. Em seu editor de código, digite os seguintes comandos:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Substitua o conteúdo do arquivo urls.py pelos seguintes comandos:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Configurar IIS
1. Desbloqueie a seção de manipuladores no arquivo applicationhost.config global.  Isso permite que o arquivo web.config use o manipulador de Python. Adicione este comando:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Ative o WFastCGI. Isso adiciona um aplicativo ao applicationhost.config global que se refere ao seu interpretador de Python executável e ao script wfastcgi.py.
   
    No Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    No Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. Crie um arquivo web.config em C:\inetpub\wwwroot\helloworld. O valor do atributo `scriptProcessor` deve corresponder à saída da etapa anterior. Para obter mais informações sobre a configuração de wfastcgi, consulte [pypi wfastcgi][wfastcgi].
   
   No Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   No Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Atualize o local do site da Web padrão IIS para apontar para a pasta do projeto Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Carregue a página da Web no seu navegador.

![Uma janela do navegador exibe a página Olá, Mundo no Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Desligar a máquina virtual do Azure
Quando você concluir este tutorial, é recomendável desligar ou remover a VM do Azure que você criou para o tutorial. Isso libera os recursos para outros tutoriais e você pode evitar incorrer em encargos de uso do Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

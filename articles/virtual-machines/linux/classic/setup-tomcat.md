---
title: "Configurar Apache Tomcat em uma máquina virtual Linux | Microsoft Docs"
description: "Saiba como configurar o Apache Tomcat7 usando Máquinas Virtuais do Azure que executam o Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: fa30c78a5a5d458ba8845c3c10b87538427786c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Configurar o Tomcat7 em uma máquina virtual Linux com o Azure
O Apache Tomcat (ou simplesmente Tomcat, também chamado anteriormente de Jacarta Tomcat) é um servidor Web de software livre e um contêiner de servlet desenvolvidos pela Apache Software Foundation (ASF). O Tomcat implementa o Java Servlet e especificações de JSP (JavaServer Pages) da Sun Microsystems. O Tomcat fornece um ambiente de servidor Web puramente Java HTTP no qual o código Java será executado. Na configuração mais simples, o Tomcat é executado em um único processo do sistema operacional. Esse processo é executado em uma máquina virtual Java (JVM). Todas as solicitações HTTP de um navegador para o Tomcat são processadas como um thread separado do processo do Tomcat.  

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda como usar o modelo de implantação clássico. Recomendamos que a maioria das novas implantações use o modelo do Resource Manager. Para usar um modelo do Resource Manager para implantar uma VM Ubuntu com Open JDK e Tomcat, veja [este artigo](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).

Neste artigo, você instalará o Tomcat7 em uma imagem do Linux e a implantará no Azure.  

Você aprenderá:  

* Como criar uma máquina virtual no Azure.
* Como preparar a máquina virtual para o Tomcat7.
* Como instalar o Tomcat7.

Supomos que você já tem uma assinatura do Azure.  Se ainda não tiver uma, você poderá se inscrever para uma avaliação gratuita no [site do Azure](https://azure.microsoft.com/). Se você tiver uma assinatura do MSDN, confira [Preço especial do Microsoft Azure: benefícios do MSDN, MPN e BizSpark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre o Azure, confira [o que é o Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Este artigo pressupõe que você tenha conhecimento prático básico do Tomcat e Linux.  

## <a name="phase-1-create-an-image"></a>Fase 1: Criar uma imagem
Nesta fase, você criará uma máquina virtual usando uma imagem do Linux no Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Etapa 1: Gerar uma chave de autenticação SSH
O SSH é uma ferramenta importante para os administradores do sistema. No entanto, não é recomendado configurar a segurança de acesso com base em uma senha determinada por humanos. Usuários mal-intencionados podem entrar em seu sistema usando um nome de usuário e uma senha fraca.

A boa notícia é que há uma maneira de deixar o acesso remoto aberto e não precisar se preocupar com as senhas. O método consiste na autenticação com criptografia assimétrica. A chave privada do usuário é a que concede a autenticação. Você ainda pode bloquear a conta do usuário para não permitir a autenticação de senha.

Outra vantagem desse método é que você não precisa de senhas diferentes para entrar em servidores diferentes. Você pode autenticar usando a chave privada pessoal em todos os servidores, fazendo com que você não precise se lembrar de várias senhas.



Siga estas etapas para gerar a chave de autenticação SSH.

1. Baixe e instale o PuTTYgen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.htm](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Execute o Puttygen.exe.
3. Clique em **Gerar** para gerar as chaves. No processo, você pode aumentar a aleatoriedade movendo o mouse sobre a área em branco na janela.  
   ![Captura de tela do PuTTY Key Generator que mostra o novo botão para gerar chave][1]
4. Após o processo de geração, Puttygen.exe mostrará sua nova chave pública.  
   ![Captura de tela do PuTTY Key Generator que mostra a nova chave pública e o botão para salvar chave privada][2]
5. Selecione e copie a chave pública e salve-a em um arquivo chamado publicKey.pem. Não clique em **Salvar chave pública**, porque o formato de arquivo da chave pública salva é diferente da chave pública que queremos.
6. Clique em **Salvar chave privada** e salve-a em um arquivo chamado privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Etapa 2: Criar a imagem no Portal do Azure
1. No [Portal](https://portal.azure.com/), clique em **Novo** na barra de tarefas para criar uma imagem. Em seguida, escolha a imagem do Linux conforme suas necessidades. O exemplo a seguir usa a imagem do Ubuntu 14.04.
![Captura de tela do portal que mostra o botão Novo][3]

2. Para o **Nome do Host**, especifique o nome para a URL que você e os clientes da Internet usarão para acessar esta máquina virtual. Defina a última parte do nome DNS, por exemplo, tomcatdemo. O Azure gerará a URL como tomcatdemo.cloudapp.net.  

3. Para a **Chave de Autenticação SSH**, copie o valor da chave do arquivo publicKey.pem, que contém a chave pública gerada pelo PuTTYgen.  
![Caixa da Chave de Autenticação SSH no portal][4]

4. Defina as outras configurações conforme necessário e, em seguida, clique em **Criar**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparar sua máquina virtual para o Tomcat7
Nesta fase, você configurará um ponto de extremidade para o tráfego do Tomcat e, em seguida, se conectará à nova máquina virtual.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Etapa 1: Abrir a porta HTTP para permitir o acesso via Web
Os pontos de extremidade no Azure são compostos por um protocolo TCP ou UDP juntamente com uma porta pública e privada. A porta privada é aquela que o serviço está escutando na máquina virtual. A porta pública é aquela em que o serviço de nuvem do Azure escuta tráfego de entrada baseado na Internet.  

A porta 8080 TCP é o número da porta padrão que o Tomcat escuta. Se esta porta for aberta com um Ponto de Extremidade do Azure, você e outros clientes da Internet poderão acessar páginas do Tomcat.  

1. No portal, clique em **Procurar** > **Máquinas Virtuais** e clique na máquina virtual que você criou.  
   ![Captura de tela do diretório de máquinas virtuais][5]
2. Para adicionar um ponto de extremidade à máquina virtual, clique na caixa **Pontos de extremidade** .
   ![Captura de tela que mostra a caixa de Pontos de Extremidade][6]
3. Clique em **Adicionar**.  

   1. Digite um nome para o ponto de extremidade em **Ponto de Extremidade** e digite 80 em **Porta Pública**.  

      Se você defini-la para 80, não será necessário incluir o número da porta na URL usada para acessar o Tomcat. Por exemplo, http://tomcatdemo.cloudapp.net.    

      Se você defini-la para outro valor, tal como 81, será necessário adicionar o número da porta à URL para acessar o Tomcat. Por exemplo, http://tomcatdemo.cloudapp.net:81/.
   2. Digite 8080 em **Porta Privada**. Por padrão, o Tomcat escuta a porta 8080 TCP. Se você alterou a porta de escuta padrão do Tomcat, deverá atualizar a **Porta Privada** para a mesma que a porta de escuta do Tomcat.  
      ![Captura de tela da interface do usuário que mostra o comando Adicionar, a Porta Pública e a Porta Privada][7]
4. Clique em **OK** para adicionar o ponto de extremidade à máquina virtual.

### <a name="step-2-connect-to-the-image-you-created"></a>Etapa 2: Conectar-se à imagem criada
Você pode escolher qualquer ferramenta SSH para se conectar à sua máquina virtual. Neste exemplo, usamos PuTTY.  

1. Obtenha o nome DNS da máquina virtual no portal.
    1. Clique em **Procurar** > **Máquinas Virtuais**.
    2. Selecione o nome da máquina virtual e clique em **Propriedades**.
    3. No bloco **Propriedades**, examine a caixa **Nome de Domínio** para obter o nome DNS.  

2. Obtenha o número da porta para conexões SSH na caixa **SSH**.  
![Captura de tela que mostra o número da porta da conexão SSH][8]

3. Baixe [PuTTY](http://www.putty.org/).  

4. Depois de baixar, clique no arquivo executável Putty.exe. Na configuração PuTTY, configure s opções básicas com o nome do host e o número da porta obtido nas propriedades da sua máquina virtual.   
![Captura de tela que mostra as opções de nome e porta do host de configuração PuTTY][9]

5. No painel esquerdo, clique em **Conexão** > **SSH** > **Autenticar**e, em seguida, clique em **Procurar** para especificar o local do arquivo privateKey.ppk. O arquivo privateKey.ppk contém a chave privada que foi gerada pelo PuTTYgen anteriormente na seção “Fase 1: Criar uma imagem” deste artigo.  
![Captura de tela que mostra a hierarquia do Diretório de conexão e o botão Procurar][10]

6. Clique em **Abrir**. Você pode ser alertado por uma caixa de mensagem. Se você tiver configurado o nome DNS e o número da porta corretamente, clique em **Sim**.
![Captura de tela que mostra a notificação][11]

7. Será solicitado que você insira inserir seu nome de usuário.  
![Captura de tela que mostra onde inserir o nome de usuário][12]

8. Insira o nome de usuário que você usou para criar a máquina virtual na seção “Fase 1: Criar uma imagem” neste artigo. Você verá algo semelhante ao que se segue:  
![Captura de tela que mostra a confirmação de autenticação][13]

## <a name="phase-3-install-software"></a>Fase 3: Instalar o software
Nesta fase, você pode instalar o Java Runtime Environment, Tomcat7 e demais componentes do Tomcat7.  

### <a name="java-runtime-environment"></a>Java runtime environment
O tomcat é escrito em Java. Há dois tipos de JDKs (Kits de desenvolvimento Java), OpenJDK e Oracle JDK. Você pode escolher aquele que preferir.  

> [!NOTE]
> Ambos os JDKs têm código quase idêntico para as classes na API do Java, porém o código para a máquina virtual é diferente. O OpenJDK tende a usar bibliotecas abertas, enquanto o Oracle JDK tende a usar bibliotecas fechadas. O Oracle JDK tem mais classes e alguns bugs corrigidos, sendo também mais estável que o OpenJDK.

#### <a name="install-openjdk"></a>Instalar o OpenJDK  

Use o seguinte comando para baixar o OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Para criar um diretório para conter os arquivos JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extrair os arquivos JDK para diretório /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Instalar o Oracle JDK


Use o seguinte comando para baixar o Oracle JDK do site do Oracle.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Para criar um diretório para conter os arquivos JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extrair os arquivos JDK para diretório /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Para definir o Oracle JDK como a máquina virtual Java padrão:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Confirme se a instalação do Java foi bem-sucedida
Você pode usar um comando semelhante ao seguinte para testar se o Java runtime environment está instalado corretamente:  

    java -version  

Se você instalou o OpenJDK, verá uma mensagem semelhante à seguinte: ![Mensagem de instalação bem-sucedida do OpenJDK][14]

Se você instalou o Oracle JDK, verá uma mensagem semelhante à seguinte: ![Mensagem de instalação bem-sucedida do Oracle JDK][15]

### <a name="install-tomcat7"></a>Instalar o Tomcat7
Use o seguinte comando para instalar o Tomcat7.  

    sudo apt-get install tomcat7  

Se você não estiver usando o Tomcat7, use a variação apropriada desse comando.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Confirme se a instalação do Tomcat7 foi bem-sucedida
Para verificar se o Tomcat7 foi instalado com êxito, navegue para o nome DNS do seu servidor do Tomcat. Neste artigo, a URL de exemplo é http://tomcatexample.cloudapp.net/. Se você encontrar uma mensagem semelhante à seguinte, o Tomcat7 estará instalado corretamente.
![Mensagem de instalação bem-sucedida do Tomcat7][16]

### <a name="install-other-tomcat7-components"></a>Instalar outros componentes do Tomcat7
Há outros componentes opcionais do Tomcat que você pode instalar.  

Use o comando **sudo apt-cache search tomcat7** para ver todos os componentes disponíveis. Use os comandos a seguir para instalar alguns componentes úteis.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fase 4: Configurar o Tomcat7
Nesta fase, você administrará o Tomcat.

### <a name="start-and-stop-tomcat7"></a>Iniciar e parar o Tomcat7
O servidor do Tomcat7 será iniciado automaticamente quando você o instala. Você também pode iniciá-lo com o seguinte comando:   

    sudo /etc/init.d/tomcat7 start

Para interromper o Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Para exibir o status do Tomcat7:

    sudo /etc/init.d/tomcat7 status

Para reiniciar os serviços do Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administração do Tomcat7
Você pode editar o arquivo de configuração de usuário do Tomcat para configurar as credenciais de administrador. Use o seguinte comando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Aqui está um exemplo:  
![Captura de tela que mostra a saída do comando sudo vi][17]  

> [!NOTE]
> Crie uma senha forte para o nome de usuário do administrador.  

Depois de editar esse arquivo, você deverá reiniciar os serviços do Tomcat7 com o comando a seguir para garantir que as alterações entrem em vigor:  

    sudo /etc/init.d/tomcat7 restart  

Abra o navegador e digite **http://<your tomcat server DNS name>/manager/html** como a URL. Para o exemplo neste artigo, a URL é http://tomcatexample.cloudapp.net/manager/html.  

Após conectar, você deverá ver algo semelhante ao seguinte:   
![Captura de tela do Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Problemas comuns
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Não é possível acessar a máquina virtual com o Tomcat e o Moodle por meio da Internet
#### <a name="symptom"></a>Sintoma  
  O Tomcat está sendo executado, mas você não consegue ver a página padrão do Tomcat com seu navegador.
#### <a name="possible-root-cause"></a>Possível causa raiz   

  * A porta de escuta do Tomcat não é a mesma que a porta privada do ponto de extremidade da máquina virtual para o tráfego do Tomcat.  

     Verifique as configurações de ponto de extremidade da porta pública e da porta privada e certifique-se de que a porta privada seja a mesma que a porta de escuta do Tomcat. Consulte a seção “Fase 1: Criar uma imagem” deste artigo para obter instruções sobre como configurar os pontos de extremidade para sua máquina virtual.  

     Para determinar a porta de escuta do Tomcat, abra /etc/httpd/conf/httpd.conf (versão Red Hat) ou /etc/tomcat7/server.xml (versão Debian). Por padrão, a porta de escuta do Tomcat é 8080. Aqui está um exemplo:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Se você estiver usando uma máquina virtual como Debian ou Ubuntu e desejar alterar a porta padrão do Tomcat Listen (por exemplo, 8081), você também deverá abrir a porta para o sistema operacional. Primeiro, abra o perfil:  

        sudo vi /etc/default/tomcat7  

     Remova a marca de comentário da última linha e altere “não” para “sim”.  

        AUTHBIND=yes
  2. O firewall desabilitou a porta de escuta do tomcat.

     Você só pode ver a página padrão do Tomcat do host local. O problema é provavelmente se deve à porta, que o Tomcat escuta, estar bloqueada pelo firewall. Você pode usar a ferramenta w3m para procurar a página da Web. Os seguintes comandos instalar w3m e navegue até a página padrão do Tomcat:  


        sudo yum  install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Solução

  * Se a porta de escuta do Tomcat não for a mesma que a porta provada do ponto de extremidade para tráfego para a máquina virtual, você precisará alterar a porta privada para a mesma porta de escuta do Tomcat.   
  2. Se o problema for causado pelo firewall/iptables, adicione as seguintes linhas a /etc/sysconfig/iptables. A segunda linha é necessária apenas para tráfego https:  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > Verifique se as linhas anteriores estão posicionadas acima de todas as linhas restringiriam o acesso globalmente, tal como a seguinte: -A INPUT -j REJECT --reject-with icmp-host-prohibited



Para recarregar o iptables, execute o seguinte comando:

    service iptables restart

Isso foi testado no CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Permissão negada ao carregar os arquivos do projeto para /var/lib/tomcat7/webapps/
#### <a name="symptom"></a>Sintoma
  Ao usar um cliente SFTP (por exemplo, o FileZilla) para se conectar à máquina virtual e navegar para /var/lib/tomcat7/webapps/ para publicar seu site, você receberá uma mensagem de erro semelhante à seguinte:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Possível causa raiz
  Você não tem permissões para acessar a pasta /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Solução  
  É necessário obter permissão da conta raiz. Você pode alterar a propriedade da pasta raiz para o nome de usuário usado ao provisionar o computador. Aqui está um exemplo com o nome de conta azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Use a opção -R para aplicar as permissões para todos os arquivos dentro de um diretório também.  

  Este comando também funciona para diretórios. A opção -R altera as permissões para todos os arquivos e diretórios dentro do diretório. Aqui está um exemplo:  

     sudo chown -R username:group directory  

  Este comando altera a propriedade (tanto de usuário como de grupo) para todos os arquivos e diretórios dentro do diretório.  

  O comando a seguir altera apenas a permissão do diretório da pasta. Os arquivos e pastas dentro do diretório não são alterados.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png

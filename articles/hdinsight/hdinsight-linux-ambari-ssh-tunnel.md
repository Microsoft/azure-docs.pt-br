---
title: "Usar o túnel SSH para acessar o Azure HDInsight | Microsoft Docs"
description: "Saiba como usar um túnel SSH para navegar com segurança em recursos da Web hospedados em seus nós HDInsight baseados em Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/21/2017
ms.author: larryfr
ms.openlocfilehash: 4b606ea3797d685b9deacf72f1bd31e0ef007f98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usar o Túnel SSH para acessar a interface do usuário do Ambari na Web, JobHistory, NameNode, Oozie, entre outras

Os clusters do HDInsight baseados em Linux oferecem acesso à interface do usuário do Ambari Web pela Internet, mas alguns recursos da interface do usuário não são acessados. Por exemplo, a interface do usuário da Web para outros serviços exibidos por meio do Ambari. Para obter a funcionalidade completa da interface do usuário do Ambari Web, você deverá usar um túnel SSH para o início do cluster.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários menus no Ambari só funcionam por meio de um túnel SSH. Esses menus dependem de sites e serviços em execução em outros tipos de nó, como nós de trabalho. Geralmente, esses sites não são protegidos e, portanto, não é seguro expô-los diretamente na Internet.

As seguintes interfaces do usuário da Web exigem um túnel SSH:

* JobHistory
* NameNode
* Pilhas de Threads
* Interface do usuário da Web do Oozie
* Interface do usuário mestre e de logs do HBase

Se você usar as Ações de Script para personalizar seu cluster, todos os serviços ou utilitários que forem instalados e que expuserem a interface do usuário da Web exigirão um túnel SSH. Por exemplo, se você instalar o Hue usando uma Ação de Script, deverá usar um túnel SSH para acessar a interface do usuário da Web do Hue.

> [!IMPORTANT]
> Se tiver acesso direto ao HDInsight por meio de uma rede virtual, você não precisará usar túneis SSH. Para obter um exemplo de acesso direto ao HDInsight por meio de uma rede virtual, consulte o documento [Conectar HDInsight em sua rede local](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

O [túnel de Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) roteia o tráfego enviado para uma porta em sua estação de trabalho local. O tráfego é roteado por meio de uma conexão SSH para o nó principal do cluster HDInsight. A solicitação é resolvida como se ela tivesse sido originada no nó principal. A resposta é então roteada de volta pelo túnel até a sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador da Web que pode ser configurado para usar um proxy SOCKS5.

    > [!WARNING]
    > O suporte a proxy SOCKS integrado do Windows não dá suporte a SOCKS5 e não funciona com as etapas neste documento. Os navegadores a seguir contam com as configurações de proxy do Windows e não funcionam com as etapas neste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > O Google Chrome também conta com as configurações de proxy do Windows. No entanto, você pode instalar extensões que dão suporte a SOCKS5. Recomendamos o [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Criar um túnel usando o comando SSH

Use o comando a seguir para criar um túnel SSH usando o comando `ssh` . Substitua **USERNAME** por um usuário SSH para seu cluster HDInsight e substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Esse comando cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

* **D 9876**: a porta local que roteará o tráfego pelo túnel.
* **C** : compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.
* **2** : forçar o SSH para tentar somente a versão 2 do protocolo.
* **q** : modo silencioso.
* **T** : desabilitar alocação pseudo-tty, já que estamos apenas encaminhando uma porta.
* **n** – impede a leitura de STDIN, já que estamos apenas encaminhando uma porta.
* **N** : não executar um comando remoto, pois estamos apenas encaminhando uma porta.
* **f** : executar em segundo plano.

Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado para o nó de cabeçalho do cluster.

## <a name="useputty"></a>Criar um túnel usando o PuTTY

O [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) é um cliente SSH gráfico do Windows. Use as etapas a seguir para criar um túnel SSH usando o PuTTY:

1. Abra o PuTTY e insira as informações da sua conexão. Se não estiver familiarizado com o PuTTY, confira a [documentação do PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.

3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH** :
   
   * **Porta de Origem** : a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.

   * **Destino** : o endereço SSH para o cluster HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dinâmico** : habilita roteamento de proxy SOCKS dinâmico.
     
     ![imagem de opções de túnel](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.

5. Quando solicitado, faça logon no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Usar o túnel de seu navegador

> [!IMPORTANT]
> As etapas nesta seção usam o navegador Mozilla FireFox, pois ele fornece as mesmas configurações de proxy em todas as plataformas. Outros navegadores modernos como Google Chrome podem exigir uma extensão como o FoxyProxy para trabalhar com o túnel.

1. Configure o navegador para usar **localhost** e a porta usada ao criar o túnel como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox. Se você tiver usado uma porta diferente da 9876, altere a porta que usou:
   
    ![imagem das configurações do Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Selecionar **DNS Remoto** resolve as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Essa configuração resolve o DNS usando o nó principal do cluster.

2. Verifique se o túnel funciona visitando um site, como [http://www.whatismyip.com/](http://www.whatismyip.com/). O IP retornado deve ser um IP usado pelo data center do Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verifique com a interface do usuário do Ambari Web

Assim que o cluster tiver sido estabelecido, use as etapas a seguir para verificar se você pode acessar as interfaces do usuário da Web do serviço Ambari Web:

1. Em seu navegador, vá para http://headnodehost:8080. O endereço `headnodehost` é enviado pelo túnel para o cluster e resolverá o nó principal Ambari em execução. Quando solicitado, insira o nome de usuário do administrador (admin) e a senha do seu cluster. Talvez a interface do usuário do Ambari Web seja solicitada uma segunda vez. Nesse caso, insira novamente as informações.

   > [!NOTE]
   > Ao usar o endereço http://headnodehost:8080 para se conectar ao cluster, você estará se conectando através do túnel. A comunicação é protegida usando o túnel SSH em vez de HTTPS. Para se conectar pela Internet usando o HTTPS, use https://CLUSTERNAME.azurehdinsight.net, em que **CLUSTERNAME** é o nome do cluster.

2. Na interface do usuário do Ambari na Web, selecione HDFS na lista à esquerda da página.

    ![Imagem com o HDFS selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando as informações do serviço HDFS forem exibidas, selecione **Links Rápidos**. Uma lista de nós de cabeçalho do cluster é exibida. Escolha um dos nós de cabeça e **Interface de Usuário do NameNode**.

    ![Imagem do menu Links Rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Quando você seleciona __Links rápidos__, pode receber um indicador de espera. Essa condição poderá ocorrer se você tiver uma conexão de Internet lenta. Aguarde um minuto ou dois pelos dados a serem recebidos do servidor e experimente a lista novamente.
   >
   > Algumas entradas no menu **Links rápidos** podem ser cortadas pelo lado direito da tela. Nesse caso, expanda o menu usando o mouse e a tecla de seta para a direita para rolar a tela para a direita e ver o restante do menu.

4. Uma página semelhante à seguinte imagem será exibida:

    ![Imagem da interface do usuário do NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Observe a URL da página; ela deve ser semelhante a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Essa URI está usando o nome de domínio totalmente qualificado (FQDN) interno do nó e é acessada apenas ao utilizar um túnel SSH.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar e usar um túnel SSH, veja o documento a seguir para outras maneiras de usar o Ambari:

* [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

Para saber mais sobre como usar o SSH com HDInsight, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).


---
title: "Instalar o RStudio com o R Server no HDInsight – Azure | Microsoft Docs"
description: Como instalar o RStudio com o Servidor R no HDInsight.
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Instalar o RStudio com o Servidor R no HDInsight

Este artigo descreve como instalar a versão de comunidade (gratuita) do [RStudio Server](https://www.rstudio.com/products/rstudio-server/) no nó de borda de um cluster usando um script personalizado. O RStudio Server que fornece um IDE baseado em navegador para uso por clientes remotos e é amplamente usado em Linux. Há vários ambientes de desenvolvimento integrado (IDEs) disponíveis para R hoje em dia, incluindo:

- RTVS ([Ferramentas do R para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx)) da Microsoft 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- [StatET](http://www.walware.de/goto/statet) baseado no Eclipse da Walware.

A vantagem de instalar o RStudio Server no nó de borda de um cluster HDInsight é que ele proporciona uma experiência completa de IDE para o desenvolvimento e a execução de scripts R com o R Server no cluster. Essa configuração pode ser consideravelmente mais produtiva do que o uso padrão do Console R.

> [!NOTE]
> O procedimento descrito neste artigo só será relevante se você não tiver selecionado a instalação da edição de comunidade do RStudio Server ao provisionar o cluster. Se ele tiver sido adicionado durante o provisionamento, acesse-o clicando no bloco **Painéis do R Server** na entrada do Portal do Azure de seu cluster e, em seguida, no bloco **R Studio Server**. 

Se preferir usar a versão Pro licenciada comercialmente do RStudio Server, você deverá seguir as instruções de instalação do [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Se você estiver usando um cluster HDInsight em que o R foi instalado usando [Instalar a Ação de Script R](hdinsight-hadoop-r-scripts-linux.md), as etapas neste documento não funcionarão corretamente, pois elas exigem um R Server no cluster HDInsight.
>
> 

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Azure HDInsight com R Server instalado. Para obter instruções, confira [Introdução ao Servidor R nos clusters HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Um cliente SSH. Para distribuições Linux e Unix ou o Macintosh OS X, o comando `ssh` é fornecido com o sistema operacional. Para o Windows, recomendamos [Cygwin](http://www.redhat.com/services/custom/cygwin/) com a [opção OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalar o RStudio no cluster usando um script personalizado

Siga estas etapas:

1. Identifique o nó de borda do cluster. Para um cluster HDInsight com R Server, esta é a convenção de nomenclatura para o nó principal e o nó de borda:
   * Nó principal `CLUSTERNAME-ssh.azurehdinsight.net`
   * Nó de borda `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH no nó de borda do cluster usando o padrão de nomenclatura fornecido na etapa 1. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Quando você estiver conectado, torne-se um usuário raiz no cluster. Na sessão do SSH, use o comando a seguir:

        sudo su -

4. Baixe o script personalizado para instalar o RStudio. Use o seguinte comando:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Altere as permissões no arquivo de script personalizado e execute o script. Use os seguintes comandos:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se você usou uma senha do SSH ao criar um cluster HDInsight com o R Server, pode ignorar esta etapa e ir para a próxima. Se, em vez disso, você usou uma chave do SSH para criar o cluster, você deve definir uma senha para seu usuário do SSH. Você precisa dessa senha ao se conectar ao RStudio. Execute os seguintes comandos:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Quando a **Senha atual do Kerberos** for solicitada, pressione **ENTER**.  Observe que você deve substituir `USERNAME` por um usuário SSH de seu cluster HDInsight. Se sua senha tiver sido definida com êxito, você deverá ver a seguinte mensagem:

        passwd: password updated successfully

    Saia da sessão do SSH.

8. Crie um túnel SSH para o cluster, mapeando `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` no cluster HDInsight para o computador cliente. Você deve criar um túnel SSH antes de abrir uma nova sessão do navegador.

   * Em um cliente Linux ou um cliente Windows com [Cygwin](http://www.redhat.com/services/custom/cygwin/), abra uma sessão de terminal e use o comando a seguir:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Substitua **USERNAME** por um usuário SSH para seu cluster HDInsight e substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.
       Você também pode usar uma chave SSH em vez de uma senha adicionando `-i id_rsa_key`.        
   * Se estiver usar um cliente do Windows com PuTTY

     1. Abra o PuTTY e insira as informações da sua conexão.
     2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.
     3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH** :

        * **Porta de Origem** : a porta no cliente que você deseja encaminhar. Por exemplo, **8787**.
        * **Destino** – o destino que deve ser mapeado para o computador cliente local. Por exemplo, **localhost:8787**.

            ![Criar um túnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Criar um túnel SSH")

     4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.
     5. Quando solicitado, faça logon no servidor para estabelecer uma sessão SSH e habilitar o túnel.

9. Abra um navegador da Web e digite a URL a seguir com base na porta que você inseriu para o túnel:

        http://localhost:8787/ 

10. Será solicitado que você insira o nome de usuário e a senha do SSH para se conectar ao cluster. Se você usou uma chave SSH ao criar o cluster, deverá inserir a senha criada na etapa 5.

    ![Conectar-se ao Studio R](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Criar um túnel SSH")

11. Para testar se a instalação do RStudio foi bem-sucedida, você pode executar um script de teste que execute trabalhos do MapReduce e do Spark baseados em R no cluster. Para baixar o script de teste a ser executado no RStudio, volte para o console do SSH e insira os comandos a seguir:

    *    Se você criou um cluster Hadoop com R, use este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Se você criou um cluster Spark com R, use este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. No RStudio, você verá o script de teste que você baixou. Clique duas vezes no arquivo para abri-lo, selecione o conteúdo e clique em **Executar**. Você deverá ver a saída no painel **Console**:

   ![Testar a instalação](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Testar a instalação")

Outra opção seria digitar `source(testhdi.r)` ou `source(testhdi_spark.r)` para executar o script.

## <a name="see-also"></a>Consulte também

* [Opções de contexto de computação para o R Server em clusters HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight](hdinsight-hadoop-r-server-storage.md)


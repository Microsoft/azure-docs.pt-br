---
title: Como executar um aplicativo com o Fortanix confidencial Computing Manager
description: Saiba como usar o Fortanix confidencial Computing Manager para converter suas imagens em contêineres
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 41e47e29553b3e4d9cd0e0e8f280b3648f224bf9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832293"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Como executar um aplicativo com o Fortanix confidencial Computing Manager

Comece a executar seu aplicativo na computação confidencial do Azure usando o [Fortanix confidencial Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) e o [Fortanix node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) do [Fortanix](https://www.fortanix.com/).


O Fortanix é um fornecedor de software de terceiros com produtos e serviços criados com base na infraestrutura do Azure. Há outros provedores de terceiros que oferecem serviços de computação confidencial semelhantes no Azure.

> [!Note]
 > Os produtos mencionados neste documento não estão sob o controle da Microsoft. A Microsoft está fornecendo essas informações a você apenas como uma conveniência, e a referência a esses produtos que não são da Microsoft não implica o endosso da Microsoft.



Este tutorial mostra como converter a imagem do aplicativo em uma imagem confidencial protegida por computação. Esse ambiente usa o software [Fortanix](https://www.fortanix.com/) , da plataforma DCsv2-Series máquinas virtuais habilitadas para SGX Intel do Azure. Essa solução orquestra políticas de segurança críticas, como verificação de identidade e controle de acesso a dados.

 Para obter suporte específico a Fortanix, junte-se à [comunidade de margem de atraso Fortanix](https://fortanix.com/community/) e use o canal #enclavemanager.


## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma conta do Fortanix confidencial Computing Manager, [Inscreva-](https://em.fortanix.com/auth/sign-up) se antes de começar.
1. Um registro do [Docker](https://docs.docker.com/) privado para enviar imagens de aplicativo convertidas.
1. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Adicionar um aplicativo ao Fortanix confidencial Computing Manager
1. Entrar no [Fortanix confidencial Computing Manager (Fortanix em)](https://em.fortanix.com)
1. Navegue até a página **contas** e selecione **adicionar conta** para criar uma nova conta.

![Criar uma conta](media/how-to-fortanix-enclave-manager/create-account.png)

1. Depois que sua conta for criada, clique em **selecionar** para selecionar a conta recém-criada. Agora, podemos começar a registrar os nós de computação e criar aplicativos.
1. Selecione o botão **+ aplicativo** para adicionar um aplicativo. Neste exemplo, vamos adicionar um aplicativo do sistema operacional enclave do Flask Server.

1. Selecione o botão **Adicionar** para o aplicativo do sistema operacional enclave.

    ![Adicionar aplicativo](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > Este tutorial aborda a adição apenas de aplicativos do sistema operacional enclave. [Leia mais](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) sobre como colocar aplicativos EDP Rust no Fortanix confidencial Computing Manager.

6. Neste tutorial, usaremos o registro do Docker do Fortanix para o aplicativo de exemplo. Preencha os detalhes das informações a seguir. Use seu registro do Docker privado para manter a imagem de saída.

    - **Nome do aplicativo**: servidor de aplicativos Python
    - **Descrição**: servidor Python Flask
    - **Nome da imagem de entrada**: fortanix/Python-Flask
    - **Nome da imagem de saída**: fortanx-Private/Python-Flask-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Tamanho da memória**: 1 GB
    - **Contagem de threads**: 128

    *Opcional*: execute o aplicativo.
    - **Hub do Docker**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Aplicativo**: fortanix/Python-Flask

        Execute o comando a seguir:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Adicione um certificado. Preencha as informações usando os detalhes abaixo e, em seguida, selecione **Avançar**:
    - **Domínio**: MyApp. domain. dom
    - **Tipo**: certificado emitido pelo gerente de computação confidencial
    - **Caminho da chave**:/appkey.pem
    - **Tipo de chave**: RSA
    - **Caminho do certificado**:/appcert.pem
    - **Tamanho da chave RSA**: 2048 bits


## <a name="create-an-image"></a>Criar uma imagem
Uma imagem de CCM Fortanix é uma versão de software ou de um aplicativo. Cada imagem é associada a um enclave hash (MRENCLAVE).
1. Na página **Adicionar imagem** , insira as **credenciais do registro** para o **nome da imagem de saída**. Essas credenciais são usadas para acessar o registro do Docker privado em que a imagem será enviada por push.

    ![criar imagem](media/how-to-fortanix-enclave-manager/create-image.png)
1. Forneça a marca de imagem e selecione **criar**.

    ![adicionar marca](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Listagem de permissão de domínio e imagem
Um aplicativo cujo domínio é adicionado à lista de permissões receberá um certificado TLS do Fortanix confidencial Computing Manager. Da mesma forma, quando um aplicativo é executado a partir da imagem convertida, ele tentará entrar em contato com o Fortanix confidencial Computing Manager. O aplicativo solicitará um certificado TLS.

Alterne para a guia **tarefas** à esquerda e aprove as solicitações pendentes para permitir o domínio e a imagem.

## <a name="enroll-compute-node-agent-in-azure"></a>Registrar o agente de nó de computação no Azure

### <a name="generate-and-copy-join-token"></a>Gerar e copiar o token de junção
No Fortanix confidencial Computing Manager, você criará um token. Esse token permite que um nó de computação no Azure se autentique. Você precisará fornecer esse token para sua máquina virtual do Azure.
1. No console de gerenciamento, selecione o botão **+ registrar nó** .
1. Selecione **gerar token** para gerar o token de junção. Copie o token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrar nós no Fortanix node Agent no Azure Marketplace

A criação de um agente de nó Fortanix implantará uma máquina virtual, uma interface de rede, uma rede virtual, um grupo de segurança de rede e um endereço IP público em seu grupo de recursos do Azure. Sua assinatura do Azure será cobrada por hora para a máquina virtual. Antes de criar um agente de nó do Fortanix, examine a [página de preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) do Azure para a série DCsv2. Excluir recursos do Azure quando não estiver em uso.

1. Acesse o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) e entre com suas credenciais do Azure.
1. Na barra de pesquisa, digite **Fortanix confidencial computação nó agente**. Selecione o aplicativo que aparece na caixa de pesquisa chamada **agente de nó de computação confidencial Fortanix** para navegar até a Home Page da oferta.
     ![pesquisar no Marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Selecione **obter agora**, preencha suas informações, se necessário, e selecione **continuar**. Você será redirecionado para a portal do Azure.
1. Selecione **criar** para inserir a página de implantação do agente de nó de computação confidencial do Fortanix.
1. Nessa página, você inserirá informações para implantar uma máquina virtual. Especificamente, essa VM é uma DCsv2-Series máquina virtual habilitada para o Intel SGX do Azure com o software do agente de nó do Fortanix instalado. O agente de nó permitirá que a imagem convertida seja executada com segurança em nós de SGX Intel no Azure.  Selecione a **assinatura** e o **grupo de recursos** em que você deseja implantar a máquina virtual e os recursos associados.

    > [!NOTE]
    > Há restrições ao implantar máquinas virtuais DCsv2-Series no Azure. Talvez seja necessário solicitar uma cota para núcleos adicionais. Leia sobre [soluções de computação confidencial em VMs do Azure](./virtual-machine-solutions.md) para obter mais informações.

1. Selecione uma região disponível.
1. Insira um nome para sua máquina virtual no campo **nome do nó** .
1. Insira o nome de usuário e a senha (ou chave SSH) para autenticação na máquina virtual.
1. Deixe o tamanho do disco do sistema operacional padrão como 200 e selecione um tamanho de VM (Standard_DC4s_v2 será suficiente para este tutorial)
1. Cole o token gerado anteriormente no campo de **token de junção** .

     ![implantar recurso](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent1.png)

1. Selecione **Examinar + criar**. Verifique se a validação foi aprovada e, em seguida, selecione **criar**. Depois que todos os recursos são implantados, o nó de computação é agora registrado no Fortanix confidencial Computing Manager.

## <a name="run-the-application-image-on-the-compute-node"></a>Executar a imagem do aplicativo no nó de computação
Execute o aplicativo executando o comando a seguir. Verifique se você alterou o IP do nó, a porta e o nome da imagem convertida como entradas para seu aplicativo específico.

Neste tutorial, o comando a ser executado é:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

onde:
- *52.152.206.164* é o IP do host do agente do nó
- *9092* é a porta que o agente do nó escuta
- *fortanix-Private/Python-Flask-SGX* é o aplicativo convertido que pode ser encontrado na guia imagens sob a coluna **nome da imagem** na tabela **imagens** no portal da Web fortanix confidencial Computing Manager.

## <a name="verify-and-monitor-the-running-application"></a>Verificar e monitorar o aplicativo em execução
1. Voltar para o [Fortanix confidencial Computing Manager](https://em.fortanix.com/console)
1. Verifique se você está trabalhando dentro da **conta** em que você registrou o nó
1. Navegue até o **console de gerenciamento** selecionando o ícone superior no painel de navegação esquerdo.
1. Selecione a guia **aplicativo**
1. Verifique se há um aplicativo em execução com um nó de computação associado


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá excluir o grupo de recursos, a máquina virtual e os recursos associados. A exclusão do grupo de recursos cancelará o registro dos nós associados à imagem convertida.

Selecione o grupo de recursos da máquina virtual e, em seguida, selecione **Excluir**. Confirme o nome do grupo de recursos terminar de excluir os recursos.

Para excluir a conta do Gerenciador de computação confidencial do Fortanix que você criou, acesse a [página contas](https://em.fortanix.com/accounts) no Fortanix confidencial Computing Manager. Passe o mouse sobre a conta que você deseja excluir. Selecione os pontos pretos verticais no canto superior direito e selecione **Excluir conta**.

  ![excluir](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as ferramentas Fortanix para converter a imagem do aplicativo para ser executada sobre uma máquina virtual de computação confidencial. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, confira [Soluções sobre máquinas virtuais](virtual-machine-solutions.md).

Para saber mais sobre as ofertas de computação confidencial do Azure, confira [visão geral da computação confidencial do Azure](overview.md)

 Saiba como concluir tarefas semelhantes usando outras ofertas de terceiros no Azure, como [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) [e o](https://sconedocs.github.io)

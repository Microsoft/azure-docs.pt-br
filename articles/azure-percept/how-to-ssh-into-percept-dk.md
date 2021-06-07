---
title: Conectar-se ao Azure Percept DK por SSH
description: Saiba como entrar em SSH em seu Azure Percept DK com a saída
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721470"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Conectar-se ao Azure Percept DK por SSH

Siga as etapas abaixo para configurar uma conexão SSH para o Azure Percept DK por [meio de OpenSSH ou de](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)saída.

## <a name="prerequisites"></a>Pré-requisitos

- Um computador host baseado em Windows, Linux ou OS X com Wi-Fi recurso
- Um cliente SSH (consulte a próxima seção para obter diretrizes de instalação)
- Um Azure Percept DK (Kit de desenvolvimento)
- Um logon SSH, criado durante a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Instalar seu cliente SSH preferencial

Se o computador host executar o Linux ou o OS X, os serviços SSH serão incluídos nesses sistemas operacionais e poderão ser executados sem um aplicativo cliente separado. Verifique a documentação do produto do sistema operacional para obter mais informações sobre como executar serviços SSH.

Se o computador host executar o Windows, você poderá ter duas opções de cliente SSH para escolher: OpenSSH e de saída.

### <a name="openssh"></a>OpenSSH

O Windows 10 inclui um cliente SSH interno chamado OpenSSH que pode ser executado com um comando simples dentro de um prompt de comando. É recomendável usar o OpenSSH com o Azure Percept se ele estiver disponível para você. Para verificar se o seu computador com Windows tem o OpenSSH instalado, siga estas etapas:

1. Vá para **Iniciar**  ->  **configurações**.

1. Selecione **Aplicativos**.

1. Em **aplicativos & recursos**, selecione **recursos opcionais**.

1. Digite **cliente OpenSSH** na barra de pesquisa de **recursos instalados** . Se o OpenSSH aparecer, o cliente já estará instalado e você poderá passar para a próxima seção. Se você não vir o OpenSSH, clique em **Adicionar um recurso**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Captura de tela de configurações mostrando o status de instalação do OpenSSH.":::

1. Selecione **cliente OpenSSH** e clique em **instalar**. Agora você pode passar para a próxima seção. Se o OpenSSH não estiver disponível para instalação no seu computador, siga as etapas abaixo para instalar a saída, um cliente SSH de terceiros.

### <a name="putty"></a>PuTTY

Se o computador com Windows não incluir o OpenSSH, recomendamos [o uso de](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)uma reutilização. Para baixar e instalar o reemitida, conclua as seguintes etapas:

1. Vá para a [página de download](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)de disparações.

1. Em **arquivos de pacote**, clique no arquivo. msi de 32 bits ou 64 bits para baixar o instalador. Se você não tiver certeza de qual versão escolher, Confira as [perguntas frequentes](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Clique no instalador para iniciar o processo de instalação. Siga os prompts conforme necessário.

1. Parabéns! Você instalou com êxito o cliente SSH de saída.

## <a name="initiate-the-ssh-connection"></a>Iniciar a conexão SSH

1. Ligue o Azure Percept DK.

1. Se o seu kit de desenvolvimento já estiver conectado a uma rede por meio de Ethernet ou Wi-Fi, pule para a próxima etapa. Caso contrário, conecte o computador host diretamente ao ponto de acesso Wi-Fi do kit de desenvolvimento. Como conectar-se a qualquer outra rede Wi-Fi, abra as configurações de rede e Internet no seu computador, clique na seguinte rede e digite a senha de rede quando solicitado:

    - **Nome da rede**: dependendo da versão do sistema operacional do seu kit de desenvolvimento, o nome do ponto de acesso do Wi-Fi é **scz-xxxx** ou **APD-xxxx** (onde "xxxx" são os últimos quatro dígitos do endereço MAC do kit de desenvolvimento)
    - **Senha**: pode ser encontrada no cartão de boas-vindas que acompanha o kit de desenvolvimento

    > [!WARNING]
    > Enquanto estiver conectado ao ponto de acesso Wi-Fi do Azure Percept DK, seu computador host perderá temporariamente a conexão com a Internet. As chamadas de conferência de vídeo ativas, o web streaming ou outras experiências baseadas em rede serão interrompidas.

1. Conclua o processo de conexão SSH de acordo com o cliente SSH.

### <a name="using-openssh"></a>Usando o OpenSSH

1. Abra um prompt de comando (**Iniciar**  ->  **prompt de comando**).

1. Insira o seguinte no prompt de comando:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Se o computador estiver conectado ao ponto de acesso Wi-Fi do kit de desenvolvimento, o endereço IP será 10.1.1.1. Se o seu kit de desenvolvimento estiver conectado pela Ethernet, use o endereço IP local do dispositivo, que você pode obter do roteador ou hub Ethernet. Se o seu kit de desenvolvimento estiver conectado por Wi-Fi, você deverá usar o endereço IP que foi atribuído ao seu kit de desenvolvimento durante a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se o seu kit de desenvolvimento estiver conectado a uma rede Wi-Fi, mas você não souber seu endereço IP, vá para o Azure Percept Studio e [abra o fluxo de vídeo do dispositivo](./how-to-view-video-stream.md). A barra de endereços na guia navegador de fluxo de vídeo mostrará o endereço IP do dispositivo.

1. Insira sua senha SSH quando solicitado.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Captura de tela de abrir logon do prompt de comando SSH.":::

1. Se esta for a primeira vez que se conecta ao seu kit de desenvolvimento por meio do OpenSSH, você também poderá ser solicitado a aceitar a chave do host. Insira **Sim** para aceitar a chave.

1. Parabéns! Você se conectou com êxito ao seu kit de desenvolvimento por SSH.

### <a name="using-putty"></a>Usando o acabamento

1. Abra o PuTTY. Insira o seguinte na janela de **configuração** de saída e clique em **abrir** para SSH em seu kit de desenvolvimento:

    1. Nome do host: [endereço IP]
    1. Porta: 22
    1. Tipo de conexão: SSH

    O **nome do host** é o endereço IP do seu kit de desenvolvimento. Se o computador estiver conectado ao ponto de acesso Wi-Fi do kit de desenvolvimento, o endereço IP será 10.1.1.1. Se o seu kit de desenvolvimento estiver conectado pela Ethernet, use o endereço IP local do dispositivo, que você pode obter do roteador ou hub Ethernet. Se o seu kit de desenvolvimento estiver conectado por Wi-Fi, você deverá usar o endereço IP que foi atribuído ao seu kit de desenvolvimento durante a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se o seu kit de desenvolvimento estiver conectado a uma rede Wi-Fi, mas você não souber seu endereço IP, vá para o Azure Percept Studio e [abra o fluxo de vídeo do dispositivo](./how-to-view-video-stream.md). A barra de endereços na guia navegador de fluxo de vídeo mostrará o endereço IP do dispositivo.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Captura de tela da janela de configuração de saída.":::

1. Um terminal de reemitida será aberto. Quando solicitado, insira seu nome de usuário e senha SSH no terminal.

1. Parabéns! Você se conectou com êxito ao seu kit de desenvolvimento por SSH.

## <a name="next-steps"></a>Próximas etapas

Depois de se conectar ao Percept do Azure DK por meio do SSH, você pode executar uma variedade de tarefas, incluindo [solução de problemas de dispositivo](./troubleshoot-dev-kit.md) e [atualizações USB](./how-to-update-via-usb.md).
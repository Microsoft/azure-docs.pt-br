---
title: Configurar um laboratório para ensinar ciência de dados com notebooks Python e Jupyter | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar ciência de dados usando notebooks Python e Jupyter.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648014"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurar um laboratório para ensinar ciência de dados com notebooks Python e Jupyter
Este artigo descreve como configurar uma VM (máquina virtual) de modelo no Lab Services com as ferramentas necessárias para ensinar aos alunos como usar [notebooks Jupyter](http://jupyter-notebook.readthedocs.io/)e como os alunos podem se conectar a seus notebooks em suas máquinas virtuais (VMS).

O Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código-fonte executável Python em uma única tela chamada notebook. A execução de um notebook resulta em um registro linear de entradas e saídas. Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

## <a name="set-up-the-lab"></a>Configurar o laboratório

### <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa ter acesso a uma assinatura do Azure e uma conta de laboratório. Discuta com o administrador da sua organização para ver se você pode obter acesso a uma assinatura do Azure existente. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Depois de ter uma assinatura do Azure, crie uma nova conta de laboratório no Azure Lab Services seguindo as instruções no tutorial: [Configurar uma conta de laboratório](tutorial-setup-lab-account.md). Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório
Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem do Marketplace | Dentro de sua conta de laboratório, habilite uma das imagens do Azure Marketplace com base em suas necessidades de sistema operacional: <br/><ul><li>Máquina Virtual de Ciência de Dados – Windows Server 2019</li><li>Máquina Virtual de Ciência de Dados – Ubuntu 18, 4</li></ul> |

> [!NOTE]
> Este artigo usa as imagens de máquina virtual de ciência de dados disponíveis no Azure Marketplace porque elas são pré-configuradas com Jupyter Notebook. No entanto, essas imagens também incluem muitas outras ferramentas de desenvolvimento e modelagem para a ciência de dados. Se você não quiser essas ferramentas adicionais e quiser uma configuração leve com apenas notebooks Jupyter, crie uma imagem de VM personalizada. Para obter um exemplo, [instalando o JupyperHub no Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Depois que a imagem personalizada for criada, você poderá carregá-la em uma galeria de imagens compartilhada para usar a imagem dentro de Azure Lab Services. Saiba mais sobre como [usar a Galeria de imagens compartilhadas no Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Configurações do laboratório
Configure o **tamanho da máquina** virtual e as configurações de imagem da **máquina virtual** , conforme mostrado na tabela a seguir ao configurar um laboratório de sala de aula. Para obter instruções sobre como criar um laboratório de sala de aula, consulte [configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ | 
| Tamanho da máquina virtual | <p>O tamanho escolhido aqui depende da carga de trabalho que você deseja executar:</p><ul><li>Pequeno ou médio – bom para uma configuração básica de acesso a blocos de anotações do Jupyter</li><li>GPU pequena (computação) – mais adequada para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aprendizado profundo</li></ul> | 
| Imagem de máquina virtual | <p>Escolha uma das imagens a seguir com base em suas necessidades de sistema operacional:</p><ul><li>[Máquina Virtual de Ciência de Dados – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Máquina Virtual de Ciência de Dados – Ubuntu 18, 4](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Máquina virtual de modelo
Depois de criar um laboratório, uma VM de modelo será criada com base no tamanho e na imagem da máquina virtual que você escolheu. Configure a VM de modelo com tudo o que você deseja fornecer aos seus alunos para essa classe. Para saber mais, consulte [como gerenciar a máquina virtual de modelo](how-to-create-manage-template.md). 

As imagens VM de Ciência de Dados por padrão vêm com muitas ferramentas e estruturas de ciência de dados necessárias para esse tipo de classe. Por exemplo, as imagens incluem:

- [Jupyter notebooks](http://jupyter-notebook.readthedocs.io/): um aplicativo Web que permite que os cientistas de dados tomem dados brutos, executem cálculos e vejam todos os resultados no mesmo ambiente. Ele será executado localmente na VM do modelo.  
- [Visual Studio Code](https://code.visualstudio.com/): um ambiente de desenvolvimento integrado (IDE) que fornece uma experiência interativa sofisticada ao escrever e testar um notebook. Para obter mais informações, consulte [trabalhando com notebooks Jupyter no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Fornecer blocos de anotações para a classe
A próxima tarefa é fornecer aos alunos blocos de anotações que você deseja que eles usem. Para fornecer seus próprios blocos de anotações, você pode salvar blocos de anotações localmente na VM do modelo. 

Se você quiser usar blocos de anotações de exemplo do Azure Machine Learning, consulte [como configurar um ambiente com notebooks Jupyter](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Opcional: habilitar área de trabalho gráfica para Linux 
A imagem **máquina virtual de ciência de dados – Ubuntu** já está provisionada com o X2GO Server e está pronta para aceitar conexões de cliente. Nenhuma etapa adicional é necessária ao configurar a VM do modelo. 

### <a name="publish-the-template-machine"></a>Publicar o computador de modelo
Quando você publicar o modelo, cada aluno registrado em seu laboratório receberá uma cópia da VM de modelo com todas as ferramentas locais e notebooks que você configurou nela.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Como os alunos se conectam aos notebooks Jupyter?
Depois de publicar o modelo, cada aluno terá acesso a uma VM que vem com tudo que você configurou previamente para a classe, incluindo os notebooks Jupyter. As seções a seguir mostram diferentes maneiras para os alunos se conectarem aos notebooks Jupyter. 

### <a name="for-windows-vms"></a>Para VMs do Windows
Se você forneceu aos alunos as VMs do Windows, eles precisam se conectar às VMs e usar os blocos de anotações do Jupyter que estão disponíveis localmente neles. 

Para se conectar a uma VM do Windows, um aluno pode usar uma conexão de área de trabalho remota (RDP). Para obter etapas detalhadas, consulte [como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md). 

Um aluno usando um Mac ou Chromebook pode seguir as instruções dos artigos a seguir para se conectar à VM de ciência de dados do Windows. 

- [Conectar-se a uma VM usando RDP em um Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Conectar-se a uma VM usando RDP em um Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Para VMs do Linux
Se você forneceu aos alunos as VMs do Linux, há várias opções que os alunos podem usar para se conectar aos seus notebooks Jupyter nas VMs:

- Acessar blocos de anotações do Jupyter localmente após a conexão com a VM
    - SSH para a VM para sessões de terminal
     - Conexão X2Go com a VM para sessões gráficas
- Use o túnel SSH para se conectar do computador local do aluno diretamente ao servidor Jupyter na VM. 

As seções a seguir fornecem detalhes sobre essas maneiras de se conectar a notebooks Jupyter. 

#### <a name="ssh-to-virtual-machine"></a>SSH para máquina virtual
Os alunos podem se conectar via SSH a suas VMs Linux a partir de uma sessão de terminal. Para obter etapas detalhadas, consulte [como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md). Se eles estiverem usando um computador cliente Windows, eles precisarão habilitar um cliente SSH baixando a [saída ou](https://www.putty.org/) habilitando o [OpenSSH no Windows](/windows-server/administration/openssh/openssh_install_firstuse) para o ssh a partir do prompt de comando. 

1.  Inicie a VM.
2.  Depois que a VM estiver em execução, clique em **conectar**, que exibirá uma caixa de diálogo que fornece a cadeia de caracteres de comando ssh, que se parecerá com o exemplo a seguir:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Vá para o seu prompt de comando ou terminal e cole esse comando e pressione **Enter**.
4.  Insira a senha para entrar na VM. 

Depois que os alunos estiverem conectados a VMs, eles poderão acessar e executar blocos de anotações do Jupyter localmente.

#### <a name="x2go-to-virtual-machine"></a>X2Go à máquina virtual
A imagem **máquina virtual de ciência de dados – Ubuntu** já está provisionada com o X2GO Server e está pronta para aceitar conexões de cliente. Para se conectar à área de trabalho gráfica do computador Linux, os alunos precisam seguir estas etapas de uso único para configurar o X2Go em seus computadores cliente:

1.  Baixe e instale o [cliente X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) para sua plataforma cliente.
2.  No [portal de Azure Lab Services](https://labs.azure.com), verifique se a VM Linux à qual você deseja se conectar foi iniciada.
3.  Depois que a VM estiver em execução, clique em **conectar**, que exibirá uma caixa de diálogo que fornece a cadeia de caracteres de comando ssh, que se parecerá com o exemplo a seguir:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Depois de ter essas informações, abra o aplicativo cliente X2Go e crie uma nova sessão. 
5.  Preencha os seguintes valores no painel **preferências de sessão** :
    - **Nome da sessão**: pode ser o que você desejar, mas é recomendável usar o nome da sua VM do laboratório.
     - **Host**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Logon**: aluno
     - **Porta SSH**: 12345
     - **Tipo de sessão**: Xfce
6. Selecione **OK**. 

    > [!NOTE]
     > Ao criar uma nova sessão do X2Go, certifique-se de usar a porta SSH, **não** a porta RDP.

Agora, para se conectar à VM, siga estas etapas:    

1.  No cliente X2Go, clique duas vezes na VM à qual você deseja se conectar. 

    ![Cliente do X2Go](./media/class-type-jupyter-notebook/x2go-client.png)
2. Insira a senha para conectar-se à VM. (Talvez você precise conceder permissão X2Go para ignorar o firewall para concluir a conexão.)
3.  Agora você deve ver a interface gráfica do seu VM de Ciência de Dados Ubuntu.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>Túnel SSH para o servidor Jupyter na VM
Alguns alunos podem querer se conectar diretamente do computador local diretamente ao servidor Jupyter dentro de suas VMs. O protocolo SSH permite o encaminhamento de porta entre o computador local e um servidor remoto (em nosso caso, a VM do laboratório do aluno), para que um aplicativo em execução em uma determinada porta no servidor seja **encapsulado** para a porta de mapeamento no computador local. Os alunos devem seguir estas etapas para túnel SSH para o servidor Jupyter em suas VMs de laboratório:

1.  No [portal de Azure Lab Services](https://labs.azure.com), verifique se a VM Linux que você deseja conectar foi iniciada.
2.  Depois que a VM estiver em execução, clique em **conectar**, que exibirá uma caixa de diálogo que fornece a cadeia de caracteres de comando ssh, que se parecerá com a seguinte cadeia de caracteres:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. No computador local, inicie um terminal ou um prompt de comando e copie a cadeia de conexão SSH para ele. Em seguida, adicione `-L 8888:localhost:8888` à cadeia de caracteres de comando, que cria o **túnel** entre as portas. A cadeia de caracteres final deve ser semelhante a:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Pressione **Enter** para executar o comando. 
5.  Quando solicitado, forneça a senha para se conectar à VM do laboratório. 
6.  Quando você estiver conectado à VM, inicie o servidor Jupyter usando este comando: 

    ```bash
     jupyter notebook
     ```
7. A execução do comando fornecerá uma URL no terminal ou no prompt de comando. A URL deve ter a seguinte aparência:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Cole essa URL em um navegador no computador local para se conectar e trabalhar em seu Jupyter Notebook. 

    > [!NOTE]
    > Visual Studio Code também permite uma ótima [experiência de edição de Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support). Você pode seguir as instruções sobre [como se conectar a um servidor Jupyter remoto](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) e usar a mesma URL da etapa anterior para se conectar de vs Code em vez de no navegador. 


## <a name="cost-estimate"></a>Estimativa de custo
Vamos abordar uma possível estimativa de custo para essa classe. Usaremos uma classe de 25 alunos. Há 20 horas de tempo de classe agendada. Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado. O tamanho da VM que escolhemos era uma GPU pequena (computação), que é 139 unidades de laboratório. Se você quiser usar as pequenas (20 unidades de laboratório) ou o tamanho médio (42 unidades de laboratório), poderá substituir a parte da unidade do laboratório na equação abaixo pelo número correto.  

Aqui está um exemplo de uma possível estimativa de custo para esta classe: 25 alunos * (20 horas agendadas + 10 horas de cota) * 139 unidades de laboratório * 0, 1 USD por hora = 1042,5 USD

Para obter mais detalhes sobre os preços, confira [Preço do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Neste artigo, percorremos as etapas para criar um laboratório para uma classe Jupyter notebooks. Você pode usar uma configuração semelhante para outras classes de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
---
title: Conectar-se a uma VM do Linux no Azure Lab Services | Microsoft Docs
description: Saiba como usar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443410"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Conectar-se às máquinas virtuais do Linux em um laboratório de sala de aula do Azure Lab Services
Este artigo mostra como os alunos podem se conectar à VM (máquina virtual) do Linux em um laboratório usando:
- Terminal SSH (protocolo secure shell)
- Área de trabalho remota da GUI (interface gráfica do usuário)

> [!IMPORTANT] 
> O SSH é configurado automaticamente para que os alunos e o instrutor possam fazer o SSH nas VMs do Linux sem nenhuma configuração adicional. No entanto, se os alunos precisarem se conectar usando uma área de trabalho remota da GUI, talvez o instrutor precise fazer uma configuração adicional.  Para obter detalhes, consulte [Habilitar conexão de área de trabalho remota para máquinas virtuais do Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Conectar-se à VM do aluno usando o SSH

1. Quando um aluno entra no portal de Laboratórios diretamente (`https://labs.azure.com`) ou usando um link de registro (`https://labs.azure.com/register/<registrationCode>`), um bloco é exibido para cada laboratório ao qual o aluno tem acesso. 
   
1. No bloco, alterne o estado do botão para iniciar a VM se ela estiver no estado parado. 

2. Selecione **Conectar**. Você verá duas opções para se conectar à VM: **SSH** e **RDP**.

    ![VM do aluno – opções de conexão](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Selecione a opção **SSH** e você verá a caixa de diálogo **Conectar-se à sua máquina virtual**:  

    ![Cadeia de conexão SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Clique no botão **Copiar** ao lado da caixa de texto para copiar as informações de conexão SSH para a área de transferência. 

5. Salve as informações de conexão SSH, como em um bloco de notas, para que você possa usar essas informações de conexão na próxima etapa.

6. De um terminal SSH (como [Putty](https://www.putty.org/)), conecte-se à sua VM.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Conecte-se à VM do aluno usando a área de trabalho remota da GUI
O instrutor pode optar por configurar as VMs para que os alunos também possam se conectar usando uma área de trabalho remota da GUI.  Nesse caso, os alunos precisam descobrir com o instrutor se devem se conectar às VMs usando o aplicativo cliente **Área de Trabalho Remota da Microsoft (RDP)** ou **X2Go**.  Ambos os aplicativos permitem que um aluno se conecte remotamente à VM e exiba a área de trabalho gráfica do Linux em seu computador local.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Conecte-se à VM do aluno usando a Área de Trabalho Remota da Microsoft (RDP)
Os alunos podem usar a Área de Trabalho Remota da Microsoft (RDP) para se conectarem às VMs do Linux depois que o instrutor configurar o laboratório com pacotes RDP e GUI para um ambiente de área de trabalho gráfica do Linux (como MATE, XFCE e assim por diante). Aqui estão as etapas para conectar: 

1. No bloco de sua VM, verifique se a VM está em execução e clique em **Conectar**. Você verá duas opções para se conectar à VM: **SSH** e **RDP**.

    ![VM do aluno – opções de conexão](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Selecione a opção **RDP**.  Quando o arquivo RDP for baixado na sua máquina, salve-o na sua VM.

3. Se você estiver se conectando de um computador Windows, normalmente, o cliente da Área de Trabalho Remota da Microsoft (RDP) já está instalado e configurado.  Como resultado, tudo o que você precisa fazer é clicar no arquivo RDP para abri-lo e iniciar a sessão remota.

    Em vez disso, se você estiver se conectando a partir de um Mac ou Chromebook, consulte as seguintes etapas:
   - [Conectar-se a uma VM usando RDP em um Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Conectar-se a uma VM usando RDP em um Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Conectar-se à VM do aluno usando o X2Go
Os alunos podem usar o X2Go para se conectarem às VMs do Linux depois que o instrutor configurar o laboratório com pacotes X2Go e GUI para um ambiente de área de trabalho gráfica do Linux (como MATE, XFCE e assim por diante).

Os alunos precisam descobrir com o instrutor qual ambiente de área de trabalho gráfica do Linux o instrutor instalou.  Essas informações são necessárias nas próximas etapas para conectar-se usando o cliente X2Go.

1. Instale o [cliente X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) no seu computador local.

1. Siga as instruções na [primeira seção](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) para copiar as informações de conexão SSH para sua VM.  Você precisa dessas informações para conectar-se usando o cliente X2Go.

1. Depois de ter as informações de conexão SSH, abra o cliente X2Go e selecione **Sessão** > **Nova sessão**.
   ![X2Go cria nova sessão](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Insira os valores no painel **Preferências de sessão** com base nas informações de conexão SSH.  Por exemplo, suas informações de conexão serão semelhantes a esta:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Usando este exemplo, os seguintes valores são inseridos:

   - **Nome da sessão** - Especifique um nome, como o nome da sua VM.
   - **Host** - A ID da sua VM; por exemplo, **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Logon** - O nome de usuário da sua VM; por exemplo, **aluno**.
   - **Porta SSH** - A porta exclusiva atribuída à sua VM; por exemplo, **12345**.
   - **Tipo de sessão** - Selecione o ambiente de área de trabalho gráfica do Linux em que o instrutor configurou sua VM.  Você precisa obter essas informações com seu instrutor.

    Por fim, clique em **OK** para criar a sessão.

    ![Preferências de sessão do X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Clique na sua sessão no painel direito.

    ![O X2Go inicia nova sessão](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Se você receber uma mensagem semelhante a esta, selecione **sim** para continuar a inserir sua senha: **A autenticidade do host '[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345' não pode ser estabelecida.  A impressão digital da chave ECDSA é SHA256:00000000000000000000000000000000000000000000. Tem certeza de que deseja continuar se conectando (sim/não)?**

2. Quando solicitado, insira sua senha e clique em **OK**.  Agora você estará conectado remotamente ao ambiente de área de trabalho da GUI da VM.

## <a name="next-steps"></a>Próximas etapas
Para saber como habilitar o recurso de conexão de área de trabalho remota para VMs do Linux em um laboratório de sala de aula, confira [Habilitar a área de trabalho remota para máquinas virtuais do Linux](how-to-enable-remote-desktop-linux.md). 


---
title: Habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services (UI) | Microsoft Docs
description: Saiba como criar uma VM de modelo com várias VMs dentro.  Em outras palavras, habilite a virtualização aninhada em uma VM de modelo no Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647912"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services manualmente

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá cada usuário no laboratório com uma máquina virtual configurada com várias VMs dentro dela.  Para obter mais informações sobre virtualização e Azure Lab Services aninhados, consulte [habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Este artigo aborda como configurar a virtualização aninhada em um computador de modelo no Azure Lab Services usando funções e ferramentas do Windows diretamente.  Há algumas coisas necessárias para habilitar uma classe para usar a virtualização aninhada.  As etapas a seguir descrevem como configurar manualmente um modelo de computador dos serviços de laboratório com o Hyper-V.  As etapas destinam-se ao Windows Server 2016 ou ao Windows Server 2019.  

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Médio (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  Caso contrário, a virtualização aninhada não funcionará.  

## <a name="enable-hyper-v-role"></a>Habilitar função do Hyper-V

As etapas a seguir descrevem as ações necessárias para habilitar o Hyper-V no Windows Server usando Gerenciador do Servidor.  Depois que a instalação for bem-sucedida, o Gerenciador do Hyper-V estará disponível para adicionar, modificar e excluir máquinas virtuais do cliente.

1. No **Gerenciador do servidor**, na página painel, clique em **adicionar funções e recursos**.
2. Na página **Antes de começar** , clique em **Avançar**.
3. Na página **Selecionar tipo de instalação** , mantenha a seleção padrão de instalação baseada em função ou recurso e clique em **Avançar**.
4. Na página **selecionar servidor de destino** , selecione Selecionar um servidor no pool de servidores.   O servidor atual já estará selecionado.  Clique em Avançar.
5. Na página **Selecionar funções do servidor**, selecione **Hyper-V**.  
6. O pop-up **Assistente para adicionar funções e recursos** será exibido.  Selecione **incluir ferramentas de gerenciamento (se aplicável)**.  Clique no botão **Adicionar recursos** .
7. Na página **Selecionar funções de servidor**, clique em **Avançar**.
8. Na **página Selecionar recursos**, clique em **Avançar**.
9. Na página **Hyper-V**, clique em **Avançar**.
10. Na página **criar comutadores virtuais** , aceite os padrões e clique em **Avançar**.
11. Na página **migração de máquina virtual** , aceite os padrões e clique em **Avançar**.
12. Na página **repositórios padrão** , aceite os padrões e clique em **Avançar**.
13. Na página **confirmar seleções de instalação** , selecione **reiniciar o servidor de destino automaticamente, se necessário**.
14. Quando a janela pop-up **Assistente de adição de funções e recursos** for exibida, clique em **Sim**.
15. Clique em **Instalar**.
16. Aguarde a página **progresso da instalação** para indicar que a função do Hyper-V foi concluída.  O computador pode ser reiniciado no meio da instalação.
17. Clique em **fechar**

## <a name="enable-dhcp-role"></a>Habilitar a função DHCP

Todas as máquinas virtuais de cliente Hyper-V criadas, precisam de um endereço IP na rede NAT.  Vamos criar a rede NAT mais tarde.  Uma maneira de atribuir endereços IP é configurar o host, nesse caso, o modelo de máquina virtual do laboratório, como um servidor DHCP.  Abaixo estão as etapas necessárias para habilitar a função DHCP.

1. No **Gerenciador do servidor**, na página **painel** , clique em **adicionar funções e recursos**.
2. Na página **Antes de começar** , clique em **Avançar**.
3. Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em função ou recurso** e em **Avançar**.
4. Na página **selecionar servidor de destino** , selecione o servidor atual no pool de servidores e clique em **Avançar**.
5. Na página **selecionar funções de servidor** , selecione **servidor DHCP**.  
6. O pop-up **Assistente para adicionar funções e recursos** será exibido.  Selecione **incluir ferramentas de gerenciamento (se aplicável)**.  Clique em **Adicionar Recursos**.

    >[!NOTE]
    >Você pode ver um erro de validação informando que nenhum endereço IP estático foi encontrado.  Esse aviso pode ser ignorado para nosso cenário.

7. Na página **Selecionar funções de servidor**, clique em **Avançar**.
8. Na página **Selecionar recursos**, clique em **Avançar**.
9. Na página **servidor DHCP** , clique em **Avançar**.
10. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
11. Aguarde a **página progresso da instalação** para indicar que a função DHCP foi concluída.
12. Clique em Fechar.

## <a name="enable-routing-and-remote-access-role"></a>Habilitar roteamento e função de acesso remoto

1. No **Gerenciador do servidor**, na página **painel** , clique em **adicionar funções e recursos**.
2. Na página **Antes de começar** , clique em **Avançar**.
3. Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em função ou recurso** e em **Avançar**.
4. Na página **selecionar servidor de destino** , selecione o servidor atual no pool de servidores e clique em **Avançar**.
5. Na página **selecionar funções de servidor** , selecione **acesso remoto**. Clique em **OK**.
6. Na página **Selecionar recursos**, clique em **Avançar**.
7. Na página **acesso remoto** , clique em **Avançar**.
8. Na página **serviços de função** , selecione **Roteamento**.
9. O pop-up **Assistente para adicionar funções e recursos** será exibido.  Selecione **incluir ferramentas de gerenciamento (se aplicável)**.  Clique em **Adicionar Recursos**.
10. Clique em **Próximo**.
11. Na página **Função de Servidor Web (IIS)**, clique em **Avançar**.
12. Na página **Selecionar serviços de função**, clique em **Avançar**.
13. Na página **Confirmar seleções de instalação**, clique em **Instalar**.
14. Aguarde a página **progresso da instalação** para indicar que a função de acesso remoto foi concluída.  
15. Clique em **fechar**

## <a name="create-virtual-nat-network"></a>Criar rede NAT virtual

Agora que todas as funções necessárias foram instaladas, é hora de criar a rede NAT.  O processo de criação envolverá a criação de um comutador e a rede NAT.  Uma rede NAT (conversão de endereços de rede) atribui um endereço IP público a um grupo de VMs em uma rede privada para permitir a conectividade com a Internet.  Em nosso caso, o grupo de VMs privadas será as VMs aninhadas.  A rede NAT permitirá que as VMs aninhadas se comuniquem umas com as outras. Um comutador é um dispositivo de rede que manipula o recebimento e o roteamento de tráfego em uma rede.

### <a name="create-a-new-virtual-switch"></a>Criar um novo comutador virtual

1. Abra o **Gerenciador do Hyper-V** nas ferramentas administrativas do Windows.
2. Selecione o servidor atual no menu de navegação à esquerda.
3. Clique em **Gerenciador de comutador virtual...** no menu **ações** no lado direito do **Gerenciador do Hyper-V**.
4. No pop-up **Gerenciador de comutador virtual** , selecione **interno** para o tipo de comutador a ser criado.  Clique em **Criar comutador virtual**.
5. Para o comutador virtual criado recentemente, defina o nome como algo fácil de memorizar.  Para este exemplo, usaremos ' LabServicesSwitch '.  Clique em **OK**.
6. Um novo adaptador de rede será criado.  O nome será semelhante a ' vEthernet (LabServicesSwitch) '.  Para verificar, abra o **painel de controle**, clique em **rede e Internet**, clique em **Exibir status da rede e tarefas**.  À esquerda, clique em **alterar configurações do adaptador**.

### <a name="create-a-nat-network"></a>Criar uma rede NAT

1. Abra a ferramenta de **Roteamento e acesso remoto** nas ferramentas administrativas do Windows.
2. Selecione o servidor local na página de navegação à esquerda.
3. Escolha **ação**  ->  **configurar e habilitar roteamento e acesso remoto**.
4. Quando o **Assistente de configuração do servidor de roteamento e acesso remoto** for exibido, clique em **Avançar**.
5. Na página **configuração** , selecione configuração **de NAT (conversão de endereços de rede)** .  Clique em **Próximo**.

    >[!WARNING]
    >Não escolha a opção "acesso à VPN (rede virtual privada) e NAT".

6. Na página **conexão de Internet NAT** , escolha "Ethernet".  Não escolha a conexão ' vEthernet (LabServicesSwitch) ' que criamos no Gerenciador do Hyper-V. Clique em **Próximo**.
7. Clique em **concluir** na última página do assistente.
8. Quando a caixa de diálogo **iniciar o serviço** for exibida, clique em **Iniciar serviço**.
9. Aguarde até que o serviço seja iniciado.

## <a name="update-network-adapter-settings"></a>Atualizar configurações do adaptador de rede

O adaptador de rede será associado ao IP usado para o IP de gateway padrão para a rede NAT criada anteriormente.  Neste exemplo, criamos um endereço IP de 192.168.0.1 com uma máscara de sub-rede de 255.255.255.0.  Usaremos o comutador virtual criado anteriormente.

1. Abra o **painel de controle**, clique em **rede e Internet**, clique em **Exibir status da rede e tarefas**.
2. À esquerda, clique em **alterar configurações do adaptador**.  
3. Na janela **conexões de rede** , clique duas vezes em ' VEthernet (LabServicesSwitch) ' para mostrar a caixa de diálogo detalhes do **status de vEthernet (LabServicesSwitch)** .
4. Clique no botão **Propriedades** .
5. Selecione o item **protocolo IP versão 4 (TCP/IPv4)** e clique no botão **Propriedades** .
6. Na caixa de diálogo **Propriedades do protocolo de Internet versão 4 (TCP/IPv4)** , selecione **usar o seguinte endereço IP**.  Para o endereço IP, digite 192.168.0.1. Para a máscara de sub-rede, digite 255.255.255.0.  Deixe o gateway padrão em branco.  Deixe os servidores DNS em branco também.

    >[!NOTE]
    > Nosso intervalo para nossa rede NAT será, em notação CIDR, 192.168.0.0/24.  Isso cria um intervalo de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  Por convenção, os gateways têm o primeiro endereço IP em um intervalo de sub-rede.

7. Clique em OK.

## <a name="create-dhcp-scope"></a>Criar Escopo do DHCP

As etapas a seguir são instruções para adicionar o escopo DHCP.  Neste artigo, nossa rede NAT é 192.168.0.0/24 na notação CIDR.  Isso cria um intervalo de endereços IP utilizáveis de 192.168.0.1 a 192.168.0.254.  O escopo criado deve estar nesse intervalo de endereços utilizáveis, excluindo o endereço IP já criado anteriormente.

1. Abra **Ferramentas administrativas** e abra a ferramenta administrativa do **DHCP** .
2. Na ferramenta **DHCP** , expanda o nó do servidor atual e selecione **IPv4**.
3. No menu Ação, escolha **novo escopo...**
4. Quando o **Assistente de novo escopo** for exibido, clique em **Avançar** na página de **boas-vindas** .
5. Na página **nome do escopo** , insira ' LabServicesDhcpScope ' ou algo mais fácil de lembrar para o nome.  Clique em **Próximo**.
6. Na página **intervalo de endereços IP** , insira os valores a seguir.

    - 192.168.0.100 para o endereço IP inicial
    - 192.168.0.200 para o endereço IP final
    - 24 para o comprimento
    - 255.255.255.0 para a máscara de sub-rede

7. Clique em **Próximo**.
8. Na página **adicionar exclusões e atraso** , clique em **Avançar**.
9. Na página **duração da concessão** , clique em **Avançar**.
10. Na página **Configurar opções de DHCP** , selecione **Sim, desejo configurar essas opções agora**. Clique em **Próximo**.
11. No **roteador (gateway padrão)**
12. Adicione 192.168.0.1, se ainda não tiver feito isso. Clique em **Próximo**.
13. Na página **nome de domínio e servidores DNS** , adicione 168.63.129.16 como um endereço IP do servidor DNS, se ainda não tiver feito isso.  168.63.129.16 é o endereço IP para um servidor DNS estático do Azure. Clique em **Próximo**.
14. Na página **servidores WINS** , clique em **Avançar**.
15. Na página **Ativar escopo** , selecione **Sim, desejo ativar esse escopo agora**.  Clique em **Próximo**.
16. Na página **concluindo o assistente de novo escopo** , clique em **concluir**.

## <a name="conclusion"></a>Conclusão

Agora seu computador de modelo está pronto para criar máquinas virtuais do Hyper-V.   Consulte [criar uma máquina virtual no Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais do Hyper-v.  Consulte também o [centro de avaliação da Microsoft](https://www.microsoft.com/evalcenter/) para conferir o software e os sistemas operacionais disponíveis.

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
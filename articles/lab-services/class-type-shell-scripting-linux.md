---
title: Configurar um laboratório de script do shell do Linux com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar script de shell no Linux.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444957"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configurar um laboratório para ensinar o script de shell no Linux
Este artigo mostra como configurar um laboratório para ensinar script de shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts do Bash tradicionais e scripts avançados. Scripts avançados são scripts que combinam comandos do Bash e o Ruby. Essa abordagem permite que o Ruby passe os dados e que os comandos do Bash interajam com o shell. 

Os alunos que fazem essas aulas de script obtêm uma máquina virtual do Linux para aprender os conceitos básicos do Linux e também se familiarizarem com os scripts do shell Bash. A máquina virtual do Linux é fornecida com o acesso à Área de Trabalho Remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você poderá criar uma conta de laboratório no Azure Lab Services ou usar uma existente. Confira o tutorial a seguir para criar uma conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Depois de criar a conta de laboratório, habilite as seguintes configurações nela: 

| Configuração da conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do Marketplace | Habilite a imagem do Ubuntu Server 18.04 LTS para uso em sua conta de laboratório. Para obter mais informações, consulte [Especificar imagens do Marketplace disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um laboratório e aplicar as seguintes configurações:

| Configurações do laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da VM (máquina virtual) | Pequena  |
| Imagem da VM | Ubuntu Server 18.04 LTS|
| Habilitar a conexão de área de trabalho remota | Habilitar. <p>Habilitar essa configuração permitirá que professores e alunos se conectem às VMs deles usando o RDP. Para obter mais informações, consulte [Habilitar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Instalar o desktop e o RDP
A imagem do Ubuntu Server 18.04 LTS não tem o servidor de área de trabalho remota do RDP instalado por padrão. Siga as instruções no artigo [Instalar e configurar a Área de Trabalho Remota para se conectar a uma VM do Linux no Azure](../virtual-machines/linux/use-remote-desktop.md) para instalar os pacotes necessários no modelo de máquina virtual para se conectar via protocolo de área de trabalho remota (RDP).

## <a name="install-ruby"></a>Instalar Ruby
O Ruby é uma linguagem dinâmica open-source que pode ser combinada com scripts bash. Esta seção mostra como usar `apt-get` para instalar a versão mais recente do [Ruby](https://www.ruby-lang.org/).

1. Instale as atualizações executando os seguintes comandos:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale o [Ruby](https://www.ruby-lang.org/).  O Ruby é uma linguagem dinâmica open-source que pode ser combinada com scripts bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento
Esta seção mostra como instalar alguns editores de texto. Gedit é o editor de texto padrão para o ambiente de área de trabalho Gnome. Ele foi projetado como um editor de texto de uso geral. O Visual Studio Code é um editor de texto que inclui suporte para depuração e integração de controle do código-fonte.

> [!NOTE]
> Há vários editores de texto diferentes disponíveis. O Visual Studio Code e o gedit são apenas dois exemplos.

1. Instale o [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale o [Visual Studio Code](https://code.visualstudio.com/).  O Visual Studio Code pode ser instalado usando o Snap Store.  Para obter opções de instalação alternativas, consulte [Downloads alternativos do Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    O modelo agora está atualizado e tem a linguagem de programação e as ferramentas de desenvolvimento necessárias para concluir o laboratório. A imagem de modelo agora pode ser publicada no laboratório. Selecione o botão **Publicar** na página de modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo 
Se desejar estimar o custo desse laboratório, você poderá usar o seguinte exemplo:
 
Para uma classe de 25 alunos com 20 horas de tempo de aula agendada e 10 horas de cota para tarefas ou atividades, o preço do laboratório seria: 

25 alunos * (20 + 10) horas * 20 Unidades de Laboratório * 0,01 USD por hora = 150 USD

Para obter mais informações sobre os preços, consulte o seguinte documento: [Preços do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a classes de script. Embora este artigo se concentre na configuração das ferramentas de script do Ruby na máquina virtual do Linux, a mesma configuração pode ser usada para outras classes de script, como o Python no Linux.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Enviar links de registro por email para alunos](how-to-configure-student-usage.md#send-invitations-to-users). 






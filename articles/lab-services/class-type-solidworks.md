---
title: Configurar um laboratório de SOLIDWORKS para a engenharia com Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para cursos de engenharia usando o SOLIDWORKS.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626097"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Configurar um laboratório para classes de engenharia usando o SOLIDWORKS

O [SolidWorks](https://www.solidworks.com/) fornece um ambiente de CAD (design auxiliado por computador) 3D para modelagem de objetos sólidos e é usado em diferentes tipos de campos de engenharia.  Com o SOLIDWORKS, os engenheiros podem facilmente criar, Visualizar, simular e documentar seus designs.

Uma opção de licenciamento comumente usada por universidades é o licenciamento de rede de SOLIDWORKS.   Com essa opção, os usuários compartilham um pool de licenças que são gerenciadas por um servidor de licenciamento.  Esse tipo de licença às vezes é chamado de licença "flutuante" porque você só precisa ter licenças suficientes para o número de usuários simultâneos.  Quando um usuário é feito usando o SOLIDWORKS, sua licença volta para o pool de licenças gerenciado centralmente para que ele possa ser reutilizado por outro usuário.

Neste artigo, mostraremos como configurar uma classe que usa o SOLIDWORKS 2019 e o licenciamento de rede.

## <a name="license-server"></a>Servidor de licenças

O licenciamento de rede do SOLIDWORKS requer que você tenha o Gerenciador de licenças do SolidNetWork instalado e ativado no servidor de licença.  Esse servidor de licença normalmente está localizado na sua rede local ou em uma rede privada no Azure.  Para obter mais informações sobre como configurar o Gerenciador de licenças do SolidNetWork em seu servidor, consulte [Instalando e ativando um Gerenciador de licenças](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) no guia de instalação do SolidWorks.  Ao configurá-lo, lembre-se do **número da porta** e do [**número de série**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) que são usados, pois eles serão necessários em etapas posteriores.

Depois que o servidor de licença estiver configurado, você precisará emparelhar a [rede virtual (VNet)](./how-to-connect-peer-virtual-network.md) para sua [conta de laboratório](./tutorial-setup-lab-account.md).  O emparelhamento de rede deve ser feito antes da criação do laboratório para que as máquinas virtuais do laboratório possam acessar o servidor de licença e o contrário.

> [!NOTE]
> Você deve verificar se as portas apropriadas estão abertas nos firewalls para permitir a comunicação entre as máquinas virtuais do laboratório e o servidor de licença.  Por exemplo, consulte as instruções sobre como [modificar as portas de computador do Gerenciador de licenças para o Firewall do Windows](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) que mostram como adicionar regras de entrada e saída ao firewall do servidor de licenças.  Talvez você também precise abrir portas para as máquinas virtuais do laboratório.  Siga as etapas no artigo sobre [configurações de firewall para laboratórios](./how-to-configure-firewall-settings.md) para obter mais informações sobre isso, incluindo como obter o endereço IP público do laboratório.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte o tutorial sobre [como configurar uma conta de laboratório](./tutorial-setup-lab-account.md). Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte o artigo sobre [como especificar imagens do Marketplace disponíveis para criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Habilite a imagem do Windows 10 pro para uso em sua conta de laboratório.|

> [!NOTE]
> Além do Windows 10, o SOLIDWORKS dá suporte a outras versões do Windows.  Consulte [SolidWorks System Requirements](https://www.solidworks.com/sw/support/SystemRequirements.html) para obter detalhes.

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula. Para obter mais informações sobre como criar um laboratório de sala de aula, consulte Configurar um tutorial de laboratório de sala de aula.

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| **GPU pequena (visualização)**.  Essa VM é mais adequada para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE]
> O tamanho de máquina virtual de **GPU pequena (visualização)** é configurado para habilitar uma experiência gráfica de alto desempenho.  Para obter mais informações sobre esse tamanho de máquina virtual, consulte o artigo sobre [como configurar um laboratório com GPUs](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Não se esqueça de [emparelhar a rede virtual](./how-to-connect-peer-virtual-network.md) para a conta de laboratório para a rede virtual do servidor de licença **antes** de criar o laboratório.

## <a name="template-virtual-machine-configuration"></a>Configuração de máquina virtual de modelo

As etapas nesta seção mostram como configurar sua máquina virtual de modelo baixando os arquivos de instalação do SOLIDWORKS e instalando o software cliente:

1. Inicie a máquina virtual de modelo e conecte-se ao computador usando o RDP.

1. Baixe os arquivos de instalação do software cliente do SOLIDWORKS. Você tem duas opções para baixar:
   - Baixe do [portal do cliente do SolidWorks](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Baixar de um diretório em um servidor.  Se você usou essa opção, precisará garantir que o servidor esteja acessível a partir da máquina virtual de modelo.  Por exemplo, esse servidor pode estar localizado na mesma rede virtual emparelhada com sua conta de laboratório.
  
    Para obter detalhes, consulte [instalação em computadores individuais no](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) guia de instalação do SOLIDWORKS no SolidWorks.

1. Depois que os arquivos de instalação forem baixados, instale o software cliente usando o Gerenciador de instalação do SOLIDWORKS. Veja detalhes sobre como [instalar um cliente de licença](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) no guia de instalação do SolidWorks.

    > [!NOTE]
    > Na caixa de diálogo **Adicionar servidor** , você será solicitado a fornecer o **número da porta** usada para o servidor de licença e o nome ou endereço IP do servidor de licença.

## <a name="cost"></a>Cost

Vamos abordar uma possível estimativa de custo para essa classe. Essa estimativa não inclui o custo da execução do servidor de licença. Usaremos uma classe de 25 alunos. Há 20 horas de tempo de classe agendada. Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado. O tamanho da máquina virtual que escolhemos era **pequena GPU (visualização)**, que é 160 unidades de laboratório.

25 alunos \* (20 horas agendadas + 10 horas de cota) \* 160 unidades de laboratório * 0, 1 USD por hora = 1200, 0 USD

>[!IMPORTANT]
> A estimativa de custo é apenas para fins de exemplo.  Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
---
title: Configurar um laboratório para ensinar o MATLAB com Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o MATLAB com Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444991"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Configurar um laboratório para ensinar o MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html), que significa o laboratório de matriz, é a plataforma de programação do [MathWorks](https://www.mathworks.com/).  Ele combina a capacidade computacional e a visualização, tornando-a uma ferramenta popular nos campos de matemática, engenharia, física e química.

Se você estiver usando uma [licença de todo o campus](https://www.mathworks.com/academia/tah-support-program/administrators.html), consulte as instruções em [baixar arquivos de instalação do MATLAB](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) para baixar os arquivos do instalador do MATLAB no computador do modelo.  

Neste artigo, mostraremos como configurar uma classe que usa o software cliente do MATLAB com um servidor de licença.

## <a name="license-server"></a>Servidor de licenças

Antes de modificar o computador de modelo para seu laboratório, você precisará configurar o servidor para executar o software [Gerenciador de licenças de rede](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Essas instruções só são aplicáveis a instituições que escolhem a opção de licenciamento de rede para MATLAB, que permite que os usuários compartilhem um pool de chaves de licença.  Você também precisará salvar o arquivo de licença e a chave de instalação do arquivo para mais tarde.  Para obter instruções detalhadas sobre como baixar um arquivo de licença, consulte a primeira etapa no artigo [instalar o Gerenciador de licenças de rede com a conexão com a Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Instruções detalhadas para a cobertura de como instalar um servidor de licenciamento estão disponíveis em [instalar o Gerenciador de licenças de rede com conexão com a Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Para habilitar o empréstimo, consulte o artigo de [licença de empréstimo](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Supondo que o servidor de licença esteja localizado em uma rede local ou em uma rede privada no Azure, não se esqueça de [emparelhar a rede virtual](how-to-connect-peer-virtual-network.md) com sua [conta de laboratório](tutorial-setup-lab-account.md).  O emparelhamento de rede deve ser feito antes da criação do laboratório para que as máquinas virtuais do laboratório possam acessar o servidor de licença.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure para começar.  Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta existente.  Para criar uma nova conta de laboratório, consulte o [tutorial configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Para criar um novo laboratório, siga [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).  Aplique as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Médio | Windows 10 |

O MATLAB tem suporte em mais sistemas operacionais.  Consulte [requisitos do sistema do MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) para obter detalhes.

> [!WARNING]
> Não se esqueça de [emparelhar a rede virtual](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) para a conta de laboratório para a rede virtual do servidor de licença antes de criar o laboratório.

## <a name="template-machine"></a>Computador de modelo

Após a criação do computador de modelo, inicie o computador e conecte-o para concluir as seguintes tarefas principais.

1. Baixe os arquivos de instalação do software cliente do MATLAB.
2. Instale o MATLAB usando a chave de instalação do arquivo.

A instalação do MATLAB será um processo de várias partes.  A primeira parte baixará os arquivos para MATLAB e quaisquer outros produtos que você deseja instalar.  Usar uma chave de instalação de arquivo requer que todos os arquivos de instalação dos produtos a serem instalados sejam previamente baixados.  A segunda parte será instalar o software MATLAB na VM de modelo e ativar o software.  Se a VM de modelo estiver configurada para ativar usando o servidor de licença, as VMs de estudante farão o mesmo.

### <a name="download-installation-files"></a>Baixar arquivos de instalação

Você deve ser um administrador de licenças para baixar os arquivos de instalação, bem como obter o arquivo de licença e a chave de instalação do arquivo.  As etapas para baixar os arquivos de instalação estão abaixo.

1. Faça logon em sua conta do [https://www.mathworks.com](https://www.mathworks.com) .
2. Escolha **minha conta**.
3. Na seção **meu software** da página conta, clique na licença anexada à instalação do Gerenciador de licenças de rede para o laboratório.
4. Na página de detalhes da licença, clique em **baixar produtos**.
5. Aguarde até que o instalador seja extraído automaticamente.
6. Inicie o instalador.  
7. Na página **entrar em sua conta do MathWorks** , insira sua conta do MathWorks.
8. Na página **contrato de licença do MathWorks** , aceite o termo e clique no botão **Avançar** .
9. Clique na lista suspensa **Opções avançadas** e escolha **desejo baixar sem instalar**.
10. Na **pasta selecionar destino**, clique em **Avançar**.
11. Selecione **Windows** como a plataforma do computador em que você vai instalar o MATLAB.
12. Na página **selecionar produto** , verifique se MATLAB está selecionado junto com outros produtos MathWorks que você gostaria de instalar.
13. Na página **confirmar seleções e download** , clique em **Iniciar Download**.  
14. Aguarde até que os produtos selecionados sejam baixados.  Clique em **Concluir**.

Você também pode baixar uma imagem ISO do site do MathWorks.

1. Faça logon em sua conta do [https://www.mathworks.com](https://www.mathworks.com) .
2. Acesse [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Selecione a versão do MATLAB que você deseja instalar.
4. Clique no link "get {version}. ISO Image" abaixo dos links relacionados, em que {Version} é algo como R2020a.
5. Clique no link de **liberação de download** azul para Windows.

### <a name="run-installer"></a>Executar instalador

Depois que os arquivos são baixados, a segunda etapa é executar o instalador. Mais uma vez, você deve ser um administrador de licenças para concluir esta etapa.  Somente os administradores de licença podem instalar o MATLAB com uma chave de instalação de arquivo.

1. Verifique o arquivo de licença baixado e verifique se a linha de servidor lista o servidor de licença corretamente.  Para obter informações sobre como o arquivo de licença deve ser formatado, consulte atualizar os artigos [licença de rede](https://www.mathworks.com/help/install/ug/network-license-files.html), [empréstimo de licença](https://www.mathworks.com/help/install/license/borrow-licenses.html)e [encontrar ID de host](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) .
2. Inicie o instalador do MATLAB.
3. Na página **entrar em sua conta do MathWorks** , insira sua conta do MathWorks.
4. Na página **contrato de licença do MathWorks** , aceite o termo e clique no botão **Avançar** .
5. Clique na lista suspensa **Opções avançadas** e escolha **uma chave de instalação de arquivo**.
6. Na página **instalar usando a chave de instalação de arquivo** , insira a chave de instalação do arquivo para o servidor de licença.   Clique em **Próximo**.
7. Na página **Selecionar arquivo de licença** , navegue até o arquivo de licença salvo ao baixar os arquivos de instalação anteriormente.
8. Na página **Selecionar pasta de destino** , clique em **Avançar**.
9. Na página **selecionar produtos** , clique em **Avançar**.
10. Na página **selecionar opções** , clique em **Avançar**.
11. Na página **confirmar seleções e instalar** , clique em **Iniciar instalação**.
12. Na página **instalação concluída** , verifique se **ativar o MATLAB** está marcado.  Clique em **Concluir**.

## <a name="cost-estimate"></a>Estimativa de custo

Vamos abordar uma possível estimativa de custo para essa classe.  Essa estimativa não inclui o custo da execução do servidor de licença.  Usaremos uma classe de 25 alunos.  Há 20 horas de tempo de classe agendada.  Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado.  O tamanho da máquina virtual que escolhemos era médio, que é 55 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custo para esta classe:

25 alunos \* (20 horas agendadas + 10 horas de cota) \* 55 unidades de laboratório \*  0, 1 USD por hora = 412,50 USD

>[!IMPORTANT]
> A estimativa de custo é apenas para fins de exemplo.  Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar, gerenciar e publicar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)

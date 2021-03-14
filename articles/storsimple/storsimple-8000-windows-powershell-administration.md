---
title: Gerenciamento de dispositivo do Windows PowerShell para StorSimple
description: Saiba como usar o Windows PowerShell para StorSimple para administrar seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 3292f337a39e2adb7dc5e78f12785ead8759f5d1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611939"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Usar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Visão geral

O Windows PowerShell para StorSimple oferece uma interface de linha de comando que você pode usar para gerenciar o seu dispositivo Microsoft Azure StorSimple. Como o nome sugere, é uma interface de linha de comando baseada no Windows PowerShell criada em um espaço de execução restrito. Da perspectiva do usuário na linha de comando, um espaço de execução restrito aparece como uma versão restrita do Windows PowerShell. Ao mesmo tempo em que mantém alguns dos recursos básicos do Windows PowerShell, essa interface tem outros cmdlets dedicados que são voltados para gerenciar seu dispositivo de Microsoft Azure StorSimple.

Este artigo descreve os recursos do Windows PowerShell para StorSimple, incluindo como você pode se conectar a essa interface, e contém links para procedimentos passo a passo de fluxos de trabalho que podem ser executados usando esta interface. Os fluxos de trabalho incluem como registrar seu dispositivo, configurar a interface de rede em seu dispositivo, instalar atualizações que exigem o dispositivo esteja no modo de manutenção, alterar o estado do dispositivo e solucionar quaisquer problemas que possam ocorrer.

Neste artigo, você aprenderá a:

* Conecte-se ao seu dispositivo StorSimple usando o Windows PowerShell para StorSimple.
* Administre o seu dispositivo StorSimple usando o Windows PowerShell para StorSimple.
* Obtenha ajuda no Windows PowerShell para StorSimple.

> [!NOTE]
> * Os cmdlets do Windows PowerShell para StorSimple permitem gerenciar seu dispositivo StorSimple por um console serial ou remotamente por meio de comunicação remota do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser usados nessa interface, vá para [referência de cmdlets do Windows PowerShell para StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps&preserve-view=true).
> * Os cmdlets do Azure PowerShell StorSimple são um conjunto diferente de cmdlets que permite automatizar tarefas de nível de serviço e migração do StorSimple a partir da linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá para [referência do cmdlet do Azure StorSimple](/powershell/module/servicemanagement/azure.service/#azure).


É possível acessar o Windows PowerShell para StorSimple através de um dos seguintes métodos:

* [Conecte-se ao console serial do dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Conectar-se remotamente ao StorSimple usando o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Conectar ao Windows PowerShell para StorSimple por meio do console serial

Você pode [baixar o PuTTY](https://www.putty.org/) ou um software de emulação de terminal similar para se conectar ao Windows PowerShell para StorSimple. Você precisa configurar o PuTTY especificamente para acessar o dispositivo Microsoft Azure StorSimple. Os tópicos a seguir contêm as etapas detalhadas sobre como configurar o PuTTy e conectar-se ao dispositivo. Diversas opções de menu do console serial também são explicadas.

### <a name="putty-settings"></a>Configurações do PuTTY

Certifique-se de usar as seguintes configurações de PuTTY para conectar-se à interface do Windows PowerShell no console serial.

#### <a name="to-configure-putty"></a>Para configurar o PuTTY

1. Na caixa de diálogo **Reconfiguração** do PuTTY, no painel **Categoria**, selecione **Teclado**.
2. Verifique se as seguintes opções (as configurações padrão quando você inicia uma nova sessão) estão selecionadas.
   
   | Item de teclado | Selecionar |
   | --- | --- |
   | Tecla BACKSPACE |Control-? (127) |
   | Teclas Home e End |Standard |
   | Teclado numérico e teclas de função |ESC[n~ |
   | Estado inicial das teclas de cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Habilitar recursos adicionais do teclado |Control-Alt é diferente de AltGr |
   
    ![Configurações do Putty permitidas](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. No painel **Categoria**, selecione **Tradução**.
5. Na caixa de listagem **Conjunto de caracteres remotos**, selecione **UTF-8**.
6. Em **Manipulação de caracteres de desenho** de linha, selecione **Use pontos de código de desenho de linha Unicode**. A captura de tela a seguir mostra as seleções corretas do PuTTY.
   
    ![Configurações do PuTTY UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora você pode usar o PuTTY para se conectar ao console serial do dispositivo seguindo as etapas abaixo.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Sobre o console serial

Ao acessar a interface do Windows PowerShell do dispositivo StorSimple por meio do console serial, uma mensagem de cabeçalho é apresentada, seguida das opções de menu.

A mensagem de cabeçalho contém informações de dispositivo StorSimple básicas, como modelo, nome, versão do software instalado e status do controlador que você está acessando. A imagem a seguir mostra um exemplo de uma mensagem de cabeçalho.

![Mensagem da faixa serial](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Você pode usar a mensagem de faixa para determinar se o controlador ao qual você está conectado é _Ativo_ ou _Passivo_.

A imagem a seguir mostra as várias opções de espaço de execução disponíveis no menu console serial.

![Registrar seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

É possível escolher um das seguintes configurações:

1. **Faça logon com acesso completo.**
   Essa opção permite que você se conecte (com as credenciais apropriadas) ao runspace **SSAdminConsole** no controlador local. (O controlador local é o controlador sendo acessado por meio do console serial do dispositivo StorSimple.) Essa opção também pode ser usada para permitir que o Suporte da Microsoft acesse o espaço de execução irrestrito (uma sessão de suporte) para solucionar qualquer problema do dispositivo. Depois que você usar a opção 1 para fazer logon, pode permitir que o engenheiro do Suporte da Microsoft acesse o espaço de execução irrestrito ao executar um cmdlet específico. Para obter detalhes, consulte [Iniciar uma sessão de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Faça logon no controlador de par com acesso completo.**
   Essa opção é igual à opção 1, exceto que você pode se conectar (com as credenciais apropriadas) ao runspace **SSAdminConsole** no controlador de par. Como o dispositivo StorSimple é um dispositivo de alta disponibilidade com dois controladores em uma configuração ativo-passivo, par refere-se ao outro controlador no dispositivo que você está acessando por meio do console serial).
   Semelhante à opção 1, essa opção também pode ser usada para permitir que o Suporte da Microsoft acesse o espaço de execução irrestrito em um controlador de par.

3. **Conecte-se com acesso limitado.**
   Essa opção é usada para acessar a interface do Windows PowerShell em modo limitado. As credenciais de acesso não são solicitadas. Essa opção se conecta a um espaço de execução mais restrito em comparação com as opções 1 e 2.  Algumas das tarefas disponíveis por meio da opção 1 que *não podem* ser executadas nesse runspace são:
   
   * Redefinir para as configurações de fábrica
   * Alterar a senha
   * Habilitar ou desabilitar o acesso de suporte
   * Aplicar atualizações
   * Instale hotfixes

     > [!NOTE]
     > Essa é a opção preferencial se você tiver esquecido a senha de administrador do dispositivo e não puder se conectar por meio das opções 1 ou 2.

4. **Alterar Idioma.**
   Essa opção permite que você altere o idioma de exibição na interface do Windows PowerShell. Os idiomas com suporte são inglês, japonês, russo, francês, coreano, espanhol, italiano, alemão, chinês e português.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Conectar-se remotamente ao StorSimple usando o Windows PowerShell para StorSimple

Você pode usar o Windows PowerShell remotamente para se conectar ao seu dispositivo StorSimple. Ao se conectar dessa maneira, você não verá um menu. (Você verá um menu apenas se usar o console serial no dispositivo para se conectar. Conectar-se remotamente leva você diretamente ao equivalente de "opção 1 – acesso completo" no console serial.) Com a comunicação remota do Windows PowerShell, você se conectar a um espaço de execução específico. Também é possível especificar o idioma de exibição.

O idioma de exibição é independente do idioma definido usando a opção **Alterar Idioma** no menu do console serial. O PowerShell remoto selecionará automaticamente a localidade do dispositivo do qual você está se conectando se nenhum for especificado.

> [!NOTE]
> Se você estiver trabalhando com hosts virtuais do Microsoft Azure e Dispositivos de Nuvem StorSimple, poderá usar o Windows PowerShell remotamente e o host virtual para conectar-se ao dispositivo de nuvem. Se tiver configurado um local de compartilhamento no host no qual salvar as informações de sessão do Windows PowerShell, você deverá estar ciente de que a entidade _Todos_ inclui somente os usuários autenticados. Portanto, se você tiver configurado o compartilhamento para permitir acesso por _Todos_ e conectar-se sem especificar credenciais, a entidade Anônima não autenticada será usada e você verá um erro. Para corrigir esse problema, no host de compartilhamento, você precisa ativar a conta de convidado e dar à conta de convidado acesso total ao compartilhamento ou especificar credenciais válidas, juntamente com o cmdlet do Windows PowerShell.


Você pode usar HTTP ou HTTPS para se conectar por meio de comunicação remota do Windows PowerShell. Use as instruções nos seguintes tutoriais:

* [Conectar-se remotamente usando HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Conectar-se remotamente usando HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de conexão

Quando você estiver decidindo como se conectar ao Windows PowerShell para StorSimple, considere os seguintes fatores:

* Conectar-se diretamente ao console serial do dispositivo é seguro, mas se conectar ao console serial em comutadores de rede não é seguro. Tenha cuidado com os riscos de segurança ao se conectar ao serial do dispositivo em comutadores de rede.
* A conexão através de uma sessão HTTP pode oferecer mais segurança do que a conexão por meio do console serial através da rede. Embora uma sessão HTTP não seja o método de conexão mais seguro, ela é aceitável em redes confiáveis.
* A conexão por meio de uma sessão HTTPS é a opção mais segura e recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o seu dispositivo StorSimple usando o Windows PowerShell para StorSimple

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na interface do Windows PowerShell do dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada apropriada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Fluxos de trabalho do Windows PowerShell para StorSimple

| Se você quiser fazer isso... | Utilize este procedimento. |
| --- | --- |
| Registre seu dispositivo |[Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar o proxy Web</br>Exibir configurações do proxy Web |[Configurar proxy da web para seu dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as configurações de interface de rede DATA 0 em seu dispositivo StorSimple |[Modificar a interface de rede DATA 0 em seu dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou desligar um controlador </br> Desligar um dispositivo</br>Redefinir o dispositivo para as configurações padrões de fábrica |[Gerenciar os controladores do dispositivo](storsimple-8000-manage-device-controller.md) |
| Instalar atualizações e hotfixes no modo de manutenção |[Atualizar seu dispositivo](storsimple-update-device.md) |
| Entrar no modo de manutenção </br>Sair do modo de manutenção |[Modos de dispositivo StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de Suporte</br>Descriptografar e editar um pacote de suporte |[Criar e gerenciar pacotes de suporte](storsimple-8000-create-manage-support-package.md) |
| Iniciar uma sessão de suporte</br> |[Iniciar uma sessão de suporte no Windows PowerShell para StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obter ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a Ajuda de cmdlet está disponível. Uma versão atualizada online da Ajuda também está disponível, a qual pode ser usada para atualizar a Ajuda em seu sistema.

Obter ajuda nessa interface é semelhante a obter ajuda no Windows PowerShell, e a maioria dos cmdlets relacionados à ajuda funcionará. Você pode encontrar ajuda para o Windows PowerShell online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

<!--The following is a brief description of the types of Help for this Windows PowerShell interface, including how to update the Help. - OK to remove? Transition not needed.-->

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda com qualquer cmdlet ou função, use o seguinte comando: `Get-Help <cmdlet-name>`
* Para obter ajuda online para qualquer cmdlet, use o cmdlet anterior com o parâmetro `-Online`: `Get-Help <cmdlet-name> -Online`
* Para obter ajuda completa, use o parâmetro `–Full` e para obter exemplos, use o parâmetro `–Examples`.

### <a name="to-update-help"></a>Para atualizar a Ajuda

Você pode atualizar facilmente a Ajuda na interface do Windows PowerShell. Execute as seguintes etapas para atualizar a Ajuda em seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar a Ajuda de cmdlet
1. Inicie o Windows PowerShell com a opção **Executar como administrador** .
2. No prompt de comando, digite: `Update-Help`
3. Os arquivos de Ajuda atualizados serão instalados.
4. Após a instalação dos arquivos de ajuda, digite: `Get-Help Get-Command` para exibir uma lista de cmdlets para os quais a ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis em um runspace, faça logon na opção de menu correspondente e execute o cmdlet `Get-Command`.


## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas com seu dispositivo StorSimple ao executar um dos fluxos de trabalho acima, consulte [Ferramentas para solucionar problemas em implantações de StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).
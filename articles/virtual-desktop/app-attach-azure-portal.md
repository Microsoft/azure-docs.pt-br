---
title: Visualização do portal de anexação do Windows Virtual Desktop MSIX – Azure
description: Como configurar o anexo de aplicativo do MSIX para a área de trabalho virtual do Windows usando o portal do Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c775d81b88c891d6d8ea0a4597b4fa4fee29c86a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737538"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Configurar a anexação de aplicativo MSIX com o portal do Azure

> [!IMPORTANT]
> A anexação do aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explicará como configurar a anexação do aplicativo MSIX (versão prévia) em um ambiente de área de trabalho virtual do Windows.

## <a name="requirements"></a>Requisitos

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e enviar [este formulário](https://aka.ms/enablemsixappattach) para habilitar a anexação do aplicativo MSIX em sua assinatura. Se você não tiver uma solicitação aprovada, o MSIX app Attach não funcionará. A aprovação de solicitações pode levar até 24 horas durante os dias úteis. Você receberá um email quando sua solicitação for aceita e concluída.

Veja o que você precisa para configurar o anexo do aplicativo MSIX:

- Uma implantação funcional da Área de Trabalho Virtual do Windows. Para saber como implantar a área de trabalho virtual do Windows (clássica), consulte [criar um locatário na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Para saber como implantar a área de trabalho virtual do Windows com a integração do Azure Resource Manager, confira [criar um pool de hosts com o portal do Azure](./create-host-pools-azure-marketplace.md).
- Um pool de hosts da área de trabalho virtual do Windows com pelo menos um host de sessão ativo.
- Esse pool de hosts deve estar no ambiente de validação. 
- A ferramenta de empacotamento MSIX.
- Um aplicativo MSIX expandido em uma imagem MSIX que é carregada em um compartilhamento de arquivos.
- Um compartilhamento de arquivos em sua implantação de área de trabalho virtual do Windows onde o pacote MSIX será armazenado.
- O compartilhamento de arquivos em que você carregou a imagem MSIX também deve ser acessível a todas as VMs (máquinas virtuais) no pool de hosts. Os usuários precisarão de permissões somente leitura para acessar a imagem.
- Se o certificado não for confiável publicamente, siga as instruções em [instalar certificados](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Desligar as atualizações automáticas para aplicativos do MSIX app Attach

Antes de começar, você deve desabilitar as atualizações automáticas para aplicativos do MSIX app Attach. Para desabilitar as atualizações automáticas, você precisará executar os seguintes comandos em um prompt de comando com privilégios elevados:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Recomendamos que você reinicie a máquina virtual depois de habilitar o Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Configurar a interface de gerenciamento de anexação de aplicativo MSIX

Em seguida, você precisará baixar e configurar a interface de gerenciamento de anexação de aplicativo MSIX para o portal do Azure.

Para configurar a interface de gerenciamento:

1. [Abra o portal do Azure](https://portal.azure.com).
2. Se você receber um prompt perguntando se considera a confiabilidade da extensão, selecione **permitir**.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela da janela de extensões não confiáveis. "Allow" é realçado em vermelho.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Adicionar uma imagem MSIX ao pool de hosts

Em seguida, você precisará adicionar a imagem MSIX ao seu pool de hosts.

Para adicionar a imagem MSIX:

1. Abra o portal do Azure.

2. Insira **área de trabalho virtual do Windows** na barra de pesquisa e, em seguida, selecione o nome do serviço.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela de um usuário selecionando "área de trabalho virtual do Windows" no menu suspenso da barra de pesquisa no portal do Azure. "Área de trabalho virtual do Windows" é realçada em vermelho.](media/find-and-select.png)

3. Selecione o pool de hosts em que você planeja colocar os aplicativos MSIX.

4. Selecione **pacotes MSIX** para abrir a grade de dados com todos os **pacotes MSIX** atualmente adicionados ao pool de hosts.

5. Selecione **+ Adicionar** para abrir a guia **Adicionar pacote MSIX** .

6. Na guia **Adicionar pacote MSIX** , insira os seguintes valores:

      - Para **caminho de imagem MSIX**, insira um caminho UNC válido apontando para a imagem MSIX no compartilhamento de arquivos. (Por exemplo, `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Quando terminar, selecione **Adicionar** para interrogar o contêiner MSIX para verificar se o caminho é válido.

      - Para o **pacote MSIX**, selecione o nome do pacote MSIX relevante no menu suspenso. Esse menu só será populado se você tiver inserido um caminho de imagem válido no **caminho da imagem MSIX**.

      - Para **aplicativos de pacote**, verifique se a lista contém todos os aplicativos MSIX que você deseja disponibilizar para os usuários em seu pacote MSIX.

      - Opcionalmente, insira um **nome de exibição** se desejar que seu pacote tenha um mais amigável em suas implantações de usuário.

      - Verifique se a **versão** tem o número de versão correto.

      - Selecione o **tipo de registro** que você deseja usar. Qual delas você usa depende de suas necessidades:

    - O **registro sob demanda** adia o registro completo do aplicativo MSIX até que o usuário inicie o aplicativo. Esse é o tipo de registro que recomendamos que você use.

    - O **bloqueio de logon** somente registra enquanto o usuário está entrando. Não recomendamos esse tipo porque ele pode levar a tempos de entrada mais longos para os usuários.

7.  Em **estado**, selecione seu estado preferencial.
    -  O status **ativo** permite que os usuários interajam com o pacote.
    -  O status **inativo** faz com que a área de trabalho virtual do Windows ignore o pacote e não o entregue aos usuários.

8. Quando terminar, selecione **Adicionar**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publicar aplicativos MSIX em um grupo de aplicativos

Em seguida, você precisará publicar os aplicativos no pacote. Você precisará fazer isso para os grupos de aplicativos de desktop e de aplicativo remoto.

Se você já tiver uma imagem MSIX, pule para [publicar aplicativos MSIX em um grupo de aplicativos](#publish-msix-apps-to-an-app-group). Se você quiser testar aplicativos herdados, siga as instruções em [Criar um pacote MSIX de um instalador de área de trabalho em uma VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) para converter o aplicativo herdado em um pacote MSIX.

Para publicar os aplicativos:

1. No provedor de recursos de área de trabalho virtual do Windows, selecione a guia **grupos de aplicativos** .

2. Selecione o grupo de aplicativos no qual você deseja publicar os aplicativos.

   >[!NOTE]
   >Os aplicativos MSIX podem ser fornecidos com o MSIX app attach para os grupos de aplicativos de aplicativos de área de trabalho e de aplicativo remoto

3. Quando você estiver no grupo de aplicativos, selecione a guia **aplicativos** . A grade de **aplicativos** exibirá todos os aplicativos existentes no grupo de aplicativos.

4. Selecione **+ Adicionar** para abrir a guia **Adicionar aplicativo** .

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela do usuário selecionando + Adicionar para abrir a guia Adicionar aplicativo](media/select-add.png)

5. Para **origem do aplicativo**, escolha a origem do seu aplicativo.
    - Se você estiver usando um grupo de aplicativos de área de trabalho, escolha **pacote MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela de um cliente selecionando pacote MSIX no menu suspenso de origem do aplicativo. O pacote MSIX está realçado em vermelho.](media/select-source.png)
    
    - Se você estiver usando um grupo de aplicativos remoto, escolha uma das seguintes opções:
        
        - Menu Iniciar
        - Caminho do aplicativo
        - Pacote MSIX

    - Para **nome do aplicativo**, insira um nome descritivo para o aplicativo.

    Você também pode configurar os seguintes recursos opcionais:
   
    - Para **nome de exibição**, insira um novo nome para o pacote que os usuários verão.

    - Para **Descrição**, insira uma breve descrição do pacote do aplicativo.

    - Se você estiver usando um grupo de aplicativos remoto, também poderá configurar essas opções:

        - **Caminho do ícone**
        - **Índice de ícone**
        - **Mostrar no Web feed**

6. Quando terminar, selecione **Salvar**.

>[!NOTE]
>Quando um usuário é atribuído ao grupo de aplicativos remoto e ao grupo de aplicativos da área de trabalho do mesmo pool de hosts, o grupo de aplicativos da área de trabalho será exibido no feed.

## <a name="assign-a-user-to-an-app-group"></a>Atribuir um usuário a um grupo de aplicativos

Depois de atribuir aplicativos MSIX a um grupo de aplicativos, você precisará conceder acesso a eles aos usuários. Você pode atribuir o acesso adicionando usuários ou grupos de usuários a um grupo de aplicativos com aplicativos MSIX publicados. Siga as instruções em [gerenciar grupos de aplicativos com o portal do Azure](manage-app-groups.md) para atribuir usuários a um grupo de aplicativos.

>[!NOTE]
>O aplicativo MSIX anexar aplicativos remotos pode desaparecer do feed quando você testa aplicativos remotos durante a visualização pública. Os aplicativos não aparecem porque o pool de hosts que você está usando no ambiente de avaliação está sendo servido por um agente de RD no ambiente de produção. Como o agente de RD no ambiente de produção não registra a presença do aplicativo MSIX anexar aplicativos remotos, os aplicativos não aparecerão no feed.

## <a name="change-msix-package-state"></a>Alterar estado do pacote MSIX

Em seguida, você precisará alterar o estado do pacote MSIX para **ativo** ou **inativo**, dependendo do que você deseja fazer com o pacote. Pacotes ativos são pacotes dos quais os usuários podem interagir quando são publicados. Os pacotes inativos são ignorados pela área de trabalho virtual do Windows, para que os usuários não possam interagir com os aplicativos dentro do.

### <a name="change-state-with-the-applications-list"></a>Alterar o estado com a lista de aplicativos

Para alterar o estado do pacote com a lista de aplicativos:

1. Vá para o pool de hosts e selecione **pacotes MSIX**. Você deverá ver uma lista de todos os pacotes MSIX existentes no pool de hosts.

2. Selecione os pacotes MSIX cujos Estados você precisa alterar e, em seguida, selecione **alterar estado**.

### <a name="change-state-with-update-package"></a>Alterar estado com pacote de atualização

Para alterar o estado do pacote com um pacote de atualização:

1. Vá para o pool de hosts e selecione **pacotes MSIX**. Você deverá ver uma lista de todos os pacotes MSIX existentes no pool de hosts.

2. Selecione o nome do pacote cujo estado você deseja alterar na lista de pacotes MSIX. Isso abrirá a guia **pacote de atualização** .

3. Alterne a opção de **estado** para **inativo** ou **ativo** e, em seguida, selecione **salvar.**

## <a name="change-msix-package-registration-type"></a>Alterar tipo de registro de pacote MSIX

Para alterar o tipo de registro do pacote:

1. Selecione **pacotes do MSIX**. Você deverá ver uma lista de todos os pacotes MSIX existentes no pool de hosts.

2. Selecione o **nome do pacote na** **grade de pacotes MSIX** , isso abrirá a folha para atualizar o pacote.

3. Alterne o **tipo de registro** por meio do botão de **bloqueio sob demanda/logon** conforme desejado e selecione **salvar.**

## <a name="remove-an-msix-package"></a>Remover um pacote MSIX

Para remover um pacote MSIX do seu pool de hosts:

1. Selecione **pacotes do MSIX**.  Você deverá ver uma lista de todos os pacotes MSIX existentes no pool de hosts.

2. Selecione as reticências no lado direito do nome do pacote que você deseja excluir e, em seguida, selecione **remover**.

## <a name="remove-msix-apps"></a>Remover aplicativos MSIX

Para remover aplicativos MSIX individuais do seu pacote:

1. Vá para o pool de hosts e selecione **grupos de aplicativos**.

2. Selecione o grupo de aplicativos do qual você deseja remover aplicativos do MSIX.

3. Abra a guia **aplicativos** .

4. Selecione o aplicativo que você deseja remover e, em seguida, selecione **remover**.

## <a name="next-steps"></a>Próximas etapas

Pergunte a nossas perguntas da Comunidade sobre esse recurso na [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão alguns outros artigos que você pode achar úteis:

- [Glossário de anexação de aplicativo MSIX](app-attach-glossary.md)
- [Perguntas frequentes sobre anexação de aplicativo MSIX](app-attach-faq.md)

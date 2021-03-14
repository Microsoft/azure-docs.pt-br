---
title: Instalar Microsoft Office contêineres de aplicativo FSLogix na área de trabalho virtual do Windows – Azure
description: Como usar o editor de regras de aplicativo para criar um contêiner de aplicativo FSLogix com o Office na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744302"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Instalar Microsoft Office usando contêineres de aplicativos FSLogix

Você pode instalar Microsoft Office de maneira rápida e eficiente usando um contêiner de aplicativo FSLogix como um modelo para as outras máquinas virtuais (VMs) em seu pool de hosts.

Veja por que usar um contêiner de aplicativo FSLogix pode ajudar a tornar a instalação mais rápida:

- O descarregamento de seus aplicativos do Office para um contêiner de aplicativo reduz os requisitos para o tamanho da sua unidade C.
- Instantâneos ou backups de sua VM levam menos recursos.
- Ter um pipeline automatizado por meio da atualização de uma única imagem torna mais fácil a atualização de suas VMs.
- Você só precisa de uma imagem para instalar o Office (e outros aplicativos) em todas as VMs em sua implantação de área de trabalho virtual do Windows.

Este artigo mostrará como configurar um contêiner de aplicativo FSLogix com o Office.

## <a name="requirements"></a>Requisitos

Você precisará dos seguintes itens para configurar o editor de regras:

- uma VM que executa o Windows sem o Office instalado
- uma cópia do Office
- uma cópia do FSLogix instalada na sua implantação
- um compartilhamento de rede ao qual todas as VMs em seu pool de hosts têm acesso somente leitura

## <a name="install-office"></a>Instalar o Office

Para instalar o Office em seu VHD ou VHDX, habilite o protocolo RDP em sua VM e siga as instruções em [instalar o Office em uma imagem mestra de VHD](install-office-on-wvd-master-image.md). Ao instalar o, verifique se você está usando [as licenças corretas](overview.md#requirements).

>[!NOTE]
>A área de trabalho virtual do Windows requer o SCA (compartilhamento do computador).

## <a name="install-fslogix"></a>Instalar o FSLogix

Para instalar o FSLogix e o editor de regras, siga as instruções em [baixar e instalar o FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Criar e preparar um VHD para armazenar o Office

Em seguida, você precisará criar e preparar uma imagem VHD para usar o editor de regras em:

1. Abra um prompt de comando como administrador. e execute o seguinte comando:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Certifique-se de manter os espaços em branco que você vê neste comando.

2. Em seguida, execute o seguinte comando:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Se você encontrar o serviço, reinicie a VM antes de continuar com a etapa 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Depois disso, vá para **arquivos de programas**  >  **FSLogix**  >  **aplicativos** e execute o seguinte comando para criar o VHD de destino:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    O VHD criado com esse comando deve conter a pasta C: \\ Program files \\ Microsoft Office.

    >[!NOTE]
    >Se você encontrar erros, desinstale o Office e comece novamente na etapa 1.

## <a name="configure-the-rule-editor"></a>Configurar o editor de regras

Agora que você preparou a imagem, precisará configurar o editor de regras e criar um arquivo no qual armazenar suas regras.

1. Vá para **arquivos de programas**  >  **FSLogix**  >  **aplicativos** e execute **RuleEditor.exe**.

2. Selecione **arquivo**  >  **novo**  >  **criar** para fazer um novo conjunto de regras e, em seguida, salve esse conjunto de regras em uma pasta local.

3. Selecione **conjunto de regras em branco** e, em seguida, selecione **OK**.

4. Selecione o botão **+**. Isso abrirá a janela **Adicionar regra** . Isso alterará as opções na caixa de diálogo **Adicionar regra** .

5. No menu suspenso, selecione **regra de contêiner de aplicativo (VHD)**.

6. Digite **C: \\ arquivos de programas \\ Microsoft Office** no campo **pasta** .

7. Para o campo **arquivo de disco** , selecione **\<path\> \\ Office. vhd** na seção **criar VHD de destino** .

8. Selecione **OK**.

9. Vá para a pasta de trabalho em **C: \\ usuários \\ \<username\> \\ documentos \\ FSLogix conjuntos de regras** e procure os arquivos. FRX e. FXa. Você precisa mover esses arquivos para a pasta regras localizada em **C: \\ arquivos de programas \\ FSLogix \\ \\ regras de aplicativos** para que as regras comecem a funcionar.

10. Selecione **aplicar regras ao sistema** para que as regras entrem em vigor.

     >[!NOTE]
     > Você precisará aplicar os arquivos de regra de aplicativo que precisarão de todos os hosts de sessão.

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre o FSLogix, Confira nossa [documentação do FSLogix](/fslogix/).
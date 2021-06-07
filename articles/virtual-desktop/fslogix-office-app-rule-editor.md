---
title: Instalar contêineres do aplicativo FSLogix do Microsoft Office na Área de Trabalho Virtual do Windows – Azure
description: Como usar o editor de regras do aplicativo para criar um contêiner do aplicativo FSLogix com o Office na Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446801"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Instalar o Microsoft Office usando contêineres de aplicativos FSLogix

Você pode instalar o Microsoft Office de maneira rápida e eficiente usando um contêiner do aplicativo FSLogix como um modelo para as outras VMs (máquinas virtuais) no seu pool de host.

Veja por que usar um contêiner do aplicativo FSLogix pode ajudar a agilizar a instalação:

- O descarregamento de aplicativos do Office para um contêiner de aplicativo reduz os requisitos para o tamanho da sua unidade C.
- Instantâneos ou backups da sua VM usam menos recursos.
- Ter um pipeline automatizado por meio da atualização de uma imagem facilita a atualização das suas VMs.
- Você só precisa de uma imagem para instalar o Office (e outros aplicativos) em todas as VMs na sua implantação da Área de Trabalho Virtual do Windows.

Este artigo mostrará como configurar um contêiner do aplicativo FSLogix com o Office.

## <a name="requirements"></a>Requisitos

Você precisará dos seguintes itens para configurar o editor de regras:

- uma VM que executa o Windows sem o Office instalado
- uma cópia do Office
- uma cópia do FSLogix instalada na sua implantação
- um compartilhamento de rede ao qual todas as VMs no seu pool de host têm acesso somente leitura

## <a name="install-office"></a>Instalar o Office

Para instalar o Office no seu VHD ou VHDX, habilite o protocolo RDP na sua VM e siga as instruções em [Instalar o Office em uma imagem mestra de VHD](install-office-on-wvd-master-image.md). Ao instalar, verifique se você está usando [as licenças corretas](overview.md#requirements).

>[!NOTE]
>A Área de Trabalho Virtual do Windows requer a SCA (Ativação de Computador Compartilhado).

## <a name="install-fslogix"></a>Instalar o FSLogix

Para instalar o FSLogix e o editor de regras, siga as instruções em [Baixar e instalar o FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Criar e preparar um VHD para armazenar o Office

Em seguida, você precisará criar e preparar uma imagem VHD para usar o editor de regras em:

1. Abra um prompt de comando como administrador. e execute o seguinte comando:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Mantenha os espaços em branco que você vê neste comando.

2. Em seguida, execute o seguinte comando:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Se você encontrar o serviço, reinicie a VM antes de continuar com a etapa 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Depois disso, vá para **Arquivos de Programas** > **FSLogix** > **Aplicativos** e execute o seguinte comando para criar o VHD de destino:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    O VHD criado com esse comando deve conter a pasta C:\\Arquivos de Programas\\Microsoft Office.

    >[!NOTE]
    >Se você encontrar erros, desinstale o Office e comece novamente na etapa 1.

## <a name="configure-the-rule-editor"></a>Configurar o editor de regras

Agora que você preparou a imagem, precisará configurar o editor de regras e criar um arquivo no qual armazenar suas regras.

1. Vá para **Arquivos de Programas** > **FSLogix** > **Aplicativos** e execute **RuleEditor.exe**.

2. Selecione **Arquivo** > **Novo** > **Criar** para fazer um novo conjunto de regras e salve-o em uma pasta local.

3. Selecione **Conjunto de Regras em Branco** e selecione **OK**.

4. Selecione o botão **+**. Isso abre a janela **Adicionar Regra**. Isso altera as opções na caixa de diálogo **Adicionar Regra**.

5. No menu suspenso, selecione **Regra de Contêiner de Aplicativo (VHD)** .

6. Digite **C:\\Arquivos de Programas\\Microsoft Office** no campo **Pasta**.

7. Para o campo **Arquivo de disco**, selecione **\<path\>\\office.vhd** na seção **Criar VHD de destino**.

8. Selecione **OK**.

9. Vá para a pasta de trabalho em **C:\\Usuários\\\<username\>\\Documentos\\Conjunto de Regras do FSLogix** e procure os arquivos .frx e .fxa. Você precisa mover esses arquivos para a pasta Regras localizada em **C:\\Arquivos de Programas\\FSLogix\\Aplicativos\\Regras** para que as regras comecem a funcionar.

10. Selecione **Aplicar Regras ao Sistema** para que as regras entrem em vigor.

     >[!NOTE]
     > Você precisará aplicar os arquivos de regra de aplicativo que precisarão de todos os hosts da sessão.

## <a name="next-steps"></a>Próximas etapas

Se quiser saber mais sobre o FSLogix, confira nossa [Documentação do FSLogix](/fslogix/).
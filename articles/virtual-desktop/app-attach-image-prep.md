---
title: Windows Virtual Desktop Prepare MSIX aplicativo anexar imagem visualização-Azure
description: Como criar uma imagem de anexação do aplicativo MSIX para um pool de hosts da área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425714"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Preparar uma imagem do MSIX para a área de trabalho virtual do Windows

> [!IMPORTANT]
> A anexação do aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O MSIX app Attach (visualização) é uma solução de camada de aplicativo que permite que você anexe dinamicamente aplicativos de um pacote MSIX a uma sessão de usuário. O sistema de pacotes MSIX separa os aplicativos do sistema operacional, facilitando a criação de imagens para máquinas virtuais. Os pacotes MSIX também oferecem maior controle sobre quais aplicativos os usuários podem acessar em suas máquinas virtuais. Você pode até separar aplicativos da imagem mestra e fornecê-los aos usuários mais tarde.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Criar um pacote VHD ou VHDX para MSIX

Os pacotes MSIX precisam estar em um formato VHD ou VHDX para funcionar corretamente. Isso significa que, para começar, você precisará criar um pacote VHD ou VHDX.

>[!NOTE]
>Se você ainda não fez isso, certifique-se de habilitar o Hyper-V seguindo as instruções em [instalar o Hyper-v no Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Para criar um pacote VHD ou VHDX para MSIX:

1. Primeiro, abra o PowerShell.
2. Em seguida, execute o seguinte cmdlet para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Verifique se o VHD é grande o suficiente para manter o pacote MSIX expandido.

3. Execute o seguinte cmdlet para montar o VHD que você acabou de criar:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Em seguida, execute este cmdlet para inicializar o VHD montado:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Depois disso, execute este cmdlet para criar uma nova partição para o VHD inicializado:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Execute este cmdlet para formatar a partição:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Por fim, crie uma pasta pai no VHD montado. Esta etapa é necessária porque o pacote MSIX deve ter uma pasta pai para funcionar corretamente. Não importa o nome da pasta pai, contanto que a pasta pai exista.

## <a name="expand-msix"></a>Expandir o MSIX

Depois disso, você precisará expandir a imagem MSIX ao "descompactar" seus arquivos no VHD.

Para expandir a imagem MSIX:

1. [Baixe a ferramenta msixmgr](https://aka.ms/msixmgr) e salve a pasta .zip em uma pasta dentro de uma VM host de sessão.

2. Descompacte a pasta .zip da ferramenta msixmgr.

3. Coloque o pacote MSIX de origem na mesma pasta em que você descompactou a ferramenta msixmgr.

4. Abra um prompt de comando como administrador e navegue até a pasta em que você baixou e descompactou a ferramenta msixmgr.

5. Execute o seguinte cmdlet para desempacotar o MSIX no VHD que você criou na seção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer depois que você terminar de desempacotar:

    > Êxito ao desempacotar e aplicar ACLs para o pacote: <package name> . msix

    >[!NOTE]
    > Se você estiver usando pacotes do Microsoft Store for Business ou Education em sua rede ou em dispositivos não conectados à Internet, será necessário baixar e instalar licenças de pacote do Microsoft Store para executar os aplicativos. Para obter as licenças, consulte [usar pacotes offline](app-attach.md#use-packages-offline).

6. Acesse o VHD montado e abra a pasta do aplicativo para verificar se o conteúdo do pacote está lá.

7. Desmonte o VHD.

## <a name="upload-msix-image-to-share"></a>Carregar imagem MSIX para compartilhamento

Depois de criar o pacote MSIX, você precisará carregar o arquivo VHD, VHDX ou CIM resultante para um compartilhamento em que as máquinas virtuais de seus usuários possam acessá-lo.

## <a name="next-steps"></a>Próximas etapas

Pergunte a nossas perguntas da Comunidade sobre esse recurso na [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão alguns outros artigos que você pode achar úteis:

- [Glossário de anexação de aplicativo MSIX](app-attach-glossary.md)
- [Perguntas frequentes sobre anexação de aplicativo MSIX](app-attach-faq.md)
---
title: Usando a ferramenta MSIXMGR – Azure
description: Como usar a ferramenta MSIXMGR para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744109"
---
# <a name="using-the-msixmgr-tool"></a>Usando a ferramenta MSIXMGR

A ferramenta MSIXMGR é para a expansão de aplicativos empacotados por MSIX em imagens MSIX. A ferramenta usa um aplicativo MSIX (. MSIX) e o expande para um arquivo VHD, VHDx ou CIM. A imagem MSIX resultante é armazenada na conta de armazenamento do Azure que sua implantação de área de trabalho virtual do Windows usa. Este artigo mostrará como usar a ferramenta MSIXMGR.

>[!NOTE]
>Para garantir a compatibilidade, verifique se os CIMs que armazenam suas imagens do MSIX são gerados na versão do sistema operacional que você está executando em seus pools de hosts de área de trabalho virtual do Windows. O MSIXMGR pode criar arquivos CIM, mas você só pode usar esses arquivos com um pool de hosts que executa o Windows 10 20H2.

## <a name="requirements"></a>Requisitos

Antes de seguir as instruções neste artigo, você precisará fazer o seguinte:

- [Baixar a ferramenta MSIXMGR](https://aka.ms/msixmgr)
- Obtenha um aplicativo MSIX (. Arquivo MSIX)
- Obter permissões administrativas no computador em que você criará a imagem MSIX

## <a name="create-an-msix-image"></a>Criar uma imagem MSIX

A expansão é o processo de fazer um aplicativo empacotado MSIX (. MSIX) e descompactá-lo em uma imagem MSIX (. VHD (x) ou. Arquivo CIM).

Para expandir um arquivo MSIX:

1. [Baixe a ferramenta MSIXMGR](https://aka.ms/msixmgr) se ainda não tiver feito isso.

2. Descompacte MSIXMGR.zip em uma pasta local.

3. Abra um prompt de comando no modo elevado.

4. Localize a pasta local da etapa 2.

5. Execute o comando a seguir no prompt de comando para criar uma imagem MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Lembre-se de substituir os valores de espaço reservado pelos valores relevantes. Por exemplo: 

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Agora que você criou a imagem, vá para a pasta de destino e verifique se criou com êxito a imagem MSIX (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Criar uma imagem MSIX em um arquivo CIM

Você também pode usar o comando na [etapa 5](#create-an-msix-image) para criar arquivos CIM e VHDX, substituindo o tipo de arquivo e o caminho de destino.

Por exemplo, veja como você usaria esse comando para criar um arquivo CIM:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Veja como você usaria esse comando para fazer um VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o anexo de aplicativo MSIX em [o que é a anexação de aplicativo MSIX?](what-is-app-attach.md)

Para saber como configurar a anexação de aplicativo, confira estes artigos:

- [Configurar a anexação de aplicativo MSIX com o portal do Azure](app-attach-azure-portal.md)
- [Configurar a anexação de aplicativo MSIX usando o PowerShell](app-attach-powershell.md)
- [Criar scripts do PowerShell para anexação do aplicativo MSIX](app-attach.md)
- [Preparar uma imagem do MSIX para a área de trabalho virtual do Windows](app-attach-image-prep.md)
- [Configurar um compartilhamento de arquivos para anexação de aplicativo MSIX](app-attach-file-share.md)

Se você tiver dúvidas sobre anexação de aplicativo MSIX, consulte o Glossário de [perguntas frequentes](app-attach-faq.md) sobre anexações de aplicativo e [anexação de aplicativo](app-attach-glossary.md).

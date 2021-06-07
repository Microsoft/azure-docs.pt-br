---
title: Criar um pacote de suporte da série StorSimple 8000
description: Saiba como criar, descriptografar e editar um pacote de suporte para o dispositivo StorSimple da série 8000.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017161"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Criar e gerenciar um pacote de suporte do StorSimple da série 8000

## <a name="overview"></a>Visão geral

Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para ajudar o Suporte da Microsoft na solução de problemas do dispositivo StorSimple. Os logs coletados são criptografados e compactados.

Este tutorial inclui instruções passo a passo para criar e gerenciar o pacote de suporte para seu dispositivo StorSimple da série 8000. Se você estiver trabalhando com uma Matriz Virtual do StorSimple, acesse [Gerar um pacote de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Criar um pacote de suporte

Em alguns casos, você precisará criar o pacote de suporte manualmente por meio do Windows PowerShell para StorSimple. Por exemplo:

* Se você precisar remover informações confidenciais de seus arquivos de log antes de compartilhar com o Microsoft Support.
* Se você estiver tendo dificuldade para carregar o pacote devido a problemas de conectividade.

Você pode compartilhar seu pacote de suporte gerado manualmente com o Suporte da Microsoft por email. Execute as etapas a seguir para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para StorSimple

1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto usado para se conectar ao dispositivo StorSimple, insira o seguinte comando:
   
    `Start PowerShell`
2. Na sessão do Windows PowerShell, conecte-se ao console do SSAdmin do dispositivo:
   
   1. No prompt de comando, insira:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Na caixa de diálogo que é aberta, insira sua senha de administrador do dispositivo. A senha padrão é _Senha1_.
     
      ![Caixa de diálogo da credencial do PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecione **OK**.
   4. No prompt de comando, insira:
     
      `Enter-PSSession $MS`
3. Na sessão que é aberta, insira o comando apropriado.
   
   * Para compartilhamentos de rede protegidos por senha, insira:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Você será solicitado a fornecer uma senha e uma frase secreta de criptografia (porque o pacote de suporte é criptografado). Um pacote de suporte será, em seguida, criado na pasta padrão (nome do dispositivo anexado com a data e a hora atuais).
   * Para compartilhamentos que não são protegidos por senha, o parâmetro `-Credential` não é necessário. Insira o seguinte:
     
       `Export-HcsSupportPackage`
     
       O pacote de suporte é criado para ambos os controladores na pasta padrão. O pacote é um arquivo compactado e criptografado que pode ser enviado ao Suporte da Microsoft para solução de problemas. Para obter mais informações, consulte [Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros do cmdlet Export-HcsSupportPackage

Você pode usar os seguintes parâmetros com o cmdlet Export-HcsSupportPackage.

| Parâmetro | Obrigatório/Opcional | Descrição |
| --- | --- | --- |
| `-Path` |Obrigatório |Use para fornecer o local da pasta compartilhada de rede na qual o pacote de suporte é colocado. |
| `-EncryptionPassphrase` |Obrigatório |Use para fornecer uma frase secreta para ajudar a criptografar o pacote de suporte. |
| `-Credential` |Opcional |Use para fornecer credenciais de acesso para a pasta compartilhada de rede. |
| `-Force` |Opcional |Use para ignorar a etapa de confirmação de frase secreta de criptografia. |
| `-PackageTag` |Opcional |Use para especificar um diretório no *Caminho* no qual o pacote de suporte é colocado. O padrão é [nome do dispositivo]-[data e hora atuais:aaaa-MM-dd-HH-mm-ss]. |
| `-Scope` |Opcional |Especifique como **Cluster** (padrão) para criar um pacote de suporte para ambos os controladores. Se você quiser criar um pacote apenas para o controlador atual, especifique o **Controlador**. |

## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Após gerar um pacote de suporte, talvez seja necessário editar o pacote para remover informações confidenciais. Isso pode incluir nomes de volume, endereços IP do dispositivo e nomes de backup dos arquivos de log.

> [!IMPORTANT]
> Você só pode editar um pacote de suporte que tenha sido gerado por meio do Windows PowerShell para StorSimple. Não é possível editar um pacote criado no Portal do Azure com o serviço do Gerenciador de Dispositivos do StorSimple.

Para editar um pacote de suporte antes de carregá-lo no site de Suporte da Microsoft, primeiro descriptografe o pacote de suporte, edite os arquivos e criptografe-os novamente. Execute as seguintes etapas:

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gere um pacote de suporte conforme descrito anteriormente, em [Para criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Baixe o script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente em seu cliente.
3. Importe o módulo do Windows PowerShell. Especifique o caminho para a pasta local em que você baixou o script. Para importar o módulo, insira:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos os arquivos são arquivos *.aes* compactados e criptografados. Para descompactar e descriptografar arquivos, insira:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observe que as extensões de arquivo reais agora são exibidas para todos os arquivos.
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Quando você for solicitado a fornecer a senha de criptografia, digite a frase secreta usada quando o pacote de suporte foi criado.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Navegue até a pasta que contém os arquivos de log. Como os arquivos de log agora estão descompactados e descriptografados, eles terão as extensões de arquivo originais. Modifique esses arquivos para remover todas as informações específicas do cliente, como nomes de volume e endereços IP de dispositivos, e salve os arquivos.
7. Feche os arquivos para compactá-los com o gzip e criptografe-os com AES-256. Isso é para a velocidade e segurança ao transferir o pacote de suporte em uma rede. Para compactar e criptografar arquivos, digite o seguinte:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Editar pacote de suporte 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Quando solicitado, forneça uma frase secreta de criptografia para o pacote de suporte modificado.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Anote a nova senha para que você possa compartilhá-la com o Suporte da Microsoft quando solicitado.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: edição de arquivos em um pacote de suporte em um compartilhamento protegido por senha

O exemplo a seguir mostra como descriptografar, editar e criptografar novamente um pacote de suporte.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [informações coletadas no pacote de Suporte](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Saiba como [usar pacotes de suporte e logs de dispositivo para solucionar problemas de implantação do dispositivo](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


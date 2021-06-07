---
title: Disco do Azure Data Box solução de problemas de desbloqueio de disco | Microsoft Docs
description: Saiba mais sobre os fluxos de trabalho para solucionar problemas da ferramenta de desbloqueio com Disco do Azure Data Box. Consulte Disco do Data Box erros de ferramenta de desbloqueio.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87925554"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Solucionar problemas de desbloqueio de disco no Disco do Azure Data Box

Este artigo se aplica a Microsoft Azure Disco do Data Box e descreve os fluxos de trabalho usados para solucionar quaisquer problemas ao usar a ferramenta de desbloqueio. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Erros da ferramenta de desbloqueio do Data Box Disk


| Mensagem de erro/Comportamento da ferramenta      | Recomendações                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| O .NET Framework atual não tem suporte. As versões com suporte são 4.5 e superiores.<br><br>A ferramenta é encerrada com uma mensagem.  | O .NET 4.5 não está instalado. Instale o .NET 4.5 ou superior no computador host que executa a ferramenta de desbloqueio do Data Box Disk.                                                                            |
| Não foi possível desbloquear ou verificar os volumes. Contatar Suporte da Microsoft  <br><br>A ferramenta não consegue desbloquear ou verificar qualquer unidade bloqueada. | A ferramenta não conseguiu desbloquear as unidades bloqueadas com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas.                                                |
| Os volumes a seguir foram desbloqueados e verificados. <br>Letras de unidade de volume: E:<br>Não foi possível desbloquear os volumes com as seguintes chaves de acesso: werwerqomnf, qwerwerqwdfda <br><br>A ferramenta desbloqueia algumas unidades e lista as letras de unidade bem-sucedidas e com falha.| Êxito parcial. Não foi possível desbloquear algumas unidades com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas. |
| Não foi possível localizar os volumes bloqueados. Verifique se o disco recebido da Microsoft está conectado corretamente e está em estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. Ou as unidades já estão desbloqueadas ou não foram detectadas. Verifique se as unidades estão conectadas e bloqueadas. <br> <br>Você também poderá ver esse erro se tiver formatado seus discos. Se você tiver formatado seus discos, eles agora não podem ser usados. Contate o Suporte da Microsoft para as próximas etapas.                                                          |
| Erro fatal: parâmetro inválido<br>Nome do parâmetro: nvalid_arg<br>USO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/PassKeys:       obtenha essa chave de acesso do pedido do Azure DataBox Disk. A chave de acesso desbloqueia seus discos.<br>/Help:           esta opção fornece ajuda sobre o uso de cmdlet e exemplos.<br>/SystemCheck:    esta opção verifica se o seu sistema atende aos requisitos para executar a ferramenta.<br><br>Pressione qualquer tecla para sair. | Parâmetro inválido inserido. Os únicos parâmetros permitidos são/SystemCheck,/PassKey e/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Desbloquear problemas em discos ao usar um cliente Windows

Esta seção detalha alguns dos principais problemas enfrentados durante a implantação de Disco do Data Box ao usar um cliente Windows para cópia de dados.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problema: não foi possível desbloquear a unidade do BitLocker
 
**Causa** 

Você usou a senha na caixa de diálogo do BitLocker e tentou desbloquear o disco usando a caixa de diálogo desbloquear unidades do BitLocker. Isso não funcionaria.

**Resolução**

Para desbloquear Data Box Disks, você precisa usar a ferramenta de Desbloqueio do Data Box Disk e fornecer a senha do portal do Azure. Para obter mais informações, acesse [tutorial: desempacotar, conectar e desbloquear disco do Azure data Box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problema: não foi possível desbloquear ou verificar alguns volumes. Contatar Suporte da Microsoft
 
**Causa**

Talvez você veja o seguinte erro no log de erros e não consiga desbloquear nem verificar alguns volumes.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Isso indica que você provavelmente não tem a versão apropriada do Windows PowerShell no seu cliente do Windows.

**Resolução**

Você pode instalar o [Windows PowerShell v5.0](https://www.microsoft.com/download/details.aspx?id=54616) e repetir a operação.
 
Se você ainda não puder desbloquear os volumes, copie os logs da pasta que tem a ferramenta de Desbloqueio do Data Box Disk e [entre em contato com o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas de validação](data-box-disk-troubleshoot.md).

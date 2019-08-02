---
title: Problemas de desbloqueio de disco de solução de problemas de disco da caixa de dados do Azure | Microsoft Docs
description: Descreve como solucionar os problemas observados no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148277"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Solucionar problemas de desbloqueio de disco no disco do Azure Data Box

Este artigo se aplica ao disco do Microsoft Azure Data Box e descreve os fluxos de trabalho usados para solucionar problemas ao usar a ferramenta de desbloqueio. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Erros da ferramenta de desbloqueio do Data Box Disk


| Mensagem de erro/Comportamento da ferramenta      | Recomendações                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| O .NET Framework atual não tem suporte. As versões com suporte são 4.5 e superiores.<br><br>A ferramenta é encerrada com uma mensagem.  | O .NET 4.5 não está instalado. Instale o .NET 4.5 ou superior no computador host que executa a ferramenta de desbloqueio do Data Box Disk.                                                                            |
| Não foi possível desbloquear ou verificar os volumes. Contatar Suporte da Microsoft  <br><br>A ferramenta não consegue desbloquear ou verificar qualquer unidade bloqueada. | A ferramenta não conseguiu desbloquear as unidades bloqueadas com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas.                                                |
| Os volumes a seguir foram desbloqueados e verificados. <br>Letras de unidade de volume: E:<br>Não foi possível desbloquear os volumes com as seguintes chaves de acesso: werwerqomnf, qwerwerqwdfda <br><br>A ferramenta desbloqueia algumas unidades e lista as letras de unidade bem-sucedidas e com falha.| Êxito parcial. Não foi possível desbloquear algumas unidades com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas. |
| Não foi possível localizar os volumes bloqueados. Verifique se o disco recebido da Microsoft está conectado corretamente e está em estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. Ou as unidades já estão desbloqueadas ou não foram detectadas. Verifique se as unidades estão conectadas e bloqueadas.                                                           |
| Erro fatal: Parâmetro inválido<br>Nome do parâmetro: nvalid_arg<br>USO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/PassKeys:       obtenha essa chave de acesso do pedido do Azure DataBox Disk. A chave de acesso desbloqueia seus discos.<br>/Help:           esta opção fornece ajuda sobre o uso de cmdlet e exemplos.<br>/SystemCheck:    essa opção verifica se o sistema atende aos requisitos para executar a ferramenta.<br><br>Pressione qualquer tecla para sair. | Parâmetro inválido inserido. Os únicos parâmetros permitidos são /SystemCheck, /PassKey e /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Desbloquear problemas para discos, ao usar um cliente Windows

Esta seção fornece detalhes sobre alguns dos principais problemas enfrentados durante a implantação de disco da caixa de dados ao usar um cliente do Windows para cópia de dados.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problema: Não foi possível desbloquear a unidade do BitLocker
 
**Causa** 

Você usou a senha na caixa de diálogo do BitLocker e tentou desbloquear o disco usando a caixa de diálogo desbloquear unidades do BitLocker. Isso não funcionaria.

**Resolução**

Para desbloquear Data Box Disks, você precisa usar a ferramenta de Desbloqueio do Data Box Disk e fornecer a senha do portal do Azure. Para obter mais informações, vá para [Tutorial: Desempacotar, conectar e desbloquear o Azure Data Box Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problema: Não foi possível desbloquear nem verificar alguns volumes. Contatar Suporte da Microsoft
 
**Causa**

Talvez você veja o seguinte erro no log de erros e não consiga desbloquear nem verificar alguns volumes.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Isso indica que você provavelmente não tem a versão apropriada do Windows PowerShell no seu cliente do Windows.

**Resolução**

Você pode instalar o [Windows PowerShell v5.0](https://www.microsoft.com/download/details.aspx?id=54616) e repetir a operação.
 
Se você ainda não puder desbloquear os volumes, copie os logs da pasta que tem a ferramenta de Desbloqueio do Data Box Disk e [entre em contato com o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas de validação](data-box-disk-troubleshoot.md).

---
title: Problemas de cópia de dados de solução de problemas de disco da caixa de dados do Azure | Microsoft Docs
description: Descreve como solucionar problemas encontrados durante a cópia de dados em disco do Azure Data Box usando os logs.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148342"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Solucionar problemas de cópia de dados no disco do Azure Data Box

Este artigo se aplica ao disco do Microsoft Azure Data Box e descreve como solucionar os problemas que você vê ao copiar os dados em discos. O artigo também aborda problemas ao usar a ferramenta de cópia de divisão.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemas de cópia de dados ao usar um sistema Linux

Esta seção fornece detalhes sobre alguns dos principais problemas enfrentados ao se usar um cliente Linux para copiar dados para discos.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problema: Unidade sendo montada como somente leitura
 
**Causa** 

Isso pode ser devido a um sistema de arquivos não limpo.

Remontar uma unidade como leitura e gravação não funciona com Data Box Disks. Não há suporte para esse cenário em unidades descriptografadas pelo dislocker. Você pode ter remontado o dispositivo com sucesso usando o seguinte comando:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Embora a remontagem tenha sido bem-sucedida, os dados não persistirão.

**Resolução**

Execute as seguintes etapas no seu sistema Linux:

1. Instalar o `ntfsprogs` pacote para o utilitário ntfsfix.
2. Desmonte os pontos de montagem fornecidos para a unidade pela ferramenta de desbloqueio. O número de pontos de montagem variam para unidades.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Execute `ntfsfix` no caminho correspondente. O número realçado deve ser igual a etapa 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Execute o seguinte comando para remover os metadados de hibernação que podem causar o problema de montagem.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Faça uma desmontagem limpa.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Fazer uma limpeza unlock e montar.
7. Teste o ponto de montagem, escrevendo um arquivo.
8. Desmonte e monte novamente para validar a persistência de arquivo.
9. Continue com a cópia de dados.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problema: Erro de dados não persistirem após a cópia
 
**Causa** 

Se você vir que sua unidade não tem dados depois de ter sido desmontada (embora os dados tenham sido copiados para ela), talvez você tenha remontado uma unidade como leitura-gravação depois de a unidade ter sido montada como somente leitura.

**Resolução**
 
Se esse for o caso, confira a resolução para [unidades sendo montadas como somente leitura](#issue-drive-getting-mounted-as-read-only).

Se esse não for o caso, copie os logs da pasta que tem a ferramenta de desbloqueio do Data Box Disk e [entre em contato com o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Erros da ferramenta de cópia de divisão do Data Box Disk

Os problemas enfrentados ao usar uma ferramenta de cópia de divisão para dividir os dados em vários discos são resumidos na tabela a seguir.

|Mensagem de erro/avisos |Recomendações |
|---------|---------|
|[Info] Recuperação de senha do BitLocker para o volume: m <br>[Erro] Exceção detectada ao recuperar a chave do BitLocker para m: de volume<br> A sequência não contém elementos.|Esse erro será gerado se o Data Box Disk de destino estiver offline. <br> Use a ferramenta `diskmgmt.msc` em discos online.|
|[Erro] Exceção gerada: A operação de WMI falhou:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = o formato da senha de recuperação fornecida é inválido. <br>As senhas de recuperação do BitLocker têm 48 dígitos. <br>Verifique se a senha de recuperação está no formato correto e, em seguida, tente novamente.|Use a ferramenta de desbloqueio do Data Box Disk para desbloquear os discos e repita o comando. Para obter mais informações, acesse <li> [Desbloquear Data Box Disk para clientes Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Desbloquear Data Box Disk para clientes Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Erro] Exceção gerada: Existe um arquivo de DriveManifest.xml na unidade de destino. <br> Isso indica que a unidade de destino pode ter sido preparada com um arquivo de diário diferente. <br>Para adicionar mais dados à mesma unidade, use o arquivo de diário anterior. Para excluir dados existentes e reutilizar a unidade de destino para um novo trabalho de importação, exclua o *DriveManifest.xml* na unidade. Execute o comando novamente com um novo arquivo de diário.| Esse erro é recebido quando você tenta usar o mesmo conjunto de unidades para várias sessões de importação. <br> Use um conjunto de unidades apenas para uma sessão de divisão e cópia.|
|[Erro] Exceção gerada: CopySessionId importdata-sept-test-1 se refere a uma sessão de cópia anterior e não pode ser reutilizado para uma nova sessão de cópia.|Esse erro é relatado ao tentar usar o mesmo nome de um trabalho anterior concluído com êxito para um novo trabalho.<br> Atribua um nome exclusivo para seu trabalho.|
|[Informações] O nome do arquivo ou diretório de destino ultrapassa o limite de comprimento NTFS. |Esta mensagem é relatada quando o arquivo de destino foi renomeado devido a um caminho de arquivo longo.<br> Modifique a opção de descarte no arquivo `config.json` para controlar esse comportamento.|
|[Erro] Exceção gerada: Sequência de escape JSON inválida. |Esta mensagem é relatada quando o config.json tem formato que não é válido. <br> Validar sua `config.json` usando [JSONlint](https://jsonlint.com/) antes de salvar o arquivo.|


## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas da ferramenta de validação](data-box-disk-troubleshoot.md).

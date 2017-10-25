---
title: 'Backup do Azure: backups consistentes com o aplicativo de VMs Linux | Microsoft Docs'
description: "Use scripts para garantir backups consistentes com o aplicativo no Azure, para máquinas virtuais do Linux. Os scripts se aplicam somente às VMs do Linux em uma implantação do Resource Manager. Os scripts não se aplicam a VMs do Windows ou implantações do Service Manager. Este artigo o guiará durante as etapas para configurar os scripts, incluindo a solução de problemas."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: backup consistente com o aplicativo; backup de VM do Azure consistente com o aplicativo; Backup de VM Linux; Backup do Azure
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Backup consistente com o aplicativo de VMs Linux do Azure (versão prévia)

Este artigo fala sobre a estrutura pré e pós-script do Linux e como ela pode ser usada para fazer backups consistentes com o aplicativo de VMs Linux do Azure.

> [!Note]
> Só há suporte para a estrutura pré e pós-script para máquinas virtuais Linux implantadas pelo Azure Resource Manager. Não há suporte a scripts para consistência com aplicativos em máquinas virtuais implantadas pelo Service Manager ou máquinas virtuais do Windows.
>

## <a name="how-the-framework-works"></a>Como funciona a estrutura

A estrutura fornece uma opção para executar pré e pós-scripts personalizados ao obter instantâneos de VM. Os pré-scripts são executados imediatamente antes de você obter o instantâneo da VM e os pós-scripts são executados imediatamente após você obter o instantâneo da VM. Isso oferece a flexibilidade para controlar seus aplicativos e o ambiente enquanto está obtendo instantâneos da VM.

Neste cenário, é importante garantir o backup da VM de forma consistente com o aplicativo. O pré-script de pode invocar as APIs nativas do aplicativo para desativar as E/Ss e liberar o conteúdo da memória para o disco. Isso garante que o instantâneo seja consistente com o aplicativo (isto é, que o aplicativo surja quando a VM for iniciada após a restauração). O pós-script pode ser usado para descongelar as E/Ss. Ele faz isso usando APIs nativas do aplicativo, de modo que o aplicativo possa retomar as operações normais após o instantâneo da VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Etapas para configurar o pré-script e o pós-script

1. Entre como o usuário raiz da VM do Linux da qual você deseja fazer backup.

2. Baixe **VMSnapshotScriptPluginConfig.json** do [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e copie-o para a pasta **/etc/azure** em todas as VMs das quais fará o backup. Crie o diretório **/etc/azure** se ele ainda não existir.

3. Copie o pré-script e o pós-script para seu aplicativo em todas as VMs das quais você planeja fazer backup. Você pode copiar os scripts em qualquer local na VM. Certifique-se de atualizar o caminho completo dos arquivos de script no arquivo **VMSnapshotScriptPluginConfig.json**.

4. Verifique se estas permissões existem para os arquivos:

   - **VMSnapshotScriptPluginConfig.json**: permissão “600.” Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura" e "gravação" para esse arquivo e nenhum usuário deve ter permissões de "execução".

   - **Arquivo de pré-script**: permissão "700".  Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.
  
   - **Pós-script** permissão "700". Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.

   > [!Important]
   > A estrutura concede aos usuários muito poder. É importante que ela seja segura e que apenas o usuário “raiz” tenha acesso aos arquivos de script e JSON críticos.
   > Se os requisitos anteriores não forem atendidos, o script não será executado. Isso resulta em backup consistente de sistema de arquivos/falha.
   >

5. Configure o **VMSnapshotScriptPluginConfig.json** conforme descrito aqui:
    - **pluginName**: deixe esse campo como está ou os scripts podem não funcionar conforme o esperado.

    - **preScriptLocation**: forneça o caminho completo do pré-script na VM que passará pelo backup.

    - **postScriptLocation**: forneça o caminho completo do pós-script na VM que passará pelo backup.

    - **preScriptParams**: forneça os parâmetros opcionais que precisam ser passados para o pré-script. Todos os parâmetros deverão estar entre aspas e deverão ser separados por vírgulas se houver vários parâmetros.

    - **postScriptParams**: forneça os parâmetros opcionais que precisam ser passados para o pós-script. Todos os parâmetros deverão estar entre aspas e deverão ser separados por vírgulas se houver vários parâmetros.

    - **preScriptNoOfRetries**: defina o número de vezes que o pré-script deve ser repetido caso haja qualquer erro antes do encerramento. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.

    - **postScriptNoOfRetries**: defina o número de vezes que o pós-script deve ser repetido caso haja qualquer erro antes do encerramento. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.
    
    - **timeoutInSeconds**: especifique os tempos limites individuais para o pré-script e o pós-script.

    - **continueBackupOnFailure**: defina esse valor como **true** se você quiser que o Backup do Azure passe por fallback para um backup consistente com falha/ consistente com o sistema de arquivos, se o pré-script ou pós-script falhar. Definir isso como **false** causará a falha do backup no caso de falha do script (exceto quando você tiver uma VM de disco único que passa por fallback par a um backup consistente com a falha independentemente dessa configuração).

    - **fsFreezeEnabled**: especifique se Linux fsfreeze deve ser chamado durante a obtenção do instantâneo da VM para garantir a consistência do sistema de arquivos. Recomendamos manter essa configuração como **true**, a menos que seu aplicativo tenha dependência na desabilitação do fsfreeze.

6. A estrutura de script está configurada. Se o backup da VM já estiver configurado, o próximo backup invocará os scripts e disparará o backup consistente com o aplicativo. Se o backup da VM não estiver configurado, configure-o usando [Fazer backup de máquinas virtuais do Azure em cofres dos Serviços de Recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Solucionar problemas

Adicione os logs apropriados ao escrever seu pré-script e pós-script e examine os logs de script para corrigir quaisquer problemas de script. Se você ainda tiver problemas para executar scripts, veja a tabela a seguir para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |O pré-script retornou um erro, portanto talvez o backup não seja consistente com o aplicativo.   | Examine os logs de falha do script para corrigir o problema.|  
|   Post-ScriptExecutionFailed |    O pós-script retornou um erro que pode afetar o estado do aplicativo. |    Examine os logs de falha do script para corrigir o problema e verificar o estado do aplicativo. |
| Pre-ScriptNotFound |  O pré-script não foi encontrado no local especificado no arquivo de configuração **VMSnapshotScriptPluginConfig.json**. |   Verifique se o pré-script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| Post-ScriptNotFound | O pós-script não foi encontrado no local especificado no arquivo de configuração **VMSnapshotScriptPluginConfig.json**. |   Verifique se o pós-script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| IncorrectPluginhostFile | O arquivo **Pluginhost**, que vem com a extensão VmSnapshotLinux, está corrompido, portanto, o pré-script e o pós-script não podem ser executados e o backup não será consistente com o aplicativo. | Desinstale a extensão **VmSnapshotLinux** e ela será automaticamente instalada novamente com o próximo backup para corrigir o problema. |
| IncorrectJSONConfigFile | O arquivo **VMSnapshotScriptPluginConfig.json** está incorreto, portanto, o pré-script e pós-script não podem ser executados e o backup não será consistente com o aplicativo. | Baixe a cópia do [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configure-a novamente. |
| InsufficientPermissionforPre-Script | Para executar scripts, o usuário "raiz" deve ser o proprietário do arquivo e o arquivo deve ter permissões "700" (ou seja, somente o proprietário deve ter permissões de "leitura", "gravação" e "execução"). | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente o "proprietário" tem permissões de "leitura", "gravação" e "execução". |
| InsufficientPermissionforPost-Script | Para executar scripts, o usuário raiz deve ser o proprietário do arquivo e o arquivo deve ter permissões "700" (ou seja, somente o proprietário deve ter permissões de "leitura", "gravação" e "execução"). | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente o "proprietário" tem permissões de "leitura", "gravação" e "execução". |
| Pre-ScriptTimeout | A execução do pré-script de backup consistente com o aplicativo atingiu o tempo limite. | Verifique o script e aumente o tempo limite no arquivo **VMSnapshotScriptPluginConfig.json** que está localizado em **/etc/azure**. |
| Post-ScriptTimeout | A execução do pós-script de backup consistente com o aplicativo atingiu o tempo limite. | Verifique o script e aumente o tempo limite no arquivo **VMSnapshotScriptPluginConfig.json** que está localizado em **/etc/azure**. |

## <a name="next-steps"></a>Próximas etapas
[Configurar o backup da VM para um cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

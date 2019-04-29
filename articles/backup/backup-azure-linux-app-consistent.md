---
title: 'Backup do Azure: backups consistentes com o aplicativo de VMs Linux'
description: Criar backups consistentes com o aplicativo para máquinas virtuais do Linux no Azure. Este artigo explica como configurar a estrutura de script para fazer backup de VMs Linux implantadas no Azure. Este artigo também inclui informações de solução de problemas.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: backup consistente com o aplicativo; backup de VM do Azure consistente com o aplicativo; Backup de VM Linux; Backup do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641119"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Backup consistente com o aplicativo de VMs Linux do Azure

Ao fazer instantâneos de backup de suas VMs, consistência com o aplicativo significa que seus aplicativos são inicializados quando as VMs são inicializadas após serem restauradas. Como você pode imaginar, a consistência com o aplicativo é extremamente importante. Para garantir que suas VMs Linux sejam consistentes, você pode usar a estrutura de pré-script e pós-script do Linux para fazer backups consistentes com aplicativos. A estrutura de pré-script e pós-script é compatível com máquinas virtuais do Linux implantadas pelo Azure Resource Manager. Scripts para consistência com aplicativos não dão suporte a máquinas virtuais implantadas pelo Service Manager ou a máquinas virtuais do Windows.

## <a name="how-the-framework-works"></a>Como funciona a estrutura

A estrutura fornece uma opção para executar pré e pós-scripts personalizados ao obter instantâneos de VM. Os pré-scripts são executados imediatamente antes de você obter o instantâneo da VM e os pós-scripts são executados imediatamente após você obter o instantâneo da VM. Pré-scripts e pós-scripts oferecem a flexibilidade para controlar seus aplicativos e o ambiente enquanto está obtendo instantâneos da VM.

Pré-scripts invocam APIs nativas do aplicativo, que fecham as E/Ss para novas seções, e liberam o conteúdo da memória para o disco. Essas ações garantem que o instantâneo seja consistente com aplicativo. Pós-scripts usam APIs de aplicativo nativo para descongelar as E/Ss, o que permite que o aplicativo retome as operações normais após o instantâneo da VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Etapas para configurar o pré-script e o pós-script

1. Entre como o usuário raiz da VM do Linux da qual você deseja fazer backup.

2. No [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), baixe **VMSnapshotScriptPluginConfig.json** e copie-o para a pasta **/etc/azure** em todas as VMs de que deseja fazer backup. Se a pasta **/etc/azure** não existir, crie-a.

3. Copie o pré-script e o pós-script para seu aplicativo em todas as VMs das quais você planeja fazer backup. Você pode copiar os scripts em qualquer local na VM. Certifique-se de atualizar o caminho completo dos arquivos de script no arquivo **VMSnapshotScriptPluginConfig.json**.

4. Verifique se estas permissões existem para os arquivos:

   - **VMSnapshotScriptPluginConfig.json**: Permissão "600". Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura" e "gravação" para esse arquivo e nenhum usuário deve ter permissões de "execução".

   - **Arquivo de pré-script**: Permissão "700".  Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.

   - **Pós-script** permissão "700". Por exemplo, apenas o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.

   > [!Important]
   > A estrutura concede aos usuários muito poder. Proteja a estrutura e garanta que somente o usuário “raiz” tenha acesso aos arquivos de script e JSON críticos.
   > Se os requisitos não forem atendidos, o script não será executado, o que resultará em uma falha do sistema de arquivos e em um backup inconsistente.
   >

5. Configure o **VMSnapshotScriptPluginConfig.json** conforme descrito aqui:
    - **pluginName**: Deixe esse campo como está, ou talvez os scripts não funcionem conforme o esperado.

    - **preScriptLocation**: Forneça o caminho completo do pré-script na VM que passará pelo backup.

    - **postScriptLocation**: Forneça o caminho completo do pós-script na VM que passará pelo backup.

    - **preScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o pré-script. Todos os parâmetros devem estar entre aspas. Se usar vários parâmetros, separe-os com uma vírgula.

    - **postScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o pós-script. Todos os parâmetros devem estar entre aspas. Se usar vários parâmetros, separe-os com uma vírgula.

    - **preScriptNoOfRetries**: Defina o número de vezes que o pré-script deve ser repetido caso haja qualquer erro antes do encerramento. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.

    - **postScriptNoOfRetries**:  Defina o número de vezes que o pós-script deve ser repetido caso haja qualquer erro antes do encerramento. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.

    - **timeoutInSeconds**: Especifique os tempos limites individuais para o pré-script e o pós-script (o valor máximo pode ser 1800).

    - **continueBackupOnFailure**: Defina esse valor como **true** se quiser que o Backup do Azure passe por fallback para um backup consistente com falha/consistente com o sistema de arquivos, se o pré-script ou pós-script falhar. Definir isso como **false** causará a falha do backup no caso de falha do script (exceto quando você tiver uma VM de disco único que passa por fallback par a um backup consistente com a falha independentemente dessa configuração).

    - **fsFreezeEnabled**: Especifique se Linux fsfreeze deve ser chamado durante a obtenção do instantâneo da VM para garantir a consistência do sistema de arquivos. Recomendamos manter essa configuração como **true**, a menos que seu aplicativo tenha dependência na desabilitação do fsfreeze.

6. A estrutura de script está configurada. Se o backup da VM já estiver configurado, o próximo backup invocará os scripts e disparará o backup consistente com o aplicativo. Se o backup da VM não estiver configurado, configure-o usando [Fazer backup de máquinas virtuais do Azure em cofres dos Serviços de Recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>solução de problemas

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

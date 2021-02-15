---
title: Recuperar de uma falha em toda a região
description: Saiba como Azure App serviço ajuda a manter os recursos de BCDR (continuidade dos negócios e recuperação de desastre). Recupere seu aplicativo de uma falha em toda a região no Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073743"
---
# <a name="move-an-app-service-app-to-another-region"></a>Mover um aplicativo do serviço de aplicativo para outra região

Este artigo descreve como colocar os recursos do serviço de aplicativo online novamente em uma região do Azure diferente durante um desastre que afete toda a região do Azure. Quando um desastre coloca toda a região do Azure offline, todos os aplicativos do serviço de aplicativo hospedados nessa região são colocados no modo de recuperação de desastre. Os recursos estão disponíveis para ajudá-lo a restaurar o aplicativo para uma região diferente ou recuperar arquivos do aplicativo afetado.

Os recursos do serviço de aplicativo são específicos da região e não podem ser movidos entre regiões. Você deve restaurar o aplicativo para um novo aplicativo em uma região diferente e, em seguida, criar configurações de espelhamento ou recursos para o novo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum. A [restauração do instantâneo](app-service-web-restore-snapshots.md) geralmente requer a camada **Premium** , mas no modo de recuperação de desastre, ela é habilitada automaticamente para seu aplicativo afetado, independentemente da camada em que o aplicativo afetado está.

## <a name="prepare"></a>Preparar

Identifique todos os recursos do serviço de aplicativo que o aplicativo afetado usa atualmente. Por exemplo:

- Aplicativos do Serviço de Aplicativo
- [Planos do Serviço de Aplicativo](overview-hosting-plans.md)
- [Slots de implantação](deploy-staging-slots.md)
- [Domínios personalizados comprados no Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração de rede virtual do Azure](web-sites-integrate-with-vnet.md)
- [Conexões híbridas](app-service-hybrid-connections.md).
- [identidades gerenciadas](overview-managed-identity.md)
- [Configurações de backup](manage-backup.md)

Determinados recursos, como certificados importados ou conexões híbridas, contêm integração com outros serviços do Azure. Para obter informações sobre como mover esses recursos entre regiões, consulte a documentação para os respectivos serviços.

## <a name="restore-app-to-a-different-region"></a>Restaurar o aplicativo para uma região diferente

1. Crie um novo aplicativo do serviço de aplicativo em uma região do Azure *diferente* do aplicativo afetado. Esse é o aplicativo de destino no cenário de recuperação de desastre.

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo afetado. Em uma região do Azure com falha, o aplicativo afetado mostra um texto de aviso. Clique no texto de aviso.

    ![Captura de tela da página do aplicativo impactado. Uma notificação de aviso é visível que descreve a situação e fornece um link para restaurar o aplicativo.](media/manage-disaster-recovery/restore-start.png)

1. Na página **restaurar backup** , configure a operação de restauração de acordo com a tabela a seguir. Quando terminar, clique em **OK**.

   | Configuração | Valor | Descrição |
   |-|-|-|
   | **Instantâneo (visualização)** | Selecione um instantâneo. | Os dois instantâneos mais recentes estão disponíveis. |
   | **Restaurar destino** | **Aplicativo existente** | Clique na observação abaixo que diz **clique aqui para alterar o aplicativo de destino de restauração** e selecione o aplicativo de destino. Em um cenário de desastre, você só pode restaurar o instantâneo para um aplicativo em uma região diferente do Azure. |
   | **Restaurar configuração de site** | **Sim** | |

    ![Captura de tela da página restaurar backup. Um instantâneo específico, as opções listadas na tabela anterior e o botão OK são realçados.](media/manage-disaster-recovery/restore-configure.png)

3. Configure [todo o resto](#prepare) do aplicativo de destino para espelhar o aplicativo afetado e verificar sua configuração.

4. Quando você estiver pronto para o domínio personalizado apontar para o aplicativo de destino, [remapeie o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Recuperar somente o conteúdo do aplicativo

Se você quiser apenas recuperar os arquivos do aplicativo afetado sem restaurá-los, use as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo afetado e clique em **obter perfil de publicação**.

    ![Captura de tela da página do aplicativo impactado. Uma notificação de aviso está visível, mas não está realçada. Em vez disso, o item obter perfil de publicação será realçado.](media/manage-disaster-recovery/get-publish-profile.png)

1. Abra o arquivo baixado e localize o perfil de publicação que contém `ReadOnly - FTP` em seu nome. Este é o perfil de recuperação de desastre. Por exemplo:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Copie três valores de atributo: 
        
    - `publishUrl`: o nome de host do FTP
    - `userName` e `userPWD` : as credenciais de FTP

1. Use o cliente FTP de sua escolha, conecte-se ao host FTP do aplicativo afetado usando o nome do host e as credenciais.

1. Uma vez conectado, baixe a pasta */site/wwwroot* inteira. A captura de tela a seguir mostra como baixar no [FileZilla](https://filezilla-project.org/).

    ![Captura de tela de uma hierarquia de arquivos do FileZilla. A pasta wwwroot é realçada e seu menu de atalho fica visível. Nesse menu, o download é realçado.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Próximas etapas
[Restaurar um aplicativo no Azure a partir de um instantâneo](app-service-web-restore-snapshots.md)

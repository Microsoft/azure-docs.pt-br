---
title: Problemas conhecidos/limitações de migração com o uso do modo híbrido
description: Saiba mais sobre problemas conhecidos/limitações de migração com o uso do serviço de migração de banco de dados do Azure no modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291836"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Problemas conhecidos/limitações de migração com o uso do modo híbrido

Os problemas conhecidos e as limitações associadas ao uso do serviço de migração de banco de dados do Azure no modo híbrido são descritos nas seções a seguir.

## <a name="installer-fails-to-authenticate"></a>Falha na autenticação do instalador

Depois de carregar o certificado para seu AdApp, há um atraso de até alguns minutos antes que ele possa ser autenticado com o Azure. O instalador tentará repetir com algum atraso, mas é possível que o atraso de propagação seja maior que a repetição, e você verá uma mensagem **FailedToGetAccessTokenException** . Se o certificado foi carregado no AdApp correto e a AppId correta foi fornecida no dmsSettings.jsem, tente executar o comando de instalação novamente.

## <a name="service-offline-after-successful-installation"></a>Serviço "offline" após a instalação bem-sucedida

Se o serviço aparecer como offline depois que o processo de instalação for concluído com êxito, tente usar as etapas a seguir.

1. Na portal do Azure, na sua instância do serviço de migração de banco de dados do Azure, navegue até a guia configurações **híbridas** e verifique se o trabalhador está registrado verificando a grade de trabalhos registrados.

    O status desse trabalhador deve estar **online**, mas pode aparecer como **offline** se houver um problema.

2. No computador de trabalho, verifique o status do serviço executando o seguinte comando do PowerShell:

    ```
    Get-Service Scenario*
    ```

    Esse comando fornece o status do serviço do Windows que executa o trabalho. Deve haver apenas um único resultado. Se o trabalho for interrompido, você poderá tentar reiniciá-lo usando o seguinte comando do PowerShell:

    ```
    Start-Service Scenario*
    ```

    Você também pode verificar o serviço na interface do usuário de serviços do Windows.

3. Se o serviço do Windows ciclos entre executar e parar, o trabalho encontrou problemas de inicialização. Verifique os logs de trabalho híbrido do serviço de migração de banco de dados do Azure para determinar o problema.

    - Os logs de processo de instalação são armazenados na pasta "logs" dentro da pasta da qual o executável do instalador foi executado.
    - Os logs de trabalho híbrido do serviço de migração de banco de dados do Azure são armazenados na pasta **WorkerLogs** , na pasta em que o trabalho está instalado. O local padrão para os arquivos de log do Hybrid Worker é **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Usando seu próprio certificado assinado

O certificado gerado pela ação GenerateCert é um certificado autoassinado, que pode não ser aceitável com base em suas políticas de segurança interna. Em vez de usar esse certificado, você pode fornecer seu próprio certificado e fornecer a impressão digital no dmsSettings.jsem. Esse certificado precisará ser carregado em seu AdApp e instalado no computador no qual você está instalando o Hybrid Worker do serviço de migração de banco de dados do Azure. Em seguida, instale esse certificado com a chave privada no repositório de certificados do computador local.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Executando o serviço de trabalho como uma conta de baixo privilégio

Por padrão, o serviço de trabalho híbrido do serviço de migração de banco de dados do Azure é executado como a conta do sistema local. Você pode alterar a conta usada para esse serviço, contanto que a conta que você usa tenha permissões de rede. Para alterar a conta ' Executar como ' do serviço, use o processo a seguir.

1. Pare o serviço, seja por meio dos serviços do Windows ou usando o comando Stop-Service no PowerShell.

2. Atualize o serviço para usar uma conta de logon diferente.

3. No certmgr para certificados de computador local, conceda permissões de chave privada para a nova conta para a **chave do aplicativo híbrido DMS** e certificados do **par de chaves do mecanismo de cenário DMS** .

    a. Abra o certmgr para exibir as seguintes chaves:

    - Chave de aplicativo híbrido do DMS
    - Chave de instalação do DMS Hybrid Worker
    - Par de chaves do mecanismo de cenário DMS

    b. Clique com o botão direito do mouse na entrada de **chave do aplicativo híbrido DMS** , aponte para **todas as tarefas** e, em seguida, selecione **gerenciar chaves privadas**.

    c. Na guia **segurança** , selecione **Adicionar** e, em seguida, digite o nome da conta.

    d. Use as mesmas etapas para conceder a permissão de chave privada para a nova conta para o certificado de **par de chaves do mecanismo de cenário DMS** .

## <a name="unregistering-the-worker-manually"></a>Cancelando o registro do trabalho manualmente

Se você não tiver mais acesso ao computador de trabalho, poderá cancelar o registro do trabalho e reutilizar a instância do serviço de migração de banco de dados do Azure executando as seguintes etapas:

1. No portal do Azure, obtido com a instância do serviço de migração de banco de dados do Azure e, em seguida, navegue até a página de configurações **híbridas** .

   Sua entrada de trabalho aparece na lista, com o status exibido como **offline**.

2. Na extrema direita da listagem de entrada de trabalho, selecione as reticências e, em seguida, selecione **Cancelar registro**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Solucionando problemas para cenários de migração específicos

As seções a seguir descrevem problemas específicos do cenário relacionados ao uso do modo híbrido do serviço de migração de banco de dados do Azure para executar uma migração online.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Migrações online para o Azure SQL Instância Gerenciada

**Alto uso da CPU**

**Problema**: para migrações online para o SQL instância gerenciada, o computador que executa o Hybrid Worker encontrará alto uso da CPU se houver muitos backups ou se os backups forem muito grandes.

**Mitigação**: para atenuar esse problema, use backups compactados, divida a migração para que ele use vários compartilhamentos ou escale verticalmente o computador que executa o Hybrid Worker.

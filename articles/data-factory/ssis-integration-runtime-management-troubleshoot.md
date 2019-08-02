---
title: Solucionar problemas de gerenciamento de Integration Runtime do SSIS no Azure Data Factory | Microsoft Docs
description: Este artigo fornece orientação para a solução de problemas de gerenciamento do SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253027"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Solucionar problemas de gerenciamento de Integration Runtime do SSIS no Azure Data Factory

Este documento fornece guias para solução de problemas de gerenciamento do SSIS Integration Runtime (SSIS IR).

## <a name="overview"></a>Visão geral

Haverá uma mensagem de erro no portal do ADF ou retornado do cmdlet do PowerShell se houver algum problema no provisionamento ou desprovisionamento do SSIS IR. O erro está sempre no formato como um código de erro com uma mensagem de erro detalhada.

Isso significa que o serviço tem alguns problemas transitórios se o código de erro for InternalServerError. Você pode repetir a operação mais tarde. Contate a equipe de suporte do Azure Data Factory se a repetição não ajudar.

Há três principais dependências externas que podem causar erros: Servidor de banco de dados SQL do Azure ou Instância Gerenciada, script de instalação personalizada e configuração de rede virtual se o código de erro não for InternalServerError.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemas de servidor do banco de dados SQL do Azure ou instância gerenciada

Um servidor de banco de dados SQL do Azure ou Instância Gerenciada é necessário se estiver Provisionando o SSIS IR com o banco de dados do catálogo do SSIS. O servidor ou Instância Gerenciada do banco de dados SQL do Azure deve ser acessível pelo IR do SSIS. A conta do servidor de banco de dados SQL do Azure ou Instância Gerenciada deve ter a permissão para criar o SSISDB (banco de dados de catálogo do SSIS). Se houver algum erro, um código de erro com detalhes de mensagem de exceção SQL será mostrado no portal do ADF. Siga as etapas abaixo para solucionar os códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Você poderá ver esse problema ao provisionar um novo IR do SSIS ou durante a execução do IR.

Isso pode ser causado pelos seguintes motivos se você vir o erro durante o provisionamento de IR e puder obter a mensagem de SqlException de detalhes na mensagem de erro.

* Problema de conexão de rede. Verifique se o nome do host do SQL Server ou do Instância Gerenciada está acessível e se não há nenhum firewall ou NSG bloqueia o IR do SSIS para acessar o servidor.
* O logon falhou e a autenticação do SQL é usada. Isso significa que a conta fornecida não pode fazer logon no SQL Server. Verifique se a conta de usuário correta foi fornecida.
* O logon falhou e a autenticação do AAD (identidade gerenciada) é usada. Adicione a identidade gerenciada de sua fábrica a um grupo do AAD e faça com que a identidade gerenciada tenha permissões de acesso ao seu servidor de banco de dados de catálogo.
* Tempo limite de conexão é sempre devido à configuração relacionada à segurança. É recomendável criar uma nova VM, fazer com que a VM ingresse na mesma VNet do IR se o IR estiver em uma VNet e, em seguida, instalar o SSMS e verificar o status de Instância Gerenciada ou o servidor do banco de dados SQL do Azure.

Para outros problemas, consulte a mensagem de erro de exceção do SQL de detalhes e corrija o problema mostrado na mensagem de erro. Contate o servidor de banco de dados SQL do Azure ou Instância Gerenciada equipe de suporte se você ainda tiver problemas.

É provável que haja algumas alterações de firewall ou grupo de segurança de rede se você vir o erro durante a execução do IR, o que faz com que o nó de trabalho de IR do SSIS não possa mais acessar o servidor do banco de dados SQL do Azure ou Instância Gerenciada. Desbloqueie o nó de trabalho do SSIS IR para acessar o servidor do banco de dados SQL do Azure ou Instância Gerenciada.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

A mensagem de erro é como "o banco de dados ' SSISDB ' atingiu sua cota de tamanho. Particione ou exclua dados, remova índices ou consulte a documentação para obter as possíveis resoluções. " As soluções possíveis são:
* Aumente a cota de tamanho do SSISDB.
* Altere as configurações de SSISDB para reduzir o tamanho como:
   * Reduzindo o período de retenção e o número de versões do projeto.
   * Reduzindo o período de retenção do log.
   * Alterar o nível padrão do log e assim por diante.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Esse erro significa que o servidor de banco de dados SQL do Azure ou Instância Gerenciada já tem um SSISDB criado e usado por outro IR. Você precisa fornecer um servidor de banco de dados SQL do Azure diferente ou Instância Gerenciada ou excluir o SSISDB existente e reiniciar o novo IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Esse erro pode ser causado por motivos abaixo,

* A conta de usuário configurada para o IR do SSIS não tem permissão para criar o banco de dados. Você pode conceder ao usuário permissão para criar o banco de dados.
* Criar tempo limite de banco de dados, como tempo limite de execução, tempo limite de operação de BD, etc. Você pode tentar novamente mais tarde para ver se o problema foi resolvido. Contate o servidor de banco de dados SQL do Azure ou Instância Gerenciada equipe de suporte se a repetição não funcionar.

Para outros problemas, verifique a mensagem de erro de exceção do SQL e corrija o problema mencionado na mensagem de erro. Se você ainda tiver problemas, contate o servidor de banco de dados SQL do Azure ou a equipe de suporte do Instância Gerenciada.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

A mensagem de erro é como "nome de objeto inválido" Catalog. catalog_properties ".", isso significa que você já tem um banco de dados chamado SSISDB, mas não é criado pelo IR do SSIS ou o banco de dados está em um estado inválido que é causado por erros no último provisionamento de IR do SSIS. Você pode remover o banco de dados existente com o nome SSISDB ou configurar um novo servidor de banco de dados SQL do Azure ou Instância Gerenciada para o IR.

## <a name="custom-setup"></a>Instalação personalizada

A instalação personalizada fornece uma interface para adicionar suas próprias etapas de configuração durante o provisionamento ou reconfiguração do seu IR do SSIS. Para obter mais informações, consulte [Personalizar a instalação para o tempo de execução de integração do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verifique se o contêiner contém apenas os arquivos de instalação personalizados necessários, pois todos os arquivos no contêiner serão baixados no nó de trabalho do SSIS IR. É recomendável testar o script de instalação personalizada em um computador local para corrigir qualquer problema de execução de script antes de executar o script no SSIS IR.

O contêiner de script de instalação personalizada será verificado durante o IR em execução também, pois o IR do SSIS será atualizado regularmente, o que precisará acessar o contêiner novamente para baixar o script de instalação personalizado e instalá-lo novamente. A verificação incluirá se o contêiner está acessível e se o arquivo Main. cmd existe.

Qualquer erro com a instalação personalizada, você verá um erro com o código CustomSetupScriptFailure, verificará a mensagem de erro que tem um código de suberro.  Siga as etapas abaixo para solucionar os códigos de sub-erro.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Isso significa que o SSIS IR não pode acessar seu contêiner de blob do Azure para a instalação personalizada. Verifique se o URI de SAS do contêiner está acessível e não expirou.

Pare o IR primeiro se o IR estiver em estado de execução, reconfigure o IR com o novo URI de SAS do contêiner de instalação personalizada e inicie o IR novamente.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Isso significa que o SSIS IR não pode localizar o script de instalação personalizada (Main. cmd) em seu contêiner de BLOB. Verifique se Main. cmd existe no contêiner, que é o ponto de entrada para instalação personalizada da instalação.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Isso significa que a execução do script de instalação personalizada (Main. cmd) falhou, você pode tentar o script no computador local primeiro ou verificar os logs de execução de instalação personalizada em seu contêiner de BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Isso significa executar o tempo limite do script de instalação personalizada. Verifique se o contêiner de BLOB contém apenas os arquivos de instalação personalizados necessários. Você também pode verificar os logs de execução de instalação personalizada em seu contêiner de BLOB. O período máximo para a configuração personalizada é definido em 45 minutos antes de atingir o tempo limite e o período máximo inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no IR do SSIS. Se um período mais longo for necessário, gere um tíquete de suporte.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Isso significa que o carregamento de logs de execução de instalação personalizada para seu contêiner de BLOBs falhou, pois o IR do SSIS não tem permissão de gravação para o contêiner de BLOB ou alguns problemas de armazenamento ou rede. Se a instalação personalizada for bem-sucedida, esse erro não afetará nenhuma função do SSIS, mas os logs estão ausentes. Se a instalação personalizada falhar com outro erro e não for possível carregar o log, relataremos esse erro primeiro para que o log possa ser carregado para análise e, depois que esse problema for resolvido, relataremos um problema mais especificado. Se esse problema não for resolvido após a repetição, contate Azure Data Factory equipe de suporte.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Ao unir o SSIS IR em uma rede virtual (VNet), ele usa a VNet em assinatura de usuário. Para obter mais informações, consulte [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando houver um problema relacionado à VNet, você verá um erro como abaixo

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Isso pode ser causado por motivos de variante. Siga as etapas abaixo para solucionar os códigos de sub-erro.

### <a name="forbidden"></a>Proibido

A mensagem de erro é como "subnetid não está habilitada para a conta atual. O provedor de recursos Microsoft. batch não está registrado na mesma assinatura da VNet. "

Isso significa que o lote do Azure não pode acessar sua VNet. Registre o provedor de recursos Microsoft. batch na mesma assinatura da VNet.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

A mensagem de erro é como "ou a VNet especificada não existe, ou o serviço de lote não tem acesso a ela" ou "a sub-rede xxx especificada não existe".

Isso significa que a VNet não existe ou o serviço de lote do Azure não pode acessá-la ou a sub-rede fornecida não existe. Verifique se a VNet e a sub-rede existem e se o lote do Azure pode acessá-las.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

A mensagem é como "falha ao provisionar Integration Runtime na vnet. Se as configurações do servidor DNS ou do NSG estiverem configuradas, verifique se o servidor DNS está acessível e se NSG está configurado corretamente "

É provável que você tenha alguma configuração personalizada do servidor DNS ou das configurações de NSG, o que causa que o nome do servidor do Azure exigido pelo IR do SSIS não pode ser resolvido ou não pode ser acessado. Para obter mais informações, consulte o documento de [configuração de VNet do SSIS ir](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) . Se você ainda tiver problemas, contate a equipe de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O SSIS IR será automaticamente atualizado regularmente e um novo pool do lote do Azure será criado durante a atualização e o pool do lote do Azure antigo será excluído, o recurso relacionado à VNet para o pool antigo será excluído e o novo recurso relacionado à VNet será criado em seu scriçõe. Esse erro significa que a exclusão do recurso relacionado à VNet para o pool antigo falhou devido a um bloqueio de exclusão na assinatura ou no nível do grupo de recursos. Ajuda para remover o bloqueio de exclusão.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

O provisionamento de IR do SSIS pode falhar por causa de tipos de motivo e, se ocorrer uma falha, todos os recursos criados serão excluídos. No entanto, os recursos de VNet não podem ser excluídos porque há um bloqueio de exclusão de recurso no nível de assinatura ou grupo de recursos. Remova o bloqueio de exclusão e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Ao parar o IR do SSIS, todo o recurso relacionado à VNet será excluído, mas a exclusão falhou devido a haver um bloqueio de exclusão de recursos na assinatura ou no nível do grupo de recursos. Ajude a remover o bloqueio de exclusão e tente parar novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Esse erro ocorre durante a execução do IR, significa que o IR é integridade antes e se tornar não íntegro, pois é sempre devido ao servidor DNS ou à configuração NSG alterada e fazer com que o SSIS IR não possa se conectar ao serviço dependente, ajudar a corrigir os problemas de configuração do servidor DNS ou do NSG Para obter mais informações, consulte [configuração de VNet de ir do SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se você ainda tiver problemas, contate a equipe de suporte do Azure Data Factory.

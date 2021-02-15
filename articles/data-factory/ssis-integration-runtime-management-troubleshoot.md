---
title: Solucionar problemas de gerenciamento de Integration Runtime do SSIS
description: Este artigo fornece orientação para a solução de problemas de gerenciamento do SSIS Integration Runtime (SSIS IR)
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361835"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Solucionar problemas de gerenciamento de Integration Runtime do SSIS no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece orientação para a solução de problemas de gerenciamento no SSIS (Azure-SQL Server Integration Services) Integration Runtime (IR), também conhecido como IR do SSIS.

## <a name="overview"></a>Visão geral

Se você tiver algum problema ao provisionar ou desprovisionar o SSIS IR, verá uma mensagem de erro no portal do Microsoft Azure Data Factory ou um erro retornado de um cmdlet do PowerShell. O erro sempre aparece no formato de um código de erro com uma mensagem de erro detalhada.

Se o código de erro for InternalServerError, o serviço terá problemas transitórios e você deverá repetir a operação mais tarde. Se uma nova tentativa não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.

Caso contrário, três principais dependências externas podem causar erros: banco de dados SQL do Azure ou Azure SQL Instância Gerenciada, um script de instalação personalizada e uma configuração de rede virtual.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Problemas do banco de dados SQL ou do SQL Instância Gerenciada

O banco de dados SQL ou o SQL Instância Gerenciada será necessário se você estiver Provisionando o SSIS IR com um banco de dados de catálogo do SSIS. O IR do SSIS deve ser capaz de acessar o banco de dados SQL ou o SQL Instância Gerenciada. Além disso, a conta de logon para o banco de dados SQL ou o SQL Instância Gerenciada deve ter permissão para criar um SSISDB (banco de dados de catálogo do SSIS). Se houver um erro, um código de erro com uma mensagem de exceção SQL detalhada será mostrado no portal de Data Factory. Use as informações na lista a seguir para solucionar problemas de códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Você poderá ver esse problema quando estiver provisionando um novo IR do SSIS ou enquanto o IR estiver em execução. Se tiver esse erro durante o provisionamento de IR, você poderá obter uma mensagem de SqlException detalhada na mensagem de erro que indica um dos seguintes problemas:

* Um problema de conexão de rede. Verifique se o nome do host para o banco de dados SQL ou o SQL Instância Gerenciada está acessível. Verifique também se nenhum firewall ou grupo de segurança de rede (NSG) está bloqueando o acesso do SSIS IR ao servidor.
* Falha no logon durante a autenticação do SQL. A conta fornecida não pode entrar no banco de dados do SQL Server. Certifique-se de fornecer a conta de usuário correta.
* Falha de logon durante a autenticação do Microsoft Azure Active Directory (AD do Azure) (identidade gerenciada). Adicione a identidade gerenciada de sua fábrica a um grupo do AAD e verifique se a identidade gerenciada tem permissões de acesso ao seu servidor de banco de dados de catálogo.
* Tempo limite de conexão. Esse erro é sempre causado por uma configuração relacionada à segurança. É recomendável que você:
  1. Crie uma nova VM.
  1. Ingresse a VM no mesmo Rede Virtual do Microsoft Azure de IR se o IR estiver em uma rede virtual.
  1. Instale o SSMS e verifique o status do banco de dados SQL ou do SQL Instância Gerenciada.

Para outros problemas, corrija o problema mostrado na mensagem de erro de exceção SQL detalhada. Se você ainda tiver problemas, entre em contato com o banco de dados SQL ou com a equipe de suporte do SQL Instância Gerenciada.

Se você vir o erro quando o IR estiver em execução, as alterações de firewall ou grupo de segurança de rede provavelmente impedirão que o nó de trabalho de IR do SSIS acesse o banco de dados SQL ou o SQL Instância Gerenciada Desbloqueie o nó de trabalho do SSIS IR para que ele possa acessar o banco de dados SQL ou o SQL Instância Gerenciada.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Este tipo de mensagem de erro pode parecer com o seguinte: "o banco de dados ' SSISDB ' atingiu sua cota de tamanho. Particione ou exclua dados, remova índices ou consulte a documentação para obter as possíveis resoluções. " 

Entre as soluções possíveis estão:
* Aumente o tamanho da cota do SSISDB.
* Altere a configuração do SSISDB para reduzir o tamanho:
   * Reduzindo o período de retenção e o número de versões do projeto.
   * Reduzindo o período de retenção do log.
   * Alterando o nível padrão do log.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Esse erro significa que o banco de dados SQL ou o SQL Instância Gerenciada já tem um SSISDB e que está sendo usado por outro IR. Você precisa fornecer um banco de dados SQL diferente ou o SQL Instância Gerenciada ou excluir o SSISDB existente e reiniciar o novo IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Esse problema pode ocorrer por um dos seguintes motivos:

* A conta de usuário configurada para o IR do SSIS não tem permissão para criar o banco de dados. Você pode conceder ao usuário permissão para criar o banco de dados.
* Um tempo limite ocorre durante a criação do banco de dados, como um tempo limite de execução ou um tempo limite de operação de BD. Você deve repetir a operação mais tarde. Se a nova tentativa não funcionar, entre em contato com o banco de dados SQL ou com a equipe de suporte do SQL Instância Gerenciada.

Para outros problemas, verifique a mensagem de erro de exceção do SQL e corrija o problema mencionado nos detalhes do erro. Se você ainda tiver problemas, entre em contato com o banco de dados SQL ou com a equipe de suporte do SQL Instância Gerenciada.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Esse tipo de mensagem de erro é semelhante a: "nome de objeto inválido" catalog.catalog_properties "." Nessa situação, você já tem um banco de dados chamado SSISDB, mas ele não foi criado pelo IR do SSIS ou o banco de dados está em um estado inválido causado por erros no último provisionamento de IR do SSIS. Você pode remover o banco de dados existente com o nome SSISDB ou pode configurar um novo banco de dados SQL ou SQL Instância Gerenciada para o IR.

## <a name="custom-setup-issues"></a>Problemas de instalação personalizada

A configuração personalizada fornece uma interface para adicionar suas próprias etapas de configuração durante o provisionamento ou reconfigurar o IR do Azure-SSIS. Para obter mais informações, confira [Configuração personalizada para o Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

Verifique se o contêiner contém apenas os arquivos de configuração personalizados necessários; todos os arquivos no contêiner serão baixados no nó de trabalho do SSIS IR. Recomendamos que você teste o script de configuração personalizada em um computador local para corrigir qualquer problema de execução de script antes de executar o script no SSIS IR.

O contêiner de script de configuração personalizada será verificado enquanto o IR estiver em execução, pois o SSIS IR é atualizado regularmente. Essa atualização requer acesso ao contêiner para baixar o script de configuração personalizada e instalá-lo novamente. O processo também verifica se o contêiner está acessível e se o arquivo main.cmd existe.

Para qualquer erro que envolva a instalação personalizada, você verá um código de erro CustomSetupScriptFailure com o subcódigo, como CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Esse erro significa que o SSIS IR não pode acessar seu contêiner de blob do Azure para a configuração personalizada. Verifique se o URI de SAS do contêiner está acessível e se não expirou.

Pare o IR se ele estiver em execução, reconfigure o IR com o novo URI de SAS do contêiner de configuração personalizada e reinicie o IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Esse erro significa que o SSIS IR não consegue encontrar um script de configuração personalizada (main.cmd) em seu contêiner de BLOBs. Verifique se main.cmd existe no contêiner, que é o ponto de entrada para configuração personalizada da instalação.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Esse erro significa que a execução do script de instalação personalizada (main.cmd) falhou. Experimente o script no computador local primeiro ou verifique os logs de execução da configuração personalizada em seu contêiner de BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Esse erro indica um tempo limite de execução de script de configuração personalizada. Verifique se o script pode ser executado silenciosamente e nenhuma entrada interativa é necessária e se o contêiner de blob contém apenas os arquivos de instalação personalizados necessários. É recomendável testar o script no computador local primeiro. Você também deve verificar os logs de execução da configuração personalizada em seu contêiner de BLOB. O período máximo para a configuração personalizada é de 45 minutos antes de atingir o tempo limite e o período máximo inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no IR do SSIS. Se você precisar de um período mais longo, gere um tíquete de suporte.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Esse erro significa que a tentativa de carregar logs de execução de configuração personalizada para seu contêiner de BLOBs falhou. Esse problema ocorre porque o IR do SSIS não tem permissões de gravação para o contêiner de BLOB ou devido a problemas de armazenamento ou rede. Se a configuração personalizada for bem-sucedida, esse erro não afetará nenhuma função do SSIS, mas os logs estarão ausentes. Se a configuração personalizada falhar com outro erro e o log não for carregado, relataremos esse erro primeiro para que o log possa ser carregado para análise. Além disso, depois que esse problema for resolvido, relataremos quaisquer problemas mais específicos. Se esse problema não for resolvido após uma nova tentativa, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Quando você ingressa no SSIS IR para a rede virtual do Azure, o SSIS IR usa a rede virtual que está sob a assinatura do usuário. Para obter mais informações, consulte [Unir o Azure-SSIS Integration Runtime a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).
Depois que o SSIS IR for iniciado com êxito, se você encontrar problemas de conexão de rede, poderá tentar usar a [ferramenta diagnosticar conectividade](ssis-integration-runtime-diagnose-connectivity-faq.md) para diagnosticar o problema por conta própria.
Quando houver um problema relacionado à rede virtual, você verá um dos erros a seguir.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Esse erro pode ocorrer por vários motivos. Para solucionar problemas, consulte as seções [Proibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Proibido

Esse tipo de erro pode ser semelhante a este: "Subnetid não está habilitado para a conta atual. Microsoft.Bato provedor de recursos ch não está registrado na mesma assinatura da VNet. "

Esses detalhes significam que o lote do Azure não pode acessar sua rede virtual. Registre o provedor de recursos Microsoft.Batch na mesma assinatura que a rede virtual.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Esse tipo de erro pode ser semelhante a este: 

- "A VNet especificada não existe ou o serviço de lote não tem acesso a ela."
- "A sub-rede xxx especificada não existe."

Esses erros significam que a rede virtual não existe, o serviço de lote do Azure não pode acessá-la ou a sub-rede fornecida não existe. Verifique se a rede virtual e a sub-rede existem e se o lote do Azure pode acessá-las.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Esse tipo de mensagem de erro pode ter a seguinte aparência: "falha ao provisionar Integration Runtime na VNet. Se as configurações do servidor DNS ou do NSG estiverem configuradas, verifique se o servidor DNS está acessível e se NSG está configurado corretamente. "

Nessa situação, você provavelmente tem uma configuração personalizada do servidor DNS ou das configurações de NSG, o que impede que o nome do servidor do Azure exigido pelo SSIS IR seja resolvido ou acessado. Para obter mais informações, consulte [Configuração de rede virtual IR do SSIS](./join-azure-ssis-integration-runtime-virtual-network.md). Se você ainda tiver problemas, entre em contato com a equipe de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O IR do SSIS será atualizado automaticamente regularmente. Um novo pool do lote do Azure é criado durante a atualização e o pool do lote do Azure antigo é excluído. Além disso, os recursos relacionados à rede virtual para o pool antigo são excluídos e os novos recursos relacionados à rede virtual são criados em sua assinatura. Esse erro significa que a exclusão de recursos relacionados à rede virtual para o pool antigo falhou devido a um bloqueio de exclusão no nível de assinatura ou grupo de recursos. Como o cliente controla e define o bloqueio de exclusão, ele deve remover o bloqueio de exclusão nessa situação.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se o provisionamento de IR do SSIS falhar, todos os recursos que foram criados serão excluídos. No entanto, se houver um bloqueio de exclusão de recurso no nível de assinatura ou grupo de recursos, os recursos de rede virtual não serão excluídos conforme o esperado. Para corrigir esse erro, remova o bloqueio de exclusão e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

Quando você parar o IR do SSIS, todos os recursos relacionados à rede virtual serão excluídos. Mas a exclusão poderá falhar se houver um bloqueio de exclusão de recurso no nível de assinatura ou grupo de recursos. Aqui também, o cliente controla e define o bloqueio de exclusão. Portanto, eles devem remover o bloqueio de exclusão e parar o IR do SSIS novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Esse erro ocorre quando o IR está em execução e significa que o IR se tornou não íntegro. Esse erro é sempre causado por uma alteração no servidor DNS ou na configuração NSG que impede que o IR do SSIS se conecte a um serviço necessário. Como a configuração do servidor DNS e do NSG é controlada pelo cliente, o cliente deve corrigir os problemas de bloqueio em seu final. Para obter mais informações, consulte [Configuração de rede virtual IR do SSIS](./join-azure-ssis-integration-runtime-virtual-network.md). Se você ainda tiver problemas, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuração de endereços IP públicos estáticos

Ao unir o Azure-SSIS IR à rede virtual do Azure, você também poderá trazer seus próprios endereços IP públicos estáticos para o IR para que o IR possa acessar fontes de dados que limitam o acesso a endereços IP específicos. Para obter mais informações, consulte [Unir o Azure-SSIS Integration Runtime a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

Além dos problemas de rede virtual acima, você também pode atender a um problema relacionado a endereços IP públicos estáticos. Verifique os erros a seguir para obter ajuda.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Esse erro pode ocorrer por vários motivos quando você inicia o Azure-SSIS IR:

| Mensagem de erro | Solução|
|:--- |:--- |
| O endereço IP público estático fornecido já está sendo usado, forneça dois não utilizados para seu Azure-SSIS Integration Runtime. | Você deve selecionar dois endereços IP públicos estáticos não utilizados ou remover as referências atuais para o endereço IP público especificado e, em seguida, reiniciar o Azure-SSIS IR. |
| O endereço IP público estático fornecido não tem nenhum nome DNS, forneça dois deles com o nome DNS para seu Azure-SSIS Integration Runtime. | Você pode configurar o nome DNS do endereço IP público em portal do Azure, como mostra a imagem abaixo. As etapas específicas são as seguintes: (1) abrir portal do Azure e ir para a página de recursos desse endereço IP público; (2) Selecione a seção **configuração** e configure o nome DNS e clique no botão **salvar** ; (3) reinicie o Azure-SSIS IR. |
| A VNet fornecida e os endereços IP públicos estáticos para seu Azure-SSIS Integration Runtime devem estar no mesmo local. | De acordo com os requisitos da rede do Azure, o endereço IP público estático e a rede virtual devem estar no mesmo local e assinatura. Forneça dois endereços IP públicos estáticos válidos e reinicie o Azure-SSIS IR. |
| O endereço IP público estático fornecido é um básico, forneça dois padrões para seu Azure-SSIS Integration Runtime. | Consulte [SKUs do endereço IP público](../virtual-network/public-ip-addresses.md#sku) para obter ajuda. |

![IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se Azure-SSIS IR o provisionamento falhar, todos os recursos que foram criados serão excluídos. No entanto, se houver um bloqueio de exclusão de recurso na assinatura ou no grupo de recursos (que contém o endereço IP público estático), os recursos de rede não serão excluídos conforme o esperado. Para corrigir o erro, remova o bloqueio de exclusão e reinicie o IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Quando você parar Azure-SSIS IR, todos os recursos de rede criados no grupo de recursos que contém seu endereço IP público serão excluídos. Mas a exclusão poderá falhar se houver um bloqueio de exclusão de recurso na assinatura ou no grupo de recursos (que contém o endereço IP público estático). Remova o bloqueio de exclusão e reinicie o IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR é automaticamente atualizado regularmente. Novos nós IR são criados durante a atualização e os nós antigos serão excluídos. Além disso, os recursos de rede criados (por exemplo, o balanceador de carga e o grupo de segurança de rede) para os nós antigos são excluídos e os novos recursos de rede são criados em sua assinatura. Esse erro significa que a exclusão de recursos de rede para os nós antigos falhou devido a um bloqueio de exclusão na assinatura ou no grupo de recursos (que contém o endereço IP público estático). Remova o bloqueio de exclusão para que possamos limpar os nós antigos e liberar o endereço IP público estático para os nós antigos. Caso contrário, o endereço IP público estático não poderá ser liberado e não será possível atualizar ainda mais seu IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Quando você quiser colocar seus próprios endereços IP públicos estáticos, dois endereços IP públicos devem ser fornecidos. Um deles será usado para criar os nós de IR imediatamente e outro será usado durante a atualização do IR. Esse erro pode ocorrer quando o outro endereço IP público é inutilizável durante a atualização. Consulte  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) para obter as possíveis causas.
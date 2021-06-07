---
title: Como usar contas de armazenamento gerenciadas pelo cliente no Log Analytics do Azure Monitor
description: Use sua própria conta de armazenamento para cenários de Log Analytics
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a6d4c5811c08aa8c4de2eeea5f5f53967c3006b2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025349"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Como usar contas de armazenamento gerenciadas pelo cliente no Log Analytics do Azure Monitor

Log Analytics se baseia no armazenamento do Azure em vários cenários. Esse uso normalmente é gerenciado automaticamente. No entanto, alguns casos exigem que você forneça e gerencie sua própria conta de armazenamento, também conhecida como conta de armazenamento gerenciada pelo cliente. Este documento aborda o uso do armazenamento gerenciado pelo cliente para logs do WAD/LAD, o link privado e a criptografia CMK (chave gerenciada pelo cliente). 

> [!NOTE]
> É recomendável que você não faça uma dependência do conteúdo Log Analytics carregar no armazenamento gerenciado pelo cliente, Considerando que a formatação e o conteúdo podem ser alterados.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingerir Diagnóstico do Azure logs de extensão (WAD/LAD)
Os agentes de extensão de Diagnóstico do Azure (também chamados de WAD e LAD para agentes do Windows e Linux, respectivamente) coletam vários logs do sistema operacional e os armazenam em uma conta de armazenamento gerenciada pelo cliente. Em seguida, você pode ingerir esses logs em Log Analytics para revisá-los e analisá-los.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Como coletar Diagnóstico do Azure logs de extensão de sua conta de armazenamento
Conecte a conta de armazenamento ao seu espaço de trabalho do Log Analytics como uma fonte de dados de armazenamento usando [o portal do Azure](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) ou chamando a [API do Storage insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Tipos de dados com suporte:
* syslog
* Eventos do Windows
* Service Fabric
* Eventos ETW
* Logs IIS

## <a name="using-private-links"></a>Usando links privados
As contas de armazenamento gerenciadas pelo cliente são usadas para ingerir logs personalizados ou logs do IIS quando links privados são usados para se conectar a recursos do Azure Monitor. O processo de ingestão desses tipos de dados primeiro carrega os logs em uma conta intermediária do armazenamento do Azure e, em seguida, os ingeri em um espaço de trabalho. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Usando uma conta de armazenamento gerenciada pelo cliente em um link privado
#### <a name="workspace-requirements"></a>Requisitos do espaço de trabalho
Ao se conectar a Azure Monitor por meio de um link privado, os agentes de Log Analytics só podem enviar logs para espaços de trabalho acessíveis em um link privado. Esse requisito significa que você deve:
* Configurar Azure Monitor um objeto AMPLS (escopo de link privado)
* Conecte-o aos seus espaços de trabalho
* Conecte o AMPLS à sua rede por meio de um link privado. 

Para obter mais informações sobre o procedimento de configuração AMPLS, consulte [usar o link privado do Azure para conectar redes com segurança a Azure monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
Para que a conta de armazenamento se conecte com êxito ao seu link privado, ela deve:
* Estar localizado em sua VNet ou em uma rede emparelhada e conectado à sua VNet por meio de um link privado.
* Estar localizado na mesma região que o espaço de trabalho ao qual ele está vinculado.
* Permitir que Azure Monitor acesse a conta de armazenamento. Se você optar por permitir apenas selecionar redes para acessar sua conta de armazenamento, deverá selecionar a exceção: "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento".
![Imagem de serviços de confiança da conta de armazenamento MS](./media/private-storage/storage-trust.png)
* Se o seu espaço de trabalho tratar o tráfego de outras redes também, você deverá configurar a conta de armazenamento para permitir o tráfego de entrada proveniente das redes/Internet relevantes.
* Coordenar a versão do TLS entre os agentes e a conta de armazenamento-é recomendável que você envie dados para Log Analytics usando o TLS 1,2 ou superior. Examine as [diretrizes específicas da plataforma](./data-security.md#sending-data-securely-using-tls-12)e, se necessário, [Configure seus agentes para usar o TLS 1,2](../agents/agent-windows.md#configure-agent-to-use-tls-12). Se por algum motivo isso não for possível, configure a conta de armazenamento para aceitar o TLS 1,0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Usando uma conta de armazenamento gerenciada pelo cliente para CMK Data Encryption
O armazenamento do Azure criptografa todos os dados em repouso em uma conta de armazenamento. Por padrão, ele usa chaves gerenciadas pela Microsoft (MMK) para criptografar os dados; No entanto, o armazenamento do Azure também permite que você use o CMK do Azure Key Vault para criptografar seus dados de armazenamento. Você pode importar suas próprias chaves para Azure Key Vault ou pode usar as APIs de Azure Key Vault para gerar chaves.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Cenários CMK que exigem uma conta de armazenamento gerenciada pelo cliente
* Criptografando consultas de alerta de log com CMK
* Criptografando consultas salvas com CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Como aplicar CMK a contas de armazenamento gerenciadas pelo cliente
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre criptografia de armazenamento do Azure e gerenciamento de chaves, consulte [criptografia de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Aplicar CMK às suas contas de armazenamento
Para configurar sua conta de armazenamento do Azure para usar o CMK com Azure Key Vault, use o [portal do Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), o [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)ou a [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Vincular contas de armazenamento ao seu espaço de trabalho do Log Analytics
### <a name="using-the-azure-portal"></a>Usando o portal do Azure
Na portal do Azure, abra o menu do espaço de trabalho ' e selecione *contas de armazenamento vinculadas*. Uma folha será aberta, mostrando as contas de armazenamento vinculadas pelos casos de uso mencionados acima (ingestão sobre o link privado, aplicando CMK a consultas salvas ou alertas).
![Imagem de folha de contas de armazenamento vinculadas ](./media/private-storage/all-linked-storage-accounts.png) selecionando um item na tabela, os detalhes da conta de armazenamento serão abertos, onde você poderá definir ou atualizar a conta de armazenamento vinculada para esse tipo. 
![Vincular uma imagem de folha ](./media/private-storage/link-a-storage-account-blade.png) de conta de armazenamento você pode usar a mesma conta para diferentes casos de uso, se preferir.

### <a name="using-the-azure-cli-or-rest-api"></a>Usando o CLI do Azure ou a API REST
Você também pode vincular uma conta de armazenamento ao seu espaço de trabalho por meio do [CLI do Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou da [API REST](/rest/api/loganalytics/linkedstorageaccounts).

Os valores de DataSourceType aplicáveis são:
* CustomLogs – para usar a conta de armazenamento para logs personalizados e ingestão de logs do IIS
* Consulta-para usar a conta de armazenamento para armazenar consultas salvas (necessárias para criptografia CMK)
* Alertas-para usar a conta de armazenamento para armazenar alertas baseados em log (necessários para a criptografia CMK)


## <a name="managing-linked-storage-accounts"></a>Gerenciando contas de armazenamento vinculadas

### <a name="create-or-modify-a-link"></a>Criar ou modificar um link
Quando você vincula uma conta de armazenamento a um espaço de trabalho, Log Analytics começará a usá-la em vez da conta de armazenamento de Propriedade do serviço. É possível 
* Registrar várias contas de armazenamento para distribuir a carga de logs entre elas
* Reutilizar a mesma conta de armazenamento para vários espaços de trabalho

### <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Para parar de usar uma conta de armazenamento, desvincule o armazenamento do espaço de trabalho. Desvincular todas as contas de armazenamento de um espaço de trabalho significa Log Analytics tentará confiar em contas de armazenamento gerenciadas pelo serviço. Se sua rede tiver acesso limitado à Internet, esses armazenamentos poderão não estar disponíveis e qualquer cenário que dependa do armazenamento falhará.

### <a name="replace-a-storage-account"></a>Substituir uma conta de armazenamento
Para substituir uma conta de armazenamento usada para ingestão,
1.  **Crie um link para uma nova conta de armazenamento.** Os agentes de registro em log receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também. O processo pode levar alguns minutos.
2.  **Desvincule a conta de armazenamento antiga para que os agentes parem de gravar na conta removida.** O processo de ingestão continua lendo os dados dessa conta até que eles sejam todos ingeridos. Não exclua a conta de armazenamento até ver que todos os logs foram ingeridos.

### <a name="maintaining-storage-accounts"></a>Mantendo contas de armazenamento
#### <a name="manage-log-retention"></a>Gerenciar retenção de log
Ao usar sua própria conta de armazenamento, a retenção cabe a você. Log Analytics não excluirá os logs armazenados no seu armazenamento privado. Em vez disso, você deve configurar uma política para lidar com a carga de acordo com suas preferências.

#### <a name="consider-load"></a>Considerar a carga
As contas de armazenamento podem lidar com uma determinada carga de solicitações de leitura e gravação antes de iniciarem as solicitações de limitação (para obter mais informações, consulte [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../../storage/common/scalability-targets-standard-account.md)). A limitação afeta o tempo necessário para a ingestão de logs. Se sua conta de armazenamento estiver sobrecarregada, registre uma conta de armazenamento adicional para distribuir a carga entre elas. Para monitorar a capacidade e o desempenho da sua conta de armazenamento, veja suas [informações no portal do Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Cobranças relacionadas
As contas de armazenamento são cobradas pelo volume de dados armazenados, o tipo de armazenamento e o tipo de redundância. Para obter detalhes, confira [Preços de blobs de blocos](https://azure.microsoft.com/pricing/details/storage/blobs) e [Preços do Armazenamento de Tabelas](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o link privado do Azure para conectar redes com segurança a Azure monitor](private-link-security.md)
- Saiba mais sobre [Azure monitor chaves gerenciadas pelo cliente](../logs/customer-managed-keys.md)
---
title: FAQ do dispositivo de migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o dispositivo migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563014"
---
# <a name="azure-migrate-appliance-common-questions"></a>Dispositivo de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o dispositivo migrações para Azure. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Perguntas sobre a [migração do servidor](common-questions-server-migration.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>O que é o dispositivo de Migrações para Azure?

O dispositivo de migrações para Azure é um dispositivo leve que a ferramenta migrações para Azure usa para descobrir e avaliar servidores físicos ou virtuais do local ou de qualquer nuvem. A ferramenta migrações para Azure: Server Migration também usa o dispositivo para a migração sem agente de servidores locais em execução no ambiente VMware.

Veja mais informações sobre o dispositivo migrações para Azure:

- O dispositivo é implantado localmente como um servidor físico ou um servidor virtualizado.
- O dispositivo descobre servidores locais e envia continuamente metadados de servidor e dados de desempenho para migrações para Azure.
- A descoberta do dispositivo é sem agente. Nada é instalado em servidores descobertos.

[Saiba mais](migrate-appliance.md) sobre o dispositivo.

## <a name="how-can-i-deploy-the-appliance"></a>Como posso implantar o dispositivo?

O dispositivo pode ser implantado usando alguns métodos:

- O dispositivo pode ser implantado usando um modelo para servidores em execução no VMware ou no ambiente do Hyper-V ([modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se você não quiser usar um modelo, poderá implantar o dispositivo para o ambiente do VMware ou do Hyper-V usando um [script do instalador do PowerShell](deploy-appliance-script.md).
- No Azure governamental, você deve implantar o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](deploy-appliance-script-government.md).
- Para servidores físicos ou virtualizados locais ou qualquer outra nuvem, você sempre implanta o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>Como o dispositivo se conecta ao Azure?

O dispositivo pode se conectar pela Internet ou usando o Azure ExpressRoute. 

- Verifique se o dispositivo pode se conectar a essas [URLs do Azure](./migrate-appliance.md#url-access). 
- Você pode usar o ExpressRoute com o emparelhamento da Microsoft. O emparelhamento público foi preterido e não está disponível para novos circuitos do ExpressRoute.
- Somente o emparelhamento privado não tem suporte.


## <a name="does-appliance-analysis-affect-performance"></a>A análise de dispositivos afeta o desempenho?

O dispositivo de migrações para Azure cria perfis de servidores locais continuamente para medir dados de desempenho. Essa criação de perfil não tem impacto sobre o desempenho em servidores com perfil.

## <a name="can-i-harden-the-appliance"></a>Posso proteger o dispositivo?

Ao usar o modelo baixado para criar o dispositivo, você poderá adicionar componentes (antivírus, por exemplo) ao modelo se deixar em vigor as regras de comunicação e firewall necessárias para o dispositivo de migrações para Azure.

## <a name="what-network-connectivity-is-required"></a>Qual é a conectividade de rede necessária?

O dispositivo precisa acessar as URLs do Azure. [Examine](migrate-appliance.md#url-access) a lista de URLs.

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o dispositivo coleta?

Consulte os seguintes artigos para obter informações sobre os dados que o dispositivo de migração do Azure coleta em servidores:

- **Servidores no ambiente VMware**: [examine](migrate-appliance.md#collected-data---vmware) os dados coletados.
- **Servidores no ambiente Hyper-V**: [examine](migrate-appliance.md#collected-data---hyper-v) os dados coletados.
- **Servidores físicos ou virtuais**:[examine](migrate-appliance.md#collected-data---physical) os dados coletados.

## <a name="how-is-data-stored"></a>Como os dados são armazenados?

Os dados coletados pelo dispositivo de migrações para Azure são armazenados no local do Azure onde você criou o projeto.

Veja mais informações sobre como os dados são armazenados:

- Os dados coletados são armazenados com segurança no CosmosDB em uma assinatura da Microsoft. Os dados são excluídos quando você exclui o projeto. O armazenamento é tratado pelas migrações para Azure. Não é possível escolher especificamente uma conta de armazenamento para os dados coletados.
- Se você usar a [visualização de dependência](concepts-dependency-visualization.md), os dados coletados serão armazenados em um espaço de trabalho do Azure log Analytics criado em sua assinatura do Azure. Os dados são excluídos quando você exclui o espaço de trabalho Log Analytics em sua assinatura.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Qual a quantidade de dados carregada durante a criação de perfil contínua?

O volume de dados enviado para as migrações para Azure depende de vários parâmetros. Por exemplo, um projeto que tem 10 servidores (cada um com um disco e uma NIC) envia aproximadamente 50 MB de dados por dia. Esse valor é aproximado; o valor real varia dependendo do número de pontos de dados para os discos e NICs. Se o número de servidores, discos ou NICs aumentar, o aumento nos dados enviados será não linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Os dados são criptografados em repouso e em trânsito?

Sim, para ambos:

- Os metadados são enviados com segurança para o serviço de migrações para Azure pela Internet via HTTPS.
- Os metadados são armazenados em um banco de dados [Cosmos do Azure](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento de BLOBs do Azure](../storage/common/storage-service-encryption.md) em uma assinatura da Microsoft. Os metadados são criptografados em repouso para armazenamento.
- Os dados para análise de dependência também são criptografados em trânsito (por HTTPS seguro). Ele é armazenado em um espaço de trabalho Log Analytics em sua assinatura. Os dados são criptografados em repouso para análise de dependência.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como o dispositivo se conecta ao vCenter Server?

Estas etapas descrevem como o dispositivo se conecta ao VMware vCenter Server:

1. O dispositivo se conecta ao vCenter Server (porta 443) usando as credenciais fornecidas quando você configura o dispositivo.
2. O dispositivo usa o VMware PowerCLI para consultar vCenter Server coletar metadados sobre os servidores gerenciados pelo vCenter Server.
3. O dispositivo coleta dados de configuração sobre servidores (núcleos, memória, discos, NICs) e o histórico de desempenho de cada servidor no mês passado.
4. Os metadados coletados são enviados para a ferramenta migrações para Azure: descoberta e avaliação (pela Internet via HTTPS) para avaliação.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>O dispositivo migrações para Azure pode se conectar a vários servidores vCenter?

Não. Há um mapeamento de um para um entre um dispositivo de [migrações para Azure](migrate-appliance.md) e vCenter Server. Para descobrir servidores em várias instâncias de vCenter Server, você deve implantar vários dispositivos.

## <a name="can-a-project-have-multiple-appliances"></a>Um projeto pode ter vários dispositivos?

Um projeto pode ter vários dispositivos registrados nele. No entanto, um dispositivo só pode ser registrado com um projeto.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>O dispositivo de migração/dispositivo de replicação do Azure pode se conectar ao mesmo vCenter?

Sim. Você pode adicionar o dispositivo migrações para Azure (usado para avaliação e migração do VMware sem agente) e o dispositivo de replicação (usado para migração baseada em agente de servidores em execução no VMware) para o mesmo servidor vCenter. Mas certifique-se de que você não está configurando ambos os dispositivos no mesmo servidor e que não tem suporte no momento.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quantos servidores posso descobrir com um dispositivo?

Você pode descobrir até 10.000 servidores no ambiente VMware, até 5.000 servidores no ambiente Hyper-V e até 1000 servidores físicos com um único dispositivo. Se você tiver mais servidores no seu ambiente local, leia sobre como [dimensionar uma avaliação do Hyper-V](scale-hyper-v-assessment.md), [dimensionando uma avaliação do VMware](scale-vmware-assessment.md)e [dimensionando uma avaliação de servidor físico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Posso excluir um dispositivo?

No momento, não há suporte para a exclusão de um dispositivo do projeto.

A única maneira de excluir o dispositivo é excluir o grupo de recursos que contém o projeto associado ao dispositivo.

No entanto, a exclusão do grupo de recursos também exclui outros dispositivos registrados, o inventário descoberto, as avaliações e todos os outros componentes do Azure no grupo de recursos que estão associados ao projeto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso usar o dispositivo com uma assinatura ou um projeto diferente?

Para usar o dispositivo com uma assinatura ou um projeto diferente, você precisará reconfigurar o dispositivo existente executando o script do instalador do PowerShell para o cenário específico (VMware/Hyper-V/físico) no dispositivo. O script limpará os componentes e as configurações do dispositivo existentes para implantar um novo dispositivo. Certifique-se de limpar o cache do navegador antes de começar a usar o Gerenciador de configuração do dispositivo implantado recentemente.

Além disso, você não pode reutilizar uma chave de projeto existente em um dispositivo reconfigurado. Certifique-se de gerar uma nova chave a partir da assinatura/projeto desejado para concluir o registro do dispositivo.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso configurar o dispositivo em uma VM do Azure?

Não. Atualmente, não há suporte para essa opção.

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir em um host ESXi?

Não. Para descobrir servidores no ambiente VMware, você deve ter vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Como fazer atualizar o dispositivo?

Por padrão, o dispositivo e seus agentes instalados são atualizados automaticamente. O dispositivo verifica se há atualizações a cada 24 horas. As atualizações que falham são repetidas. 

Somente o dispositivo e os agentes do dispositivo são atualizados por essas atualizações automáticas. O sistema operacional não é atualizado pelo Azure migrar atualizações automáticas. Use as atualizações do Windows para manter o sistema operacional atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a integridade do agente?

Sim. No portal, acesse a página de **integridade do agente** para o migrações para Azure: descoberta e avaliação ou migrações para Azure: ferramenta de migração de servidor. Lá, você pode verificar o status da conexão entre o Azure e os agentes de descoberta e avaliação no dispositivo.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Posso adicionar várias credenciais de servidor no dispositivo VMware?

Sim, agora damos suporte a várias credenciais de servidor para executar o inventário de software (descoberta de aplicativos instalados), análise de dependência sem agente e descoberta de instâncias de SQL Server e bancos de dados. [Saiba mais](tutorial-discover-vmware.md#provide-server-credentials) sobre como fornecer credenciais no Gerenciador de configuração de dispositivo.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Que tipo de credenciais de servidor posso adicionar ao dispositivo VMware?
Você pode fornecer credenciais de autenticação de domínio/Windows (não domínio)/Linux (não domínio)/SQL Server no Gerenciador de configuração de dispositivo. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as manipulamos.​

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Quais tipos de propriedades de conexão de SQL Server são compatíveis com a migração do Azure para descoberta de SQL?
As Migrações para Azure criptografarão a comunicação entre o dispositivo das Migrações para Azure e as instâncias do SQL Server de origem (com a propriedade de conexão Encrypt definida como TRUE). Essas conexões são criptografadas com [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido como TRUE); a camada de transporte usará o SSL para criptografar o canal e ignorar a cadeia de certificados para validar a confiança. O servidor do dispositivo precisa ser configurado para [confiar na autoridade raiz do certificado](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Se nenhum certificado tiver sido provisionado no servidor quando ele for iniciado, SQL Server gerará um certificado autoassinado que será usado para criptografar pacotes de logon. [Saiba mais](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
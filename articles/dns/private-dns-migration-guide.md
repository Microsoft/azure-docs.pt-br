---
title: Como migrar as Zonas Privadas do DNS do Azure herdadas para um novo modelo de recurso
titleSuffix: Azure DNS
description: Este guia fornece instruções passo a passo sobre como migrar as zonas DNS privadas herdadas para o modelo de recurso mais recente
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 3f0856f85e279f97934fff506a052c8fd214ff73
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641228"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrar zonas privadas do DNS do Azure herdadas para um novo modelo de recurso

Durante a versão prévia pública, zonas DNS privadas foram criadas usando o recurso "dnszones" com a propriedade "zoneType" definida como "Private". Essas zonas não serão mais compatíveis após 31 de dezembro de 2019 e deverão ser migradas ao modelo de recurso em disponibilidade geral, que usa o tipo de recurso "privateDnsZones" em vez de "dnszones". O processo de migração é simples; fornecemos um script do PowerShell para automatizar esse processo. Este guia fornece instruções passo a passo para migrar suas Zonas Privadas do DNS do Azure para o novo modelo de recurso.

Para descobrir os recursos de dnszones que exigem migração, execute o comando abaixo na CLI do Azure.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Pré-requisitos

Verifique se que você instalou a versão mais recente do Azure PowerShell. Para saber mais sobre o Azure PowerShell (Az) e como instalá-lo, visite https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Verifique se você já tem o módulo Az.PrivateDns para o Azure PowerShell instalado. Para instalar esse módulo, abra uma janela de do PowerShell com privilégios (modo Administrativo) e insira o comando a seguir

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>O processo de migração é totalmente automatizado e não deve gerar tempo de inatividade. No entanto, se você está usando as Zonas Privadas do DNS do Azure (versão prévia) em um ambiente de produção crítico, deve executar o processo de migração a seguir durante um período de manutenção planejada. Não modifique a configuração ou os conjuntos de registros de zonas DNS privadas durante a execução do script de migração.

## <a name="installing-the-script"></a>Instalando o script

Abra uma janela do PowerShell com privilégios (modo Administrativo) e execute o comando a seguir

```powershell
install-script PrivateDnsMigrationScript
```

Insira "A" quando a instalação do script for solicitada

![Instalando o script](./media/private-dns-migration-guide/install-migration-script.png)

Você também pode obter a versão mais recente do script do PowerShell manualmente em https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>O script de migração não deve ser executado no Azure cloud Shell e deve ser executado em uma VM ou computador local conectado à Internet.

## <a name="running-the-script"></a>Executar o script

Execute o comando a seguir para executar o script

```powershell
PrivateDnsMigrationScript.ps1
```

![Executar o script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Inserir a ID da assinatura e entrar no Azure

Insira a ID da assinatura que contém as zonas DNS privadas que você pretende migrar. É preciso entrar na sua conta do Azure. Conclua a entrada para que o script possa acessar os recursos de zona DNS privada na assinatura.

![Logon no Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecionar as zonas DNS que você deseja migrar

O script obterá a lista de todas as zonas DNS privadas na assinatura e solicitará a confirmação de quais você deseja migrar. Insira "A" para migrar todas as zonas DNS privadas. Depois de executar essa etapa, o script criará novas zonas DNS privadas usando o novo modelo de recurso e copiará os dados na nova zona DNS. A etapa não alterará as zonas DNS privadas existentes.

![Selecionar as zonas DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Alternando a resolução DNS para as novas zonas DNS

Após copiar as zonas e os registros para o novo modelo de recurso, o script pedirá que você mude a resolução DNS para as novas zonas DNS. Isso remove a associação entre as zonas DNS privadas herdadas e suas redes virtuais. Quando a zona herdada é desvinculada das redes virtuais, as novas zonas DNS criadas na etapa acima assumem o controle da resolução DNS automaticamente para essas redes virtuais.

Selecione “A” para mudar a resolução DNS de todas as redes virtuais.

![Alternando a resolução de nomes](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verificar a resolução DNS

Antes de continuar, verifique se a resolução DNS está funcionando conforme o esperado nas suas zonas DNS. Você pode entrar nas VMs do Azure e consultar nslookup em relação às zonas migradas para verificar se a resolução DNS está funcionando.

![Verificar a resolução de nomes](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se você achar que as consultas DNS não estão sendo resolvidas, aguarde alguns minutos e repita-as. Se as consultas DNS estão funcionando conforme o esperado, digite “Y” quando o script solicitar a remoção da rede virtual da zona DNS privada.

![Confirmar a resolução de nomes](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se por algum motivo a resolução DNS em relação às zonas migradas não estiver funcionando conforme o esperado, insira “N” na etapa acima para que o script alterne a resolução DNS novamente para as zonas herdadas. Crie um tíquete de suporte para podermos ajudá-lo na migração de suas zonas DNS.

## <a name="cleanup"></a>Limpeza

Esta etapa excluirá as zonas DNS herdadas e deverá ser executada somente após verificar se a resolução DNS está funcionando conforme o esperado. A exclusão de cada zona DNS privada será solicitada individualmente. Digite “Y” em cada prompt após verificar se a resolução DNS para as zonas está funcionando corretamente.

![Limpeza](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Atualizar sua automação

Se você estiver usando automação que inclui modelos, scripts do PowerShell ou código personalizado desenvolvido com SDK, deverá atualizar sua automação a fim de usar o novo modelo de recurso para as zonas DNS privadas. Abaixo apresentamos os links para a nova documentação de SDK/PS/CLI do DNS privado.
* [API REST das Zonas Privadas do DNS do Azure](/rest/api/dns/privatedns/privatezones)
* [CLI das Zonas Privadas do DNS do Azure](/cli/azure/network/private-dns/link/vnet?view=azure-cli-latest)
* [PowerShell para Zonas Privadas do DNS do Azure](/powershell/module/az.privatedns/)
* [SDK das Zonas Privadas do DNS do Azure](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Caso precise de mais ajuda

Crie um tíquete de suporte se você precisar de ajuda com o processo de migração ou se, por qualquer motivo, as etapas listadas acima não funcionarem para você. Inclua o arquivo de transcrição gerado pelo script do PowerShell no tíquete de suporte.

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).

* Saiba mais sobre zonas e registros DNS, visitando [Visão geral de zonas e registros DNS](dns-zones-records.md).

* Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

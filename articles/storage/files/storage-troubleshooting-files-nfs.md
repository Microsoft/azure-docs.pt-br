---
title: Solucionar problemas de compartilhamento de arquivos NFS do Azure-arquivos do Azure
description: Solucionar problemas de compartilhamento de arquivos NFS do Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574458"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Solucionar problemas de compartilhamentos de arquivos NFS

Este artigo lista alguns problemas comuns relacionados aos compartilhamentos de arquivos NFS do Azure. Ele fornece possíveis causas e soluções alternativas quando esses problemas são encontrados.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>falha de chgrp "filename": argumento inválido (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Causa 1: idmapping não está desabilitado
Os arquivos do Azure não permitem UID/GID alfanuméricos. Portanto, idmapping deve ser desabilitado. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Causa 2: idmapping foi desabilitado, mas foi reabilitado após encontrar um nome de arquivo/dir inadequado
Mesmo que o idmapping tenha sido desabilitado corretamente, as configurações para desabilitar o idmapping são substituídas em alguns casos. Por exemplo, quando os arquivos do Azure encontrarem um nome de arquivo inadequado, ele retornará um erro. Ao ver esse código de erro específico, o cliente NFS v 4,1 Linux decide reabilitar idmapping e as solicitações futuras são enviadas novamente com UID/GID alfanumérico. Para obter uma lista de caracteres sem suporte em arquivos do Azure, consulte este [artigo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Dois pontos é um dos caracteres sem suporte. 

### <a name="workaround"></a>Solução alternativa
Verifique se o idmapping está desabilitado e se nada está reativando-o e, em seguida, execute o seguinte:

- Desmontar o compartilhamento
- Desabilitar o mapeamento de ID com # Echo Y >/sys/Module/NFS/Parameters/nfs4_disable_idmapping
- Montar o compartilhamento de volta
- Se estiver executando rsync, execute rsync com o argumento "– numeric-IDs" do diretório que não tem nenhum nome de arquivo/dir inválido.

## <a name="unable-to-create-an-nfs-share"></a>Não é possível criar um compartilhamento NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Causa 1: a assinatura não está habilitada

Sua assinatura pode não ter sido registrada para a visualização do NFS dos arquivos do Azure. Você precisará executar alguns commandlets adicionais do Cloud Shell ou de um terminal local para habilitar o recurso.

> [!NOTE]
> Talvez seja necessário aguardar até 30 minutos para que o registro seja concluído.


#### <a name="solution"></a>Solução

Use o script a seguir para registrar o recurso e o provedor de recursos, substitua `<yourSubscriptionIDHere>` antes de executar o script:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Causa 2: configurações de conta de armazenamento sem suporte

O NFS só está disponível em contas de armazenamento com a seguinte configuração:

- Camada-Premium
- Tipo de conta-armazenamento de
- Regiões- [lista de regiões com suporte](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Solução

Siga as instruções em nosso artigo: [como criar um compartilhamento NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Causa 3: a conta de armazenamento foi criada antes da conclusão do registro

Para que uma conta de armazenamento use o recurso, ela deve ser criada depois que a assinatura tiver concluído o registro para NFS. Pode levar até 30 minutos para que o registro seja concluído.

#### <a name="solution"></a>Solução

Após a conclusão do registro, siga as instruções em nosso artigo: [como criar um compartilhamento NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Não é possível conectar ou montar um compartilhamento de arquivos NFS do Azure

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Causa 1: a solicitação provém de um cliente em uma rede não confiável/IP não confiável

Diferentemente do SMB, o NFS não tem autenticação baseada no usuário. A autenticação de um compartilhamento baseia-se na configuração de sua regra de segurança de rede. Devido a isso, para garantir que apenas conexões seguras sejam estabelecidas com seu compartilhamento NFS, você deve usar os pontos de extremidade de serviço ou privados. Para acessar compartilhamentos locais, além de pontos de extremidade privados, você deve configurar VPN ou ExpressRoute. Os IPs adicionados à lista de permissões da conta de armazenamento para o firewall são ignorados. Você deve usar um dos seguintes métodos para configurar o acesso a um compartilhamento NFS:


- [Ponto de extremidade de serviço](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Acessado pelo ponto de extremidade público
    - Disponível somente na mesma região.
    - O emparelhamento VNet não dará acesso ao seu compartilhamento.
    - Cada rede virtual ou sub-rede deve ser adicionada individualmente à lista de permissões.
    - Para acesso local, os pontos de extremidade de serviço podem ser usados com as VPNs de ExpressRoute, ponto a site e site a site, mas é recomendável usar o ponto de extremidade privado, pois ele é mais seguro.

O diagrama a seguir ilustra a conectividade usando pontos de extremidade públicos.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagrama de conectividade de ponto de extremidade público." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - O acesso é mais seguro do que o ponto de extremidade de serviço.
    - O acesso ao compartilhamento NFS via link privado está disponível de dentro e fora da região do Azure da conta de armazenamento (entre regiões, local)
    - O emparelhamento de rede virtual com redes virtuais hospedadas no ponto de extremidade privado concede acesso de compartilhamento de NFS aos clientes em redes virtuais emparelhadas.
    - Pontos de extremidade privados podem ser usados com as VPNs de ExpressRoute, ponto a site e site a site.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagrama de conectividade de ponto de extremidade privado." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Causa 2: a transferência segura necessária está habilitada

A criptografia dupla ainda não tem suporte para compartilhamentos NFS. O Azure fornece uma camada de criptografia para todos os dados em trânsito entre data centers do Azure usando o MACSec. Os compartilhamentos NFS só podem ser acessados de redes virtuais confiáveis e de túneis de VPN. Nenhuma criptografia de camada de transporte adicional está disponível em compartilhamentos NFS.

#### <a name="solution"></a>Solução

Desabilite a transferência segura necessária na folha de configuração da sua conta de armazenamento.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Captura de tela da folha de configuração da conta de armazenamento, desabilitando a transferência segura necessária.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Causa 3: NFS-o pacote comum não está instalado
Antes de executar o comando Mount, instale o pacote executando o comando específico do distribuição abaixo.

Para verificar se o pacote NFS está instalado, execute: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Solução

Se o pacote não estiver instalado, instale o pacote em sua distribuição.

##### <a name="ubuntu-or-debian"></a>Ubuntu ou Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Use o Gerenciador de pacotes do DNF: `sudo dnf install nfs-common` .

As versões mais antigas do Red Hat Enterprise Linux e do CentOS usam o Gerenciador de pacotes yum: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Use o Gerenciador de pacotes do zypper: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Causa 4: firewall bloqueando a porta 2049

O protocolo NFS se comunica com seu servidor pela porta 2049, certifique-se de que essa porta esteja aberta para a conta de armazenamento (o servidor NFS).

#### <a name="solution"></a>Solução

Verifique se a porta 2049 está aberta no cliente executando o seguinte comando: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Se a porta não estiver aberta, abra-a.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (arquivos de lista) mostra resultados incorretos/inconsistentes

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Causa: inconsistência entre valores em cache e valores de metadados de arquivo de servidor quando o identificador de arquivo está aberto
Às vezes, o comando "listar arquivos" exibe um tamanho diferente de zero conforme esperado e, em vez disso, o comando de arquivos da próxima lista mostra o tamanho 0 ou um carimbo de data/hora muito antigo. Esse é um problema conhecido devido ao cache inconsistente de valores de metadados de arquivo enquanto o arquivo está aberto. Você pode usar uma das seguintes soluções alternativas para resolver isso:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Solução alternativa 1: para buscar o tamanho do arquivo, use wc-c em vez de ls-l
O uso de WC-c sempre buscará o valor mais recente do servidor e não terá nenhuma inconsistência.

#### <a name="workaround-2-use-noac-mount-flag"></a>Solução alternativa 2: usar o sinalizador de montagem "NOAC"
Monte novamente o sistema de arquivos usando o sinalizador "NOAC" com o comando Mount. Isso sempre irá buscar todos os valores de metadados do servidor. Pode haver alguma sobrecarga de desempenho secundária para todas as operações de metadados se essa solução alternativa for usada.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

---
title: Pré-requisitos de cache do Azure HPC
description: Pré-requisitos para usar o cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 7d40dcf80d9ec566146bbe46bc2cb3c558584fcd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775758"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Pré-requisitos para o cache HPC do Azure

Antes de usar o portal do Azure para criar um novo cache do Azure HPC, verifique se o seu ambiente atende a esses requisitos.

## <a name="video-overviews"></a>Visões gerais de vídeo

Assista a esses vídeos para obter uma visão geral rápida dos componentes do sistema e o que eles precisam para trabalhar juntos.

(Clique na imagem de vídeo ou no link para assistir.)

* [Como funciona](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – explica como o cache HPC do Azure interage com o armazenamento e os clientes

  [![imagem de miniatura de vídeo: cache do HPC do Azure: como ele funciona (clique para visitar a página de vídeo)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Prerequisites](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) -descreve os requisitos para armazenamento nas, armazenamento de BLOBs do Azure, acesso à rede e acesso de cliente

  [![imagem de miniatura de vídeo: cache do HPC do Azure: pré-requisitos (clique para visitar a página de vídeo)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Leia o restante deste artigo para obter recomendações específicas.

## <a name="azure-subscription"></a>Assinatura do Azure

Uma assinatura paga é recomendada.

## <a name="network-infrastructure"></a>Infraestrutura da rede

Dois pré-requisitos relacionados à rede devem ser configurados antes que você possa usar o cache:

* Uma sub-rede dedicada para a instância de cache do HPC do Azure
* Suporte a DNS para que o cache possa acessar o armazenamento e outros recursos

### <a name="cache-subnet"></a>Sub-rede de cache

O cache HPC do Azure precisa de uma sub-rede dedicada com estas qualidades:

* A sub-rede deve ter pelo menos 64 endereços IP disponíveis.
* A sub-rede não pode hospedar nenhuma outra VM, mesmo para serviços relacionados, como computadores cliente.
* Se você usar várias instâncias de cache do HPC do Azure, cada uma precisará de sua própria sub-rede.

A prática recomendada é criar uma nova sub-rede para cada cache. Você pode criar uma nova rede virtual e uma sub-rede como parte da criação do cache.

### <a name="dns-access"></a>Acesso DNS

O cache precisa do DNS para acessar recursos fora de sua rede virtual. Dependendo de quais recursos você está usando, talvez seja necessário configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure:

* Para acessar os pontos de extremidade do armazenamento de BLOBs do Azure e outros recursos internos, você precisa do servidor DNS baseado no Azure.
* Para acessar o armazenamento local, você precisa configurar um servidor DNS personalizado que possa resolver seus nomes de host de armazenamento. Você deve fazer isso **antes** de criar o cache.

Se você usar apenas o armazenamento de BLOBs, poderá usar o servidor DNS padrão fornecido pelo Azure para seu cache. No entanto, se precisar de acesso ao armazenamento ou a outros recursos fora do Azure, você deverá criar um servidor DNS personalizado e configurá-lo para encaminhar as solicitações de resolução específicas do Azure para o servidor DNS do Azure.

Para usar um servidor DNS personalizado, você precisa executar estas etapas de instalação antes de criar o cache:

* Crie a rede virtual que hospedará o cache do HPC do Azure.
* Crie o servidor DNS.
* Adicione o servidor DNS à rede virtual do cache.

  Siga estas etapas para adicionar o servidor DNS à rede virtual no portal do Azure:

  1. Abra a rede virtual no portal do Azure.
  1. Escolha **servidores DNS** no menu **configurações** na barra lateral.
  1. Selecionar **Personalizado**
  1. Insira o endereço IP do servidor DNS no campo.

Um servidor DNS simples também pode ser usado para balancear a carga de conexões de cliente entre todos os pontos de montagem de cache disponíveis.

Saiba mais sobre as configurações de servidor DNS e redes virtuais do Azure na [resolução de nomes para recursos em redes virtuais do Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Permissões

Verifique esses pré-requisitos relacionados à permissão antes de começar a criar seu cache.

* A instância de cache precisa ser capaz de criar NICs (interfaces de rede virtual). O usuário que cria o cache deve ter privilégios suficientes na assinatura para criar NICs.

* Se estiver usando o armazenamento de BLOB, o cache HPC do Azure precisará de autorização para acessar sua conta de armazenamento. Use o Azure RBAC (controle de acesso baseado em função) para fornecer ao cache acesso ao seu armazenamento de BLOBs. São necessárias duas funções: colaborador de conta de armazenamento e colaborador de dados de blob de armazenamento.

  Siga as instruções em [Adicionar destinos de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para adicionar as funções.

## <a name="storage-infrastructure"></a>Infraestrutura de armazenamento
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

O cache dá suporte a contêineres de blob do Azure, exportações de armazenamento de hardware NFS e contêineres de blob ADLS montados em NFS (atualmente em visualização). Adicione destinos de armazenamento depois de criar o cache.

Cada tipo de armazenamento tem pré-requisitos específicos.

### <a name="blob-storage-requirements"></a>Requisitos de armazenamento de BLOBs

Se você quiser usar o armazenamento de BLOBs do Azure com o cache, precisará de uma conta de armazenamento compatível e de um contêiner de blob vazio ou de um contêiner que é preenchido com os dados formatados do cache HPC do Azure, conforme descrito em [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

> [!NOTE]
> Requisitos diferentes se aplicam ao armazenamento de BLOBs montado em NFS. Leia [requisitos de armazenamento ADLS-NFS](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) para obter detalhes.

Crie a conta antes de tentar adicionar um destino de armazenamento. Você pode criar um novo contêiner ao adicionar o destino.

Para criar uma conta de armazenamento compatível, use estas configurações:

* Desempenho: **Standard**
* Tipo de conta: **StorageV2 (uso geral v2)**
* Replicação: **armazenamento com redundância local (LRS)**
* Camada de acesso (padrão): **quente**

É uma boa prática usar uma conta de armazenamento no mesmo local que o cache.

Você também deve conceder ao aplicativo de cache acesso à sua conta de armazenamento do Azure, conforme mencionado em [permissões](#permissions)acima. Siga o procedimento em [Adicionar destinos de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para fornecer ao cache as funções de acesso necessárias. Se você não for o proprietário da conta de armazenamento, faça com que o proprietário execute esta etapa.

### <a name="nfs-storage-requirements"></a>Requisitos de armazenamento NFS
<!-- linked from configuration.md -->

Se estiver usando um sistema de armazenamento NFS (por exemplo, um sistema de NAS de hardware local), verifique se ele atende a esses requisitos. Talvez seja necessário trabalhar com os administradores de rede ou os gerenciadores de firewall para seu sistema de armazenamento (ou data center) para verificar essas configurações.

> [!NOTE]
> A criação do destino de armazenamento falhará se o cache tiver acesso insuficiente ao sistema de armazenamento NFS.

Mais informações estão incluídas em [solucionar problemas de configuração de nas e destino de armazenamento NFS](troubleshoot-nas.md).

* **Conectividade de rede:** O cache HPC do Azure precisa de acesso à rede de largura de banda alta entre a sub-rede de cache e o data center do sistema NFS. O [ExpressRoute](../expressroute/index.yml) ou acesso semelhante é recomendado. Se estiver usando uma VPN, talvez seja necessário configurá-la como fixe TCP MSS em 1350 para garantir que os pacotes grandes não sejam bloqueados. Leia [restrições de tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) para obter ajuda adicional Solucionando problemas de configurações de VPN.

* **Acesso à porta:** O cache precisa de acesso a portas TCP/UDP específicas em seu sistema de armazenamento. Tipos diferentes de armazenamento têm requisitos de porta diferentes.

  Para verificar as configurações do seu sistema de armazenamento, siga este procedimento.

  * Emita um `rpcinfo` comando para o sistema de armazenamento para verificar as portas necessárias. O comando a seguir lista as portas e formata os resultados relevantes em uma tabela. (Use o endereço IP do seu sistema no lugar do *<storage_IP>* termo.)

    Você pode emitir esse comando de qualquer cliente Linux que tenha a infraestrutura de NFS instalada. Se você usar um cliente dentro da sub-rede do cluster, ele também poderá ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Verifique se todas as portas retornadas pela ``rpcinfo`` consulta permitem o tráfego irrestrito da sub-rede do cache HPC do Azure.

  * Se você não puder usar o `rpcinfo` comando, verifique se essas portas comumente usadas permitem o tráfego de entrada e de saída:

    | Protocolo | Porta  | Serviço  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montado   |
    | TCP/UDP  | 4047  | status   |

    Alguns sistemas usam números de porta diferentes para esses serviços – consulte a documentação do seu sistema de armazenamento para ter certeza.

  * Verifique as configurações de firewall para garantir que elas permitam o tráfego em todas essas portas necessárias. Certifique-se de verificar os firewalls usados no Azure, bem como firewalls locais no seu data center.

* **Acesso à raiz** (leitura/gravação): o cache se conecta ao sistema back-end como ID de usuário 0. Verifique essas configurações no seu sistema de armazenamento:
  
  * Habilitar `no_root_squash`. Essa opção garante que o usuário raiz remoto possa acessar arquivos pertencentes à raiz.

  * Verifique as políticas de exportação para certificar-se de que elas não incluem restrições de acesso de raiz da sub-rede do cache.

  * Se o armazenamento tiver quaisquer exportações que sejam subdiretórios de outra exportação, verifique se o cache tem acesso à raiz para o segmento mais baixo do caminho. Leia [acesso à raiz em caminhos de diretório](troubleshoot-nas.md#allow-root-access-on-directory-paths) no artigo solução de problemas de destino de armazenamento NFS para obter detalhes.

* O armazenamento de back-end do NFS deve ser uma plataforma de hardware/software compatível. Contate a equipe de cache do Azure HPC para obter detalhes.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Requisitos de armazenamento de BLOB (ADLS-NFS) montados em NFS (visualização)

O cache HPC do Azure também pode usar um contêiner de blob montado com o protocolo NFS como um destino de armazenamento.

> [!NOTE]
> O suporte ao protocolo NFS 3,0 para o armazenamento de BLOBs do Azure está em visualização pública. A disponibilidade é restrita e os recursos podem mudar entre agora e quando o recurso se torna disponível de forma geral. Não use a tecnologia de visualização em sistemas de produção.
>
> Leia mais sobre esse recurso de visualização em [suporte ao protocolo NFS 3,0 no armazenamento de BLOBs do Azure](../storage/blobs/network-file-system-protocol-support.md).

Os requisitos da conta de armazenamento são diferentes para um destino de armazenamento de blob ADLS-NFS e para um destino de armazenamento de BLOBs padrão. Siga as instruções em [montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0](../storage/blobs/network-file-system-protocol-support-how-to.md) com cuidado para criar e configurar a conta de armazenamento habilitada para NFS.

Esta é uma visão geral das etapas. Essas etapas podem ser alteradas, portanto, sempre consulte as [instruções do ADLS-NFS](../storage/blobs/network-file-system-protocol-support-how-to.md) para obter detalhes atuais.

1. Verifique se os recursos necessários estão disponíveis nas regiões em que você planeja trabalhar.

1. Habilite o recurso de protocolo NFS para sua assinatura. Faça isso *antes* de criar a conta de armazenamento.

1. Crie uma rede virtual segura (VNet) para a conta de armazenamento. Você deve usar a mesma rede virtual para sua conta de armazenamento habilitada para NFS e para o cache do Azure HPC. (Não use a mesma sub-rede que o cache.)

1. Crie a conta de armazenamento.

   * Em vez de usar as configurações da conta de armazenamento para uma conta de armazenamento de BLOBs padrão, siga as instruções no [documento de instruções](../storage/blobs/network-file-system-protocol-support-how-to.md). O tipo de conta de armazenamento com suporte pode variar por região do Azure.

   * Na seção **rede** , escolha um ponto de extremidade privado na rede virtual segura que você criou (recomendado) ou escolha um ponto de extremidade público com acesso restrito da VNet segura.

   * Não se esqueça de concluir a seção **avançada** , onde você habilita o acesso de NFS.

   * Conceda ao aplicativo de cache acesso à sua conta de armazenamento do Azure, conforme mencionado em [permissões](#permissions)acima. Você pode fazer isso na primeira vez em que criar um destino de armazenamento. Siga o procedimento em [Adicionar destinos de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para fornecer ao cache as funções de acesso necessárias.

     Se você não for o proprietário da conta de armazenamento, faça com que o proprietário execute esta etapa.

## <a name="set-up-azure-cli-access-optional"></a>Configurar o acesso de CLI do Azure (opcional)

Se você quiser criar ou gerenciar o cache do HPC do Azure na interface de linha de comando do Azure (CLI do Azure), precisará instalar o software da CLI e a extensão do cache HPC. Siga as instruções em [configurar CLI do Azure para o cache do HPC do Azure](az-cli-prerequisites.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar uma instância de cache do HPC do Azure](hpc-cache-create.md) do portal do Azure

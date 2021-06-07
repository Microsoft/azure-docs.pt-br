---
title: Visão geral do provisionamento do Linux
description: Visão geral de como trazer suas imagens de VM do Linux ou criar imagens para serem usadas no Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563210"
---
# <a name="azure-linux-vm-provisioning"></a>Provisionamento de VM do Linux do Azure
Quando você cria uma VM com base em uma imagem generalizada (Galeria de Imagens Compartilhadas ou Imagem Gerenciada), o painel de controle permitirá que você crie uma VM e passe parâmetros e configurações para a VM. Isso é chamado de *provisionamento* de VM. Durante o provisionamento, a plataforma disponibiliza os valores de parâmetros de criação de VM necessários (nome de host, nome de usuário, senha, chaves SSH, customData) para a VM enquanto ela é inicializada. 

Um agente de provisionamento embutido dentro da imagem fará interface com a plataforma, conectando-se com até várias interfaces de provisionamento independentes. Defina as propriedades e o sinal como a plataforma que ele concluiu. 

Os agentes de provisionamento podem ser o [Agente Linux do Azure](../extensions/agent-linux.md) ou a [inicialização de nuvem](./using-cloud-init.md). Estes são [pré-requisitos](create-upload-generic.md) da criação de imagens generalizadas.

Os agentes de provisionamento fornecem suporte para todas as [distribuições Linux do Azure](./endorsed-distros.md) endossadas e você descobrirá que as imagens de distribuição endossadas, em muitos casos, serão fornecidas com o cloud-init e o Agente do Linux. Isso lhe dá a opção de ter o cloud-init para lidar com o provisionamento. Em seguida, o Agente do Linux dará suporte para manipular as [Extensões do Azure](../extensions/features-windows.md). O fornecimento de suporte para extensões significa que a VM será qualificada para dar suporte a serviços adicionais do Azure, como Redefinição de Senha de VM, Monitoramento do Azure, Backup do Azure, Criptografia de Disco do Azure etc.

Após a conclusão do provisionamento, o cloud-init será executado em cada inicialização. O cloud-init vai monitorar se há alterações na VM, como alterações de rede, montagem e formatação do disco efêmero e inicialização do Agente do Linux. O Agente do Linux é executado continuamente no servidor, buscando um “estado de meta” (nova configuração) da plataforma do Azure, portanto, sempre que você instalar extensões, o agente poderá processá.

Embora, no momento, haja dois agentes de provisionamento, o cloud-init deve ser o agente de provisionamento escolhido e o Agente do Linux deve ser instalado para suporte de extensão. Com isso, é possível aproveitar as otimizações de plataforma e desabilitar/remover o Agente do Linux. Para obter mais detalhes sobre como criar imagens sem o agente e como removê-lo, leia esta [documentação](disable-provisioning.md).

Se você tem um kernel do Linux que não dá suporte à execução de nenhum agente, mas deseja definir algumas propriedades de criação da VM, como hostname, customData, userName, password, ssh keys, este documento aborda como você pode [criar imagens generalizadas sem um agente](no-agent.md) e atender aos requisitos da plataforma.


## <a name="provisioning-agent-responsibilities"></a>Responsabilidades do agente de provisionamento

**Provisionamento de imagem**
  
- Criação de uma conta de usuário
- Configurando os tipos de autenticação do SSH
- Implantação de chaves públicas do SSH e pares de chaves
- Configuração do nome de host
- Publicando o nome do host para a plataforma de DNS
- Relatório de impressão digital da chave host SSH para a plataforma
- Gerenciamento de recursos de disco
- Formatação e montagem do disco do recurso
- Consumir e processar `customData`
 
**Rede**
  
- Gerencia as rotas para melhorar a compatibilidade com os servidores DHCP de plataforma
- Garante a estabilidade do nome da interface de rede

**Kernel**
  
- Configura NUMA virtual (desabilitar para kernel <`2.6.37`)
- Consome entropia de Hyper-V para `/dev/random`
- Configura os tempos limite de SCSI para o dispositivo raiz (o qual poderia ser remoto)

**Diagnóstico**
  
- Redirecionamento de console de porta serial

## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

- Um DVD anexado ao tempo de inicialização para as implementações de IaaS. O DVD inclui um arquivo de configuração em conformidade com OVF que inclui todas as informações de provisionamento que não sejam os pares de chaves SSH real.
- Um ponto de extremidade TCP que expõe uma API REST usada para obter a implantação e a configuração de topologia.


## <a name="azure-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento do Azure
O Agente do Linux e o cloud-init dependem de alguns pacotes de sistema para funcionar corretamente:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Utilitários do sistema de arquivos: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Ferramentas de senha: chpasswd, sudo
- Ferramentas de processamento de texto: sed, grep
- Ferramentas de rede: roteamento ip
- Suporte a kernel para montar sistemas de arquivos UDF.

## <a name="next-steps"></a>Próximas etapas

Se precisar, você poderá [desabilitar o provisionamento e remover o agente do Linux](disable-provisioning.md).

---
title: Solução de problemas de implantação do OpenShift no Azure | Microsoft Docs
description: Solução de problemas de implantação do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: af6746e7246b8783e5bdbef34cf1b57427aa7ebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771270"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Solução de problemas de implantação do OpenShift no Azure

Se o cluster do OpenShift não for implantado com êxito, o portal do Azure fornecerá a saída de erro. A saída pode ser difícil de ler, o que dificulta a identificação do problema. Examine rapidamente essa saída para o código de saída 3, 4 ou 5. A seguir, informações sobre esses três códigos de saída:

- Código de saída 3: Seu nome de usuário de assinatura do Red Hat / senha ou ID / chave de ativação estão incorreta
- Código de saída 4: Sua ID do Pool do Red Hat está incorreta ou não há direitos disponíveis
- Código de saída 5: Não foi possível provisionar o Volume do Pool dinâmico do Docker

Para todos os outros códigos de saída, conecte-se ao (s) host (s) via ssh para visualizar os arquivos de log.

**OpenShift Container Platform**

SSH para o host ansioso playbook. Para o modelo ou a oferta do Marketplace, use o host de bastiões. Do bastião, você pode SSH para todos os outros nós no cluster (mestre, infra, CNS, compute). Você precisará ser root para visualizar os arquivos de log. A raiz está desativada para o acesso SSH por padrão, portanto, não use root para SSH em outros nós.

**OKD**

SSH para o host ansioso playbook. Para o modelo OLD (versão 3.9 e anterior), use o host master-0. Para o modelo OLD (versão 3.10 e posterior), use o host de bastiões. Do ansible playbook host, você pode SSH para todos os outros nós no cluster (mestre, infra, CNS, compute). Você precisará ser root (sudo su -) para visualizar os arquivos de log. A raiz está desativada para o acesso SSH por padrão, portanto, não use root para SSH em outros nós.

## <a name="log-files"></a>Arquivos de log

Os arquivos de log (stderr e stdout) para os scripts de preparação de host estão localizados em `/var/lib/waagent/custom-script/download/0` em todos os hosts. Se ocorreu um erro durante a preparação do host, exiba esses arquivos de log para determinar o erro.

Se os scripts de preparação foi executado com êxito, em seguida, os arquivos de log no `/var/lib/waagent/custom-script/download/1` diretório do host de guia estratégico do ansible precisam ser examinados. Se o erro ocorreu durante a instalação real do OpenShift, o arquivo stdout exibirá o erro. Use estas informações para entrar em contato com o Suporte para obter mais assistência.

Saída de exemplo

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Os erros mais comuns durante a instalação são:

1. Chave privada tem a frase secreta
2. O segredo do cofre da chave com chave privada não foi criado corretamente
3. As credenciais do principal de serviço foram inseridas incorretamente
4. A entidade de serviço não tem acesso de contribuidor ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>Chave privada tem uma senha

Você verá um erro que a permissão foi negada para o ssh. SSH para o host de guia estratégico do ansible para verificar se há uma senha na chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>O segredo do cofre da chave com chave privada não foi criado corretamente

A chave privada é copiada para o host de guia estratégico do ansible - ~/.ssh/id_rsa. Confirme se este arquivo está correto. Teste abrindo uma sessão SSH em um dos nós do cluster a partir do ansible host do manual.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais do principal de serviço foram inseridas incorretamente

Ao fornecer a entrada para o modelo ou a oferta do Marketplace, as informações incorretas foram fornecidas. Certifique-se de usar o appId (clientId) e a senha (clientSecret) corretos para o principal do serviço. Verifique emitindo o seguinte comando azure cli.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>A entidade de serviço não tem acesso de contribuidor ao grupo de recursos

Se o provedor de nuvem do Azure estiver habilitado, o principal de serviço usado deverá ter acesso de colaborador ao grupo de recursos. Verifique emitindo o seguinte comando azure cli.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, você também pode usar os seguintes comandos para obter mais informações:

1. status de systemctl \<serviço >
2. journalctl -xe

---
title: Implantar o IBM DB2 pureScale no Azure
description: Aprenda como implantar uma arquitetura de exemplo usada recentemente para migrar uma empresa de seu ambiente IBM DB2 em execução no z/OS para o IBM DB2 pureScale no Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 91ab11dce1c88f698640ce446b529c9cbe92cac5
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221513"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implantar o IBM DB2 pureScale no Azure

Este artigo descreve como implantar uma [arquitetura de exemplo](ibm-db2-purescale-azure.md) que um cliente empresarial usou recentemente para migrar de seu ambiente IBM DB2 em execução no z/OS para o IBM DB2 pureScale no Azure.

Para seguir as etapas usadas na migração, confira os scripts de instalação no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub. Esses scripts baseiam-se na arquitetura para uma carga de trabalho OLTP (processamento) típico, o tamanho médio de transações online.

## <a name="get-started"></a>Introdução

Para implantar essa arquitetura, faça o download e execute o script deploy.sh localizado no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub.

O repositório também tem scripts para configurar um painel do Grafana. Você pode usar o painel para consultar o Prometheus, o sistema de monitoramento e alerta de código-fonte aberto incluso no DB2.

> [!NOTE]
> O script deploy.sh no cliente cria chaves SSH privadas e as passa para o modelo de implantação por HTTPS. Para maior segurança, recomendamos o uso do [Azure Key Vault](../../../../key-vault/general/overview.md) para armazenar segredos, chaves e senhas.

## <a name="how-the-deployment-script-works"></a>Como funciona o script de implantação

O script deploy.sh cria e configura os recursos do Azure para essa arquitetura. O script solicita as assinaturas e máquinas virtuais do Azure usadas no ambiente de destino e executa as seguintes operações:

-   Configura o grupo de recursos, a rede virtual e as sub-redes no Azure para a instalação.

-   Configura os grupos de segurança de rede e o SSH para o ambiente.

-   Configura várias NICs no armazenamento compartilhado e nas máquinas virtuais do DB2 pureScale.

-   Cria as máquinas virtuais de armazenamento compartilhado. Se você usar Espaços de Armazenamento Diretos ou outra solução de armazenamento, consulte [espaços de armazenamento diretos visão geral](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Cria a máquina virtual de jumpbox.

-   Cria as máquinas virtuais do DB2 pureScale.

-   Cria a máquina virtual de testemunha que o DB2 pureScale efetua ping. Ignore esta parte da implantação se sua versão do DB2 pureScale não exigir uma testemunha.

-   Cria uma máquina virtual do Windows a ser usada para teste, mas não instala nada nela.

Em seguida, os scripts de implantação configuram a vSAN (rede de área de armazenamento virtual) do iSCSI para armazenamento compartilhado no Azure. Neste exemplo, o iSCSI se conecta ao cluster de armazenamento compartilhado. Na solução de cliente original, o GlusterFS foi usado. No entanto, a IBM não dá mais suporte a essa abordagem. Para manter o suporte da IBM, você precisa usar um sistema de arquivos compatível com iSCSI com suporte. A Microsoft oferece Espaços de Armazenamento Diretos (S2D) como uma opção.

Essa solução também oferece a opção de instalar os destinos iSCSI como um único nó do Windows. O iSCSI fornece uma interface de armazenamento de bloco compartilhado sobre TCP/IP que permite que o procedimento de configuração DB2 pureScale use uma interface de dispositivo para se conectar ao armazenamento compartilhado.

Os scripts de implantação executam estas etapas gerais:

1.  Configure um cluster de armazenamento compartilhado no Azure. Essa etapa envolve pelo menos dois nós do Linux.

2.  Configure uma interface iSCSI direta em servidores Linux de destino para o cluster de armazenamento compartilhado.

3.  Configure o iniciador iSCSI nas máquinas virtuais do Linux. O iniciador acessará o cluster de armazenamento compartilhado usando um destino iSCSI. Para detalhes da configuração, confira [Como configurar um iniciador e um destino iSCSI no Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) na documentação do RootUsers.

4.  Instale a camada de armazenamento compartilhado para a interface iSCSI.

Depois que os scripts criam o dispositivo iSCSI, a etapa final é instalar o DB2 pureScale. Como parte da configuração do DB2 pureScale, o [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anteriormente conhecido como GPFS) é compilado e instalado no cluster do GlusterFS. Esse sistema de arquivos em cluster permite que o DB2 pureScale compartilhe dados entre as máquinas virtuais que executam o mecanismo DB2 pureScale. Para obter mais informações, consulte a documentação do [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) no site da IBM.

## <a name="db2-purescale-response-file"></a>Arquivo de resposta do DB2 pureScale

O repositório do GitHub inclui o DB2server.rsp, um arquivo de resposta (.rsp) que permite gerar um script automatizado para a instalação do DB2 pureScale. A tabela a seguir lista as opções do DB2 pureScale que o arquivo de resposta usa para a configuração. Você pode personalizar o arquivo de resposta conforme necessário para seu ambiente.

> [!NOTE]
> Um arquivo de resposta de amostra, DB2server.rsp, está incluso no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub. Se você usar esse arquivo, deverá editá-lo antes que ele funcione em seu ambiente.

| Nome da tela               | Campo                                        | Valor                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bem-Vindo                   |                                              | Nova instalação                                                                                           |
| Escolha um produto          |                                              | Versão do DB2 11.1.3.3. Edições de servidor com o DB2 pureScale                                              |
| Configuração             | Diretório                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Selecione o tipo de instalação                 | Típico                                                                                               |
|                           | Eu concordo com os termos da IBM                     | Verificado                                                                                               |
| Proprietário da instância            | Usuário Existente Por Instância, Nome do Usuário        | DB2sdin1                                                                                              |
| Usuário Isolado               | Usuário existente, nome de usuário                     | DB2sdfe1                                                                                              |
| Sistema de arquivos do cluster       | Caminho de dispositivo de partição de disco de compartilhado            | /dev/dm-2                                                                                             |
|                           | Ponto de montagem                                  | /DB2sd\_1804a                                                                                         |
|                           | Disco de dados compartilhado                         | /dev/dm-1                                                                                             |
|                           | Ponto de montagem (dados)                           | /DB2fs/datafs1                                                                                        |
|                           | Cluster de disco compartilhado                          | /dev/dm-0                                                                                             |
|                           | Ponto de montagem (Log)                            | /DB2fs/logfs1                                                                                         |
|                           | Desempate de serviços de cluster do DB2. Caminho de dispositivo | /dev/dm-3                                                                                             |
| Lista de hosts                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF primário preferencial                         | cf1                                                                                                   |
|                           | CF secundário preferencial                       | cf2                                                                                                   |
| Arquivo de resposta e o resumo | Primeira opção                                 | Instalar o DB2 Server Edition com o recurso IBM DB2 pureScale e salvar minhas configurações em um arquivo de resposta |
|                           | Nome do arquivo de resposta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Observações sobre essa implantação

- Os valores para / dev-dm0, / dev-dm1, / dev-dm2 e / dev-dm3 podem ser alterados após reiniciar na máquina virtual em que a configuração ocorre (d0 no script automatizado). Para encontrar os valores corretos, você pode emitir o comando a seguir antes de concluir o arquivo de resposta no servidor em que a instalação será executada:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Os scripts de configuração usam aliases para os discos iSCSI para que os nomes reais possam ser encontrados facilmente.

- Quando o script de configuração é executado em d0, os valores de **/ dev / dm- \*** podem ser diferentes em d1, cf0 e cf1. A diferença nos valores não afeta a configuração do DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Solução de problemas e problemas conhecidos

O repositório do GitHub inclui uma base de dados de conhecimento mantida pelos autores. Ela lista possíveis problemas que você pode encontrar e resoluções que pode tentar. Por exemplo, problemas conhecidos podem ocorrer quando:

-   Você está tentando alcançar o endereço IP do gateway.

-   Você está compilando a GPL (licença pública geral).

-   O handshake de segurança entre hosts falha.

-   O instalador do DB2 detecta um sistema de arquivos existente.

-   Você está instalando um IBM Spectrum Scale manualmente.

-   Você está instalando o DB2 pureScale quando o IBM Spectrum Scale já foi criado.

-   Você está removendo o DB2 pureScale e o IBM Spectrum Scale.

Para obter mais informações sobre esses e outros problemas conhecidos, confira o arquivo kb.md no repositório [DB2onAzure](https://aka.ms/DB2onAzure).

## <a name="next-steps"></a>Próximas etapas

-   [Criação de usuários necessários para uma instalação do Recurso DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Solução de dados de cluster do DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)

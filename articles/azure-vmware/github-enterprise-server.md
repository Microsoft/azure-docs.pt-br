---
title: Configurar o GitHub Enterprise Server em sua nuvem privada da solução Azure VMware
description: Saiba como configurar o GitHub Enterprise Server em sua nuvem privada da solução Azure VMware.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 59a76c3976f6fcda88423b7b78344f2abed1ea84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382014"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Configurar o GitHub Enterprise Server em sua nuvem privada da solução Azure VMware

Neste artigo, percorreremos as etapas para configurar o GitHub Enterprise Server, a versão "local" do [github.com](https://github.com/), em sua nuvem privada da solução Azure VMware. O cenário que abordaremos é uma instância do GitHub Enterprise Server que pode servir até 3.000 desenvolvedores que executam até 25 trabalhos por minuto em ações do GitHub. Ele inclui a configuração do (em vez de escrever) recursos de *Visualização* , como ações do github. Para personalizar a instalação para suas necessidades específicas, examine os requisitos listados em [instalando o GitHub Enterprise Server no VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Antes de começar

O GitHub Enterprise Server requer uma chave de licença válida. Você pode se inscrever para uma [licença de avaliação](https://enterprise.github.com/trial). Se você pretende estender os recursos do GitHub Enterprise Server por meio de uma integração, você pode se qualificar para uma licença de desenvolvedor de cinco lugares gratuita. Inscreva-se para esta licença por meio [do programa de parceiro do GitHub](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Instalando o GitHub Enterprise Server no VMware

Baixe [a versão atual do GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) para VMware ESXi/VSPHERE (OVA) e [implante o modelo de ova](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) que você baixou.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Escolha executar o GitHub no local ou na nuvem.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Implante o modelo OVA.":::  

Forneça um nome reconhecível para sua nova máquina virtual, como GitHubEnterpriseServer. Você não precisa incluir os detalhes da versão no nome da VM, pois esses detalhes se tornam obsoletos quando a instância é atualizada. Selecione todos os padrões por enquanto (vamos editar esses detalhes em breve) e aguarde até que o OVA seja importado.

Depois de importado, [ajuste a configuração de hardware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) com base em suas necessidades. Em nosso cenário de exemplo, precisaremos da configuração a seguir.

| Recurso | Configuração padrão | Configuração padrão + "recursos beta" (ações) |
| --- | --- | --- |
| vCPUs | 4 | 8 |
| Memória | 32 GB | 61 GB |
| Armazenamento anexado | 250 GB | 300 GB |
| Armazenamento raiz | 200 GB | 200 GB |

No entanto, suas necessidades podem variar. Consulte as diretrizes sobre considerações de hardware em [instalando o GitHub Enterprise Server no VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Consulte também [Adicionar recursos de CPU ou memória para o VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) para personalizar a configuração de hardware com base em sua situação.

## <a name="configuring-the-github-enterprise-server-instance"></a>Configurando a instância do servidor do GitHub Enterprise

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Instale o GitHub Enterprise.":::  

Depois que a VM (máquina virtual) recentemente configurada estiver ligada, [Configure-a por meio do navegador](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Será necessário carregar o arquivo de licença e definir uma senha do console de gerenciamento. Certifique-se de anotar essa senha em um local seguro.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Acesse o Shell de administração via SSH.":::    

É recomendável, pelo menos, executar as seguintes etapas:

1. Carregue uma chave SSH pública para o console de gerenciamento, para que você possa [acessar o Shell administrativo via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Configure o TLS em sua instância](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) para que você possa usar um certificado assinado por uma autoridade de certificação confiável.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Configurando sua instância.":::

Aplique suas configurações.  Enquanto a instância é reiniciada, você pode continuar com a próxima etapa, **Configurando o armazenamento de BLOBs para ações do GitHub**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Crie sua conta de administrador.":::

Depois que a instância for reiniciada, você poderá criar uma nova conta de administrador na instância. Lembre-se de anotar a senha desse usuário também.

### <a name="other-configuration-steps"></a>Outras etapas de configuração

Para proteger sua instância para uso em produção, as seguintes etapas opcionais de instalação são recomendadas:

1. Configure a [alta disponibilidade](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) para proteção em relação ao:

    - Falhas de software (nível de aplicativo ou so)
    - Falhas de hardware (armazenamento, CPU, RAM e assim por diante)
    - Falhas do sistema de host de virtualização
    - Rede logicamente ou fisicamente severa

2. [Configure](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [backup-Utilities](https://github.com/github/backup-utils), fornecendo instantâneos com versão para recuperação de desastres, hospedados em disponibilidade que é separada da instância primária.
3. [Configure o isolamento de subdomínio](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation), usando um certificado TLS válido, para atenuar scripts entre sites e outras vulnerabilidades relacionadas.

## <a name="configuring-blob-storage-for-github-actions"></a>Configurando o armazenamento de BLOBs para ações do GitHub

> [!NOTE]
> [Atualmente, as ações do GitHub estão disponíveis como uma versão beta limitada no GitHub Enterprise Server versão 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

O armazenamento de BLOBs externo é necessário para habilitar as ações do GitHub no servidor do GitHub Enterprise (atualmente disponível como um recurso "beta"). Esse armazenamento de BLOBs externo é usado por ações para armazenar artefatos e logs. As ações no GitHub Enterprise Server [dão suporte ao armazenamento de BLOBs do Azure como um provedor de armazenamento](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (e outros). Então, provisionaremos uma nova conta de armazenamento do Azure com um [tipo de conta de armazenamento](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) de BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Provisionar conta de armazenamento de BLOBs do Azure.":::

Depois que a implantação do novo recurso BlobStorage for concluída, copie e anote a cadeia de conexão (disponível em chaves de acesso). Precisaremos dessa cadeia de caracteres em breve.

## <a name="setting-up-the-github-actions-runner"></a>Configurando o executor de ações do GitHub

> [!NOTE]
> [Atualmente, as ações do GitHub estão disponíveis como uma versão beta limitada no GitHub Enterprise Server versão 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Neste ponto, você deve ter uma instância do GitHub Enterprise Server em execução, com uma conta de administrador criada. Você também deve ter armazenamento de blob externo que as ações do GitHub usarão para persistência.

Agora, vamos criar em algum lugar para que as ações do GitHub sejam executadas; novamente, usaremos a solução Azure VMware.

Primeiro, vamos provisionar uma nova VM no cluster. Vamos basear nossa VM em [uma versão recente do Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Provisionar uma nova VM.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Provisione uma nova etapa 2 da VM.":::

Depois que a VM for criada, ligue-a e conecte-se a ela via SSH.

Em seguida, instale [o aplicativo executor de ações](https://github.com/actions/runner) , que executa um trabalho de um fluxo de trabalhos de ações do github. Identifique e baixe a versão mais recente do Linux x64 do executor de ações, seja na [página versões](https://github.com/actions/runner/releases) ou executando o script rápido a seguir. Esse script exige que a rotação e a [JQ](https://stedolan.github.io/jq/) estejam presentes na VM.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Agora você deve ter um arquivo localmente em sua VM, Actions-Runner-Linux-arm64- \* . tar. gz. Extrair este tarball localmente:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Essa extração desempacota alguns arquivos localmente, incluindo um `config.sh` script e `run.sh` , que voltaremos em breve.

## <a name="enabling-github-actions"></a>Habilitando ações do GitHub

> [!NOTE]
> [Atualmente, as ações do GitHub estão disponíveis como uma versão beta limitada no GitHub Enterprise Server versão 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Quase lá! Vamos configurar e habilitar as ações do GitHub na instância do servidor do GitHub Enterprise. Precisaremos [acessar o Shell administrativo da instância do servidor empresarial do GitHub sobre SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)e, em seguida, executar os seguintes comandos:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Próxima Execução:

`ghe-actions-check -s blob`

Você deve ver a saída: "o armazenamento de BLOBs está íntegro".

Agora que as ações do GitHub estão configuradas, habilite-as para seus usuários. Entre em sua instância do GitHub Enterprise Server como administrador e selecione o ícone de ![ Rocket.](media/github-enterprise-server/rocket-icon.png) no canto superior direito de qualquer página. Na barra lateral esquerda, selecione **visão geral da empresa**, **políticas**, **ações** e selecione a opção para **habilitar ações para todas as organizações**.

Em seguida, configure o executor na guia **executores auto-hospedados** . Selecione **Adicionar novo** e, em seguida, **novo executor** na lista suspensa.

Na próxima página, você verá um conjunto de comandos a serem executados. precisamos apenas copiar o comando para **Configurar** o executor, por exemplo:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Copie o `config.sh` comando e cole-o em uma sessão no executor de ações (criado anteriormente).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Executor de ações.":::

Use o comando run.sh para *executar* o executor:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Execute o executor.":::

Para disponibilizar esse executor para as organizações em sua empresa, edite seu acesso à organização:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Edite o acesso ao executor.":::

Aqui, vamos disponibilizá-lo para todas as organizações, mas você pode limitar o acesso a um subconjunto de organizações e até mesmo a repositórios específicos.

## <a name="optional-configuring-github-connect"></a>Adicional Configurando o GitHub Connect

Embora essa etapa seja opcional, é recomendável se você planeja consumir ações de código aberto disponíveis em GitHub.com. Ele permite que você crie o trabalho de outras pessoas referenciando essas ações reutilizáveis em seus fluxos.

Para habilitar o GitHub Connect, siga as etapas em [habilitando o acesso automático a ações do github.com usando o GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Depois que o GitHub Connect estiver habilitado, selecione o **servidor para usar as ações de github.com na opção execuções de fluxo de trabalho** .

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Habilitar o uso de ações de GitHub.com em execuções de fluxo de trabalho.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Configurando e executando seu primeiro fluxo de trabalho

Agora que as ações e o GitHub Connect estão configurados, vamos colocar todo esse trabalho em bom uso. Aqui está um exemplo de fluxo de trabalho que faz referência a excelente [octokit/solicitação-ação](https://github.com/octokit/request-action), permitindo "gerar script" do GitHub por meio de interações usando a API do GitHub, da plataforma de ações do github.

Neste fluxo de trabalho básico, usaremos `octokit/request-action` apenas para abrir um problema no GitHub usando a API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Fluxo de trabalho de exemplo.":::

>[!NOTE]
>GitHub.com hospeda a ação, mas quando ela é executada no GitHub Enterprise Server, ela usa *automaticamente* a API do GitHub Enterprise Server.

Se você optar por não habilitar o GitHub Connect, poderá usar o fluxo de trabalho alternativo a seguir.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Exemplo de fluxo de trabalho alternativo.":::

Navegue até um repositório em sua instância e adicione o fluxo de trabalho acima como: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Outro fluxo de trabalho de exemplo.":::

Na guia **ações** do seu repositório, aguarde a execução do fluxo de trabalho.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Fluxo de trabalho de exemplo executado.":::

Você também pode ver que ele está sendo processado pelo executor.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Fluxo de trabalho processado pelo executor.":::

Se tudo tiver sido executado com êxito, você verá um novo problema em seu repositório, intitulado "Olá, mundo".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Exemplo no repositório.":::

Parabéns! Você acabou de concluir seu primeiro fluxo de trabalho de ações no GitHub Enterprise Server, em execução em sua nuvem privada da solução Azure VMware.

Neste artigo, configuramos uma nova instância do GitHub Enterprise Server, o equivalente autohospedado de GitHub.com, na parte superior da sua nuvem privada da solução Azure VMware. Essa instância inclui suporte para ações do GitHub e usa o armazenamento de BLOBs do Azure para persistência de logs e artefatos. Mas estamos apenas abordando a superfície do que você pode fazer com as ações do GitHub. Confira a lista de ações no [Marketplace do GitHub](https://github.com/marketplace)ou [Crie sua própria](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a configuração do GitHub Enterprise Server em sua nuvem privada da solução Azure VMware, talvez queira saber mais sobre: 

- [Introdução às ações do GitHub](https://docs.github.com/en/actions).
- [Unindo o programa Beta](https://resources.github.com/beta-signup/).
- [Administração do GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started).

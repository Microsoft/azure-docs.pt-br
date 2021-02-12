---
title: Solução do Azure VMware por CloudSimple-configure o vCenter na nuvem privada para a automação do vRealize
description: Descreve como configurar um VMware vCenter Server em sua nuvem privada do CloudSimple como um ponto de extremidade para a automação do VMware vRealize
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895046"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configurar o vCenter em sua nuvem privada para a automação do VMware vRealize

Você pode configurar um VMware vCenter Server em sua nuvem privada do CloudSimple como um ponto de extremidade para a automação do VMware vRealize.

## <a name="before-you-begin"></a>Antes de começar

Conclua estas tarefas antes de configurar o servidor do vCenter:

* Configure uma [conexão VPN site a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre seu ambiente local e sua nuvem privada.
* [Configure o encaminhamento DNS de solicitações de DNS locais](on-premises-dns-setup.md) para os servidores DNS para sua nuvem privada.
* Envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para criar um usuário administrativo de IaaS de automação vRealize com o conjunto de permissões que estão listadas na tabela a seguir.

| Valor do atributo | Permissão |
------------ | ------------- |  
| Repositório de dados |  Alocar espaço <br> Procurar repositório de armazenamento |
| Cluster de repositório de armazenamento | Configurar um cluster de repositório de armazenamento |
| Pasta | Criar pasta <br>Excluir Pasta |
| Global |  Gerenciar atributos personalizados<br>Definir atributo personalizado |
| Rede | Atribuir rede |
| Permissões | Modificar permissões |
| Recurso | Atribuir VM ao pool de recursos<br>Migrar máquina virtual desligada<br>Migrar máquina virtual ligada |
| Inventário de máquina virtual |  Criar a partir de existente<br>Criar Novo<br>Mover<br>Remover | 
| Interação da máquina virtual |  Configurar mídia de CD<br>Interação do console<br>Conexão de Dispositivo<br>Desligar<br>Ligar<br>Redefinir<br>Suspend<br>Instalação de ferramentas | 
| Configuração de Máquina Virtual |  Adicionar disco existente<br>Adicionar novo disco<br>Adicionar ou remover<br>Remover disco<br>Avançado<br>Alterar contagem de CPU<br>Alterar recurso<br>Estender disco virtual<br>Controle de Alterações de disco<br>Memória<br>Modificar configurações do dispositivo<br>Renomear<br>Definir anotação (versão 5,0 e posterior)<br>Configurações<br>Posicionamento do Swapfile |
| Provisionamento |  Personalizar<br>Clonar modelo<br>Clonar máquina virtual<br>Implantar o modelo<br>Ler especificações de personalização |
| Estado da Máquina Virtual | Criar instantâneo<br>Remover instantâneo<br>Reverter para instantâneo |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instalar a automação do vRealize em seu ambiente local

1. Entre no dispositivo de servidor IaaS da automação do vRealize como o administrador de IaaS que o suporte do CloudSimple criou para você.
2. Implante um agente do vSphere para o ponto de extremidade de automação do vRealize.
    1. Acesse https://*VRA-URL*: 5480/Installer, em que *VRA-URL* é a URL que você usa para acessar a interface do usuário de administração da automação do vRealize.
    2. Clique no **instalador IaaS** para baixar o instalador.<br>
    A Convenção de nomenclatura para o arquivo do instalador é setup_ *VRA-URL* @5480.exe .
    3. Execute o instalador. Na tela de Boas-Vindas, clique em **Avançar**.
    4. Aceite o EULA e clique em **Avançar**.
    5. Forneça as informações de entrada, clique em **aceitar certificado** e, em seguida, clique em **Avançar**.
    ![credenciais do vRA](media/configure-vra-endpoint-login.png)
    6. Selecione **instalação personalizada** e **agentes de proxy** e clique em **Avançar**.
    ![tipo de instalação do vRA](media/configure-vra-endpoint-install-type.png)
    7. Insira as informações de entrada do servidor IaaS e clique em **Avançar**. Se você estiver usando Active Directory, insira o nome de usuário no formato **domínio \ usuário** . Caso contrário, use **user@domain** Format.
    ![informações de logon do vRA](media/configure-vra-endpoint-account.png)
    8. Para as configurações de proxy, digite **vSphere** para o **tipo de agente**. Insira um nome para o agente.
    9. Insira o FQDN do servidor IaaS no **host do serviço do Gerenciador** e os campos do **host do serviço Web Gerenciador de modelos** . Clique em **testar** para testar a conexão para cada um dos valores de FQDN. Se o teste falhar, modifique as configurações de DNS para que o nome de host do servidor IaaS seja resolvido.
    10. Insira um nome para o ponto de extremidade do vCenter Server para a nuvem privada. Registre o nome para uso posterior no processo de configuração.

        ![proxy de instalação do vRA](media/configure-vra-endpoint-proxy.png)

    11. Clique em **Avançar**.
    12. Clique em **Instalar**.

## <a name="configure-the-vsphere-agent"></a>Configurar o agente vSphere

1. Vá para https://*VRA-URL*/vcac e entre como **ConfigurationAdmin**.
2. Selecione pontos de extremidade das extremidades da **infraestrutura**  >    >  .
3. Selecione **novo**  >    >  **vSphere** virtual.
4. Insira o nome do ponto de extremidade vSphere que você especificou no procedimento anterior.
5. Para **endereço**, insira a URL de vCenter Server de nuvem privada no formato https://*vCenter-FQDN*/SDK, em que *vCenter-FQDN* é o nome do servidor vCenter.
6. Insira as credenciais para o usuário administrativo IaaS do vRealize Automation que o suporte do CloudSimple criou para você.
7. Clique em **testar conexão** para validar as credenciais do usuário. Se o teste falhar, verifique a URL, as informações da conta e o [nome do ponto de extremidade](#verify-the-endpoint-name) e teste novamente.
8. Após um teste bem-sucedido, clique em **OK** para criar o ponto de extremidade vSphere.
    ![acesso de configuração de ponto de extremidade vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verificar o nome do ponto de extremidade

Para identificar o nome do ponto de extremidade correto do vCenter Server, faça o seguinte:

1. Abra um prompt de comando no dispositivo IaaS.
2. Altere o diretório para C:\Arquivos de programas (x86) \VMware\vCAC\Agents\agent-name, em que *Agent-Name* é o nome atribuído ao ponto de extremidade do vCenter Server.
3. Execute o comando a seguir.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A saída é semelhante à seguinte. O valor do `managementEndpointName` campo é o nome do ponto de extremidade.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```

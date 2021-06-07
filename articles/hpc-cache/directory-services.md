---
title: Usar grupos estendidos no cache HPC do Azure
description: Como configurar serviços de diretório para acesso de cliente a destinos de armazenamento no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563361"
---
# <a name="configure-directory-services"></a>Configurar serviços de diretório

As configurações de **serviços de diretório** permitem que o cache do Azure HPC use uma fonte externa para autenticar usuários para acessar o armazenamento de back-end.

Talvez seja necessário habilitar **grupos estendidos** se o fluxo de trabalho incluir destinos de armazenamento NFS e clientes que são membros de mais de 16 grupos.

Depois de clicar no botão para habilitar grupos estendidos, você deve escolher a fonte que o cache HPC do Azure usará para obter as credenciais de usuário e de grupo.

* [Active Directory](#configure-active-directory) -obter credenciais de um servidor Active Directory externo. Você não pode usar Azure Active Directory para esta tarefa.
* [Arquivo simples](#configure-file-download) – baixar `/etc/group` e `/etc/passwd` arquivos de um local de rede.
* [LDAP](#configure-ldap) -obtenha as credenciais de uma fonte compatível com LDAP (Lightweight Directory Access Protocol).

> [!NOTE]
> Verifique se o cache pode acessar sua fonte de informações de grupo de dentro de sua sub-rede segura.<!-- + details/examples -->

O campo **nome de usuário baixado** mostra o status do download de informações mais recentes do grupo.

![captura de tela da página de configurações da página serviços de diretório no portal, com a opção Sim selecionada para grupos estendidos e o menu suspenso rotulado baixar origem abrir](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Configurar o Active Directory

Esta seção explica como configurar o cache para obter credenciais de usuário e de grupo de um servidor de Active Directory externo (AD).

Em **detalhes do Active Directory**, forneça estes valores:

* **DNS primário** -especifique o endereço IP de um servidor de nome de domínio que o cache pode usar para resolver o nome de domínio do AD.

* **DNS secundário** (opcional) – Insira o endereço de um servidor de nomes a ser usado se o servidor primário não estiver disponível.

* **Nome de domínio DNS do AD** – forneça o nome de domínio totalmente qualificado do servidor do AD ao qual o cache se associará para obter as credenciais.

* **Nome do servidor de cache (conta de computador)** – defina o nome que será atribuído a esse cache do HPC quando ele ingressar no domínio do AD. Especifique um nome que seja fácil de reconhecer como esse cache. O nome pode ter até 15 caracteres e pode incluir letras maiúsculas ou minúsculas, números e hifens (-).

Na seção **credenciais** , forneça um nome de usuário e senha de administrador do AD que o cache HPC do Azure pode usar para acessar o servidor do AD. Essas informações são criptografadas quando armazenadas e não podem ser consultadas.

Salve as configurações clicando no botão na parte superior da página.

![captura de tela da seção detalhes de download com Active Directory valores preenchidos](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Configurar download de arquivo

Esses valores serão necessários se você quiser baixar arquivos com as informações de usuário e grupo. Os arquivos devem estar no padrão Linux/UNIX `/etc/group` e no `/etc/passwrd` formato.

* **URI do arquivo de usuário** – Insira o URI completo para o `/etc/passwrd` arquivo.
* **URI do arquivo de grupo** – Insira o URI completo para o `/etc/group` arquivo.

![captura de tela da seção de detalhes de download para um download de arquivo simples](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Configurar LDAP

Preencha esses valores se desejar usar uma origem LDAP não AD para obter credenciais de usuário e de grupo. Verifique com o administrador LDAP se você precisa de ajuda com esses valores.

* **Servidor LDAP** -Insira o nome de domínio totalmente qualificado ou o endereço IP do servidor LDAP a ser usado. <!-- only one, not up to 3 -->

* **DN base LDAP** -especifique o nome distinto base para o domínio LDAP, no formato DN. Pergunte ao administrador de LDAP se você não conhece seu DN de base.

O servidor e o DN de base são as únicas configurações necessárias para fazer o LDAP funcionar, mas as opções adicionais tornam sua conexão mais segura.

![captura de tela da área de configuração de LDAP da página de configurações de página dos serviços de diretório](media/group-download-details-ldap.png)

Na seção **acesso seguro** , você pode habilitar a criptografia e a validação de certificado para a conexão LDAP. Depois de clicar em **Sim** para habilitar a criptografia, você terá estas opções:

* **Validar certificado** – quando isso é definido, o certificado do servidor LDAP é verificado em relação à autoridade de certificação no campo URI abaixo.

* **URI do certificado de autoridade de certificação** – especifique o caminho para o certificado autoritativo. Isso pode ser um link para um certificado validado pela autoridade de certificação ou para um certificado autoassinado. Esse campo é necessário para usar a configuração de certificados validados externamente.

* **Certificado de download automático** – escolha **Sim** se quiser tentar baixar um certificado assim que enviar essas configurações.

Preencha a seção de **credenciais** se quiser usar credenciais estáticas para segurança LDAP. Essas informações são criptografadas quando armazenadas e não podem ser consultadas.

* **Associar DN** – Insira o nome distinto do associado a ser usado para autenticar no servidor LDAP. (Use o formato DN.)
* **Associar senha** – forneça a senha para o DN de ligação.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o acesso de cliente na [montagem do cache HPC do Azure](hpc-cache-mount.md)
* Se suas credenciais não forem baixadas corretamente, consulte o administrador da sua fonte de credenciais. Abra um [tíquete de suporte](hpc-cache-support-ticket.md) , se necessário.

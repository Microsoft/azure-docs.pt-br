---
title: Azure AD Connect-atualizar o certificado TLS/SSL para um farm de AD FS | Microsoft Docs
description: Este documento detalha as etapas para atualizar o certificado TLS/SSL de um farm de AD FS usando Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279900"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado TLS/SSL para um farm de Serviços de Federação do Active Directory (AD FS) (AD FS)

## <a name="overview"></a>Visão geral
Este artigo descreve como você pode usar Azure AD Connect para atualizar o certificado TLS/SSL para um farm de Serviços de Federação do Active Directory (AD FS) (AD FS). Você pode usar a ferramenta Azure AD Connect para atualizar facilmente o certificado TLS/SSL para o farm de AD FS mesmo se o método de entrada do usuário selecionado não estiver AD FS.

Você pode executar toda a operação de atualização do certificado TLS/SSL para o farm de AD FS em todos os servidores de Federação e proxy de aplicativo Web (WAP) em três etapas simples:

![Três etapas](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre os certificados usados pelo AD FS, confira [Noções básicas sobre os certificados usados pelo AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11)).

## <a name="prerequisites"></a>Pré-requisitos

* **Farm do AD FS**: certifique-se de que seu farm do AD FS seja baseado no Windows Server 2012 R2 ou posterior.
* **Azure AD Connect**: certifique-se de que a versão do Azure AD Connect seja 1.1.553.0 ou posterior. Você usará a tarefa **Atualizar certificado SSL do AD FS**.

![Atualizar tarefa TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Etapa 1: fornecer informações do farm do AD FS

O Azure AD Connect tenta obter as informações sobre o farm do AD FS automaticamente por:
1. Consulta das informações do farm do AD FS (Windows Server 2016 ou superior).
2. Referência às informações de execuções anteriores, armazenadas localmente com o Azure AD Connect.

Modifique a lista de servidores exibida adicionando ou removendo os servidores para refletir a configuração atual do farm do AD FS. Assim que as informações do servidor são fornecidas, Azure AD Connect exibe a conectividade e o status atual do certificado TLS/SSL.

![Informações do servidor AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Se a lista contiver um servidor que não faz mais parte do farm do AD FS, clique em **Remover** para excluir o servidor da lista de servidores no farm do AD FS.

![Servidor offline na lista](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> A remoção de um servidor da lista de servidores do farm do AD FS no Azure AD Connect é uma operação local e atualiza as informações para o farm do AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não modificará à configuração no AD FS para refletir a alteração.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Etapa 2: fornecer um novo certificado TLS/SSL

Depois de confirmar as informações sobre AD FS servidores de farm, Azure AD Connect solicita o novo certificado TLS/SSL. Forneça um certificado PFX protegido por senha para continuar a instalação.

![Certificado TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Depois de fornecer o certificado, o Azure AD Connect passará por uma série de pré-requisitos. Verifique o certificado para garantir que esteja correto para o farm do AD FS:

-   O nome da entidade/nome alternativo da entidade do certificado é o mesmo que o nome do serviço de federação, ou é um certificado curinga.
-   O certificado é válido por mais de 30 dias.
-   A cadeia confiável de certificado é válida.
-   O certificado é protegido por senha.

## <a name="step-3-select-servers-for-the-update"></a>Etapa 3: Selecionar servidores para atualização

Na próxima etapa, selecione os servidores que precisam ter o certificado TLS/SSL atualizado. Servidores offline não podem ser selecionados para a atualização.

![Selecionar servidores para atualizar](./media/how-to-connect-fed-ssl-update/selectservers.png)

Após a conclusão da configuração, o Azure AD Connect exibirá a mensagem que indica o status da atualização e fornece uma opção para verificar a entrada do AD FS.

![Configuração concluída](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Perguntas frequentes

* **Qual deve ser o nome do assunto do certificado para o novo AD FS certificado TLS/SSL?**

    O Azure AD Connect verifica se o nome da entidade/nome da entidade alternativo do certificado contém o nome do serviço de federação. Por exemplo, se o nome do seu serviço de Federação for fs.contoso.com, o nome da entidade alternativo/nome da entidade deverá ser fs.contoso.com.  Certificados curinga também são aceitos.

* **Por que as credenciais estão sendo solicitadas novamente na página do servidor WAP?**

    Se as credenciais fornecidas para conexão com servidores do AD FS também não tiverem o privilégio para gerenciar os servidores WAP, o Azure AD Connect solicitará credenciais que tenham privilégio administrativo nos servidores WAP.

* **O servidor é mostrado como offline. O que devo fazer?**

    O Azure AD Connect não poderá executar nenhuma operação se o servidor estiver offline. Se o servidor fizer parte do farm do AD FS, verifique a conectividade com o servidor. Depois de resolver o problema, pressione o ícone de atualização para atualizar o status no assistente. Se o servidor fazia parte do farm, mas agora não existe mais, clique em **Remover** para excluí-lo da lista de servidores que o Azure AD Connect mantém. Remover o servidor da lista no Azure AD Connect não altera a própria configuração do AD FS. Se você estiver usando o AD FS no Windows Server 2016 ou posterior, o servidor permanecerá nas definições de configuração e será exibido na próxima vez em que a tarefa for executada.

* **Posso atualizar um subconjunto de meus servidores de farm com o novo certificado TLS/SSL?**

    Sim. Você também pode executar a tarefa **Atualizar certificado SSL** novamente para atualizar os servidores restantes. Na página **Selecionar servidores para atualização de certificado SSL**, você pode classificar a lista de servidores na **Data de expiração do SSL** para acessar facilmente os servidores que ainda não foram atualizados.

* **Removi o servidor na execução anterior, mas ele ainda está sendo mostrado como offline e listado na página servidores de AD FS. Por que o servidor offline ainda existe mesmo depois de removê-lo?**

    Remover o servidor da lista no Azure AD Connect não o remove na configuração do AD FS. O Azure AD Connect consulta o AD FS (Windows Server 2016 ou posterior) para saber quaisquer informações sobre o farm. Se o servidor ainda estiver presente na configuração do AD FS, ele será relacionado novamente na lista.  

## <a name="next-steps"></a>Próximas etapas

- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
- [Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect](how-to-connect-fed-management.md)
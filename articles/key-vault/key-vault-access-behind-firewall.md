---
title: "Acessar o Key Vault por trás do firewall | Microsoft Docs"
description: "Saiba como acessar o Azure Key Vault de um aplicativo por trás de um firewall"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: d00c6e0acf437d2bfc3c27e948f4646a6685b08f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Acessar o Cofre de Chaves do Azure por trás de um firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>P: meu aplicativo de cliente do cofre de chaves precisa estar por trás de um firewall. Quais portas, hosts ou endereços IP devo abrir para permitir o acesso a um cofre de chaves?
Para acessar um cofre de chaves, o aplicativo de cliente do cofre de chaves precisa acessar vários pontos de extremidade para várias funcionalidades:

* Autenticação via Azure AD (Azure Active Directory)
* Gerenciamento do Cofre de Chaves do Azure. Isso inclui criar, ler, atualizar, excluir e definir políticas de acesso por meio do Azure Resource Manager.
* O acesso e o gerenciamento de objetos (chaves e segredos) armazenados no cofre de chaves em si passa pelo ponto de extremidade específico do cofre de chaves (por exemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Dependendo do ambiente e configuração, há algumas variações.   

## <a name="ports"></a>Portas
Todo o tráfego para o cofre de chaves de todas as três funções (autenticação, gerenciamento e acesso ao plano de dados) passa por HTTPS: porta 443. No entanto, ocasionalmente, haverá tráfego HTTP (porta 80) para CRL. Os clientes que dão suporte a OCSP não devem chegar a CRL, mas ocasionalmente podem chegar a [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticação
Os aplicativos cliente do cofre de chaves precisarão acessar os pontos de extremidade do Azure Active Directory para autenticação. O ponto de extremidade usado depende da configuração de locatário do Azure AD, o tipo de entidade (UPN ou entidade de serviço) e o tipo de conta (por exemplo, uma conta da Microsoft ou uma conta corporativa ou de estudante).  

| Tipo de entidade | Ponto de extremidade:porta |
| --- | --- |
| Usuário usando a conta da Microsoft<br> (por exemplo, user@hotmail.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Governo dos EUA:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443<br><br> e <br>login.live.com:443 |
| Usuário ou entidade de serviço que usa uma conta corporativa ou de estudante com o Azure AD (por exemplo, user@contoso.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Governo dos EUA:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443 |
| Usuário ou entidade de serviço que usa uma conta corporativa ou de estudante, além de AD FS (Serviços de Federação do Active Directory) ou outro ponto de extremidade federado (por exemplo, user@contoso.com) |Todos os pontos de extremidade de uma conta corporativa ou de estudante, além do AD FS ou outros pontos de extremidade federados |

Há outros cenários complexos possíveis. Confira [Fluxo de autenticação do Azure Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Integrar aplicativos com o Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais.  

## <a name="key-vault-management"></a>Gerenciamento do Cofre de Chaves
Para o gerenciamento do Cofre de Chaves (CRUD e configuração de política de acesso), o aplicativo cliente do cofre de chaves precisa acessar um ponto de extremidade do Azure Resource Manager.  

| Tipo de operação | Ponto de extremidade:porta |
| --- | --- |
| Operações do plano de controle do cofre de chaves<br> por meio do Azure Resource Manager |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 |
| Graph API do Active Directory do Azure |**Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> graph.windows.net:443<br><br> **Azure Alemanha:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operações do cofre de chaves
Para todas as operações de criptografia e gerenciamento de objetos (chaves e segredos) do cofre de chaves, o cliente do cofre de chaves precisa acessar o ponto de extremidade do cofre de chaves. O sufixo DNS do ponto de extremidade varia de acordo com o local de seu cofre de chaves. O ponto de extremidade do Cofre de Chaves está no formato: *nome-do-cofre*.*sufixo-dns-específico-da-região*, conforme descrito na tabela a seguir.  

| Tipo de operação | Ponto de extremidade:porta |
| --- | --- |
| Operações, incluindo operações criptográficas em chaves; criar, ler, atualizar e excluir chaves e segredos; definir ou obter marcas e outros atributos em objetos de cofre de chaves (chaves ou segredos) |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalos de endereços IP
O serviço de Cofre de Chaves usa outros recursos do Azure, como infraestrutura PaaS. Portanto, não é possível fornecer um intervalo específico de endereços IP que pontos de extremidade do serviço de Cofre de Chaves terão em determinado momento. Se o firewall dá suporte apenas a intervalos de endereços IP, confira o documento [Intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Para autenticação e identidade (Azure Active Directory), seu aplicativo deve ser capaz de se conectar aos pontos de extremidade descritos em [Endereços de autenticação e identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Próximas etapas
Se você tiver dúvidas sobre o Cofre de Chaves, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).


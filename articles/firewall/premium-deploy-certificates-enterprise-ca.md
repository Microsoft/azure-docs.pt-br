---
title: Implantar e configurar certificados de AC corporativa para o Azure firewall Premium Preview
description: Saiba como implantar e configurar certificados de autoridade de certificação corporativa para o Azure firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525468"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Implantar e configurar certificados de AC corporativa para a versão prévia do firewall do Azure

> [!IMPORTANT]
> O firewall Premium do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


O Azure firewall Premium Preview inclui um recurso de inspeção de TLS, que requer uma cadeia de autenticação de certificado. Para implantações de produção, você deve usar uma PKI corporativa para gerar os certificados que você usa com o firewall Premium do Azure. Use este artigo para criar e gerenciar um certificado de autoridade de certificação intermediário para o Azure firewall Premium Preview.

Para obter mais informações sobre certificados usados pelo Azure firewall Premium Preview, consulte [Azure firewall Premium Preview Certificates](premium-certificates.md).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para usar uma AC corporativa para gerar um certificado a ser usado com o Azure firewall Premium Preview, você deve ter os seguintes recursos: 

- uma floresta Active Directory 
- uma autoridade de certificação raiz dos serviços de certificação Active Directory com o registro na Web habilitado 
- um firewall do Azure Premium com a política de firewall da camada Premium 
- um Azure Key Vault 
- uma identidade gerenciada com permissões de leitura para **certificados e segredos** definidos na política de acesso de Key Vault 

## <a name="request-and-export-a-certificate"></a>Solicitar e exportar um certificado

1. Acesse o site de registro da Web na AC raiz, normalmente, `https://<servername>/certsrv` e selecione **solicitar um certificado**.
1. Selecione **solicitação avançada de certificado**.
1. Selecione **criar e enviar uma solicitação para esta autoridade de certificação**.
1. Preencha o formulário usando o modelo de autoridade de certificação subordinado, conforme mostrado:
1. Envie a solicitação e instale o certificado.
1. Supondo que essa solicitação seja feita de um Windows Server usando o Internet Explorer, abra **Opções da Internet**.
1. Navegue até a guia **conteúdo** e selecione **certificados**.
1. Selecione o certificado que acabou de ser emitido e, em seguida, selecione **Exportar**.
1. Selecione **Avançar** para iniciar o assistente. Selecione **Sim, exportar a chave privada** e, em seguida, selecione **Avançar**.
1. o formato de arquivo. pfx é selecionado por padrão. Desmarque **incluir todos os certificados no caminho de certificação, se possível**. Se você exportar toda a cadeia de certificados, o processo de importação para o Firewall do Azure falhará.
1. Atribua e confirme uma senha para proteger a chave e, em seguida, selecione **Avançar**.
1. Escolha um nome de arquivo e um local de exportação e, em seguida, selecione **Avançar**.
1. Selecione **concluir** e mova o certificado exportado para um local seguro.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Adicionar o certificado a uma política de firewall

1. Na portal do Azure, navegue até a página certificados do seu Key Vault e selecione **gerar/importar**.
1. Selecione **importar** como o método de criação, nomeie o certificado, selecione o arquivo. pfx exportado, insira a senha e, em seguida, selecione **criar**.
1. Navegue até a página **inspeção TLS (versão prévia)** da política de firewall e selecione sua identidade gerenciada, Key Vault e certificado. 
1. Selecione **Salvar**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="Inspeção TLS":::

## <a name="validate-tls-inspection"></a>Validar a inspeção de TLS

1. Crie uma regra de aplicativo usando a inspeção TLS para a URL de destino ou FQDN de sua escolha.  Por exemplo: `*bing.com`.
1. Em um computador ingressado no domínio dentro do intervalo de origem da regra, navegue até o destino e selecione o símbolo de bloqueio ao lado da barra de endereços em seu navegador. O certificado deve mostrar que foi emitido por sua autoridade de certificação corporativa em vez de uma AC pública.
1. Mostre o certificado para exibir mais detalhes, incluindo o caminho do certificado.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="detalhes do certificado":::
1. No Log Analytics, execute a seguinte consulta KQL para retornar todas as solicitações que foram sujeitas à inspeção TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   O resultado mostra a URL completa do tráfego inspecionado: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="consulta KQL":::

## <a name="next-steps"></a>Próximas etapas

[Azure firewall Premium Preview no portal do Azure](premium-portal.md)

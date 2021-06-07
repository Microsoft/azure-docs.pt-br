---
title: Configurar um laboratório para usar Área de Trabalho Remota gateway no Azure DevTest Labs
description: Saiba como configurar um laboratório no Azure DevTest Labs com um gateway de área de trabalho remota para garantir o acesso seguro às VMs do laboratório sem precisar expor a porta RDP.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b15d4d39199c1a30eae292ece67f4553b656f530
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639589"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configurar seu laboratório no Azure DevTest Labs para usar um gateway de área de trabalho remota
No Azure DevTest Labs, você pode configurar um gateway de área de trabalho remota para seu laboratório para garantir o acesso seguro às VMs (máquinas virtuais) do laboratório sem a necessidade de expor a porta RDP. O laboratório fornece um local central para que os usuários do laboratório exibam e se conectem a todas as máquinas virtuais às quais eles têm acesso. O botão **conectar** na página **máquina virtual** cria um arquivo RDP específico do computador que você pode abrir para se conectar ao computador. Você pode personalizar e proteger ainda mais a conexão RDP conectando seu laboratório a um gateway de área de trabalho remota. 

Essa abordagem é mais segura porque o usuário do laboratório se autentica diretamente no computador do gateway ou pode usar as credenciais da empresa em um computador do gateway ingressado no domínio para se conectar a seus computadores. O laboratório também dá suporte ao uso da autenticação de token para o computador do gateway que permite aos usuários se conectarem às máquinas virtuais do laboratório sem ter a porta RDP exposta à Internet. Este artigo apresenta um exemplo de como configurar um laboratório que usa autenticação de token para se conectar a máquinas de laboratório.

## <a name="architecture-of-the-solution"></a>Arquitetura da solução

![Arquitetura da solução](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. A ação [obter conteúdo do arquivo RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) é chamada quando você seleciona o botão **conectar** . 1. 
1. A ação obter conteúdo do arquivo RDP invoca `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` para solicitar um token de autenticação.
    1. `{gateway-hostname}` é o nome de host do gateway especificado na página **configurações do laboratório** do seu laboratório na portal do Azure. 
    1. `{lab-machine-name}` é o nome do computador que você está tentando conectar.
    1. `{port-number}` é a porta na qual a conexão precisa ser feita. Normalmente, essa porta é 3389. Se a VM do laboratório estiver usando o recurso de [IP compartilhado](devtest-lab-shared-ip.md) no DevTest Labs, a porta será diferente.
1. O gateway de área de trabalho remota adia a chamada de `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` para uma função do Azure para gerar o token de autenticação. O serviço DevTest Labs inclui automaticamente a chave de função no cabeçalho da solicitação. A tecla de função deve ser salva no cofre de chaves do laboratório. O nome desse segredo a ser mostrado como **segredo do token de gateway** na página de configurações do **laboratório** do laboratório.
1. Espera-se que a função do Azure retorne um token para autenticação de token com base em certificado no computador do gateway.  
1. A ação obter conteúdo do arquivo RDP retorna o arquivo RDP completo, incluindo as informações de autenticação.
1. Você abre o arquivo RDP usando seu programa de conexão RDP preferencial. Lembre-se de que nem todos os programas de conexão RDP oferecem suporte à autenticação de token. O token de autenticação tem uma data de expiração, definida pelo aplicativo de funções. Faça a conexão com a VM do laboratório antes que o token expire.
1. Depois que o computador do gateway de área de trabalho remota autenticar o token no arquivo RDP, a conexão será encaminhada ao seu computador de laboratório.

### <a name="solution-requirements"></a>Requisitos da solução
Para trabalhar com o recurso de autenticação de token do DevTest Labs, há alguns requisitos de configuração para as máquinas de gateway, DNS (serviços de nomes de domínio) e funções.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisitos para computadores do gateway de área de trabalho remota
- O certificado TLS/SSL deve ser instalado no computador do gateway para lidar com o tráfego HTTPS. O certificado deve corresponder ao FQDN (nome de domínio totalmente qualificado) do balanceador de carga para o farm de gateway ou o FQDN do próprio computador se houver apenas um computador. Os certificados TLS/SSL curinga não funcionam.  
- Um certificado de autenticação instalado no (s) computador (es) de gateway. Crie um certificado de assinatura usando [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) script.
- Instale o módulo de [autenticação conectável](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) que dá suporte à autenticação de token para o gateway de área de trabalho remota. Um exemplo desse tipo de módulo é `RDGatewayFedAuth.msi` fornecido com as [imagens do System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true). Para obter mais informações sobre o System Center, consulte documentação e [detalhes de preços](https://www.microsoft.com/cloud-platform/system-center-pricing)do [System Center](/system-center/) .  
- O servidor de gateway pode manipular solicitações feitas para o `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    O nome do host do gateway é o FQDN do balanceador de carga do farm de gateway ou o FQDN do próprio computador se houver apenas um computador. O `{lab-machine-name}` é o nome do computador do laboratório que você está tentando conectar e a `{port-number}` porta is na qual a conexão será feita.  Por padrão, essa porta é 3389.  No entanto, se a máquina virtual estiver usando o recurso de [IP compartilhado](devtest-lab-shared-ip.md) no DevTest Labs, a porta será diferente.
- O módulo de [solicitação de roteamento de aplicativos](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para o IIS (servidor de informações da Internet) pode ser usado para redirecionar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` solicitações para a função do Azure, que manipula a solicitação para obter um token para autenticação.


## <a name="requirements-for-azure-function"></a>Requisitos para o Azure function
A função do Azure manipula a solicitação com o formato de `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` e retorna o token de autenticação com base no mesmo certificado de autenticação instalado nos computadores do gateway. O `{function-app-uri}` é o URI usado para acessar a função. A tecla de função é passada automaticamente no cabeçalho da solicitação. Para obter uma função de exemplo, consulte [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Requisitos para rede

- O DNS para o FQDN associado ao certificado TLS/SSL instalado nos computadores do gateway deve direcionar o tráfego para o computador do gateway ou o balanceador de carga do farm de máquinas do gateway.
- Se o computador do laboratório usa IPs privados, deve haver um caminho de rede do computador do gateway para o computador do laboratório, seja por meio do compartilhamento da mesma rede virtual ou do uso de redes virtuais emparelhadas.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configurar o laboratório para usar a autenticação de token 
Esta seção mostra como configurar um laboratório para usar um computador de gateway de área de trabalho remota que dá suporte à autenticação de token. Esta seção não aborda como configurar um farm de gateway de área de trabalho remota em si. Para obter essas informações, consulte a seção [exemplo para criar um gateway de área de trabalho remota](#sample-to-create-a-remote-desktop-gateway) no final deste artigo. 

Antes de atualizar as configurações do laboratório, armazene a chave necessária para executar a função com êxito para retornar um token de autenticação no cofre de chaves do laboratório. Você pode obter o valor da chave de função na página **gerenciar** da função no portal do Azure. Para obter mais informações sobre como salvar um segredo em um cofre de chaves, consulte [Adicionar um segredo a Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Salve o nome do segredo para uso posterior.

Para localizar a ID do cofre de chaves do laboratório, execute o seguinte comando de CLI do Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configure o laboratório para usar a autenticação de token usando estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione seu **laboratório**.
1. Na página do laboratório, selecione **configuração e políticas**.
1. No menu à esquerda, na seção **configurações** , selecione **configurações de laboratório**.
1. Na seção **área de trabalho remota** , insira o nome de domínio totalmente qualificado (FQDN) ou o endereço IP do computador ou farm do gateway dos serviços de área de trabalho remota para o campo **nome de host do gateway** . Esse valor deve corresponder ao FQDN do certificado TLS/SSL usado em máquinas de gateway.

    ![Opções de área de trabalho remota em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Na seção **área de trabalho remota** , para segredo do **token de gateway** , insira o nome do segredo criado anteriormente. Esse valor não é a própria chave de função, mas o nome do segredo no cofre de chaves do laboratório que contém a tecla de função.

    ![Segredo do token de gateway nas configurações do laboratório](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salvar** For.

    > [!NOTE] 
    > Ao clicar em **salvar**, você concorda em [área de trabalho remota termos de licença do gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Bem-vindo ao serviços de área de trabalho remota](/windows-server/remote/remote-desktop-services/Welcome-to-rds) e [implantar seu ambiente de área de trabalho remota](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se a configuração do laboratório por meio da automação for preferida, consulte [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) para obter um exemplo de script do PowerShell para definir as configurações de **segredo** do gateway e **do nome de host** . O [repositório GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) também fornece um modelo de Azure Resource Manager que cria ou atualiza um laboratório com as configurações de **nome de host** e de **segredo** do gateway do gateway.

## <a name="configure-network-security-group"></a>Configurar um grupo de segurança de rede
Para proteger ainda mais o laboratório, um NSG (grupo de segurança de rede) pode ser adicionado à rede virtual usada pelas máquinas virtuais do laboratório. Para obter instruções sobre como configurar um NSG, consulte [criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

Aqui está um exemplo de NSG que permite apenas o tráfego que passa pela primeira vez pelo gateway para alcançar máquinas de laboratório. A origem nessa regra é o endereço IP do computador do gateway único ou o endereço IP do balanceador de carga na frente dos computadores do gateway.

![Grupo de segurança de rede – regras](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exemplo para criar um gateway de área de trabalho remota

> [!NOTE] 
> Usando os modelos de exemplo, você concorda em [área de trabalho remota termos de licença do gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Bem-vindo ao serviços de área de trabalho remota](/windows-server/remote/remote-desktop-services/Welcome-to-rds) e [implantar seu ambiente de área de trabalho remota](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

O [repositório GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornece alguns exemplos para ajudar a configurar os recursos necessários para usar a autenticação de token e o gateway de área de trabalho remota com o DevTest Labs. Esses exemplos incluem modelos de Azure Resource Manager para máquinas de gateway, configurações de laboratório e aplicativo de funções.

Siga estas etapas para configurar uma solução de exemplo para o farm de gateway de área de trabalho remota.

1. Crie um certificado de autenticação.  Execute [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Salve a impressão digital, a senha e a codificação base64 do certificado criado.
2. Obtenha um certificado TLS/SSL. O FQDN associado ao certificado TLS/SSL deve ser para o domínio que você controla. Salve a impressão digital, a senha e a codificação Base64 para esse certificado. Para obter a impressão digital usando o PowerShell, use os comandos a seguir.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    Para obter a codificação base64 usando o PowerShell, use o comando a seguir.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. Baixar arquivos de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    O modelo requer acesso a alguns outros modelos do Resource Manager e recursos relacionados no mesmo URI base. Copie todos os arquivos de [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) e RDGatewayFedAuth.msi para um contêiner de BLOB em uma conta de armazenamento.  
4. Implantar **azuredeploy.jsno** de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . O modelo usa os seguintes parâmetros:
    - adminUsername – obrigatório.  Nome de usuário do administrador para os computadores do gateway.
    - adminPassword – obrigatório. Senha para a conta de administrador para os computadores do gateway.
    - instanceCount – número de máquinas de gateway a serem criadas.  
    - alwaysOn – indica se é para manter o aplicativo Azure Functions criado em um estado quente ou não. Manter o Azure Functions aplicativo evitará atrasos quando os usuários tentam se conectar pela primeira vez à VM do laboratório, mas têm implicações de custo.  
    - tokenLifetime – o período de tempo que o token criado será válido. O formato é HH: MM: SS.
    - sslCertificate – a codificação base64 do certificado TLS/SSL para o computador do gateway.
    - sslCertificatePassword – a senha do certificado TLS/SSL para o computador do gateway.
    - sslCertificateThumbprint-a impressão digital do certificado para identificação no repositório de certificados local do certificado TLS/SSL.
    - signCertificate – a codificação Base64 para o certificado de autenticação para o computador do gateway.
    - signCertificatePassword – a senha para o certificado de autenticação para o computador do gateway.
    - signCertificateThumbprint-a impressão digital do certificado para identificação no repositório de certificados local do certificado de autenticação.
    - _artifactsLocation – local do URI onde todos os recursos de suporte podem ser encontrados. Esse valor deve ser um UIR totalmente qualificado, não um caminho relativo.
    - _artifactsLocationSasToken – o token de SAS (assinatura de acesso compartilhado) usado para acessar recursos de suporte, se o local for uma conta de armazenamento do Azure.

    O modelo pode ser implantado usando o CLI do Azure usando o seguinte comando:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Aqui estão as descrições dos parâmetros:

    - O {Storage-Account-Endpoint} pode ser obtido executando `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  O {Storage-acct-Name} é o nome da conta de armazenamento que contém os arquivos que você carregou.  
    - O {container-Name} é o nome do contêiner no {Storage-acct-Name} que mantém os arquivos que você carregou.  
    - O {SAS-token} pode ser obtido executando `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - O {Storage-acct-Name} é o nome da conta de armazenamento que contém os arquivos que você carregou.  
        - O {container-Name} é o nome do contêiner no {Storage-acct-Name} que mantém os arquivos que você carregou.  
        - A {UTC-Expiration-Date} é a data, em UTC, na qual o token SAS expirará e o token SAS não poderá mais ser usado para acessar a conta de armazenamento.

    Registre os valores para gatewayFQDN e gatewayIP da saída de implantação de modelo. Você também precisará salvar o valor da chave de função para a função recém-criada, que pode ser encontrada na guia Configurações do [aplicativo de funções](../azure-functions/functions-how-to-use-azure-function-app-settings.md) .
5. Configure o DNS para que o FQDN do certificado TLS/SSL Direcione para o endereço IP de gatewayIP da etapa anterior.

    Depois que o farm de gateway de Área de Trabalho Remota é criado e as atualizações de DNS apropriadas são feitas, ele está pronto para ser usado por um laboratório no DevTest Labs. As configurações de **segredo do token** do gateway e do nome de **host do gateway** devem ser configuradas para usar as máquinas de gateway implantadas. 

    > [!NOTE]
    > Se o computador do laboratório usa IPs privados, deve haver um caminho de rede do computador do gateway para o computador do laboratório, seja por meio do compartilhamento da mesma rede virtual ou de uma rede virtual emparelhada.

    Quando o gateway e o laboratório estiverem configurados, o arquivo de conexão criado quando o usuário do laboratório clicar na **conexão** incluirá automaticamente as informações necessárias para se conectar usando a autenticação de token.     

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo a seguir para saber mais sobre Serviços de Área de Trabalho Remota: [serviços de área de trabalho remota documentação](/windows-server/remote/remote-desktop-services/Welcome-to-rds)
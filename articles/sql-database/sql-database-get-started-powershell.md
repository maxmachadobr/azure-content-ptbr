<properties
    pageTitle="Nova configuração do Banco de Dados SQL com o PowerShell | Microsoft Azure"
    description="Saiba como criar um banco de dados SQL com o PowerShell. As tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell."
    keywords="criar um novo banco de dados sql, configuração do banco de dados"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# Criar um banco de dados SQL e executar tarefas comuns de instalação de banco de dados com cmdlets do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Saiba como criar um banco de dados SQL usando os cmdlets do PowerShell. (Para criar bancos de dados elásticos, consulte [Criar um novo pool de banco de dados elásticos com o PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Configuração do banco de dados: criar um grupo de recursos, servidor e regra de firewall

Assim que você tiver acesso para executar os cmdlets em sua assinatura do Azure selecionada, a próxima etapa será estabelecer o grupo de recursos que contém o servidor no qual o banco de dados será criado. Você pode editar o próximo comando a fim de usar qualquer local válido de sua escolha. Execute **(Get-AzureRmLocation | Where-Object {$\_.Providers -eq "Microsoft.Sql" }).Locations** para obter uma lista de locais válidos.

Execute o seguinte comando para criar um grupo de recursos:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Criar um servidor

Os bancos de dados SQL são criados dentro dos servidores do Banco de Dados SQL. Execute **New-AzureRmSqlServer** para criar um servidor. Esse nome para seu servidor deve ser exclusivo para todos os servidores do Banco de Dados SQL. Se o nome do servidor já existir, você obterá um erro. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Você pode editar o comando para usar qualquer local válido escolhido, mas deve usar o mesmo local que usou para o grupo de recursos criado na etapa anterior.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando você executar esse comando, serão solicitados seu nome de usuário e senha. Não insira suas credenciais do Azure. Em vez disso, insira o nome de usuário e a senha para criar como o administrador do servidor. O script na parte inferior deste artigo mostra como definir as credenciais do servidor no código.

Os detalhes do servidor são exibidos após a criação bem-sucedida do servidor.

### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Para acessar o servidor, será necessário estabelecer uma regra de firewall. Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para seu computador.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall são exibidos após a criação bem-sucedida da regra.

Para permitir que outros serviços do Azure acessem o servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress para 0.0.0.0. Essa regra permite que o tráfego do Azure de qualquer assinatura do Azure acesse o servidor.

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md).


## Criar um banco de dados SQL

Agora que você tem um grupo de recursos, um servidor e uma regra de firewall configurados, é possível acessar o servidor.

O comando a seguir cria um banco de dados SQL (em branco) na camada de serviço Standard com um nível de desempenho S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes do banco de dados são exibidos após a criação bem-sucedida do banco de dados.

## Criar um Banco de Dados SQL com script do PowerShell

O script do PowerShell a seguir cria um banco de dados SQL e todos os seus recursos dependentes. Substitua todos os `{variables}` por valores específicos para sua assinatura e recursos (remova os **{}** quando definir seus valores).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Próximas etapas
Depois de criar um banco de dados SQL e de realizar tarefas básicas de configuração de banco de dados, você estará pronto para o seguinte:

- [Gerenciar o Banco de Dados SQL com o PowerShell](sql-database-command-line-tools.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)


## Recursos adicionais

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0824_2016-->
<properties 
	pageTitle="Protegendo os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS na nuvem." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Protegendo os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS

Se sua organização for federada com o Active Directory do Azure e você tiver recursos que são acessados pelo AD do Azure, será possível usar o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory para proteger esses recursos. Use os procedimentos a seguir para proteger os recursos do Active Directory do Azure com o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory.

## Para proteger recursos do AD do Azure usando o AD FS, siga este procedimento: 



1. Use as etapas descritas em [ativar autenticação multifator](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para que os usuários habilitem uma conta.
2. Use o procedimento a seguir para configurar uma regra de declaração:

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	Inicie o Console de gerenciamento do AD FS.
- 	Navegue até Terceira Parte Confiável e clique com o botão direito do mouse em Terceira Parte Confiável. Selecione Editar Regras de Declaração...
- 	Clique em Adicionar Regra...
- 	No menu suspenso, selecione Enviar Declarações Usando uma Regra Personalizada e clique em Avançar.
- 	Insira um nome para a regra de declaração.
- 	Em Regra personalizada: adicione o seguinte:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Declaração correspondente:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Clique em OK. Clique em Concluir. Feche o Console de gerenciamento do AD FS.

Os usuários podem concluir a conexão usando o método local (como o cartão inteligente).

## IPs confiáveis para usuários federados
IPs confiáveis permitem aos administradores ignorar a autenticação multifator para endereço IP específico ou para usuários federados que têm as solicitações originadas em seu próprios intranet. As seções a seguir descrevem como configurar IPs confiáveis do Azure Multi-Factor Authentication com usuários federados e desviar a autenticação de multifatores quando uma solicitação se originar de dentro de uma intranet de usuários federados. Isso é conseguido por meio da configuração do AD FS para usar uma passagem ou filtrar um modelo de declaração de entrada com o tipo de declaração Dentro da rede corporativa. Este exemplo usa o Office 365 para a relação de confiança com terceira parte confiável.

### Configurar as regras de declarações do AD FS

A primeira coisa que precisamos fazer é configurar as declarações do AD FS. Estamos criando duas regras declarações: uma para o tipo de declaração Dentro da rede corporativa e um adicional para manter nossos usuários conectados.

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione a relação de confiança com terceira parte confiável.
3. No meio, clique com o botão direito do mouse na plataforma de identidade Microsoft Office 365 e selecione **Editar Regras de Declaração...**![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Em Regras de Transformação de Emissão, clique em **Adicionar Regra**.![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. No Assistente Adicionar Regra de Declaração de Transformação, selecione Passar ou filtrar uma Declaração de Entrada na lista e clique em Avançar.![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Na caixa ao lado do nome da regra de declaração, nomeie a regra. Por exemplo: InsideCorpNet.
7. Na lista suspensa, ao lado do tipo de declaração de entrada, selecione Dentro da rede corporativa.![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Clique em Concluir.
9. Em Regras de Transformação de Emissão, clique em **Adicionar Regra**.
10. No Assistente Adicionar Regra de Declaração de Transformação, selecione Enviar Declarações Usando uma Regra Personalizada da lista suspensa e clique em Avançar.
11. Na caixa abaixo do nome da regra de declaração: insira Manter Usuários Conectados.
12. Na caixa de regra Personalizada, digite:
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche o gerenciamento do AD FS.



### Configurar IPs confiáveis do Azure Multi-Factor Authentication com usuários federados
Agora que as declarações estão prontas, podemos configurar IPs confiáveis.

1. Entre no Portal de Gerenciamento do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório em que deseja configurar IPs confiáveis.
4. No Diretório que você selecionou, clique em Configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações de Serviço, em IPs Confiáveis, selecione **Para solicitações de usuários federados originárias da minha intranet.**![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Clique em Salvar.
8. Depois que as atualizações forem aplicadas, clique em Fechar.


É isso! Neste ponto, os usuários federados do Office 365 devem somente ter que usar MFA quando uma declaração for originada fora da intranet corporativa.

<!---HONumber=AcomDC_0810_2016-->
<properties
	pageTitle="Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo | Microsoft Azure"
	description="Explica as expressões de provisionamento declarativo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="markusvi;andkjell"/>


# Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo
A sincronização do Azure AD Connect criada com base em provisionamento declarativo foi introduzida pela primeira vez no Forefront Identity Manager 2010. Ela permite a você implementar sua lógica completa de negócios de integração de identidade sem a necessidade de escrever código compilado.

Uma parte essencial do provisionamento declarativo é a linguagem de expressão usada nos fluxos de atributo. A linguagem usada é um subconjunto de VBA (Visual Basic® for Applications) da Microsoft. Essa linguagem é usada no Microsoft Office e os usuários com experiência em VBScript também a reconhecerão. A Linguagem de Expressão de Provisionamento Declarativo está apenas usando funções e não é uma linguagem estruturada. Não existem métodos nem instruções. As funções são aninhadas no fluxo do programa expresso.

Para obter mais detalhes, consulte [Bem-vindo ao Visual Basic para referência de linguagem de aplicativos para Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos são fortemente tipados. Uma função aceita somente atributos do tipo correto. Ela também diferencia maiúsculas de minúsculas. Tanto nomes de função, quanto nomes de atributo devem ter a capitalização apropriada, ou um erro será gerado.

## Identificadores e definições de idioma

- As funções têm um nome seguido por argumentos entre parênteses: FunctionName(argumento 1, argumento N).
- Os atributos são identificados por colchetes, [NomeDoAtributo]
- Parâmetros são identificados por sinais de porcentagem: %NomeDoParâmetro%
- Constantes de cadeia de caracteres estão entre aspas: por exemplo, "Contoso" (Observação: deve-se usar aspas normais "", e não inglesas “”)
- Valores numéricos são expressos sem aspas e espera-se que sejam de tipo decimal. Valores hexadecimais são prefixados com &H. Por exemplo, 98052, &HFF
- Valores boolianos são expressos com constantes: True, False.
- Literais e constantes internas são expressos apenas com seu nome: NULL, CRLF, IgnoreThisFlow

### Funções
O provisionamento declarativo usa muitas funções para habilitar a possibilidade de transformar valores de atributo. Essas funções podem ser aninhadas para que o resultado de uma função seja passada para outra função.

`Function1(Function2(Function3()))`

A lista completa de funções pode ser encontrada em [referência de função](active-directory-aadconnectsync-functions-reference.md).

### Parâmetros
Um parâmetro é definido por um Connector ou por um administrador usando o PowerShell. Geralmente, os parâmetros contêm valores que são diferentes de sistema para sistema; por exemplo, o nome do domínio no qual o usuário está localizado. Esses parâmetros podem ser usados em fluxos de atributo.

O Active Directory Connector forneceu os seguintes parâmetros para Regras de Sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domain.Netbios | O formato Netbios do domínio que está sendo importado no momento, por exemplo, FABRIKAMSALES |
| Domain.FQDN | O formato FQDN do domínio que está sendo importado no momento, por exemplo, sales.fabrikam.com |
| Domain.LDAP | O formato LDAP do domínio que está sendo importado no momento, por exemplo, DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | O formato Netbios do nome da floresta que está sendo importado no momento, por exemplo, FABRIKAMCORP |
| Forest.FQDN | O formato FQDN do nome da floresta que está sendo importado no momento, por exemplo, fabrikam.com |
| Forest.LDAP | O formato LDAP do nome da floresta que está sendo importado no momento, por exemplo, DC=fabrikam,DC=com |

O sistema fornece o seguinte parâmetro, usado para obter o identificador do conector em execução no momento: `Connector.ID`

Veja um exemplo que preenche o domínio de atributo metaverso com o nome netbios do domínio em que o usuário está localizado: `domain` <- `%Domain.Netbios%`

### Operadores
Os operadores a seguir podem ser usados:

- **Comparação**: <, <=, <>, =, >, >=
- **Matemática**: +, -, *, -
- **Cadeia de caracteres**: & (concatenado)
- **Lógica**: && (e), || (ou)
- **Ordem de avaliação**: ( )

Os operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Por exemplo, o * (multiplicador) não é avaliado antes - (subtração). 2*(5+3) não é o mesmo que 2*5+3. Os parênteses ( ) são usados para alterar a ordem de avaliação quando a ordem de avaliação da direita à esquerda não é apropriada.

## Atributos de vários valores
As funções podem operar em atributos de valor único e de vários valores. Para atributos com vários valores, a função opera sobre cada valor e aplica a mesma função para cada valor.

Por exemplo: `Trim([proxyAddresses])` Faz um corte de todos os valores no atributo proxyAddress. `Word([proxyAddresses],1,"@") & "@contoso.com"` Para cada valor com um símbolo @, substitua o domínio por @contoso.com. `IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Procure o endereço SIP e remova-o dos valores.

## Recursos adicionais

- [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
- [Azure AD Connect Sync: referência de funções](active-directory-aadconnectsync-functions-reference.md)
- [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0831_2016-->
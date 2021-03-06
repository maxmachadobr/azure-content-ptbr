<properties 
	pageTitle="SDK e API do Python para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba tudo sobre o SDK e a API do Python, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Python para o Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter="python" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# SDKs e APIs de Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## SDK e API do Python para o Banco de Dados de Documentos

<table>
<tr><td>***Baixe o SDK***</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência de API do Python] (http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação do SDK do Python] (http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Introdução**</td><td>[Introdução ao SDK do Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataforma atual com suporte**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Notas de versão

### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção muito grande de taxa de solicitação, código de erro 429.) Por padrão, o Banco de Dados de Documentos tenta cada solicitação novamente nove vezes quando o código de erro 429 é encontrado, respeitando o tempo retryAfter no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Banco de Dados de Documentos agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Este tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.

- O Banco de Dados de Documentos agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para denotar a contagem de repetições limitadas e o tempo cumulativo que a solicitação aguardou entre as tentativas.

- A classe RetryPolicy foi removida e a propriedade correspondente (retry\_policy) foi exposta na classe document\_client. Como alternativa, foi introduzida uma classe RetryOptions, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Suporte adicionado para contas de banco de dados de várias regiões.

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Adicionado o suporte para o recurso TTL (vida útil) para documentos.

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correções de bugs relacionadas ao particionamento no lado do servidor a fim de permitir caracteres especiais no caminho de partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementação de [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementar o Upsert. Novos métodos UpsertXXX adicionados para dar suporte ao recurso Upsert.
- Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Oferece suporte ao índice Geoespacial.
- Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \\ ou terminar com um espaço.
- Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa a política de indexação V2.

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Oferece suporte à conexão de proxy.

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- SDK DO GA.

## Datas de lançamento e de baixa
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Banco de Dados de Documentos usando um SDK obsoleto será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do SDK do Banco de Dados de Documentos do Azure para Python anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.

<br/>

| Versão | Data do lançamento | Data de desativação 
| ---	  | ---	         | ---
| [1.9.0](#1.9.0) | 07 de julho de 2016 |---
| [1.8.0](#1.8.0) | 14 de julho de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.1](#1.6.1) | 08 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de março de 2016 |---
| [1.5.0](#1.5.0) | 03 de janeiro de 2016 |---
| [1\.4.2](#1.4.2) | 06 de outubro de 2015 |---
| [1\.4.1](#1.4.1) | 06 de outubro de 2015 |---
| [1\.2.0](#1.2.0) | 06 de agosto de 2015 |---
| [1\.1.0](#1.1.0) | 09 de julho de 2015 |---
| [1\.0.1](#1.0.1) | 25 de maio de 2015 |---
| [1\.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.4-prelease | 14 de janeiro de 2015 | 29 de fevereiro de 2016
| 0.9.3-prelease | 09 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.2-prelease | 25 de novembro de 2014 | 29 de fevereiro de 2016
| 0.9.1-prelease | 23 de setembro de 2014 | 29 de fevereiro de 2016
| 0.9.0-prelease | 21 de agosto de 2014 | 29 de fevereiro de 2016

## Perguntas frequentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Confira também

Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->

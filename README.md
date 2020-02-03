# Solução Teste Bureau

![Diagrama Bureau](https://user-images.githubusercontent.com/22353317/73624533-f49bc080-461f-11ea-9dd0-ec3a183afa3a.png)

#### Cloud AWS
- **Edge Service** escolhido é o Cloud Front como CDN para uma melhor disponibilização das interfaces de comunicação. Também poderíamos incluir outros serviços como WAF, AWS Shield e Route 53.
- **AWS VPC** camada de proteção que permite acesso apenas com VPN.

#### API Gateway
- **Interfaces de comunicação**
	- Devido a necessidade de atender ao máximo de situações possíveis, eu disponibilizaria 2 métodos principais de consumo da API.
		- Rest
		- Soap (WebService)
	- Particularmente eu também disponibilizaria uma API em GraphQL.
	- Também poderia ser desenvolvido métodos adicionais em XML-RPC, JSON-RPC e até mesmo um SDK dependendo da necessidade.


#### Banco de Dados
- **Base A** - PostgreSQL
	- Devido a sensibilidade dos dados da base A o PostgreSQL deve ter um Master-Slave configurada como também encriptação por colunas e encriptação dos arquivos do banco de dados, além claro dos usuário com permissões especificas.

- **Base B** - PostgreSQL
	- Seguindo os mesmos princípios da base A devido aos dados sensíveis mas com a necessidade de um acesso um pouco mais rápido, eu seguiria os mesmo níveis de segurança da base A com a diferença de adicionar uma replicação de leitura ou camada de cache.

	- Para a parte de extração de dados para aprendizado de máquina eu teria uma replicação da base B em arquivo colunar (Apache Parquet) específica para essa extração. Esses arquivos seriam salvos em um bucket s3 para futuramente serem utilizados em ciência de dados.

- **Base C** - PostgreSQL
	- Para a base C, como não armazena dados sensíveis não teríamos Master-Slave, replicação ou encriptação aumentando a performance. Também ativaria algumas configurações para melhorar a performance e adicionaria uma camada em cache com o Redis ou Elasticsearch dependendo da necessidade.

- **PII**
	- Indo um pouco além e levando em consideração a sensibilidade dos dados que serão armazenados eu incluiria em um processo adicional para já estarmos de acordo com a GDPR/LGPD. Devemos ter um histórico de todos os dados replicados, para caso seja solicitado podermos apagar completamente os dados. Poderíamos utilizar o Dynamic Masking, In-Place Anonymization ou alguma Masking Function para a anonimização dos dados.

#### Microsserviços
- **Microsserviços**
	- Cada microserviço se conectaria a sua respectiva base. 
		- Microserviço A -> Base de dados A
		- Microserviço B -> Base de dados B
		- Microserviço C -> Base de dados C
	- Na necessidade de acesso a algum dado que está em outra base que não seja aquela que o microserviço está conectado a solicitação do dado aconteceria através da mensageria, assim o serviço que se conecta com a base em que o dado está forneceria ao outro microserviço o dado necessário.

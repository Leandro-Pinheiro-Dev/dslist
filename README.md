# Projeto DSList - Intensivão Java Spring

## Descrição

O **Projeto DSList** é uma aplicação backend desenvolvida utilizando o **Java** com o framework **Spring Boot**. O objetivo do projeto é criar um sistema de listas de jogos, onde o usuário pode visualizar jogos pertencentes a diferentes listas, ordená-los e interagir com as informações através de endpoints da API.

A aplicação inclui uma camada de persistência de dados usando o **JPA** com um banco de dados relacional, e uma interface para comunicação com o frontend que pode ser implementada utilizando tecnologias como **React** ou **Vue.js**.

## Funcionalidades

- **Cadastro de Jogos**: O sistema permite que os jogos sejam cadastrados no banco de dados, incluindo informações como título, ano de lançamento, plataforma, descrição, etc.
- **Listas de Jogos**: O sistema organiza os jogos em diferentes listas (por exemplo, "Aventura e RPG", "Jogos de plataforma"), onde é possível visualizar a posição de cada jogo dentro da lista.
- **Ordenação de Jogos**: A posição de cada jogo dentro de uma lista pode ser alterada através de um endpoint de modificação.
- **CORS (Cross-Origin Resource Sharing)**: A aplicação está configurada para aceitar requisições de origens específicas, permitindo que o frontend se conecte ao backend em diferentes ambientes.

## Tecnologias Utilizadas

- **Java 17** e **Spring Boot 2.x**: Framework para o desenvolvimento do backend.
- **JPA (Java Persistence API)**: Para a comunicação com o banco de dados.
- **Maven**: Para gerenciamento de dependências e construção do projeto.
- **H2 Database**: Para o ambiente de testes com um banco de dados em memória.
- **PostgreSQL ou MySQL**: Para o ambiente de produção.

## Estrutura do Projeto

A estrutura do projeto segue um padrão de camadas para separar a lógica de negócios, dados e controle da aplicação.

### Código de Exemplos

#### Plug-in Maven (pom.xml)

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version>
</plugin>
```

#### Configuração do Spring (application.properties)

```properties
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false
cors.origins=${CORS_ORIGINS:http://localhost:5173,http://localhost:3000}
```

#### Configuração para Testes (application-test.properties)

```properties
# H2 Connection
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

#### Configuração CORS (WebConfig.java)

```java
@Configuration
public class WebConfig {

	@Value("${cors.origins}")
	private String corsOrigins;
	
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedMethods("*").allowedOrigins(corsOrigins);
			}
		};
	}
	
}
```

#### Consulta de Jogos (GameListRepository.java)

```java
@Query(nativeQuery = true, value = """
	SELECT tb_game.id, tb_game.title, tb_game.game_year AS `year`, tb_game.img_url AS imgUrl, 
	tb_game.short_description AS shortDescription, tb_belonging.position
	FROM tb_game
	INNER JOIN tb_belonging ON tb_game.id = tb_belonging.game_id
	WHERE tb_belonging.list_id = :listId
	ORDER BY tb_belonging.position
		""")
List<GameMinProjection> searchGameList(Long listId);

@Modifying
@Query(nativeQuery = true, value = "UPDATE tb_belonging SET position = :newPosition WHERE list_id = :listId AND game_id = :gameId")
void updateBelongingPosition(Long listId, Long gameId, Integer newPosition);
```

#### Importação de Dados (import.sql)

```sql
INSERT INTO tb_game_list (name) VALUES ('Aventura e RPG');
INSERT INTO tb_game_list (name) VALUES ('Jogos de plataforma');

-- Inserção de jogos
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES ('Mass Effect Trilogy', 4.8, 2012, 'Role-playing (RPG), Shooter', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/1.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti unde repellat non quibusdam! Id nihil itaque ipsum!', 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum placeat eligendi, quis maiores veniam.');
-- [Outros jogos seguem um padrão semelhante...]
```

## Endpoints da API

A aplicação oferece endpoints REST para as seguintes funcionalidades:

- **GET** `/games`: Lista todos os jogos.
- **GET** `/games/{id}`: Retorna os detalhes de um jogo específico.
- **POST** `/games`: Cria um novo jogo.
- **GET** `/lists/{id}/games`: Retorna os jogos de uma lista específica.
- **PUT** `/lists/{id}/games/{gameId}/position`: Atualiza a posição de um jogo dentro de uma lista.

## Contribuições

Este projeto foi desenvolvido como parte do **Intensivão Java Spring**, conduzido pelo professor **Nelio Alves**. Se você deseja contribuir, fique à vontade para criar uma **pull request** ou relatar issues no repositório.

---


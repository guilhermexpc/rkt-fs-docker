# Comandos básicos do Docker

## Listar containers

```powershell
docker ps
```

Lista apenas os containers em execução.

```powershell
docker ps -a
```

Lista todos os containers, incluindo os que estão parados. A flag `-a` significa
“all” (todos).

## Listar imagens

```powershell
docker image ls
```

Lista as imagens disponíveis localmente.

## Criar uma imagem

```powershell
docker build -t nome-da-imagem .
```

Cria uma imagem usando o `Dockerfile` do diretório atual. A flag `-t` define o
nome e a tag da imagem.

Exemplo:

```powershell
docker build -t api:1.0 .
```

Nesse caso, `api` é o nome e `1.0` é a tag. Se a tag não for informada, o
Docker usa `latest`:

```powershell
docker build -t api .
```

Também é possível criar uma imagem já com uma tag específica:

```powershell
docker build -t meu-usuario/api:1.0 .
```

## Criar e executar um container

```powershell
docker run -p 3333:3333 api:1.0
```

Cria um container a partir da imagem `api:1.0` e publica a porta `3333` do
container na porta `3333` da máquina.

O formato da publicação é:

```text
-p porta-da-maquina:porta-do-container
```

Para executar em segundo plano, use a flag `-d`:

```powershell
docker run -d -p 3333:3333 --name api-container api:1.0
```

A flag `-d` significa “detached”. O container continua executando, mas o
terminal fica livre. Sem `-d`, os logs permanecem ligados ao terminal atual.

Para executar o container do Postgres em segundo plano:

```powershell
docker run --name meu-postgres -p 5432:5432 -d meu-postgres
```

## Acessar o terminal do container

```powershell
docker exec -t container-id sh
```

Executa o shell `sh` dentro de um container em execução. Substitua
`container-id` pelo ID ou nome do container.

## Remover um container

```powershell
docker rm api-container
```

Remove um container parado.

Para remover um container mesmo que ele esteja em execução, use `-f`:

```powershell
docker rm -f api-container
```

A flag `-f` significa “force” e força a parada e a remoção do container.

## Remover uma imagem

```powershell
docker rmi api:1.0
```

Remove uma imagem local.

Se a imagem estiver sendo usada ou não puder ser removida normalmente, use
`-f`:

```powershell
docker rmi -f api:1.0
```

A flag `-f` força a remoção da imagem. Isso pode exigir a remoção dos
containers que ainda a utilizam.

## Exemplo completo

```powershell
docker build -t api:1.0 .
docker run -d -p 3333:3333 --name api-container api:1.0
docker ps
docker logs api-container
docker rm -f api-container
docker rmi api:1.0
```

<!-- Docker Compose -->

## Docker Compose

O Docker Compose permite definir vários containers, suas configurações e a
forma como eles se relacionam em um arquivo YAML. Neste projeto, o arquivo
usado é `docker-compose.yml`, criado na raiz do projeto, no mesmo diretório do
`Dockerfile`.

### Criar o arquivo `docker-compose.yml`

Um exemplo básico para a API é:

```yaml
services:
	api:
		build:
			context: .
			dockerfile: Dockerfile
		image: nodejs
		container_name: api-nodejs
		ports:
			- "3333:3333"
		depends_on:
			- postgres
```

O Compose usa a propriedade `services` para agrupar os serviços que serão
executados. Cada nome abaixo de `services` identifica um serviço. Neste
exemplo, `api` é o serviço da aplicação.

As principais propriedades usadas são:

- `build`: informa que a imagem será criada a partir de um Dockerfile.
- `context`: define o diretório enviado para o processo de build. O valor `.`
  representa o diretório atual, a raiz do projeto.
- `dockerfile`: informa qual arquivo será usado para criar a imagem. Neste
  caso, é o arquivo `Dockerfile`.
- `image`: define o nome da imagem criada ou usada pelo serviço. Aqui, a imagem
  será chamada de `nodejs`.
- `container_name`: define o nome do container. O container da API será chamado
  de `api-nodejs`.
- `ports`: publica portas no formato `porta-da-maquina:porta-do-container`.
  Assim, a porta `3333` da máquina encaminha requisições para a porta `3333`
  do container.
- `depends_on`: define uma dependência entre serviços. Neste exemplo, a API
  depende do serviço `postgres`, que será iniciado antes dela.

O serviço do banco pode ser adicionado ao mesmo arquivo:

```yaml
	postgres:
		image: postgres:alpine3.24
		container_name: postgres
		ports:
			- "5432:5432"
		environment:
			POSTGRES_USER: postgres
			POSTGRES_PASSWORD: postgres
			POSTGRES_DB: api
		volumes:
			- database:/var/lib/postgresql/data

volumes:
	database:
```

No Compose, `image` deve conter apenas o nome da imagem, como
`postgres:alpine3.24`. O comando `docker pull` não deve ser escrito dentro do
valor dessa propriedade. O Compose baixa a imagem automaticamente quando
necessário.

### Validar a configuração

```powershell
docker compose config
```

`config`: Valida e exibe a configuração final do arquivo Compose.

### Criar e iniciar os serviços

```powershell
docker compose up -d
```

`up`: Cria e inicia os serviços definidos no arquivo Compose.

`-d`: Executa os containers em segundo plano.

Para baixar explicitamente as imagens antes de iniciar os serviços, use:

```powershell
docker compose pull
```

`pull`: Baixa as imagens dos serviços definidos no arquivo Compose.

### Parar e remover os serviços

```powershell
docker compose down
```

`down`: Remove os containers e a rede criada pelo Compose. As imagens e os
volumes nomeados são mantidos.

```powershell
docker compose down -v
```

`-v`: Remove os volumes declarados no arquivo, como `database`.

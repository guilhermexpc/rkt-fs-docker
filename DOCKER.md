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

<!-- Comentário adicionado ao final do arquivo -->

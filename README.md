# [API Playbook Backstage](https://backstage.io)

## Rodando ambiente de desenvolvimento

Você precisa ter o [Node.js](https://nodejs.org/) e o [Yarn](https://yarnpkg.com/) instalados no seu ambiente.

Para instalar as dependências:

```sh
yarn install
```


Para executar o backend e o frontend juntos, basta executar:

```sh
yarn dev
```

Para executar somente o frontend, basta executar:

```sh
yarn start
```

Para executar somente o backend, basta executar:

```sh
yarn start-backend
```

A partir deste ponto, você tem a aplicação rodando sem erros, porém não conseguirá acessar sem fazer a configuração da integração com o GitHub.

## Configurar integração com o GitHub

Crie um aplicativo OAuth no GitHub acessando [https://github.com/settings/applications/new](https://github.com/settings/applications/new).

O URL da página inicial (Homepage URL) deve apontar para o frontend do Backstage:

- Local: http://localhost:3000
- Produção: https://url-produtivo

O URL de retorno de autorização (Authorization callback URL) deve apontar para o backend de autenticação:

- Local: http://localhost:7007/api/auth/github/handler/frame
- Produção: https://url-produtivo/api/auth/github/handler/frame

Disponibilize o `Client ID` e o `Client Secret` nas respectivas variáveis de ambiente: `AUTH_GITHUB_CLIENT_ID` e `AUTH_GITHUB_CLIENT_SECRET`.

## Rodando via Docker

Empacote a aplicação:

```sh
yarn build:backend --config ../../app-config.yaml
```

Construa a imagem do container:

```sh
docker image build . -f packages/backend/Dockerfile --tag backstage-apiplaybook
```

Execute o container localmente (não se esqueça de passar as variáveis de ambiente):

```sh
docker run -e "AUTH_GITHUB_CLIENT_ID=" -e "AUTH_GITHUB_CLIENT_SECRET=" -e "DATABASE_URL=" -e "GHE_TOKEN=" --name backstage-apiplaybook -it -p 7007:7007 backstage-apiplaybook 
```

Tanto o frontend quanto o backend estarão disponíveis em [http://localhost:7007](http://localhost:7007)

## Publicando no heroku

1. Instale a [heroku-cli](https://devcenter.heroku.com/articles/heroku-cli).
2. Autentique-se na CLI:
	```sh
	heroku login 
	```
3. Caso não tenha um app criado, crie:
	```sh
	heroku create <nome-do-seu-app>
	```
4. Configure a aplicação para utilizar a stack de container:
	```sh
	heroku stack:set container -a <nome-do-seu-app>
	```
5. Efetue a autenticação de container para publicação no registry:
	```sh
	heroku container:login
	```
6. Publique a imagem no registry do Heroku (a tag da sua imagem deve ser igual ao registry):
	```sh
	docker image push registry.heroku.com/<nome-do-seu-app>/web
	```
7. Execute o deploy do container no seu app criado no heroku:
	```sh
	heroku container:release web -a <nome-do-seu-app>
	```



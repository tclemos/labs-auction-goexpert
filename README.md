# EU VOU FAZER UM LEILÃO

![EU VOU FAZER UM LEILÃO](./image.png)

## Adaptações do repo original

- alteração da API POST /auction para retornar o leilão criado, facilitando o acesso as campos gerados durante a criação, como ID, STATUS e TIMESTAMP.
- Aparentemente o repo já tem um pedaço da solução solicitada no desafio e também tem um vídeo explicando como fechar o leilão automaticamente com uma go routine, porém a solução proposta não funciona caso o servidor seja reiniciado, então implementei do meu jeito.
- O mutex e map de alteração de status que estava no repositório de Bid foi movido para o repositório de Auction, assim a atualização dos dados do banco de dados trabalharão de forma concorrente com os bids.

## Como testar?

O arquivo [.env](./cmd/auction/.env) possui as variáveis de configuração que serão utilizadas na execução da aplicação

O fechamento do leilão é verificado a cada intervalo configurado na variável de ambiente `AUCTION_CLOSE_INTERVAL`
O tempo máximo para um leilão ficar ativo é definido na variável de ambiente `AUCTION_MAX_DURATION`

O comando abaixo inicializará a aplicação e suas dependências

```bash
docker compose up -d
```

O arquivo [test.http](./test/test.http) oferece requests para testar a aplicação

Para testar a finalização automática de leilões, siga os seguintes passos:

- Execute o request para criação de leilão
- Utilizando a resposta da criação de leilão, copie o valor do campo `id`.
- Altere o request de busca de leilão para utilizar o `id` do leilão criado.
- Utilize o request de busca de leilão para listar o leilão criado e observe o campo `status`. Repita esse passo até verificar que o leilão está fechado.
  - caso o leilão esteja ativo, o valor estará como `0`
  - caso tenha sido fechado, o valor estará como `1`
  
A configuração atual do arquivo `.env` verificará leilões para serem fechados a cada `5s` e fechará os leilões `30s` após terem sido criados.

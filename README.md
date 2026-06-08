# Intelligent Buy Coupa Launcher

POC estatica de launcher para abrir o Intelligent Buy a partir de um iframe configurado no Coupa.

Este projeto e exclusivo para Coupa/Sourcing. Ele foi pensado para aparecer em um painel horizontal/medio dentro da tela de requisicao do Coupa e oferecer duas acoes:

- Abrir Mapa
- Abrir Resumo

## Arquivos

- `index.html.html`: pagina principal do launcher Coupa.
- `launcher.config.js`: configuracao das rotas do Intelligent Buy.

## Como funciona

O Coupa chama o launcher enviando parametros pela URL. O parametro principal esperado e:

- `object_id`: identificador do objeto Coupa.

Quando `object_id` estiver presente, o launcher tambem envia os aliases esperados pelo Intelligent Buy:

- `projectId=<object_id>`
- `eventId=<object_id>`
- `negotiationCode=<object_id>`

Todos os demais query params recebidos do Coupa sao preservados ao abrir Mapa ou Resumo.

## Configuracao

Os destinos ficam centralizados em `launcher.config.js`:

```js
window.ARIBA_LAUNCHER_CONFIG = {
  appBaseUrl: 'https://uintelligentbuy-hml.stratesys.io',
  mapPath: '/admin/maps',
  summaryPath: '/admin/review/quotation',
};
```

Apesar do nome legado da variavel de configuracao, este projeto esta documentado e preparado para uso no Coupa.

- `appBaseUrl`: URL base do Intelligent Buy.
- `mapPath`: rota usada para abrir o Mapa.
- `summaryPath`: rota usada para abrir o Resumo.

## Exemplo local

```text
http://localhost:3000/index.html.html?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364
```

Ao clicar em `Abrir Mapa`, a URL gerada deve seguir este formato:

```text
https://uintelligentbuy-hml.stratesys.io/admin/maps?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364&projectId=455&eventId=455&negotiationCode=455
```

Ao clicar em `Abrir Resumo`, a URL gerada deve seguir este formato:

```text
https://uintelligentbuy-hml.stratesys.io/admin/review/quotation?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364&projectId=455&eventId=455&negotiationCode=455
```

Se o navegador bloquear a nova aba, o launcher informa o bloqueio no proprio iframe.

## Como rodar localmente

Na raiz deste projeto:

```bash
npx serve .
```

Depois abra a URL exibida pelo `serve`, por exemplo:

```text
http://localhost:3000/index.html.html
```

## Deploy

O projeto e estatico e nao precisa de build.

Sugestao para Vercel, Azure Static Web Apps ou outro hosting estatico:

- Build command: vazio.
- Output directory: vazio ou `.` conforme a UI solicitar.
- Publicar `index.html.html` e `launcher.config.js` juntos.

## Validacao no Coupa

O teste final precisa ser feito dentro do Coupa para confirmar:

1. Se o iframe permite `window.open` em uma acao de clique do usuario.
2. Se os query params chegam corretamente ao launcher.
3. Se `object_id` chega preenchido na tela de requisicao.
4. Se o Intelligent Buy recebe `projectId`, `eventId` e `negotiationCode` com o valor de `object_id`.
5. Se `/admin/review/quotation` e a rota final correta para o Resumo no ambiente HML.

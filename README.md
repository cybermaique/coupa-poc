# Intelligent Buy Launchers

POC estática de launchers para abrir o Intelligent Buy a partir de iframes em ferramentas externas.

## Entradas disponíveis

- `index.html`: launcher vertical para SAP Ariba. Preserva os query params recebidos e abre Mapa ou Resumo em uma nova aba.
- `coupa.html`: launcher horizontal para Coupa/Sourcing. Usa `object_id` como identificador do objeto e preenche os aliases esperados pelo Intelligent Buy.

## Configuração

Os destinos ficam centralizados em `launcher.config.js`:

```js
window.ARIBA_LAUNCHER_CONFIG = {
  appBaseUrl: 'https://uintelligentbuy-hml.stratesys.io',
  mapPath: '/admin/maps',
  summaryPath: '/admin/review/quotation',
};
```

- `appBaseUrl`: URL base do Intelligent Buy.
- `mapPath`: rota usada para abrir o Mapa.
- `summaryPath`: rota usada para abrir o Resumo.

## Launcher Ariba

O `index.html` mantém o comportamento original:

- Recebe os parâmetros enviados pela Ariba na URL do launcher.
- Mantém esses parâmetros ao abrir `Abrir Mapa` ou `Abrir Resumo`.
- Usa fallback manual quando o navegador ou o iframe bloqueia a abertura automática da nova aba.

Exemplo local:

```text
http://localhost:3000?realm=744862388-T&eventId=Doc2200752930&projectId=WS2200752923
```

Valide:

1. `Abrir Mapa` abre `https://uintelligentbuy-hml.stratesys.io/admin/maps` preservando os parâmetros.
2. `Abrir Resumo` abre `https://uintelligentbuy-hml.stratesys.io/admin/review/quotation` preservando os parâmetros.
3. O fallback exibe `Abrir link manualmente` e `Copiar link` caso a nova aba seja bloqueada.

## Launcher Coupa

O `coupa.html` foi pensado para o iframe horizontal/médio do Coupa.

Ele preserva todos os parâmetros recebidos e, quando `object_id` existir, também envia:

- `projectId=<object_id>`
- `eventId=<object_id>`
- `negotiationCode=<object_id>`

Exemplo local:

```text
http://localhost:3000/coupa.html?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364
```

URLs esperadas ao clicar:

```text
https://uintelligentbuy-hml.stratesys.io/admin/maps?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364&projectId=455&eventId=455&negotiationCode=455
```

```text
https://uintelligentbuy-hml.stratesys.io/admin/review/quotation?coupahost=stratesys-latam-demo.coupacloud.com&object_id=455&object_type=quote_request&user_id=1364&projectId=455&eventId=455&negotiationCode=455
```

O launcher Coupa não exibe link manual nem botão de copiar. Se a nova aba for bloqueada, ele apenas informa o bloqueio no próprio iframe.

## Como rodar localmente

Na raiz deste projeto:

```bash
npx serve .
```

Depois abra a URL exibida pelo `serve`, por exemplo:

```text
http://localhost:3000
```

## Deploy na Vercel ou Static Web Apps

Configure o projeto apontando para esta pasta como raiz. Não é necessário build.

Sugestão:

- Build command: vazio.
- Output directory: vazio ou `.` conforme a UI solicitar.
- `launcher.config.js`, `index.html` e `coupa.html` devem ficar publicados juntos.

## Validação dentro das ferramentas

O teste final precisa ser feito dentro da SAP Ariba e do Coupa para confirmar:

1. Se o modal/iframe permite `window.open` em uma ação de clique do usuário.
2. Se os query params chegam corretamente ao launcher.
3. Se o Intelligent Buy recebe os parâmetros esperados ao abrir Mapa ou Resumo.
4. Se `/admin/review/quotation` é a rota final correta para o Resumo no ambiente HML.
